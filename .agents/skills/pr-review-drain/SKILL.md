---
name: pr-review-drain
description: 현재 branch 또는 지정한 PR의 리뷰 댓글, unresolved thread, checks를 clean 상태까지 처리할 때 사용한다.
---

# pr-review-drain

현재 branch 또는 지정한 PR의 review feedback을 clean 상태까지 닫을 때 사용한다.

## 사용 조건

- PR에 Codex review comment, GitHub review, inline comment, unresolved thread가 있다.
- 리뷰 finding별로 DnD를 정의하고 수정/검증/커밋/푸시/resolve/re-review를 반복해야 한다.
- PR이 merge 준비 상태인지 기계적 증거로 확인해야 한다.

## 읽을 문서

1. `AGENTS.md`
2. `docs/README.md`
3. PR 본문
4. 관련 issue
5. 관련 PRD/FEATURE_REQUIREMENTS/PLANS/DESIGN 문서

## workflow

1. 대상 PR을 찾는다.
   - 현재 branch 기준: `gh pr view --json number,url,baseRefName,headRefName,headRefOid,state`
   - 지정 PR이 있으면 해당 PR을 사용한다.
2. 리뷰 입력을 모두 수집한다.
   - review comments
   - reviews
   - review threads
   - 일반 PR comments
   - GitHub checks
3. finding을 정규화한다.
   - 중복 comment를 묶는다.
   - 이미 해결된 comment와 unresolved thread를 구분한다.
   - 각 finding에 severity, 파일, 근거, DnD를 붙인다.
4. 수정 계획을 세운다.
   - P1/P2/P3 또는 merge-blocking finding을 먼저 처리한다.
   - 스타일 취향보다 버그, 회귀, 위험한 가정, 누락 검증을 우선한다.
5. 필요한 코드/문서/test를 수정한다.
6. 검증을 실행한다.
   - PR 본문의 검증 명령
   - 관련 테스트
   - 기본 `./scripts/verify`
7. commit/push한다.
8. 해결된 thread를 resolve한다.
9. `@codex review` 또는 저장소 규칙에 맞는 재리뷰 요청을 남긴다.
10. clean signal을 확인한다.
    - unresolved thread 없음
    - GitHub checks pass
    - Codex가 "no major issues" 또는 이에 준하는 clean 신호를 냄
    - 로컬 working tree clean

## GitHub 명령 힌트

```sh
gh pr view --json number,url,baseRefName,headRefName,headRefOid,state,reviewDecision
gh pr checks
gh api repos/:owner/:repo/pulls/<pr-number>/comments
gh api repos/:owner/:repo/pulls/<pr-number>/reviews
gh api graphql -f query='query { ... }'
```

GraphQL thread 조회는 저장소와 PR 번호에 맞춰 reviewThreads를 요청한다.

## DnD 템플릿

```text
Finding:
- 근거:
- 영향:
- 수정 범위:
- 완료 조건:
- 검증 명령:
```

## 완료 기준

- unresolved thread가 없다.
- GitHub checks가 pass다.
- Codex clean signal이 있다.
- 로컬 working tree가 clean이다.
- 수정 commit이 push됐다.
- 처리한 finding과 남은 리스크가 최종 요약에 포함됐다.

## 최종 요약에 포함할 것

- PR URL
- base/head
- 처리한 finding
- 실행한 검증 명령과 결과
- resolve한 thread 수
- 재리뷰 요청 여부
- 남은 리스크
