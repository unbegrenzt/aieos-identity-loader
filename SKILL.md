---
name: aieos-identity-loader
description: Load and apply AIEOS identity from JSON to the agent
license: MIT-0
compatibility: opencode >=2.0
version: 1.0.0
tags: [identity, personality, aieos]
---

# AIEOS Identity Loader

Loads an AIEOS JSON identity file and applies it to the agent's personality.

## Quick Start

1. Place `aieos.json` in your project root
2. Say: "Load AIEOS identity" or "Use my persona"

## Configuration

| Default path | `./aieos.json` |
| Custom path | Say: "Load AIEOS from path/to/file.json" |

## Validation

Must have:
- `standard.protocol: "AIEOS"`
- `standard.version` starting with `1.2`
- `identity.names.first`
- `psychology.traits.ocean` (all 5 traits, 0-1 values)

Optional fields get defaults: formality 0.5, missing OCEAN → 0.5

## What to Extract

| Required | Optional |
|----------|-----------|
| identity.names.first | psychology.neural_matrix |
| psychology.traits.ocean | psychology.moral_compass |
| linguistics.text_style | history.origin_story |
| linguistics.idiolect.catchphrases | linguistics.style_descriptors |

## Triggers

"Use AIEOS identity", "Load my persona", "Act as [name]", "Apply identity from aieos.json"

## Examples

- Full: [examples/nova.json](./examples/nova.json)
- Minimal: [examples/minimal.json](./examples/minimal.json)
- Testing: [examples/invalid/](./examples/invalid/)

## Docs

Full validation rules and troubleshooting in [README.md](./README.md)