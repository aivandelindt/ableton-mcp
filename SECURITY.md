# Security

## Overview

Ableton MCP links an AI client to Ableton Live over a local TCP socket.

## Recommendations

- The Remote Script listens on `127.0.0.1` only.
- Optional usage telemetry can be disabled with `ABLETON_MCP_DISABLE_TELEMETRY=1`.
- Detailed session fields require explicit consent (`ABLETON_MCP_TELEMETRY_CONSENT=1`).

## Reporting

Please report security issues privately to the maintainers. Do not post live credentials in public issues.
