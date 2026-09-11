# GhostChat 👻
[![Go Version](https://img.shields.io/badge/Go-1.21%2B-blue.svg)](https://golang.org/)
[![Security](https://img.shields.io/badge/Security-AES--256--GCM-red.svg)]()
[![Network](https://img.shields.io/badge/Network-Libp2p-orange.svg)]()

GhostChat es una utilidad TUI P2P descentralizada para comunicaciones efímeras. Diseñada bajo el principio de "Zero Trust", elimina dependencias de infraestructura centralizada mediante redes peer-to-peer directas y criptografía de grado militar.

## 1. Arquitectura y Modelo de Seguridad

El sistema está diseñado para mitigar ataques MITM (Man-in-the-Middle) y escaneos de red pasivos/activos.

* **Networking Core:** Construido sobre `libp2p` con ruteo Kademlia DHT y AutoNAT/Relay para atravesar firewalls y conectar a nivel global a través de internet.
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
