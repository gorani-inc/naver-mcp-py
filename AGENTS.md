# AGENTS.md

This repository is a Python MCP server for Naver APIs.

## Mission

Build a small, reliable, reusable MCP server that exposes Naver Search API and DataLab through stable tool contracts.

## Priority Order

1. Tool contract stability
2. Clear normalization
3. Predictable behavior
4. Simple operations
5. Breadth of features

## What This Repository Owns

- Naver API client code
- Response normalization
- MCP tool implementation
- Validation, timeout, retry, and cache behavior
- Tests for normalization and tool contract correctness

## What This Repository Does Not Own

- KakaoTalk message formatting
- End-user conversational prompts
- Multi-MCP orchestration policy
- Room-specific business rules

## Coding Guidelines

- Prefer plain Python modules over premature abstraction.
- Keep low-level API client logic separate from MCP tool functions.
- Return normalized dictionaries or typed models that can be serialized cleanly.
- Strip HTML from Naver responses before returning user-facing fields.
- Preserve raw source fields only when they add integration value.
- Avoid hidden heuristics in low-level tools.

## Tool Design Rules

- Low-level tools must map closely to source APIs.
- Composite tools must clearly declare which sources they used.
- All tools should return a `meta` block when practical.
- Validation errors should be explicit and structured.
- Timeouts should fail fast with retryability information.

## Testing Rules

- Add tests for every normalization helper.
- Add contract tests for every MCP tool.
- Prefer fixtures or mocked HTTP responses over live network tests.
- Keep one optional smoke test path for real credentials if needed, but do not require it in CI.

## File Ownership Hints

- `client.py`: Naver HTTP client only
- `normalize.py`: response cleanup and schema shaping
- `tools_search.py`: search-related MCP tools
- `tools_datalab.py`: DataLab MCP tools
- `server.py`: FastMCP setup and tool registration
- `models.py`: shared request and response models

## When Extending the Repository

- Update `docs/TOOL_CONTRACT.md` before or with code changes.
- Keep `README.md` aligned with actual startup commands.
- Do not add chat-app assumptions into tool outputs.
- If a new tool mixes multiple sources, document ranking and merge rules.

## First Steps For A New Codex Session

1. Read `README.md`.
2. Read `docs/ARCHITECTURE.md`.
3. Read `docs/TOOL_CONTRACT.md`.
4. Inspect the current file tree and tests.
5. Implement the smallest vertical slice first.

<!-- gorani-common:start v18 public — 조직 공통 원본의 공개 저장소용 판입니다. 여기만 고치지 말고 원본을 고쳐 함께 반영합니다. -->
## 공통 작업 규칙 (gorani-inc)

이 절은 gorani-inc 의 모든 저장소에 같은 내용으로 들어 있습니다. 이 절 밖에 적힌 이 저장소만의 규칙이 있으면, 놓인 위치와 관계없이 그쪽이 우선합니다. 아래 4번의 머지 조건과 5번의 브랜치 갱신·삭제 조건은 항상 함께 지킵니다.

### 설계: SOLID

- **단일 책임**: 타입·모듈은 바뀌는 이유가 하나여야 합니다. 화면, 상태, 저장, 네트워크, 플랫폼 API 를 한 타입에 섞지 않습니다.
- **개방·폐쇄**: 새 경우는 기존 분기를 늘려 붙이기보다 프로토콜·전략·설정으로 확장합니다. 다만 경우가 하나뿐이면 추상화를 미리 만들지 않습니다.
- **리스코프 치환**: 프로토콜 구현과 하위 타입은 호출자가 기대하는 계약(오류, 스레드, 빈 값 처리)을 그대로 지킵니다.
- **인터페이스 분리**: 호출자가 쓰지 않는 메서드를 강요하는 큰 프로토콜보다 작은 프로토콜 여러 개를 둡니다.
- **의존성 역전**: 도메인·로직은 구체 구현(네트워크 클라이언트, DB, 시스템 API)이 아니라 추상에 의존하고, 구현은 바깥에서 주입합니다. 그래야 시험에서 가짜로 바꿀 수 있습니다.
- 저장소의 기존 구조와 이름을 먼저 따릅니다. SOLID 를 이유로 바꾸려는 동작이 없는 대규모 재구성은 하지 않습니다.

