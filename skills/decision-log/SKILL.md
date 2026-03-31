---
name: decision-log
description: Create an Obsidian note documenting a non-code work decision — research, investigation, planning, or design choices. Entirely interview-based with no git context required. Use when the user wants to log a problem they investigated, options they considered, and a decision they reached.
---

# Decision Log Skill

Turn a non-code decision or investigation into a structured Obsidian note. No git context — entirely driven by interview. Designed for research, design choices, planning, and any work where thinking and judgment matter more than code changes.

## Configuration

```
VAULT_PATH = /path/to/your/vault/Dev Logs
```

Replace with the actual folder path in your Obsidian vault.

## Workflow

### Step 1 — Receive topic input

The user provides:
- **Topic name**: a short label for the decision or investigation

If missing, ask for it before proceeding.

### Step 2 — Find related notes in the vault

Use the **obsidian-cli skill** to search for existing notes related to this topic:

```bash
obsidian search query="{topic-name}" limit=10
obsidian search query="decision-log" limit=10
```

If Obsidian is not running, skip silently and proceed without wikilinks.

### Step 3 — Interview the user

Ask the following 5 questions all at once:

1. 어떤 문제나 상황이 있었나요? 왜 이 결정이 필요했나요?
2. 해결을 위해 무엇을 조사하거나 참고했나요? (문서, 사례, 사람 등)
3. 어떤 선택지들을 검토했나요? 각각 어떤 장단점이 있었나요?
4. 최종적으로 어떤 결정을 내렸고, 핵심 근거는 무엇인가요?
5. 이 결정으로 해결되지 않는 부분이나 다음에 이어질 것이 있나요?

### Step 4 — Write the decision log

Use the **obsidian-markdown skill** to generate the output file. Apply frontmatter, wikilinks, and callouts as defined in that skill.

Save the file to:

```
{VAULT_PATH}/{YYYY-MM-DD}-{topic-name}.md
```

Where `{topic-name}` is the topic name lowercased with spaces replaced by hyphens.

## Output Structure

```
frontmatter   → title, date, tags: [decision-log], status: done
# {Topic Name}
## 배경 / 문제       → 왜 이 결정이 필요했는지
## 검토한 것들       → 조사하거나 참고한 것들, [[wikilinks]] where relevant
## 고려한 옵션       → 선택지와 각각의 장단점
               → use > [!warning] for tradeoffs
## 결정 및 근거      → 최종 결정, 1인칭으로
               → use > [!insight] for the key reasoning
## 다음 액션         → use > [!question] for unresolved items, omit if nothing
```

> You MUST write the entire log in Korean, regardless of the language the user used. Keep each section concise — this is a log, not documentation.
