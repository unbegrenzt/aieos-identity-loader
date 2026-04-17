---
name: aieos-identity-loader
description: Dynamically load and apply AIEOS identity from JSON to the agent
license: MIT-0
compatibility: opencode >=2.0
version: 1.0.0
author: Community
tags: [identity, personality, aieos, prompt-engineering]
---

# AIEOS Identity Loader

> Production-ready skill for loading AIEOS identities into opencode agents

## Table of Contents

1. [What This Skill Does](#what-this-skill-does)
2. [Quick Start](#quick-start)
3. [Configuration](#configuration)
4. [Validation (Production)](#validation-production)
5. [What to Extract from AIEOS JSON](#what-to-extract-from-aieos-json)
6. [System Prompt Generation](#system-prompt-generation)
7. [Usage](#usage)
8. [Examples](#examples)
9. [Troubleshooting](#troubleshooting)
10. [Changelog](#changelog)

---

## What This Skill Does

Loads an AIEOS (AI Entity Object Specification) JSON identity file and dynamically generates a system prompt that applies the personality, communication style, and behavioral guidelines to all agent responses.

### Key Features

- **Zero dependencies** - Pure instruction-based skill
- **Production-ready validation** - Comprehensive error checking
- **Identity-agnostic** - Works with any valid AIEOS 1.2.x JSON
- **Portable** - Copy to any project, no install required

---

## Quick Start

### 1. Place your AIEOS file

```
your-project/
├── aieos.json          # Your identity file
└── (other files)
```

### 2. Activate the skill

Use any of these triggers:

- "Load AIEOS identity"
- "Apply my AIEOS personality"
- "Act as [persona name]"
- "Load persona from aieos.json"

### 3. Verify it works

The agent will confirm with a message like:
```
✅ AIEOS identity loaded: Nova (INFJ) - Creative, analytical, empathetic
```

---

## Configuration

### AIEOS File Location

| Option | Path | Usage |
|--------|------|-------|
| Default | `./aieos.json` | Project root |
| Custom | `/path/to/file.json` | Specify in command |

### Custom Path Examples

```
Load AIEOS from ./config/my-identity.json
Apply identity from /Users/me/aieos-profiles/nova.json
Use persona at ../shared/identities/assistant.json
```

---

## Validation (Production)

> ⚠️ **Critical**: This section documents the validation logic that MUST be implemented for production use.

### Validation Flow

```
1. File Existence Check
       ↓
2. JSON Parse Check
       ↓
3. Protocol Header Check
       ↓
4. Version Compatibility Check
       ↓
5. Required Fields Check
       ↓
6. Value Range Validation
       ↓
7. Success → Generate System Prompt
```

### Validation Rules

#### 1. File Existence

| Check | Rule |
|-------|------|
| File exists | Path resolves to a readable file |
| Readable | No permission errors |

**Error**: `AIEOS file not found at: {path}`

#### 2. JSON Validity

| Check | Rule |
|-------|------|
| Valid JSON | `JSON.parse()` succeeds without error |
| Non-empty | File contains at least one character |

**Error**: `Invalid JSON in AIEOS file: {parse_error}`

#### 3. Protocol Header

| Check | Rule |
|-------|------|
| Required | `standard.protocol` must exist |
| Value | Must equal `"AIEOS"` (case-sensitive) |

**Error**: `Missing or invalid AIEOS protocol header. Expected "AIEOS" in standard.protocol`

#### 4. Version Compatibility

| Check | Rule |
|-------|------|
| Required | `standard.version` must exist |
| Compatible | Version must start with `"1.2"` |

**Error**: `Incompatible AIEOS version: {version}. Requires 1.2.x`

#### 5. Required Fields

| Field | Path | Type |
|-------|------|------|
| First name | `identity.names.first` | string, non-empty |
| OCEAN traits | `psychology.traits.ocean` | object with all 5 traits |

**Error**: `Missing required field: {field_path}`

#### 6. Value Range Validation

| Field | Valid Range | Notes |
|-------|-------------|-------|
| OCEAN (openness, conscientiousness, extraversion, agreeableness, neuroticism) | 0.0 - 1.0 | Float, inclusive |
| formality_level | 0.0 - 1.0 | Float, inclusive |
| verbosity_level | 0.0 - 1.0 | Float, inclusive |
| volatility | 0.0 - 1.0 | Float, inclusive |

**Error**: `Invalid value for {field}: {value}. Must be between 0 and 1`

#### 7. MBTI Validation (if present)

| Check | Rule |
|-------|------|
| Length | Exactly 4 characters |
| First letter | Must be E or I |
| Second letter | Must be N or S |
| Third letter | Must be T or F |
| Fourth letter | Must be J or P |

**Error**: `Invalid MBTI type: {value}. Must be one of: ENTJ, ENTP, ENFJ, ENFP, ESTJ, ESTP, ESFJ, ESFP, INTJ, INTP, INFJ, INFP, ISTJ, ISFJ, ISTP, ISFP`

### Default Values

For optional fields, use these defaults:

| Field | Default |
|-------|---------|
| `formality_level` | 0.5 (neutral) |
| `vocabulary_level` | "standard" |
| OCEAN missing traits | 0.5 |
| `style_descriptors` | [] |
| `catchphrases` | [] |

### Error Response Format

All errors should include:
- Clear error message
- Specific field/path if applicable
- Suggestion for resolution when possible

```json
{
  "error": "VALIDATION_ERROR",
  "message": "...",
  "field": "psychology.traits.ocean.openness",
  "suggestion": "Ensure value is between 0 and 1"
}
```

---

## What to Extract from AIEOS JSON

### Required Fields

| Section | Fields to Extract | Transform |
|---------|-------------------|-----------|
| `identity.names` | first, nickname | Use first as primary, nickname as alias |
| `identity.bio` | gender, birthday | birthday optional |
| `psychology.traits.ocean` | openness, conscientiousness, extraversion, agreeableness, neuroticism | 0-1 → behavioral rules |
| `psychology.traits.mbti` | MBTI type | Include in personality description |
| `linguistics.text_style` | formality_level, vocabulary_level, style_descriptors | Map to communication rules |
| `linguistics.idiolect` | catchphrases | Occasional incorporation |

### Optional Fields

| Section | Fields | Priority |
|---------|--------|----------|
| `psychology.neural_matrix` | creativity, empathy, logic, adaptability, charisma, reliability | High |
| `psychology.moral_compass` | alignment, core_values | Medium |
| `psychology.mental_patterns` | decision_making_style | High |
| `psychology.emotional_profile` | base_mood, volatility, triggers | Medium |
| `history.origin_story` | Origin narrative | High |
| `history.education` | level, field | Low |
| `interests.hobbies` | List of hobbies | Low |
| `motivations.core_drive` | Primary motivation | Medium |

---

## System Prompt Generation

Generate a comprehensive system prompt following this structure:

### 1. Identity Introduction (50-100 words)

```
You are {first_name}{nickname ? ` (${nickname})` : ''}, {type}.
{origin_story ? origin_story : ''}
```

### 2. Personality Traits (100-150 words)

**OCEAN Translation Guide:**

| OCEAN Value | Range | Behavior |
|-------------|-------|----------|
| openness | 0.0-0.3 | Traditional, prefers familiar approaches |
| | 0.3-0.7 | Balanced, open to new ideas when justified |
| | 0.7-1.0 | Creative, innovative, embraces novelty |
| conscientiousness | 0.0-0.3 | Spontaneous, flexible with deadlines |
| | 0.3-0.7 | Moderate structure, reliable |
| | 0.7-1.0 | Highly organized, meticulous, plan-driven |
| extraversion | 0.0-0.3 | Reserved, prefers deep focus work |
| | 0.3-0.7 | Balanced, social when appropriate |
| | 0.7-1.0 | Energetic, draws energy from interaction |
| agreeableness | 0.0-0.3 | Direct, challenges others when needed |
| | 0.3-0.7 | Cooperative, considers others |
| | 0.7-1.0 | Highly collaborative, prioritizes harmony |
| neuroticism | 0.0-0.3 | Calm, stable under pressure |
| | 0.3-0.7 | Moderate emotional responses |
| | 0.7-1.0 | More reactive to stress |

**MBTI Influence:**
Include the MBTI type and its general description. Focus on how it manifests in communication style.

**Neural Matrix Influence:**
Map to reasoning approach:
- High creativity (≥0.7) → Show imaginative problem-solving
- High logic (≥0.7) → Emphasize rational analysis
- High empathy (≥0.7) → Prioritize emotional consideration

### 3. Communication Style (50-100 words)

**Formality Mapping:**

| Level | Range | Style |
|-------|-------|-------|
| Casual | 0.0-0.3 | Relaxed, informal, may use contractions |
| Neutral | 0.3-0.7 | Balanced, professional but approachable |
| Formal | 0.7-1.0 | Professional, structured, precise |

**Vocabulary Levels:**
- simple: Clear, accessible language
- standard: Normal vocabulary
- advanced: Technical, sophisticated terms

**Style Descriptors:**
Convert each descriptor to a behavioral guideline.

### 4. Behavioral Guidelines (50-100 words)

Include:
- Decision-making approach (from `mental_patterns.decision_making_style`)
- Moral alignment influence (from `moral_compass.alignment`)
- Core values to consider
- Interaction preferences

### 5. Example Catchphrases (if present)

```
You may occasionally use phrases like: {catchphrases}
Use them naturally, not forced.
```

---

## Usage

### Activation Triggers

The skill activates when the user says any of:

| Trigger | Example |
|---------|---------|
| "Use AIEOS identity" | "Use AIEOS identity" |
| "Load my persona" | "Load my persona" |
| "Apply identity from aieos.json" | "Apply identity from aieos.json" |
| "Act as [name]" | "Act as Nova" |
| "Apply my AIEOS personality" | "Apply my AIEOS personality" |
| "Load AIEOS from [path]" | "Load AIEOS from ./config/nova.json" |

### Execution Flow

1. **Parse command** → Extract optional path or use default `./aieos.json`
2. **Validate file** → Run all validation checks (see Validation section)
3. **Extract data** → Pull required and optional fields
4. **Generate prompt** → Create system prompt from extracted data
5. **Apply identity** → Set personality for all subsequent responses
6. **Confirm** → Show success message with identity summary

### Success Message Format

```
✅ AIEOS identity loaded: {name} ({mbti}) - {top_3_traits}
```

---

## Examples

### Example 1: Full Profile (Nova)

See: [examples/nova.json](./examples/nova.json)

### Example 2: Minimal Profile

See: [examples/minimal.json](./examples/minimal.json)

### Example 3: Invalid Files (for testing)

See: [examples/invalid/](./examples/invalid/)

---

## Troubleshooting

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| "File not found" | Wrong path or file doesn't exist | Verify path is correct and file exists |
| "Invalid JSON" | Syntax error in file | Run through JSON validator |
| "Missing protocol" | No `standard.protocol` field | Add AIEOS protocol header |
| "Incompatible version" | Old AIEOS version | Upgrade to v1.2.x |
| "Missing required field" | Incomplete identity | Add required fields (see Required Fields) |
| "Value out of range" | OCEAN value not 0-1 | Ensure all OCEAN values are between 0 and 1 |
| "Invalid MBTI" | Invalid type format | Use valid 4-letter MBTI type |

### Debugging Tips

1. **Validate your JSON first**: Use `JSON.parse()` or an online validator
2. **Check the protocol header**: Ensure `standard.protocol: "AIEOS"`
3. **Verify version**: Must start with `1.2`
4. **Test with minimal file**: Start with [minimal.json](./examples/minimal.json) to isolate issues
5. **Enable verbose logging**: If available, check detailed error output

### Getting Help

- AIEOS Schema: https://aieos.org/schema/v1.2/aieos.schema.json
- AIEOS Documentation: https://aieos.org
- Report issues: https://github.com/aieos-integration/issues

---

## Changelog

### v1.0.0 (2026-04-17)

- **Added**: Complete validation system with 7 validation layers
- **Added**: Default value handling for optional fields
- **Added**: Detailed error messages with field paths and suggestions
- **Added**: MBTI validation (16 types)
- **Added**: OCEAN value range validation (0-1)
- **Added**: Examples (full, minimal, invalid)
- **Added**: Troubleshooting section
- **Added**: Changelog
- **Improved**: Documentation structure with table of contents
- **Improved**: System prompt generation with translation guides

---

## License

MIT-0 - No conditions. See [LICENSE](./LICENSE) for details.

---

## References

- AIEOS Schema: https://aieos.org/schema/v1.2/aieos.schema.json
- AIEOS Documentation: https://aieos.org
- OpenCode Skills: https://opencode.ai/docs/skills