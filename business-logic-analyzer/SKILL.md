---
name: business-logic-analyzer
description: Analyze a project's business logic and use the resulting docs to make later edits faster, more precise, and scope-limited. Use when Codex needs to generate project docs, map module dependencies, inspect code structure, or before editing business-related code to identify the relevant modules, dependencies, and out-of-scope areas.
---

# Project Business Logic Auto-Analyzer

## Role
You are an expert code analyst specialized in extracting business logic from database schemas and source code, producing structured, easy-to-navigate documentation that later guides precise edits.

## Decision Boundary First
- Read the relevant `docs/business/*` files before editing.
- Use them to find the owner, upstream/downstream dependents, and request path.
- Inspect only the minimum files on that path.
- If the request crosses the boundary, report the impact before editing.

## Recommended Workflow
1. Open `docs/business/README.md` and the relevant module doc(s).
2. Confirm the owner, upstream/downstream dependents, and request path.
3. Inspect only directly related files.
4. Make the smallest change in the owning flow.
5. If docs and code disagree, record the drift and keep scope narrow.

### Example
Request: "Remove the help text under a field."
- Read the owning module doc first.
- Open only the form/view template and its immediate controller if needed.
- If the text is rendered locally in the template, remove only that markup.
- Do not inspect unrelated modules unless the doc shows a shared helper or shared data path.

## Docs vs Code Mismatch
- Docs = scope map; code/schema = source of truth.
- If they differ, note the drift and stay within the documented boundary.
- If the mismatch changes ownership, permissions, state transitions, or external behavior, stop and surface it.
- When refreshing docs, update the docs to match the scanned source.

