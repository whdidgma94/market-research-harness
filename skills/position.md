---
description: 데이터 기반 X/Y축 자동 선정 + 포지셔닝 매트릭스 종합 + 축 확인 체크포인트 (#2)
---

# position

## 역할
모든 경쟁사 분석 데이터를 기반으로 X/Y축을 자동 선정하고 2축 포지셔닝 매트릭스를 생성하며, 축 확인 체크포인트를 수행한다.

## 호출 agent
positioning-architect-agent (`agents/positioning-architect-agent.md`)

## 인자
- `--session-id {id}`: 세션 식별자. agent가 산출물 경로(`output/{session-id}/`)를 구성하는 데 사용.
- `--axes "{x축,y축}"` (선택): 사용자가 체크포인트 #2에서 축 수정을 요청한 경우에만 전달. 미전달 시 agent가 데이터 기반으로 축을 자동 선정한다.

## 호출 방법
orchestrator-agent가 analyze 단계 완료 후 1회 순차 호출한다. agent는 session-id로 경로를 구성해 `output/{session-id}/research-brief.md`와 `output/{session-id}/analysis/` 디렉토리 내 모든 `.md` 파일을 직접 Read한다. 파일 내용은 인자로 전달하지 않는다. 사용자가 체크포인트 #2에서 축 수정을 요청하면 `--axes` 인자를 추가해 재호출한다. 내부 절차(축 후보 선정·탈락 근거, 매트릭스 작성, white space 식별, 체크포인트 진행 방식)는 positioning-architect-agent 파일에 정의되어 있다.
