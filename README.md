# 한국어 고객 목소리(CX) 텍스트 — 교육용 계산 결과

AI 데이터 인텔리전스 과정 「비정형 데이터(자연어)」 실습이 읽는 **미리 계산한 결과 파일**입니다. 원문 데이터는 이 저장소에 두지 않고, 실습 노트북이 원 출처에서 직접 받아 가공합니다. 이 저장소의 파일은 그 가공본의 **행 번호에 붙는 계산 열**(명사 목록·토픽 번호·LLM 라벨)입니다.

## 파일

| 파일 | 내용 | 행 | 크기 |
|---|---|---|---|
| `data/complaints_nouns.parquet` | 민원 사건제목의 명사 목록(Kiwi 0.23.1, 가공본 행 번호 기준) | 499,844 | 6.23 MB |
| `data/llm_review_labels.parquet` | 리뷰 표본 2만 건의 LLM 라벨(전체 극성 2만 건, 속성×극성 1,000건 — Qwen3-4B-Instruct-2507) | 20,000 | 332 KB |
| `data/llm_review_predecessor.parquet` | 실습 함정용 「전임자」 LLM 출력 2,000건(스키마 강제 없음) | 2,000 | 132 KB |
| `data/llm_topic_names.parquet` | 민원 토픽 40개의 LLM 이름·설명 | 40 | 28 KB |
| `data/topics_complaints.parquet` | 민원 표본 2만 건의 토픽 배정(KURE-v1 임베딩 → UMAP → HDBSCAN, 시드 0·1·2와 최소 군집 크기별) | 20,000 | 599 KB |
| `data/topics_reviews.parquet` | 리뷰 표본 2만 건의 토픽 배정 | 20,000 | 387 KB |
| `data/llm_model_compare.parquet` | 오픈 LLM 세 개(Qwen3.5-2B·Qwen3-1.7B·Qwen3-4B-Instruct-2507)를 리뷰 100건에 돌려 본 비교표(스키마 통과율·별점과의 일치·건당 시간) | 3 | 13 KB |
| `data/course_codebook_v0.json` | 민원 토픽 상위 8개의 테마 코드북 v0(이름·정의·포함·제외·예, 무작위 10건 검산 결과) | 8행 + 기타 | 13 KB |

## 열

- **complaints_nouns.parquet**: `row_id`(가공본 행 번호), `명사`(명사 목록)
- **llm_review_labels.parquet**: `row_id`, `rank`, `overall`, `p_overall`, `aspects_done`, `status`, `tries`, `overall_json`, `aspects_json`, 모델·판 정보
- **llm_review_predecessor.parquet**: `row_id`, `raw`(모델 출력 원문), 모델·판 정보
- **llm_topic_names.parquet**: `topic`, `count`, `words`, `name`, `description`, 프롬프트·모델 정보
- **topics_complaints.parquet**: `row_id`, `rank`(표본 순위), `topic_s0`~`topic_s2`, `prob_s0`, `topic_m*_s*`(최소 군집 크기별), `topic_e5h_*`(e5-small 비교), `x2d`·`y2d`(2차원 좌표), `ver`
- **topics_reviews.parquet**: `row_id`, `rank`, `topic_s0`~`topic_s2`, `prob_s0`, `topic_m*_s0`, `topic_e5h_s0`, `ver`
- **llm_model_compare.parquet**: `model`, `license`, `weights_fp16_gb`, `fits_t4_fp16`, `n`, `parse_ok_first`, `schema_ok_first`, `schema_ok_after_retry`, `overall_json_vs_star`, `overall_choice_vs_star`, `sec_per_review_json`, `sec_per_review_choice`, `device`, `revision` 등
- **course_codebook_v0.json**: `version`, `topics_file`, `judge`, `기준`, `rows`(테마·토픽·상위어·LLM 이름 초안·정의·포함·제외·예·검산), `기타`

파일마다 SHA-256 은 [SHA256SUMS](SHA256SUMS)에 있습니다.

## 로딩

```python
import pandas as pd
DATA_URL = "https://github.com/studio-js/korean-cx-text/raw/main/data"
topics = pd.read_parquet(f"{DATA_URL}/topics_complaints.parquet")
```

`row_id` 는 실습 노트북의 준비 셀이 원자료를 가공한 표의 행 번호입니다(원자료 SHA-256 과 가공 규칙은 노트북에 있습니다).

## 원자료 복제본(`raw/`)

실습 노트북은 원 출처가 느리거나 주소가 바뀔 때를 대비해 이 복제본을 먼저 받고, 받지 못하면 원 출처로 갑니다. 파일은 원 출처에서 받은 그대로이며 SHA-256 이 원 출처 파일과 같습니다.

| 파일 | 원 출처 | 크기 |
|---|---|---|
| `raw/kftc_complaints_1.csv` | 공정거래위원회 소비자 민원학습데이터(data.go.kr 15098314, 파일 1, cp949) | 67.9 MB |
| `raw/naver_shopping.txt` | github.com/bab2min/corpus `sentiment/naver_shopping.txt` | 20.6 MB |
| `raw/kca_std_answers.csv` | 한국소비자원 소비자상담 표준답변(data.go.kr 15144809, cp949) | 1.5 MB |

## 출처·라이선스

> - 공정거래위원회 「소비자 민원학습데이터 소비자상담 접수내역」(공공데이터포털 15098314, 파일 1, 이용허락범위 제한 없음)을 가공했습니다. 원 출처: https://www.data.go.kr/data/15098314/fileData.do
> - 네이버 쇼핑 리뷰(github.com/bab2min/corpus `sentiment/naver_shopping.txt`, README 기준 Public Domain)를 가공했습니다.
> - LLM 라벨·이름은 Qwen3-4B-Instruct-2507(Apache-2.0)로 만든 결과이며, 사람이 검수한 정답이 아닙니다.

상세 고지는 [LICENSE-DATA.md](LICENSE-DATA.md)를 보십시오.
