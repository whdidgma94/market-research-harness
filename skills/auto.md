---
description: 전체 시장 조사 파이프라인 자동 실행 (intake→collect→analyze→position→report)
---

# auto

## 역할
분석 대상·경쟁사 목록·조사 범위를 인자로 받아 전체 파이프라인을 자동 실행한다.

## 호출 agent
orchestrator-agent (`agents/orchestrator-agent.md`)

## 인자
- 분석 대상 문자열: 분석할 제품/서비스 이름 또는 설명
- 경쟁사 목록 문자열: 경쟁사명 쉼표 구분 목록 (최대 10개. 초과 시 상위 10개 자동 선택 후 경고 출력)
- `--scope "{자유 텍스트}"`: 조사 범위 지시. 지역/시장/기간 정보를 이 텍스트에 포함한다. 예: `--scope "한국 SaaS 시장, 2024년 기준, 가격·기능·고객지원 품질 중심"`
- session-id: orchestrator-agent가 `date +%Y%m%d-%H%M%S` 형식으로 신규 생성 (호출 시 전달 불필요)

## 호출 방법
`/analyze` 커맨드로 호출한다. orchestrator-agent에 분석 대상 문자열, 경쟁사 목록 문자열, `--scope` 자유 텍스트 세 인자를 전달한다. session-id는 orchestrator-agent가 생성하므로 호출자가 별도로 생성하거나 전달하지 않는다. 내부 단계별 절차는 orchestrator-agent 파일에 정의되어 있다.
