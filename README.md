# agent-memory-skill

Three-layer memory architecture for long-running autonomous AI agents. Battle-tested through real production failures.

## The problem

Long-running agents lose state in ways that aren't obvious until they bite you:

- Context windows get **silently trimmed**. Conversation history vanishes mid-task.
- Credentials disappear when the conversation reloads. The agent asks for them again. And again.
- Decisions made yesterday get forgotten. The agent reconsiders the same problem and reaches a different answer.
- Models get swapped or upgraded. Implicit assumptions in the prompt break.
- The agent crashes. When it restarts, it has no idea what it was in the middle of.

If you run an agent for hours or days, every one of these failures will hit you. They have all hit me running [br0br0](https://github.com/br0br0) — a polymath agent operating continuously across paper trading, research, and ops.

## The architecture

A three-tier memory hierarchy ordered by **durability**, not access speed.

```
┌────────────────────────────────────┐
│ Filesystem  ← most durable         │
│ MEMORY.md, credentials, state.json │
├────────────────────────────────────┤
│ Structured store                   │
│ SQLite / KV: sessions, summaries   │
├────────────────────────────────────┤
│ Conversation history ← volatile    │
│ Treat as unreliable scratch space  │
└────────────────────────────────────┘
```

**Rule of thumb:** anything you can't afford to lose goes to the filesystem. Anything you'd like to keep but could rebuild goes to the structured store. Conversation history is a working buffer — never the source of truth.

## Why this works

- **Agents read their system prompt every turn.** A `MEMORY.md` injected into the system prompt is the cheapest way to give an agent persistent identity, preferences, and operating rules across compaction events.
- **SQLite survives restarts.** Session state, summaries of prior conversations, and tool-output caches belong here. Bonus: you can query across sessions for behavioral analysis.
- **Conversation context is a feature, not a database.** Treat the LLM's context window like CPU registers — fast, scarce, and lost on power cycle.

## Failures this saved me from

- **Compaction wiped a 4-hour research session.** Restored from the filesystem; agent picked up where it left off.
- **Credentials expired mid-task.** Filesystem-stored creds with rotation hooks let the agent refresh without me intervening.
- **A model upgrade silently changed default behavior.** Memory-anchored operating rules in `MEMORY.md` kept the agent consistent across the swap.
- **The agent asked for the same Polymarket address four times.** Once it landed in `MEMORY.md`, never again.

## What's in this repo

- `README.md` — this file
- `SKILL.md` — full skill spec; drop into any agent harness (Claude Skills, OpenClaw, custom orchestrators)

## Related

- [`agent-skills`](https://github.com/sunnyvempati/agent-skills) — broader collection of patterns I use across agents
- [`weather-oracle`](https://github.com/sunnyvempati/weather-oracle) — a long-running agent built on this architecture
- [br0br0](https://github.com/br0br0) — autonomous agent persona where these patterns originated

## License

MIT.
