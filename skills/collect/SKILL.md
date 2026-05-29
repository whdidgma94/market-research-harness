---
description: 경쟁사 1개에 대한 웹 데이터 수집
---

# collect

## 역할
지정된 경쟁사 1개에 대해 WebSearch/WebFetch로 공개 웹 데이터를 수집하고 원시 데이터 파일을 생성한다.

## 호출 agent
web-collector-agent (`agents/web-collector-agent.md`)

## 인자
- `--session-id {id}`: 세션 식별자. agent가 산출물 경로(`output/{session-id}/`)를 구성하는 데 사용.
- `--competitor "{경쟁사 이름}"`: 수집 대상 경쟁사 이름. competitors.json에 정의된 정규화 슬러그를 사용.

## 호출 방법
orchestrator-agent가 경쟁사 수만큼 병렬로 호출한다. 각 호출은 경쟁사 1개를 담당한다. agent는 session-id로 경로를 구성해 `output/{session-id}/research-brief.md`와 `output/{session-id}/competitors.json`을 직접 Read한다. 파일 내용은 인자로 전달하지 않는다. 내부 절차(WebSearch/WebFetch 검색, 재시도, 실패 처리, 출력 파일 형식)는 web-collector-agent 파일에 정의되어 있다.