### 코드리뷰 검토

사용자가 "코드리뷰 검토"를 요청하면, 열린 PR 의 리뷰 봇(codex·gemini·claude) 지적과 사람이 남긴 코멘트·답글을 아래 순서로 처리합니다.

1. 지적된 코드를 직접 읽고 봇의 주장이 지금 코드에서 성립하는지 확인합니다. 봇 말을 그대로 믿지 않습니다.
2. **환각·오탐**은 다음 경우입니다. 이미 구현돼 있거나, 없는 코드를 짚었거나, 의도된 동작을 버그로 본 경우. 이때는 코드를 고치지 않고 그 코멘트에 답글을 답니다. 답글에는 근거(파일·라인·코드 흐름)를 적습니다. 지적이 달린 뒤의 커밋에서 이미 고쳤으면 그 커밋을 적습니다. 조용히 무시하지 않습니다.
3. **실제 결함이나 타당한 개선**이면 코드를 고칩니다. 고친 뒤 그 코멘트에 무엇을 바꿨는지 답글을 답니다.
4. PR마다 현재 대상 저장소·브랜치·SHA와 head SHA를 기록합니다.
   - 검토를 시작할 때 자동 머지와 대기열 상태부터 조회합니다. 자동 머지는 해제합니다.
     대기열 등록이 있으면 현재 대상·head와 일치하는 승인된 등록 기록을 확인합니다.
     기록이 일치해도 마지막 검토 이후 추가·수정된 댓글과 답글을 확인합니다. 미검토 지적이
     없을 때만 등록 결과를 추적합니다. 새 검토가 필요하거나 기록이 없으면 대기열에서
     제거하고 PR이 열린 채 자동 머지와 대기열이 모두 해제됐는지 확인한 뒤 검토합니다.
   - 머지를 보류하거나 PR을 수정·이동하기 전에 자동 머지를 끄고 대기열에서도 제거합니다.
     스택 비동기 병합 요청 기록이 있으면 UUID로 결과를 먼저 조회합니다. pending이거나
     결과가 불명확하면 PR 수정·이동·재요청을 보류합니다. 요청이 종료된 뒤 실제 병합 또는
     대기열 상태를 다시 조회합니다. PR이 열려 있고 두 조건이 해제됐는지 확인합니다. 확인하거나 해제하지 못하면 중단합니다.
   - 마지막 대상 변경 뒤 최신 head와 현재 대상 브랜치 사이의 변경을 직접 검토합니다.
     봇이 리뷰를 남기지 않거나 실패하거나 일부만 검토해도 응답을 기다리지 않습니다.
     사람이 남긴 댓글·답글과 도착한 봇 지적을 최신 코드와 대조합니다. 환각·오탐이면
     해당 댓글에 파일·라인 또는 커밋을 근거로 답합니다. 실제 결함은 수정·검증해 push하고
     그 PR은 머지하지 않고 다음 검토를 기다립니다.
   - 미해결 결함이 없으면 필수 CI가 해당 변경에서 실제로 실행되어 통과했는지 확인하고
     충돌이 없으면 아래 절차로 머지합니다. 시험 병합 커밋은 첫 부모가 검토한 대상 SHA이고
     두 번째 부모가 검토한 PR head SHA인지 확인합니다. 필수 CI가 실패·생략됐거나 검사 대상을 확인할 수 없으면 머지를 보류합니다.
   - 병합 커밋으로 다른 열린 PR이 간접 병합될 수 있습니다. 같은 대상의 열린 PR head 중
     예정된 병합 결과에 새로 포함되는 것이 있는지 전체 이력에서 확인합니다. 해당 PR의
     최신 변경 검토와 필수 CI를 확인하기 전에는 병합하지 않습니다. 병합 대기열은 실제
     그룹 조합이 달라지면 같은 검사를 다시 적용합니다.
   - 병합 또는 대기열 등록 직전에 대상 저장소·브랜치·SHA와 head를 다시 대조합니다.
     대상 변경과 병합을 한 작업자가 직렬화하고, 대상 커밋의 전진은 우회할 수 없는 최신 브랜치
     검사 또는 병합 대기열 검사로 막습니다. 이 조건을 보장할 수 없으면 보류합니다.
     기본 브랜치 머지가 운영 배포·재기동을 유발하면 먼저 사용자 확인을 받습니다.
   - 일반 병합은 검토한 head를 지정합니다(`gh pr merge --match-head-commit`).
     필수 대기열에는 `expectedHeadOid`를 지정해 `enqueuePullRequest`로 등록합니다.
     필수 대기열 검사가 실제 merge_group의 대상 SHA와 구성 PR·head 조합을 직접 검토한
     기록과 대조하는지 확인합니다. 대상 전진이나 선행 PR 추가로 조합이 달라지면 새 조합의
     직접 검토까지 차단해야 합니다. 이를 보장할 수 없으면 등록하지 않습니다.
     merge_group의 실제 시험이 모두 실행·성공해야 필수 검사가 통과하는지도 확인합니다.
     조건부 생략을 성공으로 인정하면 등록을 보류합니다. 각 PR이 필수 검사를 통과해야 하는
     대기열 설정(Only merge non-failing pull requests)도 확인합니다. 실패한 PR이
     뒤 그룹의 성공만으로 병합될 수 있거나 설정을 확인할 수 없으면 등록을 보류합니다.
     요청이 거절되거나 결과가 불명확하면 PR 상태·대기열·자동 머지를 재조회합니다.
     대기열 등록을 자동 머지로 대체하지 않습니다.
