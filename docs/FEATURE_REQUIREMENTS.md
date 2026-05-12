# 기능 요구사항

이 문서는 PRD를 구현 가능한 요구사항, acceptance criteria, 테스트 요구사항으로 분해한다.

## 용어

| 용어 | 의미 |
| --- | --- |
| PRD | 제품 목표와 MVP 범위 기준 |
| Acceptance Criteria | 사용자가 기대하는 완료 조건 |
| Definition of Done | 구현, 검증, 문서, 리뷰까지 포함한 완료 기준 |
| mother branch | issue 전체의 기준 branch |
| sub PR | issue를 리뷰 가능한 의미 단위로 나눈 PR |
| review drain | PR 리뷰 finding을 clean 상태까지 닫는 반복 절차 |

## 공통 요구사항

### FR-COMMON-001: 요구사항은 검증 가능한 형태여야 한다

- 모든 기능 요구사항은 acceptance criteria를 가져야 한다.
- acceptance criteria는 사람이 읽을 수 있고, 가능하면 테스트나 명령으로 확인 가능해야 한다.
- 모호한 요구사항은 구현 범위에 넣지 않고 open question으로 남긴다.

Acceptance Criteria:

- [ ] 각 기능 항목에 완료 판정 조건이 있다.
- [ ] 테스트 또는 수동 검증 방법이 명시되어 있다.
- [ ] 제외 범위가 분명하다.

### FR-COMMON-002: 문서와 구현은 함께 갱신되어야 한다

- 기능 동작, 운영 규칙, 보안 경계, 신뢰성 기준이 바뀌면 관련 문서를 갱신한다.
- 새 문서를 추가하면 `docs/generated/context-map.json`과 인덱스를 함께 갱신한다.

Acceptance Criteria:

- [ ] 문서 라우터가 새 기준을 찾을 수 있다.
- [ ] `./scripts/verify docs`가 통과한다.

## 기능 요구사항 템플릿

### FR-AREA-001: 기능 이름

설명:

- 

Acceptance Criteria:

- [ ] 

테스트 요구사항:

- 

문서 요구사항:

- 

제외 범위:

- 
