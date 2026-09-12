# GhostChat 👻
[![Go Version](https://img.shields.io/badge/Go-1.21%2B-blue.svg)](https://golang.org/)
[![Security](https://img.shields.io/badge/Security-AES--256--GCM-red.svg)]()
[![Network](https://img.shields.io/badge/Network-Libp2p-orange.svg)]()

Chat P2P descentralizado y cifrado de extremo a extremo escrito en Go, construido sobre [libp2p](https://github.com/libp2p/go-libp2p) y [Bubble Tea](https://github.com/charmbracelet/bubbletea) (interfaz TUI). Diseñado bajo el principio de "Zero Trust", elimina dependencias de infraestructura centralizada mediante redes peer-to-peer directas.

## 1. Arquitectura y Características

* **🔐 Cifrado E2E real (AES-256-GCM):** Túneles cifrados con claves derivadas (SHA-256) del código de invitación. Entropía pura para mitigación de ataques MITM.
* **🌐 Sin servidores y NAT Traversal:** Descubrimiento de pares mediante DHT Kademlia pública de IPFS. Atraviesa firewalls con *hole punching* y *AutoRelay* con candidatos estáticos.
* **🕵️ Anonimato y Volatilidad:** Apodo configurable por sesión. Operación 100% In-Memory (sin persistencia de red o claves en disco duro).
* **🛡️ Handshake Challenge-Response:** Descarte silencioso de paquetes en capa de aplicación para clientes no autenticados.
* **🗑️ Kill switch:** Pulsa `Esc` o `Ctrl+C` para destruir la sesión, purgar la memoria RAM y cerrar descriptores de red instantáneamente.

## 2. Instalación y Ejecución

### Opción A: Binarios precompilados
No requiere dependencias externas ni runtime de Go.
* **Windows:** Ejecuta `ghostchat.exe` directamente.
* **Linux / macOS:**
  ```bash
  chmod +x ghostchat-<os>
  ./ghostchat-<os>