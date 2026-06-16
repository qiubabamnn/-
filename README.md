# business-logic-analyzer

A Codex skill for analyzing a repository's business logic and generating a structured documentation suite.

It scans SQL schema files and source code, maps module dependencies and data flows, and produces Markdown docs that are easy to browse on GitHub.

The generated docs are meant to be reused during later changes: read the relevant business docs first, then make the smallest change that stays inside the documented flow.

## What it generates

When this skill runs against a project, it creates a `docs/business/` folder in that project with:

- `README.md` - master overview with module graph and links
- `architecture-overview.md` - GitHub-friendly one-page summary
- `team-handoff.md` - concise handoff note in Chinese
- `modules/*.md` - one detailed document per module
- key business flows documented with Mermaid sequence diagrams

## When to use it

Use this skill when you want to:

- analyze a project's business logic
- map module dependencies
- understand key data flows
- generate project documentation
- document a backend/frontend codebase for handoff or review

## How to use

Ask Codex to analyze the repository, for example:

- "Analyze this project's business logic and generate project docs."
- "Map the module dependencies in this codebase."
- "Generate a business documentation suite for this project."
- "梳理这个项目的业务逻辑并生成项目文档。"

The skill will automatically inspect the repository, infer module responsibilities from code and schema, and write the documentation into `docs/business/`.
When you later change business-related code, use those docs to narrow the scope before editing.

## Recommended workflow

1. Open `docs/business/README.md` and the relevant module doc.
2. Use the module graph to confirm the owner, upstream dependencies, and downstream dependents.
3. Inspect only the directly related files.
4. If docs and code disagree, record the drift and keep the edit scope narrow.

## Docs vs code mismatches

- Treat docs as the scope map and code/schema as the source of truth.
- If there is a mismatch, do not fan out to unrelated modules.
- When the task is a docs refresh, update the docs from the scanned source.
- When the task is a code change, report the mismatch if it affects ownership or behavior.

## Skill metadata

`agents/openai.yaml` stores the UI metadata for this skill. Keep it in sync with `SKILL.md` and validate the skill folder after edits.

## Install and use

If someone shared this skill with you, copy the folder into your local Codex skills directory so Codex can discover it automatically.

- Windows: `%USERPROFILE%\.codex\skills\business-logic-analyzer\`
- macOS / Linux: `~/.codex/skills/business-logic-analyzer/`

Keep these files together:

- `SKILL.md`
- `README.md`
- `agents/openai.yaml`

Use it by asking Codex to analyze a repository, or invoke it explicitly with `$business-logic-analyzer`.
When it generates docs, open `docs/business/README.md` first, then the relevant module doc before editing code.

## Repository layout

```text
business-logic-analyzer/
├── README.md
├── SKILL.md
└── agents/
    └── openai.yaml
```

If you store multiple skills in one repo, keep each skill in its own folder.

## Notes

- `SKILL.md` controls how Codex uses the skill.
- `README.md` explains how to install and use it if someone else shared the folder with you.
- The generated business documentation belongs in the analyzed project, not in this skill repository.

## License

Add your preferred license here if you plan to publish the skill publicly.
