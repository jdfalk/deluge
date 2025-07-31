# TODO

This document outlines the multi‑phase plan for modernizing Deluge, improving its Web UI, integrating select plugins, and establishing a robust testing framework. The goal is to provide enough detail for a new developer to join the project and immediately understand the roadmap.

## Phase 1: Comprehensive Codebase Assessment

1. **Inventory and Architectural Overview**
   - Map all packages and modules within the `deluge` directory.
   - List the core components (e.g., daemon, client, web UI, plugin system, RPC interface).
   - Document dependencies specified in `requirements.txt`, `requirements-dev.txt`, and `requirements-tests.txt`.
   - Examine CI setup (`.github/workflows/*.yml`) to understand current linting, building, and testing steps.

2. **Style and Linting Baseline**
   - Review existing `setup.cfg` and `.pylintrc` for style rules.
   - Record any deviations from standard PEP 8, such as inconsistent imports or naming conventions.
   - Determine current usage of type hints; note files lacking annotations.

3. **Plugin Inventory**
   - Catalog all first-party plugins and their functionality.
   - Identify plugins that are widely used and consider them for core integration.

4. **Web UI Assessment**
   - Evaluate the current web interface (located within `deluge/ui/web` and associated `templates`).
   - Note limitations for mobile/desktop compatibility and features lacking modern design principles.

5. **Testing Baseline**
   - Review existing tests under `deluge/tests` (if present).
   - Record how the test suite runs (`pytest`, `tox`, etc.).
   - Note coverage gaps—particularly for plugins and web functionality.

## Phase 2: Modernizing the Codebase

1. **Upgrade to Latest Python Standards**
   - Ensure Python ≥3.10 is used across development and CI.
   - Add or enhance type hints (using `typing` / `typing_extensions`) for all modules.
   - Apply `ruff` or `flake8` (or both) to enforce PEP 8 and other lint rules.
   - Configure `black` or `pre-commit` hooks to format code consistently.

2. **Refactor Code Organization**
   - Split large modules into smaller, well-defined packages.
   - Adopt a clear separation between API layers (libtorrent interface), business logic (torrent/session management), and presentation/UI layers.
   - Remove dead code and consolidate duplicate utilities.

3. **Plugin Architecture Improvements**
   - Transition the plugin interface to explicit entry points (using `importlib.metadata.EntryPoint`).
   - Document plugin lifecycle: loading, enabling, disabling, and configuration.
   - Provide typed plugin hooks so plugin authors can benefit from static analysis.

4. **Dependency Updates**
   - Update `libtorrent` to a modern version supported by the community.
   - For web-related dependencies (e.g., `aiohttp`, `jinja2`), update to their latest major versions and document any breaking changes.

5. **Documentation**
   - Use `sphinx` with type-hint autodoc to generate API documentation.
   - Maintain a developer guide describing how to set up a dev environment, run tests, and contribute.

## Phase 3: Building the New Web UI

### Overview

Create a fully modern, responsive web client using React and Material Design that mirrors all existing functionality while offering improved usability.

1. **Project Setup**
   - Initialize a new React project (e.g., using `create-react-app` with TypeScript template or `Vite`).
   - Use Material Design guidelines with `@mui/material` and `@mui/icons-material`.
   - Organize components following a logical hierarchy: layout (navigation, sidebar), pages (torrent list, preferences), shared widgets (status bar, speed charts).

2. **Key Features**
   - **Torrent List View**
     - Sortable columns for name, size, progress, download/upload speeds.
     - Filtering by labels, trackers, and states (queued, seeding, paused, etc.).
     - Context menu actions (pause, resume, remove, recheck, etc.).

   - **Details Pane**
     - Tabs for General, Files, Peers, Trackers, Options, and Activity graphs.
     - Real-time updates using WebSockets or long polling.

   - **Add Torrent Dialog**
     - Drag-and-drop support for `.torrent` files.
     - Paste magnet links or torrent URLs.
     - Configurable options (download location, file priority).

   - **Preferences and Settings**
     - Mirror all existing Daemon/Client/Web UI settings.
     - Integrate plugin settings directly into the preferences dialog (if plugin is part of core).

   - **Notifications and Badge Handling**
     - Provide user notifications for completed downloads.
     - Implement Service Worker support for registering the site as a handler for `magnet:` and `.torrent` links.
     - Document how to add the site as a handler in major browsers.

3. **Responsive Design**
   - Use Material UI’s responsive grid to ensure good usability on phones, tablets, and desktops.
   - Allow the sidebar to collapse on narrow screens.
   - Provide touch-friendly controls for mobile.

