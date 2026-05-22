# 시장 조사·경쟁사 분석 하네스 (Market Research & Competitive Analysis Harness)

분석 대상 제품/서비스와 경쟁사 목록을 입력받아 웹 데이터를 수집·분석하고 포지셔닝 매트릭스와 시장 분석 보고서를 자동 생성하는 Claude Code 하네스.

---

## 커맨드 목록

```
/analyze "분석대상" "경쟁사1,...,경쟁사N" --scope "범위"   ← 전체 파이프라인 실행 (수집→분석→포지셔닝→보고서)
/intake "분석대상" "경쟁사목록" --scope "범위"             ← 조사 범위 정리 + 확인만 실행
/collect {session-id}                                     ← 웹 데이터 수집 단계만 재실행
/analyze-one {session-id} "경쟁사"                        ← 특정 경쟁사 분석만 재실행
/position {session-id}                                    ← 포지셔닝 매트릭스만 재생성
/report {session-id}                                      ← 최종 보고서만 재생성
```

> 주1: 전체 실행 커맨드 `/analyze`와 단일 경쟁사 재분석 커맨드는 이름이 겹치지 않도록 후자를 `/analyze-one`으로 둔다.
> 주2: 경쟁사는 **최대 10개**까지 분석한다. 10개를 초과해 입력하면 상위 10개만 자동 선택되고 경고 메시지가 출력된다.
> 주3: 지역/시장 범위는 별도 인자 없이 `--scope` 자유 텍스트에 포함한다. 예: `--scope "한국 SaaS 시장, 2024년 기준"`.

---

## 파이프라인 규칙

1. 모든 산출물은 `.harness-artifacts/market-research-harness/output/{session-id}/` 하위에 저장된다.
2. 조사 범위(분석 대상·경쟁사·지역 시장 범위·조사 축)는 `intake` 단계에서 사용자 확인을 거친 뒤에만 수집이 시작된다.
3. 경쟁사는 최대 10개까지 분석하며, 초과 입력 시 상위 10개만 자동 선택되고 경고가 출력된다.
4. 지역/시장 범위는 `--scope` 자유 텍스트에 포함하여 지정한다 (별도 인자 없음).
5. 웹 데이터 수집과 경쟁사 분석은 경쟁사별로 병렬 수행된다.
6. 웹 검색은 Claude Code 내장 WebSearch/WebFetch 도구를 사용하며, 외부 API 키가 필요 없다.
7. 포지셔닝 매트릭스의 X/Y축은 수집된 분석 데이터를 기반으로 자동 선정되며, 매트릭스 생성 전 사용자 확인을 거쳐 승인 또는 수정된다.
8. 최종 보고서는 경영진 보고에 활용 가능한 수준으로 `final-report.md`에 저장된다.

---

## 산출물 경로

```
.harness-artifacts/market-research-harness/output/
  {session-id}/
    research-brief.md        # 사용자 입력 정리 (분석 대상, 경쟁사 목록, 조사 범위·분석 축)
    competitors.json         # 경쟁사 목록 + 메타데이터 (이름, 도메인, 세그먼트, 수집 상태)
    web-data/
      {competitor-name}.md   # 경쟁사별 수집 원시 데이터 (출처 URL, 수집 일시 포함)
    analysis/
      {competitor-name}.md   # 경쟁사별 분석 결과 (조사 축별 정량·정성)
    positioning-matrix.md    # 2축 포지셔닝 매트릭스 + white space
    final-report.md          # 최종 시장 분석 보고서
```

- `{session-id}`는 `YYYYMMDD-HHMMSS` 형식. orchestrator-agent가 `intake` 단계 시작 시 1회 생성하여 모든 하위 단계에 동일하게 전달한다.
- `{competitor-name}`은 `competitors.json`에 정의된 정규화 슬러그(소문자, 공백→하이픈)를 사용한다.
- 단독 커맨드(`/collect`, `/analyze-one`, `/position`, `/report`)는 기존 session-id를 인자로 전달하여 같은 산출물 디렉토리를 재사용한다.
