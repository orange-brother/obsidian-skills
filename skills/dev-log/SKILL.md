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

### Step 3 — Interview the user

Ask 3–5 targeted questions to surface tacit knowledge that cannot be found in the code. Focus on:

- **Decision rationale**: Why this approach over alternatives?
- **Tradeoffs**: What did you give up or accept?
- **Surprises**: What was harder or easier than expected?
- **Insights**: What would you do differently next time?
- **Open ends**: What's left unresolved or could follow from this?

Ask all questions at once, not one by one. Keep questions short and concrete based on what you learned from the git context.

### Step 4 — Write the dev log

Generate a single Obsidian markdown file using the structure below. Save it to:

```
{VAULT_PATH}/{YYYY-MM-DD}-{task-name}.md
```

Where `{task-name}` is the task name lowercased with spaces replaced by hyphens.

## Output Format

```markdown
---
title: {Task Name}
date: {YYYY-MM-DD}
tags:
  - dev-log
status: done
---

# {Task Name}

## Background

{User's background description, lightly expanded if needed}

## What Changed

{Summary of changed files and commits from git context. Use a short bullet list.}

## Decisions & Reasoning

{Key decisions the user made, derived from the interview. Write in first person as if the user is writing.}

## Insights

{Lessons learned, surprises, and things worth remembering for next time.}

## Open Questions

{Anything unresolved, deferred, or worth revisiting. Omit this section if there is nothing.}
```

> Write the log in the same language the user used when describing the task. Keep each section concise — this is a log, not documentation.