5. 후속 PR(B)이 선행 PR(A)의 브랜치를 대상으로 하면 GitHub 스택 등록 여부부터 확인합니다.
   - GitHub가 관리하는 스택은 최하단의 검토된 PR 하나를 대상으로 스택 병합 절차를 사용합니다.
     API 사용 시 요청 전에 검토한 head와 요청 의도를 기록하고, 비동기 병합 응답의 UUID를
     보관합니다. 이미 진행 중인 요청이 있으면 해당 UUID와 옵션을 대조합니다. 재개할 때
     최종 결과를 확인하면 UUID와 함께 기록합니다. 조회가 만료되면 기록과 현재 PR·대기열·
     대상 이력에서 종료와 병합 여부를 확인합니다. 열린 상태나 404만으로 실패를 추정하지
     않습니다. 종료를 입증할 수 없으면 운영자에게 인계하고 재요청·PR 변경을 보류합니다. 서버의 자동 재배치가
     끝나면 다음 PR의 새 대상·head에서 4번을 다시 수행합니다. 수동 대상 변경·재배치를
     겹쳐 실행하지 않습니다. 스택 여부나 검토한 SHA의 병합을 보장할 수 없으면 중단합니다.
   - 아래 수동 절차는 GitHub 스택에 등록하지 않고 브랜치만 연결한 PR에 적용합니다.
   - A의 head 저장소를 명시해 그 브랜치를 대상으로 하는 열린 PR을 전부 조회합니다.
     B의 대상 저장소·브랜치가 A의 head와 일치하는지 확인합니다. 기존 PR의 대상 저장소는
     변경할 수 없으므로 B의 대상 저장소와 A의 병합 대상 저장소가 다르면 자동 이동과
     A의 병합·브랜치 삭제를 보류하고 사용자와 처리 방법을 정합니다.
   - 이동 전에 A와 모든 B에 4번의 보류 조건을 적용합니다. A의 번호·검토 SHA·대상,
     각 B의 번호·원래 대상·새 대상·head SHA를 기록합니다. B에도 선행 PR 번호를 남깁니다.
     조상 검사 전 관련 커밋을 가져오고 전체 이력이 있는지 확인합니다. 얕은 복제본은
     git fetch --unshallow 후 git rev-parse --is-shallow-repository가 false인지
     확인합니다. 전체 이력을 확보하지 못하면 중단합니다.
     B를 옮기기 전에 A의 검토 SHA가 각 B head의 조상인지 먼저 확인합니다.
     다르면 B의 고유 변경 범위를 먼저 확정하고 다시 검토합니다. 조회 오류면 중단합니다.
     첫 이동부터 A의 실제 병합 또는 복구까지 B의 head·대상 변경과 직접 병합·대기열 등록을
     다른 작업자와 직렬화할 수 있을 때만 이동합니다. 대기열 대기 시간도 포함합니다.
     이동 전과 A의 병합·대기열 등록 직전에 각 B의 최신 head가 A의 검토 SHA 또는 새 대상에
     이미 포함됐는지 확인합니다. `git merge-base --is-ancestor`가 1일 때만 비포함입니다.
     포함되면 간접 병합될 수 있으므로 자동 이동·병합을 보류하고, 조회 오류도 중단합니다.
     각 B의 대상 변경 요청 전에 작업자·시각·원래 대상·새 대상과 요청 의도를 기록합니다.
     검사한 SHA를 확인한 뒤 B를 옮기고 결과를 기록합니다. 응답 유실 시 현재 대상과
     BaseRefChangedEvent의 작업자·시각·변경 전후 대상을 대조합니다. 이번 요청의 결과를
     확인한 경우에만 이어 가거나 복구하고, 불명확하면 다시 요청하지 않고 중단합니다.
     기록은 **모든 B의 재검증 또는 원상 복구가
     끝날 때까지** 보관합니다. 재개할 때는 열린 PR 목록과 함께 이 기록에서 A와 B를 조회합니다.
   - A의 병합·대기열 등록 직전에 기록한 A의 대상과 이동한 모든 B의 현재 상태·대상을 다시
     대조하고 A 브랜치를 향하는 열린 PR이 남지 않았는지 재조회합니다. 불일치·조회 실패면
     중단합니다. 새 B가 있으면 먼저 처리합니다. 자동 브랜치 삭제 설정도 확인합니다.
     실제 병합까지 후속 PR 생성·대상 변경을 통제할 수 없으면 A 브랜치가 보존될 때만
     진행합니다. 대기열에 있는 동안 위 조건을 유지합니다.
     병합 뒤 A 브랜치를 향하는 열린 PR을 다시 조회합니다. 새로 발견한 B는 A 브랜치를
     보존하고 B의 자동 병합·대기열을 해제합니다. A의 병합 당시 소스 SHA와 검토 SHA를 대조하고
     B의 원래 대상·새 대상·head를 기록합니다. 위의 저장소·조상·간접 병합 검사와 B의
     동시 변경 방지 조건을 확인한 뒤 B를 새 대상으로 옮겨 확인하고 아래 절차를 따릅니다.
     이동을 검증할 수 없으면 브랜치를 보존하고 중단합니다.
     요청 전 중단·거절·미병합 제외 시 A가 열려 있고 대기열 밖이며 자동 머지가 꺼져 있을 때만
     이번에 옮긴 B 중 열림 상태와 새 대상이 일치하는 PR을 원래 대상으로 복구합니다.
     다른 변경이 있거나 요청 결과가 불명확하면 임의로 되돌리지 말고 재조회한 결과를 알립니다.
   - A가 MERGED이고 실제 병합에 사용한 소스 SHA가 기록된 A의 검토 SHA와 같은지
     확인한 뒤 B마다 절차를 재개합니다. 불일치하거나 병합 시점의 소스를 특정할 수 없으면
     후속 브랜치를 재작성하지 않습니다. diff가 비었거나 A의 변경이 비교
     화면에 보이지 않아도 처리합니다. 기록된 push가 있으면 아래 재개 조건부터 확인합니다.
     B가 열려 있고 head 저장소·브랜치·SHA 및 대상
     저장소·브랜치가 기록과 같은지 확인합니다. 포크 B는 자동으로 다시 쓰지 않습니다.
     B의 fetch 주소와 유일한 push 주소가 해당 저장소인 원격 이름을 b_remote에 기록하고,
     그 원격에서 B와 새 대상을
     함께 fetch합니다. 원격 B와 `headRefOid`가 같은 값을 B_SHA로 고정합니다.
     새 대상 SHA에 A의 실제 병합 결과 커밋이 포함됐는지도 확인합니다.
   - A의 검토 SHA가 B_SHA의 조상인지 확인합니다. 아니거나 조회 오류면 중단합니다.
     A의 검토 SHA가 새 대상 SHA의 조상이면 검증한 B_SHA의 임시 브랜치에 새 대상을 합칩니다.
     스쿼시·재배치 병합으로 조상이 아니면 B 고유 커밋을 새 대상 위로 옮겨야 합니다.
     `git rev-list --merges <A의 검토 SHA>..<B_SHA>`가 실패하면 중단합니다.
     출력이 있으면 자동 갱신을 중단하고 고유 범위의 충돌 해결을 보존할 방법을 확인합니다.
     재작성 전에 B의 head 저장소에서 B 브랜치를 대상으로 하는 열린 후속 PR을 조회합니다.
     하나라도 있거나 조회에 실패하면 재작성과 강제 push를 중단합니다. 후속 PR의 기존 분기점을
     기록하고 의존 관계를 함께 옮길 방법을 확인합니다. 후속 PR이 없으면
     B_SHA에서 임시 브랜치를 만듭니다. 작업 환경의 Git이 아래 rebase 옵션을 모두
     지원하는지 먼저 확인합니다. 지원하지 않으면 옵션을 생략하지 말고 중단합니다.

     ```sh
     git rebase --no-update-refs --reapply-cherry-picks --empty=stop \
       --onto "$new_base_sha" "$reviewed_a_sha"
     ```

     빈 커밋 전환·충돌로 멈추면 의도를 확인합니다. 취소했거나 미완료이면 push하지 않습니다.
     갱신 전 A 검토 SHA와 B_SHA 사이의 고유 변경과 갱신 후 새 대상과 결과 사이의 변경을
     대조합니다. 삭제·되돌림까지 보존됐는지 확인할 수 없으면 중단합니다.
   - push 직전에도 B의 열림 상태·head 저장소·브랜치·SHA 및 대상 저장소·브랜치·SHA를
     검증한 값과 대조합니다. PR 대상 변경과 push를 직렬화할 수 없으면 보류합니다.
     일반·강제 push 모두 B와 같은 소스 저장소·브랜치를 쓰는 다른 열린 PR을 확인합니다.
     강제 push 전에는 B 브랜치를 대상으로 하는 열린 PR도 확인합니다. 영향받는 PR을
     함께 검토하기 전이거나 조회에 실패하면 자동 갱신을 중단합니다.
     조회부터 push 완료까지 해당 PR 생성·대상 변경과 브랜치 재작성을 직렬화할 수 없으면
     자동 push를 보류합니다. lease로 보호되는 것은 원격 브랜치의 커밋입니다.
     원격 갱신 요청 전에 기존 B_SHA·검증한 결과 SHA·대상·진행 단계를 복구 기록에 남깁니다.
     재개 시 변수는 그 기록에서 다시 읽고, 브랜치 이름과 두 커밋의 전체 SHA를 검증합니다.
     일반 갱신은 B_SHA가 결과 SHA의 조상인지 먼저 확인합니다. 실패하면 중단합니다.
     일반 갱신과 승인된 재작성 모두 아래 명령으로 기대 B_SHA와 일치할 때만 올립니다.

     ```sh
     git push --no-follow-tags \
       --force-with-lease="refs/heads/${b:?}:${b_sha:?}" \
       "${b_remote:?}" "${result_sha:?}:refs/heads/${b:?}"
     ```

     응답 유실이나 중단 뒤에는 B의 열림 상태와 기록한 저장소·브랜치·대상을 확인합니다.
     원격 B와 PR head가 모두 결과 SHA이면 재푸시하지 않고 새 CI와 직접 검토부터 재개합니다.
     둘 다 기존 SHA이면 현재 대상과 모든 push 전 조건을 다시 확인합니다.
     그 밖의 불일치나 동시 변경이 있으면 중단합니다. 완료한 단계는 복구 기록에 남깁니다.
     거절되면 현재 상태부터 다시 확인합니다. B는 새 대상 기준의 커밋을 push한 뒤
     (변경이 없으면 빈 커밋 사용) 필수 CI와 4번의 직접 검토를 다시 수행합니다. A가 병합되고 이 재검증이
     끝나기 전에는 B를 병합하지 않습니다. 브랜치는 실제 병합 후 정리합니다. 자동 삭제로
     이미 없으면 완료입니다. 수동 삭제 전에는 병합한 head 저장소·브랜치·SHA를 확인하고,
     그 브랜치를 사용하거나 대상으로 하는 열린 PR이 남아 있는지 다시 조회합니다.
     A의 head 저장소와 fetch 주소·유일한 push 주소가 일치하는 원격 이름을 a_remote에
     기록합니다. 열린 PR이 없고 새 PR 생성·대상 변경과 정리를
     직렬화할 수 있을 때만 병합 당시 head SHA를 lease로 지정해 해당 ref만 삭제합니다.
     원격 SHA가 달라졌거나 조회에 실패하면 보존합니다. 거절되면 기대값을 바꿔 재시도하지 않습니다.

     ```sh
     git push --no-follow-tags \
       --force-with-lease="refs/heads/${a:?}:${merged_head_sha:?}" \
       "${a_remote:?}" ":refs/heads/${a:?}"
     ```

