# Technical Design Overview

This document summarizes the high‑level architecture of the Deluge codebase as evaluated in Phase 1.

## Core Components
- **Daemon** (`deluge/core`): Handles torrent management, networking, and plugin integration.
- **Client** (`deluge/ui/client.py`): RPC interface used by console, GTK UI and Web UI.
- **Web UI** (`deluge/ui/web`): Browser-based interface serving HTML and JavaScript.
- **Plugins** (`deluge/plugins`): Extend functionality via entry points.
- **RPC Interface** (`deluge/core/rpcserver.py`): Exposes core methods to clients.

## Package Layout
```
deluge/
├── core/             # Torrent engine, daemon logic
├── ui/               # Multiple user interfaces (console, gtk, web)
├── plugins/          # First-party plugins
└── tests/            # Test suite
```

## Data Flow
1. The **Daemon** maintains torrent state and exposes RPC methods via the **RPC Server**.
2. UI components invoke RPC calls through the **Client** library.
3. **Plugins** register RPC methods and hooks to extend daemon behavior.
4. The **Web UI** communicates with the daemon using JSON-RPC over HTTP.

For a full inventory of files and functions see `codebase_assessment_phase1.md`.
