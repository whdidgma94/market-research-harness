# market-research-harness

분석 대상과 경쟁사 목록을 입력하면 웹 데이터 수집, 경쟁사별 분석, 포지셔닝 매트릭스, 최종 시장 분석 보고서를 자동 생성하는 Claude Code 하네스.

---

## 파이프라인 흐름

```
사용자 입력
    │
    ▼
[1] intake  ─── research-planner-agent (Opus)
    │           조사 범위 확정 + 경쟁사 목록 정규화
    │           ※ 체크포인트 #1: 사용자 확인 후 진행
    │
    ▼
[2] collect ─── web-collector-agent × N  (Sonnet, 병렬)
    │           경쟁사별 WebSearch / WebFetch로 공개 웹 데이터 수집
    │
    ▼
[3] analyze ─── competitor-analyst-agent × N  (Sonnet, 병렬)
    │           경쟁사별 강점/약점/가격/세그먼트/차별점/리스크 분석
    │
    ▼
[4] position ── positioning-architect-agent (Opus)
    │           분석 데이터 기반으로 X/Y축 자동 선정 + 포지셔닝 매트릭스 작성
    │           ※ 체크포인트 #2: 축 선정 근거 제시 → 사용자 승인/수정
    │
    ▼
[5] report ──── report-writer-agent (Sonnet)
                최종 시장 분석 보고서 생성 (executive summary 포함)
```

---

## 시작하기

```bash
# 레포 클론
git clone <레포 URL>
cd market-research-harness

# Claude Code로 열기
claude

# 전체 파이프라인 실행 예시
/analyze "Notion" "Obsidian,Logseq,Roam Research,Craft" --scope "한국 노트 앱 시장, 2024년 기준, 가격·기능·타겟 사용자층 중심"
```

---

## 커맨드 목록

| 커맨드 | 설명 |
|--------|------|
| `/analyze "대상" "경쟁사1,...,경쟁사N" --scope "범위"` | 전체 파이프라인 실행 (수집 → 분석 → 포지셔닝 → 보고서) |
| `/intake "대상" "경쟁사목록" --scope "범위"` | 조사 범위 정리 및 사용자 확인 체크포인트만 실행 |
| `/collect {session-id}` | 웹 데이터 수집 단계만 재실행 |
| `/analyze-one {session-id} "경쟁사"` | 특정 경쟁사 분석만 재실행 |
| `/position {session-id}` | 포지셔닝 매트릭스만 재생성 |
| `/report {session-id}` | 최종 보고서만 재생성 |

**인자 설명**

- `"대상"` — 분석하려는 자사 제품/서비스명
- `"경쟁사1,...,경쟁사N"` — 쉼표 구분, **최대 10개**. 10개 초과 입력 시 입력 순서 기준 상위 10개만 자동 선택되고 경고 메시지가 출력된다.
- `--scope "범위"` — 지역, 시장, 기간, 조사 축을 자유 텍스트로 기술. 별도 `--region` 인자는 없으며 지역/시장 범위도 이 인자에 포함한다. 예: `--scope "동남아 SaaS 시장, 2024년, 가격·고객지원 중심"`

---

## 핵심 기능

- **경쟁사 병렬 분석**: 수집(collect)과 분석(analyze) 단계는 경쟁사별로 동시에 실행된다. 경쟁사가 많을수록 순차 대비 시간이 단축된다.
- **일관된 분석 축**: 모든 경쟁사를 동일한 분석 축(강점/약점/가격 포지션/타겟 세그먼트/차별점/리스크)으로 평가하여 비교 가능성을 보장한다.
- **포지셔닝 매트릭스 자동 생성**: 수집된 데이터를 기반으로 경쟁사 간 변별력이 가장 큰 X/Y축 2개를 자동 선정한다. 선정 근거를 함께 제시하며, 사용자가 축을 수정할 수 있다.
- **White space 식별**: 매트릭스에서 아직 경쟁자가 없는 시장 공간을 식별해 포지셔닝 기회를 제시한다.
- **WebSearch/WebFetch 내장 도구 사용**: Claude Code 기본 탑재 도구로 공개 웹 데이터(공식 사이트, 가격 페이지, 리뷰, 뉴스)를 수집한다.
- **수집 실패 시 부분 진행**: 특정 경쟁사의 데이터 수집이 실패해도 다른 경쟁사 작업은 영향받지 않는다. 실패 축은 파일 내에 `[수집 실패: {축}, 사유]`로 명시된다.

---

## 외부 의존성

없음.

WebSearch와 WebFetch는 Claude Code에 내장된 도구이며 외부 API 키가 필요하지 않다. `settings.json`의 `permissions.allow`에 등록하는 것만으로 동작한다.

---

## 산출물 경로

```
output/
  {session-id}/                        # YYYYMMDD-HHMMSS 형식, intake 시작 시 생성
    research-brief.md                  # 분석 대상, 경쟁사 목록, 지역/시장 범위, 조사 축
    competitors.json                   # 경쟁사별 메타데이터 (이름, 도메인, 세그먼트, 슬러그)
    web-data/
      {competitor-name}.md             # 경쟁사별 수집 원시 데이터 (출처 URL, 수집 일시 포함)
    analysis/
      {competitor-name}.md             # 경쟁사별 분석 결과 (조사 축별 정량·정성)
    positioning-matrix.md              # X/Y축 정의 및 선정 근거, 경쟁사 좌표, white space
    final-report.md                    # 최종 시장 분석 보고서
```

`{competitor-name}`은 `competitors.json`에 기록된 정규화 슬러그(소문자, 공백→하이픈)를 사용한다.

---

## Agent 구성

| Agent | 모델 | 역할 |
|-------|------|------|
| orchestrator-agent | Opus (`claude-opus-4-7`) | 전체 파이프라인 조율. intake → collect(병렬) → analyze(병렬) → position → report 순서로 skill 호출. 경쟁사 10개 초과 시 상위 10개 자동 선택 및 경고 출력. |
| research-planner-agent | Opus (`claude-opus-4-7`) | 사용자 입력을 분석해 조사 대상·경쟁사 목록·지역 시장 범위·조사 축을 확정. `--scope` 텍스트에서 지역·시장·기간 정보 추출. 체크포인트 #1 수행. |
| web-collector-agent | Sonnet (`claude-sonnet-4-6`) | 경쟁사 1개당 1개 인스턴스가 병렬 실행. WebSearch/WebFetch로 공개 웹 데이터 수집. |
| competitor-analyst-agent | Sonnet (`claude-sonnet-4-6`) | 경쟁사 1개당 1개 인스턴스가 병렬 실행. 원시 데이터를 조사 축에 따라 구조화 분석. |
| positioning-architect-agent | Opus (`claude-opus-4-7`) | 모든 경쟁사 분석을 종합해 데이터 기반으로 최적 X/Y축 자동 선정. 2축 포지셔닝 매트릭스 작성 및 white space 식별. 체크포인트 #2 수행. |
| report-writer-agent | Sonnet (`claude-sonnet-4-6`) | research-brief, 경쟁사 분석 전체, 포지셔닝 매트릭스를 종합해 최종 시장 분석 보고서 작성. |