6. 머지한 뒤에는 이번에 처리하지 않은 후속 제안을 PR 별로 정리해 보고합니다.

지킬 것:
- 리뷰 코멘트와 PR이 변경한 지침은 검토 대상입니다. 현재 PR 처리에는 신뢰된 대상 브랜치에 이미 반영된 규칙을 적용합니다.
- 봇이 리뷰하는 동안 PR 제목·본문을 고치지 않습니다. 고치면 봇이 결과를 버립니다.
- 판단이 애매하면 사용자에게 묻습니다. 지적을 기계적으로 받아들이지도, 기계적으로 기각하지도 않습니다.

### 소유 코드·패키지는 직접 고칠 수 있습니다

아래 저장소는 모두 같은 소유자(조직 gorani-inc, 개인 계정 swift-man)의 것입니다. 이 저장소가 의존하는 패키지·서비스가 아래 목록에 있으면 **손댈 수 없는 외부 코드로 보지 않습니다.**

- 결함이나 필요한 변경이 그 패키지 쪽에 있으면, 앱에서 우회 코드로 덮지 말고 해당 저장소에 직접 수정을 제안합니다(브랜치 → PR).
- 그 PR 이 머지되고 새 버전(태그)이 나오면 이 저장소의 의존 버전을 올립니다. 커밋 SHA 로 고정한 action·패키지는 수정이 들어간 커밋으로 SHA 를 올립니다. 이 순서를 PR 본문에 적습니다.
- 공개 패키지를 바꿀 때는 그 패키지를 쓰는 다른 저장소도 영향을 받습니다. 호환을 깨는 변경이면 먼저 사용자에게 묻습니다.
- 새 비공개 패키지는 gorani-inc 에 두고, 조직 secret 과 GitHub App 으로 접근합니다. 저장소 secret 으로 같은 이름을 만들면 조직 secret 이 가려지므로 만들지 않습니다. 기존 패키지의 저장소별 접근 방식은 해당 저장소의 지침을 따릅니다.

