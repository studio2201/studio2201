# studio2201 Architectural Design

**studio2201** is a specialized digital foundry focused on experimental web experiences. Rather than building massive, monolithic game engines, the organization is dedicated to building hyper-focused, single-purpose interactive containers.

## 1. The "9x9" Vision (9 Tools, 9 Games)

The core architecture of studio2201 revolves around the "9x9" paradigm. The organization will consist of exactly **9 Web-Based Tools** and **9 Web-Based Games**.
*   **Hyper-Focused Scope**: Each repository does exactly one thing and does it flawlessly. There is no feature creep.
*   **Web-Native Containers**: Every tool and game is packaged as a standalone, ultra-lightweight web container (Docker/Podman) that can be spun up in milliseconds.

## 2. Copyright Safety & Clean Room Design

Due to past issues with upstream copyright claims, studio2201 enforces a strict **Clean Room Design** policy.
*   **No Code Copying**: We do not fork, clone, or heavily borrow from existing open-source game engines or toolsets. Every line of logic is written from first principles.
*   **Sovereign Assets**: All visual assets, physics engines, and state synchronization algorithms are developed in-house to guarantee 100% intellectual property ownership.

## 3. Technology Stack

While the tools and games are simple in scope, they are built on modern, highly performant web primitives:
*   **WASM & WebGL**: Leveraging modern browser APIs for smooth, 60fps rendering without requiring heavy desktop installations.
*   **OpenOODA Synergy**: Where multiplayer state synchronization is required, the games will utilize lightweight OpenOODA CRDT patterns to ensure deterministic state across clients.

## 4. Governance

*   studio2201 strictly follows the organization's global governance rules (`RULES.md`, `SWARM.md`, `PROBE.md`) managed in the meta-repository. 
*   All development cycles follow the strict `OODA.md` iteration loop to ensure clean, provable code generation that avoids copyright entanglement.

## 5. The "9 Tools" Roster (unRAID / Homelab Focus)

The first 9 repositories focus strictly on core infrastructure and utilities demanded by the unRAID and self-hosting communities. They explicitly avoid heavy LLM/AI dependencies, relying instead on pure mathematical engineering (CRDTs, WebRTC, WASM) and the `openOODA` backend.

1.  **Dashboard Hub**: A glassmorphism startpage that uses `openOODA` WebSockets to display real-time docker container states and NAS metrics.
2.  **Uptime Monitor**: A ping/port monitor utilizing CRDTs to synchronize uptime states across multiple unRAID boxes, preventing false-positive alerts during local network drops.
3.  **P2P File Drop**: A secure file-sharing portal utilizing WebRTC data channels for direct browser-to-browser transfers without filling up unRAID storage arrays.
4.  **Config Vault**: A markdown and snippet manager for storing `docker-compose` files and scripts, featuring real-time collaborative editing via CRDTs.
5.  **Proxy Manager**: An `openOODA`-powered reverse proxy with a beautiful GUI that automates Let's Encrypt certificates and routing without touching config files.
6.  **Container Updater**: A visual alternative to Watchtower that provides graphical diffs of container updates and 1-click rollback support.
7.  **Network Mapper & WoL**: Scans the local subnet to map devices and provides a clean UI for issuing Wake-on-LAN packets to sleeping VMs or backup NAS nodes.
8.  **Log Streamer**: A lightweight, client-side WASM log viewer that tails docker containers in real-time, pushing all regex and parsing load to the browser to save NAS CPU cycles.
9.  **DDNS Engine**: A dedicated dynamic DNS updater linking home IPs to Cloudflare/Route53 using `openOODA`'s asynchronous runtime for flawless reliability.
