# business-logic-analyzer

A Codex skill for analyzing a repository's business logic and generating a structured documentation suite.

It scans SQL schema files and source code, maps module dependencies and data flows, and produces Markdown docs that are easy to browse on GitHub.

## What it generates

When this skill runs against a project, it creates a `docs/business/` folder in that project with:

- `README.md` - master overview with module graph and links
- `architecture-overview.md` - GitHub-friendly one-page summary
- `team-handoff.md` - concise handoff note in Chinese
- `modules/*.md` - one detailed document per module

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

## Install

### Option 1: Copy into the Codex skills directory

Place the skill folder in your local Codex skills directory so Codex can discover it automatically.

- Windows: `%USERPROFILE%\.codex\skills\business-logic-analyzer\`
- macOS / Linux: `~/.codex/skills/business-logic-analyzer/`

The folder should contain at least:

- `SKILL.md`
- `README.md`

### Option 2: Keep it in your own GitHub repo

If you want to version the skill in GitHub, keep the skill folder in your repo and copy or symlink it into the Codex skills directory when you want to use it locally.

## Repository layout

```text
business-logic-analyzer/
├── README.md
└── SKILL.md
```

If you store multiple skills in one repo, keep each skill in its own folder.

## Publishing to GitHub

If this folder is already inside a Git repository, publish it with:

```powershell
git status
git add README.md SKILL.md
git commit -m "docs: add business logic analyzer skill"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

If you are starting from scratch in this folder:

```powershell
git init
git add README.md SKILL.md
git commit -m "docs: add business logic analyzer skill"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

## Notes

- `SKILL.md` is the file that Codex uses to understand the skill's behavior.
- `README.md` is for GitHub users and collaborators who want to understand, install, or share the skill.
- The generated business documentation belongs in the analyzed project, not in this skill repository.

## License

Add your preferred license here if you plan to publish the skill publicly.
