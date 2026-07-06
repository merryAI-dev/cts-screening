# cts-screening — KOICA CTS 사전적격심사 자동화

## Repo 경계 (중요)

- 이 repo 는 **KOICA CTS 전용 파이프라인**이다. 규칙을 DB 로 관리하는 범용 심사 플랫폼(simsa)은
  **https://github.com/merryAI-dev/simsa** (로컬: `/Users/boram/Hwp/cts_screening` — 폴더명이
  언더스코어인 쪽이 simsa 임에 주의!)에 있다.
- **CTS 관련 실행·수정은 반드시 이 repo 에서** 한다. simsa 폴더에는 CTS 코드가 더 이상 없다.
- `converter.py`/`format_detect.py`/`batch_convert.py`/`vlm_cache.py` 는 두 repo 가 각자 사본을
  유지한다 (의도된 분기, 동기화 없음). 버그 수정 시 필요하면 양쪽에 따로 반영.
- 범용화가 필요한 기능(규칙 편집 UI, 골든셋 등)은 simsa 에 만들고, 이 repo 는 CTS 심사표
  산출(엑셀)과 KOICA 공식 기준 반영에 집중한다.

## 실행

- `python3.14 web_app.py --port 8765` — 웹 UI. CLI 단계 실행은 README 참고.
- 워커 수: `screen_batch.py <dir> 10`, `cross_check.py <json> 4` (둘째 인자).
- 대규모(300기업) 운영·비용은 docs/SCALING.md.

## 규칙

- **하드코딩 금지**: 국가·서류·자격 기준은 data/reference_data.json + criteria/*.jsonl 에 데이터로,
  판정은 프롬프트 주입으로. 특정 지원기업 실명·값을 코드/기준에 넣지 않는다 (과적합 재발 방지).
- 날짜 판정 프롬프트에는 반드시 심사 기준일을 주입한다.
- 프롬프트 버전(screen-vX, xcheck-vX)을 올리면 캐시 전량 무효화 — 배치 시작 전에만 변경.
- 지원기업 개인정보가 담기는 converted/, runs/, cache/, *.xlsx 는 gitignore — 커밋 전 git status 확인.
- 미입력 공백: reference_data.json 의 eligible_countries(KOICA 대상국)와 announcement_date(공고일)는
  공고 확정 시 채워야 자동판정이 완성된다.
