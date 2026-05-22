---
description: 사용자 입력 정리 및 조사 범위 확정 + 사용자 확인 체크포인트 (#1)
---

# intake

## 역할
사용자 요청 인자를 정리해 research-brief.md와 competitors.json을 생성하고, 사용자 확인 체크포인트를 수행한다.

## 호출 agent
research-planner-agent (`agents/research-planner-agent.md`)

## 인자
- `--session-id {id}`: 세션 식별자. agent가 산출물 경로(`output/{session-id}/`)를 구성하는 데 사용.
- `--target "{분석 대상}"`: 분석할 제품/서비스 이름 또는 설명.
- `--competitors "{경쟁사1,경쟁사2,...}"`: 경쟁사 목록 (최대 10개). orchestrator-agent가 사전 절삭을 완료한 상태로 전달.
- `--scope "{자유 텍스트}"`: 조사 범위 지시. 지역·시장·기간 정보를 자연어로 포함한다. `--region` 등 별도 지역 인자는 없으며, 지역/시장 범위는 이 텍스트 안에 기술한다.

## 호출 방법
orchestrator-agent가 파이프라인 첫 번째 단계로 호출한다. 단독으로는 `/intake` 커맨드로도 호출 가능하다. 파일 내용은 인자로 전달하지 않으며, agent가 session-id로 산출물 경로를 구성해 직접 파일을 작성한다. 내부 절차(research-brief.md 생성, 경쟁사 정규화, 체크포인트 진행 방식)는 research-planner-agent 파일에 정의되어 있다.
