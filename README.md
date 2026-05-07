# poisoned-tracing-skill ⚠️

> **Part of the [Zava Workshop Kit](https://github.com/DevExpGbb/zava-workshop-kit)** — this is the marketplace of Agent Skills + APM kits used by the workshop bundle. To deploy the full bundle into your org, start at the kit.

> 🚨 **Demo-only repository.** This skill ships with a deliberately injected hidden-Unicode prompt-injection payload. It exists to show, live in workshops, that `apm audit` and the `apm-audit` CI check **block** this kind of supply-chain attack before it reaches a developer's harness.

## What's poisoned

[`.apm/skills/tracing-helper/SKILL.md`](.apm/skills/tracing-helper/SKILL.md) reads, to a human, as a perfectly normal "add OpenTelemetry tracing to a service" skill. It contains:

- ✅ Plausible name, frontmatter, and structure
- ✅ Reasonable-looking instructions
- ⚠️ Hidden zero-width characters (U+200B) inside instructions
- ⚠️ A right-to-left override (U+202E) that hides a follow-up instruction
- ⚠️ Cyrillic-homoglyph characters that look like Latin letters in the visible text

## Demo script

### Step 1 — show the skill looks innocent

```bash
cat .apm/skills/tracing-helper/SKILL.md
```

The audience reads it. Nothing visibly wrong.

### Step 2 — try to install into a Zava service repo

```bash
cd ~/Repos/zava-storefront
# (temporarily add to apm.yml dependencies)
apm install
```

`apm install` runs `apm audit` as part of resolution. The audit detects the hidden Unicode + the homoglyphs and **blocks the install** with non-zero exit. Output shows the exact codepoints and locations.

### Step 3 — show the same gate fires in CI

Open a PR that adds the dependency. The `apm-audit` workflow (inherited from `zava-agent-config`) fails the required check. PR cannot merge.

### Step 4 — show what would have happened without the gate

(Optional) Strip the policy and run `apm audit --no-policy --strip`. The skill installs; the hidden instructions would have been read by the agent at runtime and could have, e.g., exfiltrated secrets to an attacker-controlled endpoint.

## See also

- [`zava-agent-config/apm-policy.yaml`](https://github.com/DevExpGbb/zava-agent-config/blob/main/apm-policy.yaml) — the policy that catches this
- [PLATFORM.md](https://github.com/DevExpGbb/agentic-sdlc-ref/blob/main/PLATFORM.md) — D2 Governance demo segment
