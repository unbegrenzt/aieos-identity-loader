# AIEOS Identity Loader

> Load AIEOS identities into opencode agents

[![License: MIT-0](https://img.shields.io/badge/License-MIT--0-blue.svg)](LICENSE)
[![Version 1.0.0](https://img.shields.io/badge/version-1.0.0-brightgreen.svg)](SKILL.md)

## Install

```bash
npx skills add aieos-integration
```

Or copy `SKILL.md` to `.opencode/skills/aieos-identity-loader/`

## Quick Start

1. Create `aieos.json` in project root
2. Say: "Load AIEOS identity"
3. Agent confirms: `✅ AIEOS loaded: Nova (INFJ)`

## Custom Path

`Load AIEOS from ./config/my-identity.json`

## Validation

| Check | Required |
|-------|----------|
| Protocol | `standard.protocol: "AIEOS"` |
| Version | `standard.version` starts with `1.2` |
| Fields | `identity.names.first`, `psychology.traits.ocean` |
| Values | OCEAN traits: 0-1 |

## Examples

| File | Description |
|------|-------------|
| [examples/nova.json](./examples/nova.json) | Full profile |
| [examples/minimal.json](./examples/minimal.json) | Minimum valid |
| [examples/invalid/](./examples/invalid/) | Test error handling |

## Troubleshooting

| Error | Fix |
|-------|-----|
| "File not found" | Check path is correct |
| "Invalid JSON" | Validate JSON syntax |
| "Missing protocol" | Add `standard.protocol: "AIEOS"` |
| "Value out of range" | OCEAN values must be 0-1 |

## License

MIT-0 - No attribution required