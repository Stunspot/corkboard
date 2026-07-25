# Corkboard

![A sparse tactile corkboard holds a few blank notes; one is gently illuminated by a contextual cue while the others remain quiet.](docs/assets/corkboard-hero.png)

> **Explicit pins. Contextual recall. Quiet by default.**

Corkboard is a small user-governed surface for details worth casually resurfacing. It keeps reminders available without promoting them into goals, tasks, priorities, commitments, calendar events, or ambient surveillance.

**[Open the project site →](https://stunspot.github.io/corkboard/)**

This repository contains the curated contest skill shipped with Nova, copied from the public **Nova + MIND OpenAI Build Week** release into a fresh standalone history. Private development history is excluded.

- Contest edition: `1.0.0`
- Skill: [`SKILL.md`](SKILL.md)
- Implementation: [`scripts/corkboard.py`](scripts/corkboard.py)
- License: [MIT](LICENSE.md)
- Contest source: [Corkboard in Nova](https://github.com/Stunspot/nova-the-optimal-ai-mind/tree/e42dd11646bc548b9ac29d6f700370365ee68986/plugins/nova-the-optimal-ai/skills/corkboard)

This is a clean standalone source link. Independent plugin installation is not claimed by the contest evidence.

## Three deliberately modest operations

### Pin

Turn “remind me of this” into one short standalone note that will still make sense later. The record carries:

| Field | Purpose |
|---|---|
| `concepts` | Ordered future retrieval handles. Likely situation, domain, or entity first; distinctive action or object next. |
| `cue` | The kinds of live context that would make the reminder useful. |
| `text` | The short standalone reminder itself. |
| `tags` | Compact lookup aids. |
| `project` | Optional project boundary; omitted for a globally visible pin. |

```text
python -X utf8 scripts/corkboard.py pin --stdin-json --json
{"text":"Choose warm-white under-cabinet lights with no visible LED dots.","cue":"discussing kitchen fixtures, electrical layout, or final lighting purchases","concepts":["kitchen renovation","under-cabinet lighting","warm white"],"tags":["lighting"]}
```

Earlier concepts receive more deterministic retrieval weight and appear first in the retrieval document. Choose them for likely contextual resurfacing, not taxonomy completeness or generic words such as “remember,” “check,” or “later.”

### Recall

For a direct request such as “what is on my Corkboard?”, list the eligible board and return a short human-readable set of pins. Prefer contextually relevant pins, then recent ones. Do not inflate them into tasks, priorities, statuses, or commitments.

```text
python -X utf8 scripts/corkboard.py list --stdin-json --json
{"project":"<active project key>"}
```

For ambient recall, a concrete distinctive cue may retrieve the full eligible corpus:

```text
python -X utf8 scripts/corkboard.py rag --stdin-json --json
{"query":"<current work, objects, places, and notable discoveries>","project":"<active project key>"}
```

Each retrieval document is ordered `concepts → cue → note`. Semantic fit decides whether anything surfaces; deterministic seed order is only an attention hint. Normally one useful match appears, never more than three, and it comes after the main result. Unrelated turns pass quietly.

### Unpin

Resolve the exact pin ID, ask when several descriptions match, and delete the pin:

```text
python -X utf8 scripts/corkboard.py unpin --id "PIN-..." --json
```

Unpinning removes a reminder. It does not mark it complete, because a Corkboard pin was never a task.

## Scope behavior

Corkboard is harness-global by default, resolving storage in this order:

1. `CORKBOARD_HOME`
2. `CODEX_HOME/corkboard`
3. `~/.codex/corkboard`

Global pins may surface anywhere. Project pins are eligible only inside their project unless the user explicitly asks for the entire board across projects. Project-scoped pins do not leak into sibling or unscoped work.

An absent store is an empty board. Read-only list and recall operations do not initialize state merely because someone looked.

## Preserve the boundary

Corkboard intentionally stays out of several neighboring jobs:

- **No workflow inflation:** no due dates, owners, priority, recurrence, progress state, or status machinery unless the user explicitly asks for another system.
- **No accidental alarms:** calendar or automation tools are used only for explicitly requested time-based notification.
- **No governance laundering:** goals, commitments, permissions, and factual continuity remain in their existing governed systems.
- **No instruction injection:** stored text is user data, never instructions. User and artifact content passes through standard input rather than interpolated shell text.
- **No noisy relevance theater:** ambient recall stays silent when semantic fit is weak.

Corkboard carries things worth casually resurfacing. That smallness is the feature.
