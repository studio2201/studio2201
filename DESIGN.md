# studio2201 Architectural Design

**studio2201** is a digital workshop that builds web tools. We do not build huge game engines. We build small, focused tools.

## 1. The Core Web Suite (7 Tools)

We will build exactly 7 tools to run our digital projects. We do not chase numbers like "9x9".
*   **One job only:** Each tool does exactly one thing perfectly. No extra features.
*   **Web Containers:** Every tool is a small Docker or Podman container. It starts in milliseconds.

## 2. Copyright Safety & Clean Room Design

We do not steal or copy code. We write all code from scratch.
*   **No Copying:** We do not fork or borrow from other open-source game engines.
*   **We Own It All:** We build our own art, physics, and networking code. We own 100% of the rights.

## 3. Technology Stack

Our tools are simple but use the best new web tech:
*   **WASM & WebGL:** We run smooth graphics in the browser. No heavy desktop downloads.
*   **OpenOODA:** We use OpenOODA networks and CRDTs to keep data in sync for everyone.

## 4. Governance

*   We strictly follow the rules in `RULES.md`, `SWARM.md`, and `PROBE.md`.
*   We write code in loops using `OODA.md` to make sure the code is clean, proven, and safe.

## 5. The "Core 7" Roster (Web Operations Toolkit)

These 7 tools help us build and run websites. They do not use heavy AI. They use math and fast web code (CRDTs, WebRTC, WASM, openOODA).

1.  **Analytics Engine (Proof of Reading):** We do not track users with cookies. We use WASM math (mouse speed, scrolling) to prove a real human read the page. It ignores bots.
2.  **Status Page (Live Diagnostics):** If a server goes down, the status page gives the user a WASM terminal. The user can run network tests to see if their internet or our server is broken.
3.  **Checkout Gateway (Passkey Payments):** This wraps Stripe to take small payments (like a $0.10 tip). The user taps a hardware Passkey to pay. There is no credit card form.
4.  **Feedback & Bug Board (Bounty Canvas):** Users report bugs by drawing on a shared canvas. Other users can pay small bounties using the Checkout Gateway to get bugs fixed faster.
5.  **Sovereign Publisher (P2P Blog):** Your blog is compiled into a CRDT block. It shares itself over a peer-to-peer network (`ImpSync`). If the main server crashes, the blog still works.
6.  **Dynamic Forms Engine (Zero-Knowledge):** Users fill out contact forms, but their email is locked with crypto on their computer. You can reply to them without ever seeing their real email.
7.  **Fallback Gateway (Idle Screen):** If a server crashes, it does not show a blank 502 error page. It shows a fun WebGL game (`idlescreen`) until the server comes back online.
