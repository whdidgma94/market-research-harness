---
description: 경쟁사 1개에 대한 분석 수행 (강점/약점/가격/세그먼트/차별점/리스크)
---

# analyze

## 역할
지정된 경쟁사 1개의 수집 원시 데이터를 조사 축에 따라 정량·정성 분석하고 분석 결과 파일을 생성한다.

## 호출 agent
competitor-analyst-agent (`agents/competitor-analyst-agent.md`)

## 인자
- `--session-id {id}`: 세션 식별자. agent가 산출물 경로(`output/{session-id}/`)를 구성하는 데 사용.
- `--competitor "{경쟁사 이름}"`: 분석 대상 경쟁사 이름. competitors.json에 정의된 정규화 슬러그를 사용.

## 호출 방법
orchestrator-agent가 경쟁사 수만큼 병렬로 호출한다. 각 호출은 경쟁사 1개를 담당한다. agent는 session-id로 경로를 구성해 `output/{session-id}/research-brief.md`와 `output/{session-id}/web-data/{competitor}.md`를 직접 Read한다. 파일 내용은 인자로 전달하지 않는다. 내부 절차(분석 축 적용, 정보 부족 처리, 출력 파일 형식)는 competitor-analyst-agent 파일에 정의되어 있다.
