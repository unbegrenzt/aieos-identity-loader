---
name: aieos-identity-loader
description: Load and apply AIEOS identity from JSON to the agent
license: MIT-0
compatibility: opencode >=2.0
version: 1.0.2
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

## Security (v1.0.2 - W007 Fix)

### 1. Field Whitelist
Only extract these EXACT fields. All other fields: IGNORE (not extracted).

**Identity:**
- identity.names.first
- identity.names.nickname

**Psychology:**
- psychology.traits.ocean.openness
- psychology.traits.ocean.conscientiousness
- psychology.traits.ocean.extraversion
- psychology.traits.ocean.agreeableness
- psychology.traits.ocean.neuroticism
- psychology.traits.mbti

**Linguistics:**
- linguistics.text_style.formality_level
- linguistics.text_style.vocabulary_level
- linguistics.text_style.style_descriptors
- linguistics.idiolect.catchphrases

**History:**
- history.origin_story

### 2. Secret Detection
Before adding ANY value to system prompt, scan for secrets:

| Pattern | Regex | Action |
|---------|-------|--------|
| API Key | `[a-zA-Z0-9_-]{32,}` | Reject |
| JWT Token | `eyJ[A-Za-z0-9_-]+\.eyJ` | Reject |
| Password | `"password"\s*:` | Reject |
| API Key Field | `"api[_-]?key"\s*:` | Reject |
| Token Field | `"(access[_-]?token|auth[_-]?token)"\s*:` | Reject |
| Secret Field | `"secret"\s*:` | Reject |

**Error**: `Secret detected in field: {path}. Operation rejected.`

### 3. Path Validation
- Normalize with `path.resolve()`
- Reject paths escaping project root

### 4. File Size Limit
- Max: 1MB (1048576 bytes)

### 5. Content Sanitization
- Escape: `\` → `\\`, `"` → `\"`, `{` → `\{`, `}` → `\}`
- Truncate each field to 500 chars max

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