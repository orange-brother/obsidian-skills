---
name: team-log
description: Create a team-facing work log from a development task and push it directly to a shared GitHub repository via gh CLI. Collects git context automatically and asks 2–3 focused questions for weekly team review. Use when the user wants to share what they worked on with their team.
---

# Team Log Skill

Turn a completed task into a team-facing work log and push it to the team's shared GitHub repository using `gh` CLI. Designed for weekly review — concise, factual, and easy to scan.

## Configuration

```
TEAM_REPO = the-swing/fe-work-logs
TEAM_REPO_PATH =
```

- `TEAM_REPO`: GitHub repository in `org/repo` format
- `TEAM_REPO_PATH`: Folder path inside the repo (e.g. `logs/swing/`). Leave empty to save at root.

## Prerequisites

- `gh` CLI must be installed and authenticated (`gh auth status`)

## Workflow

### Step 1 — Receive task input

The user provides:
- **Task name**: a short label for the task

If missing, ask for it before proceeding.

### Step 2 — Collect context automatically

Without asking the user, run:

```bash
git config user.name
git config user.email
git log --oneline -10
git diff HEAD~1 HEAD --stat
git status
gh pr view
```

Extract:
- **작업자**: from `git config user.name`
- **변경 파일 및 커밋**: from git log/diff
- **PR 링크**: from `gh pr view` if available

Do not ask the user about things you can derive from git.

### Step 3 — Interview the user

Ask exactly 3 questions, all at once:

1. 왜 이 방식을 선택했나요? (핵심 결정 한두 줄로)
2. 어려웠던 점이나 예상 밖이었던 것이 있었나요?
3. 팀원이 알아야 할 것이 있나요? (영향 범위, 주의사항, 의존성 등)

### Step 4 — Write and upload the log

Generate the markdown file and upload to the team repo via `gh` CLI:

```bash
# Write to temp file
cat > /tmp/team-log-output.md << 'EOF'
{file content}
EOF

# Upload to team repo
gh api repos/{TEAM_REPO}/contents/{TEAM_REPO_PATH}{YYYY-MM-DD}-{task-name}.md \
  --method PUT \
  -f message="team-log: {task-name}" \
  -f content="$(base64 /tmp/team-log-output.md)"

# Clean up
rm /tmp/team-log-output.md
```

If the file already exists, fetch its SHA and include it in the request:

```bash
SHA=$(gh api repos/{TEAM_REPO}/contents/{TEAM_REPO_PATH}{YYYY-MM-DD}-{task-name}.md --jq '.sha' 2>/dev/null)
# Add -f sha="$SHA" to the PUT request above
```

## Output Structure

```markdown
---
title: {Task Name}
date: {YYYY-MM-DD}
author: {git user.name}
tags:
  - team-log
status: done
---

# {Task Name}

| 항목 | 내용 |
|------|------|
| 작업자 | {git user.name} |
| 날짜 | {YYYY-MM-DD} |
| PR | {PR link or 없음} |

## 작업 내용

{변경된 파일과 커밋 요약. 짧은 bullet list.}

## 고민한 흔적

{핵심 결정 이유와 어려웠던 점. 1인칭으로, 2~4문장.}

## 영향 범위 및 팀 공유

{팀원이 알아야 할 것. 없으면 "없음".}

## 다음 액션

{이어질 작업이나 블로커. 없으면 생략.}
```

> You MUST write the entire log in Korean, regardless of the language the user used. Keep it concise — this is a team scan document, not a personal retrospective.
