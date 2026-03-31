Obsidian 작업을 위한 Agent Skills 모음입니다.

[Agent Skills 스펙](https://agentskills.io/specification)을 따르기 때문에 Claude Code, Codex CLI 등 호환 에이전트에서 모두 사용할 수 있습니다.

## 설치

```bash
npx skills add https://github.com/orange-brother/obsidian-skills.git
```

설치 후 Claude Code, Codex 등 에이전트를 재시작하면 모든 스킬을 바로 사용할 수 있습니다.

### 수동 설치

#### Claude Code

```bash
git clone https://github.com/orange-brother/obsidian-skills.git
cp -r obsidian-skills/skills/* ~/.claude/skills/
```

#### Codex CLI

```bash
git clone https://github.com/orange-brother/obsidian-skills.git
cp -r obsidian-skills/skills/* ~/.codex/skills/
```

#### OpenCode

```sh
git clone https://github.com/orange-brother/obsidian-skills.git ~/.opencode/skills/obsidian-skills
```

`skills/` 폴더만 복사하지 말고 레포 전체를 클론하세요. OpenCode는 `~/.opencode/skills/` 하위의 모든 `SKILL.md`를 자동으로 인식합니다. 재시작 후 바로 사용 가능합니다.

## 스킬 목록

| 스킬 | 설명 |
|------|------|
| [obsidian-markdown](skills/obsidian-markdown) | wikilinks, embeds, callouts, properties 등 Obsidian 전용 문법으로 `.md` 파일 작성/편집 |
| [obsidian-bases](skills/obsidian-bases) | views, filters, formulas, summaries를 사용한 `.base` 파일 작성/편집 |
| [obsidian-cli](skills/obsidian-cli) | Obsidian CLI를 통해 vault 노트 관리, 플러그인·테마 개발 |
| [dev-log](skills/dev-log) | 작업명과 배경을 입력하면 git 맥락을 수집하고 인터뷰를 통해 회고 노트를 vault에 저장 |
| [team-log](skills/team-log) | dev-log와 동일한 흐름으로 회고 노트를 작성하고 gh CLI로 팀 GitHub 레포에 바로 업로드 |
| [decision-log](skills/decision-log) | 코드 외 업무(리서치, 설계, 기획 등)의 문제 → 검토 → 결정 과정을 인터뷰 기반으로 vault에 저장 |

## 설치 경로

`npx skills add`로 설치하면 에이전트별로 다음 경로에 저장됩니다:

| 에이전트 | 경로 |
|---------|------|
| Claude Code | `~/.agents/skills/` |
| Codex | `~/.codex/skills/` |

## dev-log 사용법

작업이 끝난 후 에이전트에게 `dev-log 스킬 써줘` 또는 `/dev-log`로 호출합니다.

1. **작업명과 배경 입력** — 작업명과 한두 줄 설명을 제공합니다
2. **git 맥락 자동 수집** — git log, diff, PR 등을 자동으로 수집합니다
3. **관련 노트 탐색** — obsidian-cli로 vault 내 기존 노트를 검색해 wikilink 연결 대상을 파악합니다
4. **인터뷰** — 결정 이유, 트레이드오프, 인사이트 등 3~5개 질문을 한 번에 던집니다
5. **노트 생성** — `YYYY-MM-DD-task-name.md` 형식으로 vault에 저장됩니다

### 출력 노트 형태

```markdown
---
title: 작업명
date: YYYY-MM-DD
tags:
  - dev-log
project: 프로젝트명
repo: org/repo
status: done
---

# 작업명

## 배경
작업 배경 설명

## 변경 내역
- [[관련 노트]]와 연결된 변경사항
- 변경된 파일 및 커밋 요약

## 고민과 판단
> [!warning] 트레이드오프
> 선택한 이유와 포기한 것

## 인사이트
> [!insight] 핵심 배움
> 다음에 기억할 것

## 다음 액션
> [!question] 미해결
> 남은 것 또는 이어질 작업
```

### 주의사항

> [!important]
> **Step 3 (관련 노트 탐색)은 Obsidian 앱이 실행 중일 때만 동작합니다.** Obsidian이 꺼져있으면 해당 단계는 건너뛰고 wikilink 없이 노트가 생성됩니다.

### 설정

사용 전 `~/.agents/skills/dev-log/SKILL.md`의 `VAULT_PATH`를 본인 vault 경로로 수정하세요:

```
VAULT_PATH = /Users/yourname/Documents/MyVault/Dev Logs
```
