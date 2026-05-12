# Codex-native 운영 보일러플레이트 아키텍처

이 저장소는 별도 오케스트레이션 서버 없이 Codex, GitHub, git worktree, 문서 라우터, hook, 재사용 가능한 skill을 조합해 개발 운영을 반복 가능하게 만드는 보일러플레이트다.

## 핵심 경계

```text
boAiler
 ├─ AGENTS.md
 │  └─ 작업 전 문서 라우터
 ├─ docs/
 │  └─ 제품 판단, 기능 요구사항, 설계, 계획, 신뢰성, 보안, 품질 기준
 ├─ .agents/skills/
 │  └─ project-bootstrap, prd-writer, issue-planner, subpr-orchestrator, pr-review-drain
 ├─ .codex/
 │  └─ config.toml, hooks.json, hooks/*
 ├─ .github/
 │  └─ issue template, PR template, verify workflow
 └─ scripts/
    └─ 문서 구조와 hook 설정 검증
```

## 운영 모델

1. 요구사항은 `prd-writer` skill로 `docs/PRD.md`와 `docs/FEATURE_REQUIREMENTS.md`에 정리한다.
2. 구현 후보는 `issue-planner` skill로 GitHub issue 초안과 acceptance criteria, Definition of Done, 예상 sub PR 계획으로 만든다.
3. 큰 issue는 `subpr-orchestrator` skill로 mother branch와 sub PR branch/worktree 단위로 쪼갠다.
4. 독립적인 sub PR은 Codex sub-agent와 worktree로 병렬 실행한다.
5. 순차 의존성이 있는 sub PR은 선행 PR merge 후 mother branch를 최신화하고 다음 branch를 만든다.
6. 각 PR은 `pr-review-drain` skill로 리뷰 댓글, unresolved thread, checks, Codex clean signal을 확인하며 닫는다.
7. hook은 workflow 자체를 대신하지 않고, 위험 명령 차단과 검증 누락 방지에 집중한다.

## 상태의 system of record

- 요구사항과 운영 기준: `docs/`
- 작업 단위와 리뷰 상태: GitHub issue/PR/checks/comments
- 로컬 격리: git branch와 git worktree
- 반복 가능한 절차: `.agents/skills/*/SKILL.md`
- 자동 안전 장치: `.codex/hooks/*`
- 검증 게이트: `./scripts/verify`

## 서버가 필요한 시점

이 보일러플레이트는 개인 또는 소규모 팀의 실용적 자동화에 맞춘다. 다음 요구가 실제로 반복되면 별도 오케스트레이션 서버로 승격한다.

- GitHub webhook을 받아 무인으로 job을 시작해야 한다.
- 여러 worker가 lease를 잡고 24시간 queue를 처리해야 한다.
- 실패한 Codex thread를 자동으로 새 thread로 복구해야 한다.
- audit log, retry policy, SLA 같은 운영 요구가 있다.
- 조직 단위로 같은 자동화 서비스를 공유해야 한다.
