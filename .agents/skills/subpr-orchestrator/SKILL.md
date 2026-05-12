---
name: subpr-orchestrator
description: GitHub issue 하나를 mother branch와 여러 sub PR/worktree로 나눠 병렬 또는 순차 작업 흐름으로 운영할 때 사용한다.
---

# subpr-orchestrator

GitHub issue 하나를 mother branch와 여러 sub PR로 나눠 실제 작업 가능한 흐름으로 운영할 때 사용한다.

## 사용 조건

- issue가 하나의 PR로 리뷰하기 크다.
- 병렬 가능한 작업을 Codex sub-agent와 worktree로 나누고 싶다.
- 순차 의존성이 있는 PR을 mother branch 기준으로 안전하게 이어가야 한다.

## 읽을 문서

1. `AGENTS.md`
2. `docs/README.md`
3. issue 본문과 댓글
4. `docs/PRD.md`
5. `docs/FEATURE_REQUIREMENTS.md`
6. `docs/PLANS.md`
7. 관련 설계 문서와 product spec

## workflow

1. issue와 문서를 읽는다.
   - 목적
   - acceptance criteria
   - DnD
   - 테스트 요구사항
   - 문서 갱신 요구사항
2. 현재 Git 상태를 확인한다.
   - `git status --short --branch`
   - `git remote -v`
   - `gh repo view`
3. mother branch를 만든다.
   - 기준 branch를 최신화한다.
   - 예: `git switch main && git pull --ff-only`
   - 예: `git switch -c issue-12-mother`
   - 예: `git push -u origin issue-12-mother`
4. sub PR 계획을 확정한다.
   - 각 sub PR의 목표
   - 제외 범위
   - 파일 소유권
   - DnD
   - 검증 명령
   - 의존성
5. 병렬 가능성을 판단한다.
   - 서로 다른 파일 또는 모듈을 수정하면 병렬 가능하다.
   - 공통 schema, 핵심 타입, 상태 전이, 문서 인덱스, lockfile을 동시에 만지면 순차 진행한다.
   - 한 PR의 출력이 다른 PR의 입력이면 순차 진행한다.
6. worktree를 만든다.
   - 예: `git worktree add ../issue-12-01-foundation -b issue-12/01-foundation issue-12-mother`
7. 병렬 sub PR은 Codex sub-agent에 위임한다.
   - worker에게 파일 소유권을 명시한다.
   - 다른 agent가 있음을 알리고, 다른 변경을 되돌리지 말라고 지시한다.
   - 각 worker는 변경 파일과 검증 결과를 한국어로 요약해야 한다.
8. 로컬 통합과 검증을 수행한다.
   - 각 worktree에서 검증 명령 실행
   - commit
   - push
   - PR 생성
9. 각 PR에 `$pr-review-drain`을 실행한다.
10. 순차 PR은 선행 PR merge 후 mother branch를 최신화하고 다음 branch/worktree를 만든다.

## sub-agent 지시 템플릿

```text
너는 issue #<number>의 Sub PR <n>을 담당한다.
소유 파일 범위는 <paths> 이다.
다른 agent가 <other paths>를 수정할 수 있으므로 해당 파일은 건드리지 않는다.
Acceptance Criteria는 아래와 같다.
작업 후 변경 파일과 검증 결과를 한국어로 요약해라.
```

## 완료 기준

- mother branch가 존재한다.
- sub PR 계획에 목표, 제외 범위, DnD, 검증 명령이 있다.
- 병렬 가능성과 순차 의존성이 표시됐다.
- 각 sub PR의 branch/worktree/PR URL이 기록됐다.
- 각 PR은 관련 검증을 통과했다.
- 각 PR은 review drain clean 조건을 충족했거나 남은 리스크가 명시됐다.

## 최종 요약에 포함할 것

- issue URL
- mother branch
- sub PR 목록과 PR URL
- 병렬/순차 실행 결과
- 검증 명령과 결과
- review drain 상태
- 남은 리스크
