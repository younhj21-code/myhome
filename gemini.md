# Antigravity Workspace Rules & Git Automation Workflow

## 1. 기본 작업 원칙 (Core Principles)
- **프로젝트 관리**: 모든 소스 코드 수정, 파일 생성 및 삭제 작업 완료 후 반드시 Git 상태(`git status`)를 점검한다.
- **검증 우선 (Verify First)**: 코드 수정 후 로컬 테스트 또는 브라우저(`/browser`) 렌더링 검증을 통과한 경우에만 커밋 및 푸시를 진행한다.
- **문서화 동기화**: 주요 기능 추가 또는 구조 변경 시 루트의 `README.md`나 `TASKS.md`의 진행 상태 체크리스트를 함께 갱신한 뒤 커밋에 포함한다.

---

## 2. 자동 커밋 및 GitHub 푸시 워크플로 (Auto-Git Workflow)
코드 수정 작업이 마무리되면 별도의 추가 지시가 없더라도 아래 4단계 절차를 순차적으로 실행한다.

### Step 1: 민감 정보 및 불필요 파일 차단 (Pre-Commit Security Check)
- `git diff` 및 스테이징 대상 파일 목록을 검토하여 다음 항목이 포함되지 않도록 `.gitignore`에 등록하거나 스테이징에서 제외한다.
  - API 키, 토큰, 비밀번호가 포함된 환경 변수 파일 (`.env`, `.env.local`, `credentials.json`, `mcp_config.json` 등)
  - 로컬 가상환경 및 빌드 캐시 (`.venv/`, `node_modules/`, `__pycache__/`, `.DS_Store`)

### Step 2: 변경 사항 스테이징 (Staging)
- 보안 점검이 완료된 변경 파일을 `git add` 명령어로 스테이징한다.

### Step 3: 커밋 메시지 작성 규칙 (Conventional Commits)
- `git diff --cached` 내용을 분석하여 아래 표준 형식에 맞춰 명확한 **한국어 커밋 메시지**를 작성한다.
  - **형식**: `<타입>(<범위>): <변경 내용 핵심 요약>`
  - **주요 타입**:
    - `feat`: 새로운 기능 또는 페이지/모듈 추가
    - `fix`: 버그 수정, 오류 해결, 예외 처리
    - `refactor`: 동작 변경 없는 코드 구조 개선 및 리팩토링
    - `style`: UI 레이아웃, CSS 스타일링, 코드 포맷팅 수정
    - `docs`: `README.md`, `GEMINI.md` 등 문서 수정
    - `chore`: 설정 파일 변경, 패키지 업데이트, 빌드 스크립트 수정
  - **본문(필요 시)**: 주요 변경 이유와 수정된 핵심 파일 목록을 불릿 기호(`-`)로 2~3줄 요약한다.

### Step 4: 원격 저장소 푸시 (Push to GitHub)
- 현재 활성화된 브랜치를 확인(`git branch --show-current`)하고, 해당 원격 브랜치로 `git push origin <branch>`를 실행한다.
- 만약 원격 저장소(`origin`)에 최신 변경 사항이 먼저 반영되어 충돌이 예상될 경우, `git pull --rebase origin <branch>`를 먼저 수행한 뒤 푸시한다.
- 푸시 완료 후 최종 커밋 해시(Commit Hash)와 원격 저장소 반영 결과를 사용자에게 간략히 보고한다.

---

## 3. 안전 가드레일 (Safety Guardrails)
- **강제 푸시 금지**: `git push --force` (`-f`) 명령은 어떠한 경우에도 실행하지 않는다.
- **메인 브랜치 보호**: 사용자가 명시적으로 지시하지 않는 한 `main` 브랜치의 히스토리를 덮어쓰는 `git reset --hard` 명령을 실행하지 않는다.
- **오류 발생 시 중단**: 인증 만료(`Authentication failed`) 또는 병합 충돌(Merge Conflict) 발생 시 임의로 해결하지 말고 오류 메시지와 함께 사용자에게 즉시 보고한다.