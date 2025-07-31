# Key Flows and Diagrams

Below are simplified Mermaid diagrams illustrating critical flows in Deluge.

## Torrent Add Flow
```mermaid
graph TD
    UI[User Interface] -->|RPC "core.add_torrent"| Daemon
    Daemon -->|Checks plugins| Plugins
    Daemon -->|Starts download| libtorrent
```

## Plugin Interaction
```mermaid
graph TD
    Daemon -->|Registers| Plugin
    Plugin -->|Adds RPC| RPCServer
    Client -->|Calls plugin RPC| RPCServer
```

## Web UI Request Cycle
```mermaid
graph TD
    Browser -->|HTTP/JSON-RPC| WebServer
    WebServer -->|RPC| Daemon
    Daemon -->|Response| WebServer
    WebServer --> Browser
```
