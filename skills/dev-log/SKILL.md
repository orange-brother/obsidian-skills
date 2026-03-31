---
name: dev-log
description: Create a retrospective Obsidian note from a development task. Given a task name and brief background, the AI collects git and code context, interviews the user about decisions and insights, and writes a structured markdown log to the vault. Use when the user wants to document a completed task, feature, or bugfix as a retrospective note.
---

# Dev Log Skill

Turn a completed task into a retrospective Obsidian note. The AI collects context from the repository, interviews the user, and writes a structured `.md` file to the vault.

## Configuration

Set your vault path before using this skill:

```
VAULT_PATH = /path/to/your/vault/Dev Logs
```

Replace `/path/to/your/vault/Dev Logs` with the actual folder path in your Obsidian vault where dev logs should be saved.

## Workflow

### Step 0 — Check configuration

Check if `VAULT_PATH` is still set to the placeholder value `/path/to/your/vault/Dev Logs`.

If it is:
1. Ask the user: "Obsidian vault 경로를 설정해주세요. dev-log 파일이 저장될 폴더 경로를 알려주세요. (예: /Users/yourname/Documents/MyVault/Dev Logs)"
2. Once the user provides the path, update this SKILL.md file — replace the placeholder with the actual path
3. Confirm the path is set and proceed

If `VAULT_PATH` is already configured, skip this step silently.

### Step 1 — Receive task input

The user provides:
- **Task name**: a short label for the task (used in the filename and title)
- **Background**: 1–3 sentences describing what the task was about

If either is missing, ask for it before proceeding.

### Step 2 — Collect git and code context

Without asking the user, run the following to gather context automatically:

```bash
git log --oneline -10
git diff HEAD~1 HEAD --stat
git diff HEAD~1 HEAD
git status
```

Also check for PR information if available (e.g. via `gh pr view`). Extract:
- What files changed and how
- What the commits say
- Any PR title or description

Use this context to understand the technical scope of the task. Do not ask the user about things you can derive from git.

### Step 2.5 — Find related notes in the vault

Use the **obsidian-cli skill** to search for existing notes related to this task. Run:

```bash
obsidian search query="{task-name}" limit=10
obsidian search query="dev-log" limit=10
```

This step requires Obsidian to be running. If it is not, skip silently and proceed without wikilinks.

Use the results to identify notes worth linking to in the final output. Do not ask the user about this — infer relevance from titles and content.

### Step 3 — Interview the user

Ask 3–5 targeted questions to surface tacit knowledge that cannot be found in the code. Focus on:

- **Decision rationale**: Why this approach over alternatives?
- **Tradeoffs**: What did you give up or accept?
- **Surprises**: What was harder or easier than expected?
- **Insights**: What would you do differently next time?
- **Open ends**: What's left unresolved or could follow from this?

Ask all questions at once, not one by one. Keep questions short and concrete based on what you learned from the git context.

### Step 4 — Write the dev log

Use the **obsidian-markdown skill** to generate the output file. Apply the following Obsidian-specific syntax throughout:

**Frontmatter** — include `title`, `date`, `tags`, `status`, `project`, and `repo` fields:
```yaml
---
title: {Task Name}
date: {YYYY-MM-DD}
tags:
  - dev-log
project: {project or repo name}
repo: {org/repo}
status: done
---
```

**Wikilinks** — link related notes, projects, or previous dev-logs using `[[Note Name]]` wherever relevant. If the task relates to a known project or previous task, link to it.

**Callouts** — use callouts to highlight key moments in the log:
- `> [!insight]` for key learnings and takeaways
- `> [!question]` for unresolved questions or open ends
- `> [!warning]` for tradeoffs or things to watch out for

Save the file to:

```
{VAULT_PATH}/{YYYY-MM-DD}-{task-name}.md
```

Where `{task-name}` is the task name lowercased with spaces replaced by hyphens.

## Output Structure

```
frontmatter   → title, date, tags: [dev-log], project, repo, status: done
# {Task Name}
## 배경            → user's description, lightly expanded
## 변경 내역        → bullet list from git context, with [[wikilinks]] where relevant
## 고민과 판단      → key decisions from interview, written in first person
               → use > [!warning] for tradeoffs
## 인사이트         → use > [!insight] for each key learning
## 다음 액션        → use > [!question] for unresolved items, omit if nothing
```

> You MUST write the entire log in Korean, regardless of the language the user used. Keep each section concise — this is a log, not documentation.