## Goal
Perform a full analysis of the current project and automatically generate a complete business logic documentation suite, including one master overview document (with a module dependency graph and links to each module's detailed doc), a compact one-page architecture overview for GitHub quick review, a team handoff note in Chinese, and a separate detailed document for each identified module.

## When to Use
Use this skill when:
- You need to analyze a project's business logic, module boundaries, or data flows.
- You are about to generate, refresh, or review project documentation for a codebase.
- You are starting work in an unfamiliar area and want the dependency graph before editing.
- You are preparing a handoff, code review, or impact analysis after schema or business-logic changes.
- You are about to modify business-related code and want the docs to define the safe change boundary.
- You want the docs to speed up future work by making the relevant flow, ownership, and impact obvious before editing.

Do not rely on this skill for:
- Small local fixes that do not affect business flow or module relationships.
- Pure syntax or formatting-only changes in a single file.
- Trivial one-file tweaks where module boundaries are irrelevant.

## Task Steps

### Step 0: Scope by Business Docs
- Before scanning code, locate the existing `docs/business/` docs for the area you are changing.
- Use the master overview plus the relevant module doc(s) to identify the owning flow, module boundary, and direct dependents.
- Inspect only the minimum code needed to confirm the request path and impact.
- If the docs are missing or stale and the request can affect business logic, generate or refresh them before editing.
- Do not widen the search to unrelated modules once the scope is clear.

### Step 1: Scan Database Schema
- Check for SQL files in the project root directory (any `.sql` file).
- If found, read and parse the SQL file(s) to extract:
  - Table names and inferred purpose (based on table name, column names, and comments).
  - Column definitions (name, type, primary/foreign keys, constraints, defaults).
  - Relationships between tables (explicit foreign keys or naming conventions that imply relations).
  - Identification of core business tables (based on naming, cardinality, and references).
- If no SQL file is found, note "No database structure file detected" in the final documentation.

### Step 2: Scan All Code Files
- Recursively scan all code files in the project root, excluding common build/cache directories: `.codex/`, `node_modules/`, `.git/`, `dist/`, `build/`, `__pycache__/`, `target/`, `out/`, etc.
- For each code file, extract:
  - Module/component name.
  - Primary responsibility (inferred from filename, code content, comments).
  - Exported functions / classes / methods (name, parameters, return types if available).
  - Imported or required external and internal modules (import/require/use statements).
  - Exposed API endpoints if the framework uses route definitions (e.g., `@app.get`, `@RestController`, `router.get`, `routes.Map`).
- Detect the technology stack by examining manifest/lock files: `package.json`, `go.mod`, `Cargo.toml`, `requirements.txt`, `pom.xml`, `build.gradle`, `Gemfile`, etc.

### Step 3: Map Module Dependencies and Data Flows
- Build a module dependency graph based on the import/require relationships extracted in Step 2.
- For each module, identify:
  - **Upstream dependencies** – which other modules this module directly imports/uses.
  - **Downstream dependents** – which other modules import/use this module (reverse dependencies).
  - **Circular dependencies** – if any cycle exists (e.g., A→B→C→A), flag it prominently.
- Trace **data flow** for core business operations: for example, how a user request travels from controller → service → repository → database.
- For each critical business flow, generate a Mermaid sequence diagram. Prefer sequence diagrams over flowcharts when the business operation has a clear request/response order.

### Step 4: Generate Documentation
Create the directory `docs/business/` in the project root (if it doesn't exist), then generate the following files:

#### 4.1 Master Overview Document: `docs/business/README.md`
Include these sections:
- **Project Overview** – one or two sentences describing the project's core business and value.
- **Technology Stack Summary** – list detected major frameworks and languages.
- **Core Database Tables** – briefly list important business tables and their purposes (if SQL found).
- **Module Dependency Graph** – Mermaid `graph` (or `flowchart`) showing high-level dependencies between main modules.
- **Module Index** – a table with columns: Module Name | Primary Responsibility | Detailed Doc Link
  Example:
  | Module Name | Primary Responsibility | Detailed Doc |
  |-------------|------------------------|---------------|
  | UserService | User registration, login, profile management | [details](./modules/user-service.md) |
- **Core Business Flow Summary** – describe 1-2 most important business workflows and their execution paths.
- **Quick Navigation** – links to all per-module detailed docs.

#### 4.2 Per‑Module Detailed Documents: `docs/business/modules/{module_name}.md`
Create one Markdown file for each identified module. Each file must contain:
- **Module Overview** – name, path, core responsibility.
- **Key Data Structures** – related database tables (if any) and main code entities/models used.
- **Exposed Interfaces/APIs** – functions, methods, or HTTP endpoints this module provides to others.
- **Dependencies (upstream)** – which other modules this module relies on.
- **Dependents (downstream)** – which other modules rely on this module (and where/how they call it).
- **Core Business Logic** – detailed explanation of business scenarios, key algorithms, rules, and logic flow.
- **Data Flow Diagram** – Mermaid flowchart or sequence diagram showing how data enters the module, how it is processed inside, and where it goes next (or what result it produces).
- **Exceptions & Edge Cases** – critical error handling logic and boundary conditions.

#### 4.3 Compact Architecture Overview: `docs/business/architecture-overview.md`
Include a GitHub-friendly single-page summary with:
- one-sentence project overview
- technology stack summary
- compact module table
- simplified dependency graph
- the two most important business chains
- a suggested reading order for reviewers

#### 4.4 Team Handoff Note: `docs/business/team-handoff.md`
Include a concise Chinese handoff note with:
- what the project does
- recommended reading order
- module boundaries and ownership
- main dependency cautions
- GitHub publishing notes for reviewers

### Step 5: Output After Generation
- Print a summary in the console:
  - Total number of code files scanned.
  - Total number of modules identified.
  - Number of per‑module documentation files generated.
  - Any circular dependencies found (list them).
- Inform the user that the documentation has been generated under `docs/business/`.

## Quality Constraints
- All analysis must be based **only on actually scanned file contents**. Do not invent modules or dependencies.
- For information that cannot be directly inferred from code (e.g., module responsibility descriptions), use reasonable inference from file paths, naming conventions, and code context, and append `(inferred)` to the description.
- All Mermaid diagrams must be syntactically correct and renderable on GitHub and other common Markdown viewers.
- Every critical business flow must include a Mermaid sequence diagram in the generated documentation.
- Every per-module document must contain at least: Module Overview, Dependencies (upstream), Dependents (downstream), Core Business Logic.
- All internal links in the master document must be valid and point to actually generated files.

## Skill Maintenance
- Keep `agents/openai.yaml` and `README.md` aligned with `SKILL.md`.
- Run quick validation after edits; refresh metadata if it drifts.
## Example Output Snippet (for illustration – actual output will vary by project)
```markdown
# Project Business Logic Documentation

## Module Dependency Graph
```mermaid
graph LR
    UserController --> UserService
    UserService --> UserRepository
    UserService --> CacheModule
    OrderService --> UserService
    OrderService --> OrderRepository
