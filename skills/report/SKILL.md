---
description: 최종 시장 분석 보고서 생성 (경영진 보고 수준)
---

# report

## 역할
브리프·경쟁사 분석·포지셔닝 매트릭스를 종합하여 경영진 보고에 활용 가능한 최종 시장 분석 보고서를 생성한다.

## 호출 agent
report-writer-agent (`agents/report-writer-agent.md`)

## 인자
- `--session-id {id}`: 세션 식별자. agent가 산출물 경로(`output/{session-id}/`)를 구성하는 데 사용.

## 호출 방법
orchestrator-agent가 position 단계 완료 후 1회 순차 호출한다. agent는 session-id로 경로를 구성해 `output/{session-id}/research-brief.md`, `output/{session-id}/analysis/` 디렉토리 내 모든 `.md` 파일, `output/{session-id}/positioning-matrix.md`를 직접 Read한다. 파일 내용은 인자로 전달하지 않는다. 내부 절차(보고서 구성, 출처 목록 작성, 면책 문구 포함 방식)는 report-writer-agent 파일에 정의되어 있다.
