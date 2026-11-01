# AI 사용 기록

허용 범위는 개념 질문과 자신이 작성한 코드의 디버깅입니다. 과제 함수의 정답이나 전체 구현을 대신 생성하게 해서는 안 됩니다. 먼저 현재 단계 검사를 실행하세요.

사용하지 않았다면 아래에 사용하지 않았다고 적습니다. 사용했다면 질문마다 기록합니다. API 키, 개인 이메일, 실제 회사 데이터는 입력하지 않습니다.

| 구분 | 질문 요약 | 공유한 자기 코드와 오류 범위 | 이해한 내용 | 직접 수정하고 검증한 결과 |
|---|---|---|---|---|
| 개념 또는 디버깅 | 작성 | 작성 | 작성 | 작성 |
| 디버깅 · 문서 변환 | 스트리밍 실행이 문서 준비 단계에서 중단되는 원인을 문의했다. | to_documents()의 Document 생성 TODO와 미완성 안내를 공유했다. | 스트리밍 전에 문서를 변환해야 하며, 본문은 page_content에, 부가 정보는 metadata에 전달해야 함을 이해했다. | main.py에서 Document(page_content=row["body"], metadata=metadata)로 수정했다. 이후 관련 검사를 통과했다. |
| 디버깅 · 검색 문법 | 키워드 점수 계산에서 발생한 문법 오류를 문의했다. | search_documents()의 score 계산식과 SyntaxError를 공유했다. | 포함 여부를 검사하는 조건에는 in 연산자가 필요하며, 일치하는 키워드마다 1씩 더해 점수를 계산함을 이해했다. | score = sum(1 for word in words if word in normalized)로 수정했다. check search로 재검사했다. |
| 디버깅 · 검색 기능 연결 | 검색 검사에서 일부 항목이 미구현으로 실패하는 원인을 문의했다. | check search의 2개 통과, 5개 실패 결과와 관련 코드를 확인했다. | 허용 후보 밖의 문서는 검색 전에 제외해야 하며, 현재 질문과 이전 대화를 검색어 구성 함수에 함께 전달해야 함을 이해했다. | search_documents()의 후보 제외 조건과 build_context_chain()의 support.search_query() 호출을 수정했다. uv run --frozen python main.py check search 재검사 후 모두 통과했다. |
| 디버깅 · 기본 체인 | 순차 체인 실행 검사에서 발생한 TypeError의 원인을 문의했다. | check chain의 12개 통과, 1개 실패 결과와 build_primary_chain()의 출처 검증 호출을 확인했다. | 딕셔너리 조회의 대괄호와 함수 인자 구분 위치를 구분해야 하며, 파싱 결과와 검색 문서를 각각 인자로 전달해야 함을 이해했다. | validate_sources(value["parsed"], value["documents"])로 수정했다. uv run --frozen python main.py check chain 재검사 후 모두 통과했다. |
| 디버깅 · 대화 이력 | 이력 검사 네 항목이 미구현으로 실패하는 원인을 문의했다. | check history의 0개 통과, 4개 실패 결과와 commit_history()의 조건 TODO를 확인했다. | primary 결과만 이력에 저장하고, 문서 없음 안내와 fallback은 저장하지 않아야 함을 이해했다. | 조건을 outcome["route"] != "primary"로 수정했다. uv run --frozen python main.py check history 재검사 후 모두 통과했다. |
| 디버깅 · 실패 복구 | 복구 검사 전체에서 같은 TypeError가 발생하는 원인을 문의했다. | check recovery의 0개 통과, 13개 실패 결과와 build_resilient_chain()의 with_retry() 호출을 확인했다. | 메서드에 정의되지 않은 인자 이름을 전달하면 체인 구성 단계부터 실패하며, 정확한 인자 이름은 wait_exponential_jitter임을 이해했다. | with_exponential_jitter=False를 wait_exponential_jitter=False로 수정했다. uv run --frozen python main.py check recovery 재검사 후 모두 통과했다. |

도구와 모델 이름:

AI의 설명이 틀렸거나 그대로 적용하지 않은 부분:

최종 코드와 설명을 스스로 설명할 수 있는지 확인:
