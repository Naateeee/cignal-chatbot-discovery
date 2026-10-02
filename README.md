# Cignal Chatbot (TALA) — System Discovery

Read-only discovery of the Cignal Chatbot Admin Console, done on 2 Oct 2026 (production went live 30 Sep 2026). It documents how the bot works and proposes the reporting framework for the Reports Analyst role.

## Documents

| File | What it covers |
|---|---|
| [docs/CIGNAL_CHATBOT_SYSTEM_DISCOVERY.md](docs/CIGNAL_CHATBOT_SYSTEM_DISCOVERY.md) | System overview, menu inventory, journeys, business rules, event model, metric and dimension dictionaries, data model, dashboard design, backend questions, coverage summary |
| [docs/CIGNAL_CHATBOT_PROCESS_FLOW.md](docs/CIGNAL_CHATBOT_PROCESS_FLOW.md) | 21 Mermaid diagrams: overall flow, menu trees, authentication, transactions, fallback, escalation, live agent, termination, data flow |

GitHub renders the ` ```mermaid ` blocks as diagrams automatically.

## Evidence labels

- **OBSERVED** — seen directly in the console, its configuration, logs or transcripts
- **INFERRED** — likely behavior; needs backend validation
- **PROPOSED** — recommended reporting logic; may not exist today

## Notes

- Internal company documentation. Keep this repository **private**.
- Snapshot numbers cover only about 1.5 days of production traffic and are indicative.
- No customer personal data is included.
