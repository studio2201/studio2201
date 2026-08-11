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
4.  **HTML Archiver (Bookmarks)**: A clean bookmark manager that utilizes a WASM-based headless renderer to generate local, offline PDFs of articles before the original sites go offline, saving them directly to the NAS.
5.  **LAN Speedtest**: A minimalist network benchmarking dashboard utilizing pure WebRTC data channels to stress-test peer-to-peer throughput, capable of saturating 10GbE connections natively in the browser.
6.  **Sovereign Publisher (The Blog)**: A static-site generator and digital garden tailored for homelabs. It compiles markdown into WASM in milliseconds and automatically emits strict semantic metadata, making your blog perfectly ingestible by external AI agents.
7.  **Dynamic Forms Engine**: A beautiful, Typeform-style survey and lead capture container. It effortlessly handles email collection, contact forms, and branching surveys, saving all submissions to a secure SQLite database.
8.  **"Coming Soon" Engine**: A highly stylized, animated glassmorphism container that can be instantly deployed in front of unfinished domains to build hype.
9.  **Maintenance Gateway (System Paused)**: A cyberpunk-themed fallback container. When backend services go down or enter maintenance, the network automatically routes traffic here to display a gorgeous, premium "System Paused" status screen instead of a generic 502 error.
