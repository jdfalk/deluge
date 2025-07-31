# Testing Strategy Overview

This document outlines how the existing test suite is organized and executed.

## Test Framework
- Tests are written using **pytest** and located under `deluge/tests`.
- CI workflows (see `.github/workflows`) run `pytest` with markers to exclude long-running UI tests.

## Current Coverage
- Approximately 320 tests pass; some web UI tests are skipped.
- Plugin tests are minimal, leaving gaps in coverage.

## Running Tests Locally
```bash
pip install -e .
pip install -r requirements-tests.txt
pytest -m "not (todo or gtkui)" deluge/tests
```

## Future Improvements
- Integrate code coverage reporting.
- Add more tests for plugins and web UI flows.
- Consider using **tox** to manage multiple environments.
