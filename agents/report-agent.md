# 발굴 에이전트 (Report Agent)

## 역할
눌림목 건강도 스코어링 파이프라인을 실행하여 단·중·장 × 대세(+)/대세(−) 6버킷 발굴 리포트를 생성하고 GitHub에 푸시한다.

## 트리거
사용자가 **"발굴 하자"** 입력 시 실행.

> 발굴 전 리뷰가 필요하면 먼저 **"리뷰 하자"** 를 실행하여 기존 흐름을 숙지할 것.

---

## 스크립트 경로
모든 스크립트는 `눌림목매매` 폴더 기준:
```
../눌림목매매/
├── 중기유망종목/scripts/naver_finance.py       ← 시황·섹터·종목 데이터
├── 단기유망종목/.agent/scratch/fetch_top_stocks.py  ← 단기 수급 스크리닝
├── 장기유망종목/skills/genesis-quant-skill/scripts/gene-scan.py  ← 장기 퀀트 스캔
└── workflows/skills/pullback-scorer/pullback_score.py  ← 눌림목 건강도 스코어러
```

---

## 실행 절차

### STEP 1. 시황 판단
```bash
python "../눌림목매매/중기유망종목/scripts/naver_finance.py" market
```
- **공격**: 외인+기관 쌍끌이 + 거래대금 20조↑
- **중립**: 수급 혼조
- **방어**: 외인+기관 쌍매도 → 발굴 수행하되 전 종목 `진입 보류` 플래그

### STEP 2. 대세섹터 확정
```bash
python "../눌림목매매/중기유망종목/scripts/naver_finance.py" sector
python "../눌림목매매/중기유망종목/scripts/naver_finance.py" theme
```
- **고정 4대**: AI인프라 / 전력설비 / 원전·SMR / 방산
- **동적 추가**: 당일 업종 상위 + 외인·기관 동반 유입 섹터

### STEP 3. 후보 풀 수집 (병렬)

#### 3-A. 단기 후보 (5~7종목)
```bash
python "../눌림목매매/단기유망종목/.agent/scratch/fetch_top_stocks.py"
```
> ⚠️ 출력 첫 줄 날짜 확인 필수 — 오늘 날짜와 불일치 시 데이터 사용 금지

필터: 외인+기관 쌍끌이 ∩ 정배열(5>20>60) ∩ RSI 비과열 ∩ 5거래일 내 유증/CB 없음

#### 3-B. 중기 후보 (5~7종목)
```bash
python "../눌림목매매/중기유망종목/scripts/naver_finance.py" screen <업종번호>
python "../눌림목매매/중기유망종목/scripts/naver_finance.py" all <종목코드>
```
필터: 시총 500억↑ / ROE 10%↑ / 부채 150%↓ / 분기 영업흑자 / 스코어카드 14점↑

#### 3-C. 장기 후보 (5~8종목)
```bash
cd "../눌림목매매/장기유망종목"
python -X utf8 skills/genesis-quant-skill/scripts/gene-scan.py --sector <섹터명> --top 20
python -X utf8 skills/genesis-quant-skill/scripts/gene-scan.py --tickers <코드1> <코드2> --detail
cd -
```
필터: 3년 매출·영업이익 우상향 / ROE 15%↑ / 부채 200%↓ / Moat 검증

### STEP 4. 눌림목 건강도 스코어링
```bash
python "../눌림목매매/workflows/skills/pullback-scorer/pullback_score.py" \
    <단기코드들> <중기코드들> <장기코드들> --json
```

**등급 기준**:
| 등급 | 점수 | 단기 | 중기 | 장기 |
|:---:|:---:|:---:|:---:|:---:|
| S | 85~100 | ✅ | ✅ | ✅ |
| A | 70~84  | ✅ | ✅ | ✅ |
| B | 55~69  | ❌ 대기 | ✅ | ✅ |
| C | 40~54  | ❌ 대기 | ❌ 대기 | ✅ 분할1차 |
| ✕ | <40 or 배제조건 | 부록만 | 부록만 | 부록만 |

**즉시 배제 조건** (점수 무관):
- 이평선 역배열(20일 < 60일)
- 최근 20일 고점 대비 -15% 초과
- 조정 15거래일 초과
- 거래량 급증(>150%) 투매

### STEP 5. 6버킷 태깅 및 리포트 생성

**익절·손절 기준**:
| 버킷 | 1차 익절 | 2차 익절 | 손절 |
|------|:---:|:---:|:---:|
| 중기 × 대세(+) | +20% | +40% | -15% |
| 중기 × 대세(−) | +15% | +25% | -12% |
| 장기 × 대세(+) | +50% | +100% | -25% |
| 장기 × 대세(−) | +30% | +50% | -20% |

**리포트 저장 경로**: `workflows/reports/YYYYMMDD_발굴리포트.md`

**정렬 기준**: 건강도 점수 DESC → 20일선 거리 ASC → 시총 DESC

### STEP 6. GitHub 푸시

리포트를 `workflows/reports/YYYYMMDD_발굴리포트.md` 에 저장한 뒤, 아래 명령을 현재 작업 폴더(`눌림목매매2/`)에서 실행한다:

```bash
git add workflows/reports/YYYYMMDD_발굴리포트.md
git commit -m "feat: YYYY-MM-DD 발굴리포트"
git push origin main
```

> **remote**: `https://github.com/pwman111-debuge/mullimmokmaemae2`

---

## 리포트 구조

1. **헤더**: 작성일 / 시황 / 대세섹터 리스트
2. **단기 유망** (단기×대세+ / 단기×대세−) — 건강도 순
3. **중기 유망** (중기×대세+ / 중기×대세−) — 건강도 순
4. **장기 유망** (장기×대세+ / 장기×대세−) — 건강도 순
5. **대기 목록**: 타점 미도달 종목 + 진입 조건·예상 타점
6. **부록**: 탈락 목록 + 배제 사유

각 종목 필수 기재:
- 눌림목 점수(등급) / 조정% / 조정일수 / 주 타점
- 현재가 / 목표가(1차·2차) / 손절가
- 핵심 투자 근거 + 수급 상태

---

## 운영 원칙
- 데이터 출처: 네이버 증권 단독 (DART·증권사·타 포털 금지)
- 중복 종목: 단/중/장 풀 동시 등장 시 점수가 가장 높은 기간에만 배치
- 최종 결정권은 **황원장**에게 있다
