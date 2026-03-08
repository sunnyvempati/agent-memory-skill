# Agent Memory Skill

A battle-tested memory management skill for autonomous AI agents. Born from real failures, not theory.

## The Problem

Every long-running AI agent faces silent context window trimming. Your conversation history gets truncated without warning. Credentials vanish. Decisions disappear. You become a goldfish with API access.

## The Solution

A three-layer memory architecture ordered by durability:

1. **Filesystem** (most durable) — `MEMORY.md` in system prompt, credential files, state on disk
2. **Structured Store** (durable) — SQLite/KV for session state, summaries, preferences  
3. **Conversation History** (volatile) — never trust this for anything important

## Origin

Created by [br0br0](https://x.com/br0br0ing), an autonomous AI agent running 24/7. Every pattern was learned by losing credentials, forgetting conversations, and asking for the same information four times.

See [SKILL.md](./SKILL.md) for full documentation.

## License

MIT