4. **API Layer**
   - Implement a modern REST-style API or GraphQL layer inside the Deluge daemon.
   - Provide endpoints for listing, adding, removing, and managing torrents.
   - Use token-based authentication (JWT or similar).
   - Consider streaming updates via WebSockets to reduce polling.

5. **Plugin Support**
   - For each plugin, create an API endpoint or WebSocket channel so that the React client can access plugin features.
   - Provide React components for plugin UIs, ideally with lazy-loading to minimize bundle size.

6. **Build and Deployment**
   - Configure Webpack/Vite to produce a static bundle served by the daemon.
   - Use environment variables for API endpoint configuration so the same build works in development and production.
   - Provide a development server for React with hot reloading.

## Phase 4: Plugin Integration Strategy

1. **Identify Core-worthy Plugins**
   - Evaluate built-in plugins such as:
     - AutoAdd: automatic torrent discovery from a directory.
     - Notifications: desktop notifications for completed downloads.
     - Scheduler: time-based bandwidth limits.
     - Extractor: automatically extract archives after completion.
   - Determine which plugins are essential to user experience and move them into core modules.

2. **Refactoring for Core Integration**
   - Move plugin code into `deluge/pluginscore` (new directory).
   - Migrate plugin preferences to centralized settings with a unified configuration schema.
   - Ensure existing plugin APIs remain for third-party plugins but mark integrated plugins as non-removable core features.

3. **Plugin Extensibility**
   - Update plugin documentation so external developers can continue building optional plugins with minimal breakage.
   - Provide a versioned plugin API to avoid frequent breaking changes.

## Phase 5: Testing and Continuous Integration

1. **Testing Strategy**
   - Adopt `pytest` for unit and integration tests.
   - Use `pytest-asyncio` for async components.
   - Aim for high coverage on the daemon, RPC layer, plugin system, and web API.

2. **Torrent Handling Tests**
   - Use the Ubuntu ISO torrent (`https://releases.ubuntu.com/25.04/ubuntu-25.04-desktop-amd64.iso.torrent`) as a known-good torrent for integration tests.
   - Mock or seed a local `libtorrent` session to avoid hitting real trackers during CI.
   - Validate magnet-link registration by generating a temporary magnet link in tests and confirming the React client accepts it.

3. **Web UI Tests**
   - Use `jest` and `@testing-library/react` for component tests.
   - Implement Cypress or Playwright for end-to-end UI tests, interacting with the running daemon via the API.
   - Test on multiple screen sizes to ensure responsiveness.

4. **CI Pipeline**
   - Use GitHub Actions to run linting, unit tests, and build the web UI.
   - Include checks that the web bundle size stays within acceptable limits.
   - Provide a nightly integration test that runs longer torrent operations using the Ubuntu ISO.

## Phase 6: Technical Design Documentation

1. **Project Overview Document**
   - Summaries of each core module: torrent management, RPC, plugin loader, Web UI server.
   - Diagrams showing how the daemon communicates with the client and web interface.

2. **API Specification**
   - Document REST endpoints or GraphQL schema.
   - Include authentication flow, error codes, and example requests/responses.

3. **Development Guide**
   - Detailed environment setup (Python version, Node/NPM version, local database, etc.).
   - Step-by-step instructions to run the daemon, web UI, and tests.
   - Guidelines for writing new plugins or modifying existing ones.

4. **Migration Guide**
   - For developers transitioning from the old plugin system or web UI, provide a guide on updating their plugins or customizing the new interface.

5. **Codebase Walkthrough**
   - Annotated file tree describing the purpose of each major directory or module.
   - High-level sequence diagrams of typical workflows: starting a torrent, controlling a plugin, updating settings, etc.

## Phase 7: Implementation Roadmap

1. **Short Term (0–3 Months)**
   - Complete the codebase assessment and documentation.
   - Start refactoring for modern Python standards (type hints, formatting, dependency updates).
   - Set up CI with linting and initial test suite using `pytest`.
   - Create the basic React project skeleton and proof of concept for the new web UI.

2. **Medium Term (3–6 Months)**
   - Finish refactoring major modules and integrate crucial plugins into core.
   - Build out essential Web UI pages (torrent list, details, add dialog).
   - Implement API endpoints required by the new client.
   - Migrate existing unit tests to `pytest` and expand coverage.

3. **Long Term (6–12 Months)**
   - Complete the full React UI, covering all plugin features.
   - Integrate magnet/torrent link handler and finalize mobile responsiveness.
   - Stabilize the API and plugin system.
   - Produce final documentation, including developer guides and migration notes.
   - Release a major version with the new UI and integrated core functionality.

