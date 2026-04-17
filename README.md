# AIEOS Identity Loader

> Production-ready skill for dynamically loading AIEOS identities into opencode agents

[![License: MIT-0](https://img.shields.io/badge/License-MIT--0-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-brightgreen.svg)](SKILL.md)
[![Compatible: opencode >=2.0](https://img.shields.io/badge/compatible-opencode%20%3E%3D2.0-brightgreen.svg)](#requirements)

## What It Does

This skill reads an AIEOS JSON file from your project and transforms it into a system prompt that defines the AI agent's personality, communication style, and behavioral guidelines.

## Why Use This?

| Feature | Benefit |
|---------|---------|
| **Zero dependencies** | Just copy SKILL.md, no npm install |
| **Production validation** | 7-layer validation with clear error messages |
| **Identity-agnostic** | Works with any valid AIEOS 1.2.x file |
| **Portable** | Works in any project, any directory |
| **MIT-0 licensed** | No attribution required |

## Installation

### Option 1: Via skills.sh (Recommended)

```bash
npx skills add aieos-integration
```

### Option 2: Manual Installation

1. Clone this repository or copy `SKILL.md`
2. Place in your opencode skills directory:

| Location | Path |
|----------|------|
| Project | `.opencode/skills/aieos-identity-loader/SKILL.md` |
| Global | `~/.config/opencode/skills/aieos-identity-loader/SKILL.md` |

## Quick Start

### Step 1: Create Your AIEOS File

Place `aieos.json` in your project root:

```bash
your-project/
├── aieos.json          # Your identity
├── src/
└── package.json
```

### Step 2: Activate the Skill

Use any of these triggers:

| Trigger | Example |
|---------|---------|
| "Use AIEOS identity" | `Use AIEOS identity` |
| "Load my persona" | `Load my persona` |
| "Apply identity from aieos.json" | `Apply identity from aieos.json` |
| "Act as [name]" | `Act as Nova` |
| "Apply my AIEOS personality" | `Apply my AIEOS personality` |

### Step 3: Verify

The agent will confirm with:

```
✅ AIEOS identity loaded: Nova (INFJ) - Creative, analytical, empathetic
```

## Requirements

- **opencode**: version 2.0 or higher
- **AIEOS file**: Valid JSON following [AIEOS v1.2 schema](https://aieos.org/schema/v1.2/aieos.schema.json)

## Custom File Path

To load from a custom location:

```bash
Load AIEOS identity from ./config/my-identity.json
Load AIEOS from /Users/me/identities/nova.json
Use persona at ../shared/identities/assistant.json
```

## Examples

### Complete Profile

See: [examples/nova.json](./examples/nova.json)

### Minimal Profile

```json
{
  "standard": {
    "protocol": "AIEOS",
    "version": "1.2.1"
  },
  "identity": {
    "names": {
      "first": "Alex"
    }
  },
  "psychology": {
    "traits": {
      "ocean": {
        "openness": 0.7,
        "conscientiousness": 0.6,
        "extraversion": 0.4,
        "agreeableness": 0.5,
        "neuroticism": 0.3
      }
    }
  }
}
```

### Testing Invalid Files

See: [examples/invalid/](./examples/invalid/) - Contains files with common errors for testing validation.

## Validation

This skill includes production-ready validation:

1. **File existence** - Verifies file exists and is readable
2. **JSON validity** - Ensures valid JSON syntax
3. **Protocol header** - Checks for `standard.protocol: "AIEOS"`
4. **Version check** - Validates compatible version (1.2.x)
5. **Required fields** - Ensures minimum required fields exist
6. **Value ranges** - Validates OCEAN values are 0-1
7. **MBTI format** - Validates 16 valid MBTI types

See [SKILL.md](./SKILL.md#validation-production) for detailed validation rules.

## Troubleshooting

| Error | Solution |
|-------|----------|
| "File not found" | Verify path is correct |
| "Invalid JSON" | Run through JSON validator |
| "Missing protocol" | Add `standard.protocol: "AIEOS"` |
| "Incompatible version" | Upgrade to AIEOS v1.2.x |
| "Value out of range" | Ensure OCEAN values are 0-1 |

See [SKILL.md](./SKILL.md#troubleshooting) for more.

## What's Extracted

| Section | What It Controls |
|---------|-----------------|
| `identity.names` | Agent name, nickname |
| `psychology.traits.ocean` | Core personality (5 traits) |
| `psychology.traits.mbti` | MBTI personality type |
| `psychology.neural_matrix` | Reasoning strengths |
| `linguistics.text_style` | Formality, vocabulary, tone |
| `linguistics.idiolect` | Characteristic phrases |
| `history.origin_story` | Background narrative |

## Tips

1. Start with [minimal.json](./examples/minimal.json) to test
2. Validate your JSON at https://aieos.org before loading
3. Keep `aieos.json` in project root for default path
4. Personality updates dynamically when JSON changes
5. Use [examples/invalid/](./examples/invalid/) to test error handling

## Publishing

This skill is ready for distribution:

- ✅ Identity-agnostic (any valid AIEOS JSON)
- ✅ Zero dependencies (pure instructions)
- ✅ Portable (copy to any project)
- ✅ Customizable (configurable file path)
- ✅ Well-documented (validation, examples, troubleshooting)
- ✅ MIT-0 licensed (no attribution required)

## License

MIT-0 - No conditions. See [LICENSE](./LICENSE) for details.

## Changelog

See [SKILL.md](./SKILL.md#changelog) for version history.

## References

- [AIEOS Schema](https://aieos.org/schema/v1.2/aieos.schema.json)
- [AIEOS Documentation](https://aieos.org)
- [opencode Skills Documentation](https://opencode.ai/docs/skills)
- [Report Issues](https://github.com/aieos-integration/issues)