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
| [json-canvas](skills/json-canvas) | nodes, edges, groups 등으로 `.canvas` 파일 작성/편집 |
| [obsidian-cli](skills/obsidian-cli) | Obsidian CLI를 통해 vault 노트 관리, 플러그인·테마 개발 |
| [defuddle](skills/defuddle) | 웹페이지에서 불필요한 요소를 제거하고 깔끔한 마크다운으로 추출 |
| [dev-log](skills/dev-log) | 작업명과 배경을 입력하면 git 맥락을 수집하고 인터뷰를 통해 회고 노트를 vault에 저장 |

## dev-log 사용법

작업이 끝난 후 에이전트에게 `dev-log 스킬 써줘` 또는 `/dev-log`로 호출합니다.

1. **작업명과 배경 입력** — 작업명과 한두 줄 설명을 제공합니다
2. **자동 수집** — AI가 git log, diff, PR 등 맥락을 자동으로 수집합니다
3. **인터뷰** — AI가 결정 이유, 트레이드오프, 인사이트 등 3~5개 질문을 한 번에 던집니다
4. **노트 생성** — `YYYY-MM-DD-task-name.md` 형식으로 vault에 저장됩니다

사용 전 `skills/dev-log/SKILL.md`의 `VAULT_PATH`를 본인 vault 경로로 수정하세요:

```
VAULT_PATH = /Users/yourname/Documents/MyVault/Dev Logs
```
