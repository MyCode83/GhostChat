# GhostChat 👻
[![Go Version](https://img.shields.io/badge/Go-1.21%2B-blue.svg)](https://golang.org/)
[![Security](https://img.shields.io/badge/Security-AES--256--GCM-red.svg)]()
[![Network](https://img.shields.io/badge/Network-Libp2p-orange.svg)]()

GhostChat es una utilidad TUI P2P descentralizada para comunicaciones efímeras. Diseñada bajo el principio de "Zero Trust", elimina dependencias de infraestructura centralizada mediante redes peer-to-peer directas y criptografía de grado militar.

## 1. Arquitectura y Modelo de Seguridad

El sistema está diseñado para mitigar ataques MITM (Man-in-the-Middle) y escaneos de red pasivos/activos.

* **Networking Core:** Construido sobre `libp2p` con descubrimiento mDNS para NAT Traversal automático.
* **Criptografía:** Túneles cifrados mediante **AES-256-GCM** (Autenticación y Cifrado Simétrico).
* **Generación de Claves:** Entropía criptográfica pura (`crypto/rand`) para la derivación de tokens de 256 bits.
* **Handshake Challenge-Response:** Descarte de paquetes en capa de aplicación para clientes no autenticados (Drop silencioso).
* **Volatilidad:** Operación 100% In-Memory. Sin persistencia de logs, claves o estado de red en el disco duro.

## 2. Instalación Rápida

Descarga el binario precompilado para tu arquitectura. No requiere dependencias externas ni runtime de Go.

* **Windows:** Ejecuta `ghostchat.exe` directamente desde la terminal.
* **Linux / macOS:** 
  ```bash
  chmod +x ghostchat-<os>
  ./ghostchat-<os>
  ```

## 3. Guía de Operación (TUI)

La interfaz basada en Bubble Tea se opera íntegramente por teclado para máxima velocidad.

* **Host (Crear Sala):** Genera un token criptográfico efímero. Compártelo a través de un canal OOB (Out-of-Band) seguro.
* **Client (Unirse):** Introduce el token para iniciar la resolución mDNS y el handshake P2P.
* **Comandos de Chat:**
  * Texto plano: Escribe el payload y pulsa `Enter`.
  * Transferencia: `/file <ruta_absoluta>` (Ej: `/file /tmp/data.pdf`). Los chunks de 64KB se envían cifrados y se reensamblan en `./downloads`.
* **Kill Switch:** Pulsa `Esc` para purgar la memoria RAM y destruir los descriptores de red instantáneamente.

## 4. Compilación y Ofuscación (Build)

Para entornos de producción, el binario debe compilarse sin la tabla de símbolos (Stripped) y aislando las rutas locales para dificultar al máximo la ingeniería inversa.

```bash
# Producción Windows (Target por defecto)
go build -ldflags="-s -w" -trimpath -o bin/ghostchat.exe ./cmd/ghost

# Cross-Compilation (Linux/macOS)
GOOS=linux GOARCH=amd64 go build -ldflags="-s -w" -trimpath -o bin/ghostchat-linux ./cmd/ghost
GOOS=darwin GOARCH=arm64 go build -ldflags="-s -w" -trimpath -o bin/ghostchat-mac ./cmd/ghost
```
