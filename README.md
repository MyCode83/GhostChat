# GHOSTCHAT — Fortified Secure E2EE P2P

Chat P2P cifrado de extremo a extremo escrito en Go, construido sobre
[libp2p](https://github.com/libp2p/go-libp2p) y
[Bubble Tea](https://github.com/charmbracelet/bubbletea) (interfaz TUI).

Sin servidores centrales: cada mensaje viaja cifrado AES-256-GCM entre pares que se
descubren en la DHT pública de IPFS/libp2p, con soporte de NAT traversal
(hole punching + relay automático vía bootstrap de IPFS).

## Características

- 🔐 **Cifrado E2E real** — AES-256-GCM; la clave se deriva (SHA-256) del código de invitación.
- 🕵️ **Anonimato** — apodo configurable por sesión.
- 🌐 **Sin servidores** — descubrimiento de pares mediante DHT Kademlia pública de IPFS.
- 🔥 **Atraviesa NAT** — hole punching + AutoRelay con candidatos estáticos de IPFS.
- 📁 **Envío de archivos** — con `/file ruta` (versión básica, cifrado).
- 🗑️ **Kill switch** — `Esc` o `Ctrl+C` destruye la sesión, cierra streams y host.
- 💬 **TUI** — interface terminal con Bubble Tea / Lipgloss.

## Requisitos

- Go 1.27 o superior (probado con go1.27.1).

## Compilar

```bash
go build -o ghostchat.exe ./cmd/ghost
```

## Ejecutar

```bash
./ghostchat.exe
```

## Uso

1. **Creador**: menú → "Crear Sala Blindada (Generar Código)".
   Copia el código de invitación generado y envíalo a tu contacto por otro canal.
2. **Contacto**: menú → "Unirse a Sala con Código" y pega el código.
3. Ambos verán `[+] ¡Conexión segura establecida con ...!` (puede tardar unos
   segundos; el descubrimiento se reintenta cada 20 s).
4. Escribe mensajes libremente, o usa `/file ruta\al\archivo` para enviar un
   archivo cifrado. `Esc` destruye la sesión; `Ctrl+C` o `q` salen.

## Estructura

```
cmd/ghost/             Entrada de la aplicación
internal/crypto/       Cifrado AES-GCM + derivación de clave
internal/network/      Capa libp2p: host, DHT, descubrimiento, encuadre de mensajes
internal/ui/           Interfaz de terminal (Bubble Tea)
```

## Correcciones de robustez (v2, 2026-09)

Esta versión corrige dos fallos que impedían el funcionamiento del chat en la v1:

1. **El creador de la sala nunca iniciaba la red** — `terminal.go` solo generaba el
   código de invitación sin llamar a `ConnectToRoomWithInvite`, dejando
   `roomKey == nil` y `globalHost == nil`, por lo que `SendMessage` era inerte.
   Ahora el flujo "Crear Sala" también inicia la red (conexión asíncrona en goroutine).
2. **Encuadre de mensajes defectuoso** — el receptor leía con `bufio.Reader.Read`
   (máx. 4096 B, sin longitud); una ráfaga de varios mensajes cifrados llegaba
   concatenada y el descifrado AES-GCM fallaba ("Paquete corrupto"). Ahora cada
   mensaje se envía con prefijo de longitud _big-endian_ de 4 bytes y se lee con
   `io.ReadFull` (límite 64 MB).

Mejoras adicionales: rediscovery periódico (20 s) en vez de una única búsqueda,
deduplicación de streams con `sync.RWMutex`, sesión cancelable (el kill switch
detiene todas las goroutines de red), y sustitución de `EnableAutoRelay()` por
`EnableAutoRelayWithStaticRelays(dht.DefaultBootstrapPeers)` (la variante sin
argumentos **panica** en go-libp2p v0.49).

## Pruebas

```bash
go test ./... -count=1
```

Incluye tests de regresión con redes libp2p reales en loopback:

- `TestFramingV1` / `TestCreadorNoIniciaRed` — reproducen los fallos de la v1.
- `TestFramingV2` — ráfaga de 3 mensajes + mensaje de 5000 bytes íntegros.
- `TestCreadorIniciaRedAlConectar` — arranque real de DHT + bootstrap de IPFS.
- `TestCrearSalaIniciaRed` — la UI inicia la red al crear sala.

## Seguridad

Este proyecto es una demo/prototipo educativo. Para producción considera:

- Firme/autentique los códigos de invitación (débil ante suplantación si el canal
  de compartición no es autónomo).
- Añada autenticación de pares y sincronización de mensajes offline.
- El envío de archivos embebe el contenido en memoria (`/file`) — tamaño limitado.
- Defina un límite de sesión y reintentos para resistir ataques Sybil en la DHT.