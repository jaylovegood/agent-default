# agent-default

에이전틱 코딩 프로젝트를 위한 설정 기본값(템플릿) 저장소입니다. [Codex](https://developers.openai.com/codex)와 [Claude Code](https://claude.com/claude-code)를 동일한 작업자로 취급하며, 새 프로젝트에 복사해 두 도구를 번갈아 쓸 수 있는 공통 환경을 만듭니다.

## 목표

- **도구 중립:** 어느 도구도 특정 단계를 전담하지 않습니다. 한도가 남은 도구, 더 잘하는 도구가 이어서 작업합니다.
- **낮은 비용:** 기본은 단독 작업이며, 서브에이전트와 교차 리뷰는 명시적으로 요청했을 때만 사용합니다.
- **끊김 없는 전환:** 채팅 기록 대신 계획 문서(plan)와 작업 트리로 인수인계합니다.

## 구성

```text
AGENTS.md                  # 모든 에이전트가 읽는 공통 지침 (정본)
CLAUDE.md                  # AGENTS.md를 import하는 Claude Code 어댑터
.codex/
  config.toml              # Codex 서브에이전트 설정 (동시 2개 제한)
  agents/                  # Codex용 explorer, reviewer (읽기 전용)
.claude/
  agents/                  # Claude Code용 explorer, reviewer (읽기 전용)
  skills -> ../.agents/skills
.agents/skills/            # 두 도구가 공유하는 스킬
docs/
  ai/WORKFLOW.md           # 작업 모드와 도구 전환 절차
  ai/USAGE.ko.md           # 사용자용 한국어 가이드
  plans/                   # 계획 템플릿과 active/ 계획
  backlog.md               # 보류 중인 작업
```

## 사용 방법

1. 이 저장소의 파일을 새 프로젝트 루트에 복사합니다. (`.claude/skills` 심볼릭 링크가 유지되도록 `cp -R` 등을 사용하세요.)
2. [AGENTS.md](AGENTS.md)의 **Project-specific section**을 프로젝트에 맞게 채웁니다: 스택, 명령어, 아키텍처 규칙, 완료 기준. 해당하지 않는 항목은 비워 둡니다.
3. 도구를 실행하고 작업 모드를 지정해 요청합니다.

```text
Quick 모드로 이 버그를 수정해줘. 서브에이전트는 사용하지 말고 관련 테스트까지 실행해줘.
```

## 작업 모드

| 모드 | 용도 | 방식 |
| --- | --- | --- |
| Quick | 범위가 작고 위험이 낮은 작업 (기본값) | 한 에이전트가 조사·수정·검증. 계획 파일 없음 |
| Standard | 여러 파일에 걸친 기능, 의미 있는 동작 변경 | `docs/plans/active/`에 계획 유지, 편집은 한 에이전트가 담당, 필요 시 읽기 전용 리뷰 |
| Critical | 인증·결제·파괴적 데이터 변경·보안 경계 | 롤백 포함 상세 계획, 별도 에이전트의 계획·최종 diff 리뷰 |

자세한 절차는 [docs/ai/WORKFLOW.md](docs/ai/WORKFLOW.md)를 참고하세요.

## 공용 스킬

Codex에서는 `$feature-flow`, Claude Code에서는 `/feature-flow`처럼 호출합니다.

| 스킬 | 설명 |
| --- | --- |
| `feature-flow` | 에이전트 사용을 최소화하며 기능을 계획·구현·검증·인계 |
| `debug-flow` | 재현, 경쟁 가설, 증거 수집, 회귀 검증 중심의 디버깅 |
| `review-diff` | 인수 기준에 대한 읽기 전용 diff 리뷰 |
| `handoff` | 다른 세션이 이어받을 수 있도록 상태를 정리 |
| `spike` | `spikes/` 아래 격리된 폴더에서 일회성 타당성 실험 |
| `ui-design` | 협업으로 디자인 방향을 정하고 토큰 중심으로 적용 |
| `overnight` | 자리를 비운 동안 제한된 큐를 무인으로 처리하고 아침 보고서 작성 |

## 도구 전환

서로의 CLI를 호출하거나 자동화하지 않습니다. 현재 도구에 인수인계를 요청하고, 다른 도구에서 이어서 진행합니다.

```text
다른 코딩 에이전트가 바로 이어서 작업할 수 있도록 handoff를 준비해줘.
```

```text
현재 작업을 이어서 진행해줘. 먼저 AGENTS.md, active plan, 현재 diff를 읽어.
```

## 언어 정책

- 에이전트가 자동으로 읽는 지침(`AGENTS.md`, 스킬, 에이전트 정의)은 토큰을 아끼기 위해 간결한 영어로 작성합니다.
- 사용자는 한국어로 요청하고, 에이전트는 별도 요청이 없으면 한국어로 답합니다.
- 사용자용 안내는 한국어로 분리합니다. ([docs/ai/USAGE.ko.md](docs/ai/USAGE.ko.md))

## 병렬 세션

서로 무관한 작업을 여러 세션에서 동시에 진행할 때는 작업마다 전용 `git worktree`와 브랜치를 만들어 분리합니다. 규칙은 [AGENTS.md](AGENTS.md)의 *Concurrent sessions*를 참고하세요.
