# OpenCode memory

- Use cavemem for prior session context and observations; use the knowledge graph for durable user preferences, project facts, and relationships.
- Search existing memories before creating or updating them. Keep graph observations atomic and avoid duplicate or transient entries.
- Never store passwords, API keys, tokens, private keys, or other secrets in memory.
