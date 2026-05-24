# 개발 환경 구성

## 목적

- 새 개발자가 로컬 설정, 검증 명령, GitHub 운영 흐름을 빠르게 찾을 수 있게 한다.
- 구현 전 단계에서도 문서 하네스와 검증 루틴을 먼저 고정한다.

## 보일러플레이트 런타임

- 이 보일러플레이트는 프로젝트 언어를 강제하지 않는다.
- Codex hook과 검증 스크립트 실행에는 Node.js 20 이상이 필요하다.
- `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `build.gradle` 같은 언어별 manifest는 실제 프로젝트가 필요할 때만 둔다.
- 현재 검증 스크립트는 외부 npm 의존성 없이 Node.js 표준 라이브러리만 사용한다.
- 실제 프로젝트 의존성을 추가할 때는 `docs/SECURITY.md`의 dependency 승인 기준을 따른다.
- 언어별 검증은 `scripts/verify-project`에 연결한다. 기본 파일은 no-op이며, 대상 프로젝트가 lint/typecheck/test/build 명령을 갖게 되면 이 파일을 수정한다.

## 환경 변수

- 실제 `.env` 파일과 secret 값은 커밋하지 않는다.
- `.env.example`은 커밋 가능하다.
- GitHub token, OpenAI/Codex 인증 값은 로그나 Codex prompt에 원문으로 넣지 않는다.

## 로컬 산출물

- `.local/`: 로컬 DB, command log, agent artifact
- `.worktrees/`: issue 또는 sub PR 작업용 git worktree
- `.codex/tmp/`: hook state
- `node_modules/`, `dist/`, `coverage/`, `test-results/`: 생성 산출물

위 경로는 커밋하지 않는다.

## 검증

문서 구조 검증:

```sh
./scripts/verify docs
```

hook 설정 검증:

```sh
./scripts/verify hooks
```

GitHub 템플릿과 workflow 검증:

```sh
./scripts/verify github
```

프로젝트별 검증:

```sh
./scripts/verify project
```

전체 검증:

```sh
./scripts/verify
```

전체 검증은 문서, hook, GitHub 운영 파일, 프로젝트별 검증 슬롯을 순서대로 실행한다. 프로젝트별 test/lint/build가 생기면 `scripts/verify-project`에서 언어별 명령을 호출하거나, 각 언어의 native task runner가 `scripts/verify`를 호출하도록 연결한다.

Java 프로젝트 예시:

```sh
./gradlew check
# 또는
./mvnw verify
```

hook은 Java/Gradle/Maven build 파일과 Gradle dependency lockfile 변경을 project 검증 설정 변경으로 취급한다. `pom.xml`, `build.gradle`, `build.gradle.kts`, `settings.gradle`, `settings.gradle.kts`, `gradle.properties`, wrapper 파일, `gradle.lockfile`, `gradle/dependency-locks/*.lockfile` 변경 후에는 `./scripts/verify`를 실행한다.

## 작업 브랜치와 worktree

권장 기본 흐름:

```sh
git switch main
git pull --ff-only
git switch -c issue-12-mother
git push -u origin issue-12-mother
git worktree add ../issue-12-01-foundation -b issue-12/01-foundation issue-12-mother
```

규칙:

- `main`에서는 mutating command를 실행하지 않는다.
- sub PR은 mother branch에서 갈라진다.
- 병렬 sub PR은 파일 소유권이 겹치지 않을 때만 진행한다.
- lockfile, context map, 문서 인덱스처럼 충돌이 쉬운 파일은 한 PR의 책임으로 둔다.