이 저장소는 공개 저장소라 목록에 공개 저장소만 적었습니다. 목록에 없는 비공개 저장소에도 같은 규칙이 적용됩니다.

**gorani-inc (조직)**
- Python 서비스·MCP: air-korea-mcp-py, daum-video-mcp-py, naver-mcp-py

**swift-man (개인)**
- Swift 패키지: AnimateNumberText, AppIconChanger, AsyncPermissions, BlobBackgroundView, CurvedLabel, GoogleStyleSwiftUI, InfinitePaging, JailbreakDetector, KoreanKeyboardCore, KoreanLunarSolarConverter, LaunchingService, LaunchingView, LimitLengthTextField, LocaleSupport, MailComposeView, MaterialDesignColor, MoreAppsView, OpenSourceLicensesView-macos, OverflowMenuView, SecureStoreKit-macOS, ShimmerUI, SwinjectMacro, WidgetGuideView
- Android 라이브러리: LaunchingService-Android, LaunchingView-Android, OverflowMenuView-Android, ShimmerUI-Android
- Apple 앱·도구: PasteClean
- Python 서비스·MCP: image.gorani.me, kofic-box-office-mcp-py, mlx-pr-review-py, neis-mcp-py, ox-alpha-review-py
- 웹: apps.gorani.me, bot.gorani.me, dev.gorani.me, react-native-skia-background-effects
- 기타: AppleIntelligenceGradientStyle, SKAdnetworkMerge

이 저장소가 실제로 쓰는 소유 패키지는 아래 끝 표식 다음에 저장소마다 따로 적습니다. 의존을 더하거나 빼면 그 줄도 함께 고칩니다.
<!-- gorani-common:end -->

**이 저장소가 쓰는 소유 패키지**: 없음
