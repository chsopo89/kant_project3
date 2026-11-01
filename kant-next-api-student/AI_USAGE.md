# AI 사용 기록

AI는 개념 질문과 직접 작성한 코드의 디버깅에만 사용할 수 있습니다. 사용하지 않았다면 아래에 `사용하지 않음`이라고 적습니다. 사용량을 늘릴 필요는 없습니다.

| 허용되는 도움 | 예시 |
|---|---|
| 개념 이해 | HTTP 404와 422의 차이, await의 의미를 다른 예시로 설명해 달라고 질문 |
| 자신의 코드 디버깅 | 직접 작성한 짧은 함수와 오류를 보여 주고 원인과 확인 방법을 질문 |

과제 전체 풀이, TODO의 완성 코드, 참고 답안을 요청하지 않습니다. 디버깅 답변도 이해한 원인과 수정 내용을 자신의 말로 기록하고 다시 실행합니다. API 키, `.env`와 인증 헤더를 질문에 포함하지 않습니다.

## 사용 여부

- 사용 여부:
- 사용한 도구:

## 도움 기록

도움을 받은 건마다 표의 행을 추가합니다. 대화 전문보다 질문과 직접 확인한 결과를 구체적으로 적습니다.

| 단계/함수 | 직접 시도한 내용과 문제 | 질문 요약 | 받은 설명 | 내가 수정하거나 확인한 내용 | 재실행 결과 |
|---|---|---|---|---|---|
| 2026-11-01, 자료 수집 | 수집 실행과 검사가 미구현으로 중단되는 원인을 문의했다. | 저장할 데이터, 페이지 파라미터, HTTP 상태 검사와 다음 페이지 확인에 TODO가 남아 있음을 확인했다. 가상환경 경고와 실행 중단 원인도 구분했다. | app.py의 collect_releases()에서 저장값을 value로 지정하고 page·per_page 전달, raise_for_status() 호출, next 링크 존재 여부 검사를 반영했다. | uv run --frozen python app.py check collect. 최초 0개 통과, 9개 실패에서 최종 저장 결과 9개 통과, 0개 실패로 확인됐다. |
| 2026-11-01, JSON 구성 문법 | 수집 명령 실행 중 모델 입력 구성 줄에서 문법 오류가 발생하는 원인을 문의했다. | JSON 변환 대상의 중괄호 누락과 괄호 위치 오류를 확인했다. 실행 전에 파일 전체의 문법을 검사하므로 다른 기능의 문법 오류도 먼저 해결해야 함을 설명받았다. | app.py의 build_prompt()에서 question과 documents를 딕셔너리로 묶고, ensure_ascii=False를 json.dumps()의 인자로 넣도록 수정했다. | uv run --frozen python app.py check collect로 재확인했다. 이후 저장된 check query 결과는 6개 통과, 0개 실패다. |
| 2026-11-01, 함수 중복 선언 | 함수 선언 뒤 본문이 필요하다는 들여쓰기 오류의 원인을 문의했다. | admit_query() 선언이 두 번 들어가 첫 번째 선언 아래에 주석만 남아 있음을 확인했다. 중복 선언만 삭제하고 실제 if 본문은 유지하도록 안내받았다. | app.py의 admit_query()에서 두 번째 def 선언을 삭제하여 기존 함수 선언과 본문을 연결했다. | uv run --frozen python app.py check collect로 파일이 읽히는지 재확인했다. 이후 저장된 check observe 결과는 6개 통과, 0개 실패다. |
| 2026-11-01, 문서 모델 길이 제한 | 문서 모델 선언 중 KeyError가 발생하는 원인을 문의했다. | support.LIMITS의 키가 빈 문자열로 작성되어 있음을 확인했다. 본문 최대 길이를 가져오려면 body 키를 사용해야 함을 설명받았다. | app.py의 Document에서 본문 길이 제한 참조를 support.LIMITS["body"]로 수정했다. | uv run --frozen python app.py check collect로 재확인했다. 이후 저장된 check document-api 결과는 7개 통과, 0개 실패다. |
| 2026-11-01, 문서 정제 | 제목·본문·URL·날짜·플래그 관련 검사가 미구현으로 실패하는 원인을 문의했다. | normalize_releases()에 남은 일곱 TODO를 확인했다. 플래그 자료형, 제목 대체, 빈 본문, 채택된 ID 기준 중복 검사와 정리된 문서 값의 연결 방법을 설명받았다. | app.py의 normalize_releases()에서 해당 조건과 문서 값을 수정했다. 잘못된 첫 행이 뒤의 정상 행을 막지 않도록 하고, 게시시각과 수집시각을 구분했다. | uv run --frozen python app.py check documents. 중간 결과 4개 통과, 18개 실패에서 최종 저장 결과 22개 통과, 0개 실패로 확인됐다. |
| 2026-11-01, 문서 API 모델 검토 | 문서 API의 미구현·입력 검증·OpenAPI 검사 실패 원인을 문의했다. | 검증 함수에 남은 TODO, 문서 객체와 문서 ID 타입의 혼동, session_id 오타를 확인했다. 조회 함수의 반환문 아래에 남은 이전 코드도 정리하도록 안내받았다. | app.py의 Document, QueryRequest, QueryReply 검증 함수에서 TODO를 삭제했다. document_ids와 source_ids를 list[DocumentId]로 수정하고 sesstion_id를 session_id로 변경했다. find_document()의 미사용 중복 코드를 삭제했다. | uv run --frozen python app.py check document-api. 최초 0개 통과, 7개 실패에서 최종 저장 결과 7개 통과, 0개 실패로 확인됐다. |
| 2026-11-01, 재시도 처리 | 재시도 후 다른 오류가 발생하는 검사에서 미구현으로 실패하는 원인을 문의했다. | timeout·HTTP 재시도 조건과 공급자 429 처리에 TODO가 남아 있음을 확인했다. 첫 시도의 timeout·5xx만 재시도하고 마지막 오류에 맞게 종료하도록 설명받았다. | app.py의 query_with_retry()에서 timeout 재시도 조건을 attempt == 0으로, HTTP 재시도 조건을 첫 시도의 500~599로 수정했다. 공급자 429는 503으로 반환하도록 수정했다. | uv run --frozen python app.py check retry. 최종 저장 결과 17개 통과, 0개 실패다. |
