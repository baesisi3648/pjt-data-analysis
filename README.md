# pjt-data-analysis
SESAC LLM DA 프로젝트 1st - 고객분석 & 물류최적화 데이터 분석

# 이번 프로젝트 한계점 & 개선점
- 데이터 전처리를 더 꼼꼼하게 하기(구매횟수 1회 고객이 많으므로, 기준을 따로 설정할 필요가 있음)
- 가설을 미리 설계하고, 전체적인 흐름으로 데이터 분석하기
- 배송비와 리뷰점수의 관계/리뷰점수 별 평균 리뷰길이/세그먼트 그룹 재지정/리뷰 행동 등 세부적인 데이터 분석에 대한 인사이트 고민
- 데이터 전처리와 상관계수를 구하는 모듈을 따로 만들어 데이터 분석 전처리를 빠르게 하기

# 📊 고객분석 & 물류최적화 데이터 분석 프로젝트

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

> 온라인 커머스 데이터를 바탕으로 **RFM 기반 고객 세분화**, **물류 KPI(리드타임·지연율)**, **매출 트렌드**, **Time‑to‑Peak/라이프사이클**, **VIP 세그먼트 인사이트**를 도출한 분석 프로젝트입니다.  
> _Updated: 2025-08-21 (KST)_

---

## 🧭 핵심 요약

- **목표**: 데이터 기반으로 고객 세분화, 물류 효율화, 매출 성장을 위한 **실행 가능한 인사이트** 제공
- **산출물**: 5개 노트북(데이터 정리 → RFM → 물류 → 트렌드 → 사이클), 재현 가능한 파이프라인, 시각화 결과
- **주요 통찰 예**  
  - 상위 세그먼트(**VVIP/VIP/고액고객**) 매출 기여도 파악 및 **관심 카테고리** 도출(옵션)
  - **리드타임 평균·p90** 및 **지연율** 월별 모니터링으로 SLA 관리 포인트 제시
  - **Time‑to‑Peak** 조기형 vs 장기형 카테고리 구분 → **초기 집중/롱테일 전략** 수립
- **바로 시작**: 아래 **설치 & 실행** 가이드를 따라 순서대로 `pr0 → pr4` 실행

> ⚠️ 숫자/지표의 **정확한 값**은 저장소의 노트북 출력

---

## 📚 목차

- [프로젝트 소개](#-프로젝트-소개)
- [프로젝트 구조](#-프로젝트-구조)
- [데이터](#-데이터)
- [설치 & 실행](#-설치--실행)
- [노트북 가이드](#-노트북-가이드)
- [재현 파이프라인](#-재현-파이프라인)
- [결과 해석 가이드](#-결과-해석-가이드)
- [한계 & 향후 과제](#-한계--향후-과제)
- [팀 / 라이선스](#-저자--라이선스)

---

## 🔎 프로젝트 소개

이 프로젝트는 **온라인 커머스 데이터**를 활용해 다음을 수행합니다.

- **고객 세분화**: RFM 점수화 및 세그먼트 정의(VVIP/VIP/고액고객 등)
- **물류 KPI 진단**: 리드타임(평균/분위수)·지연율의 **월별 추이** 및 **이상 구간** 탐지
- **매출 트렌드 분석**: 월별 매출, MoM/YoY 성장률, (옵션) **월 단위 CAGR**
- **라이프사이클 분석**: **Time‑to‑Peak 로리팝**, **매출가중 라이프사이클 스택바**
- (옵션) **VIP 인사이트**: 상위 세그먼트 관심 카테고리 파악 → 마케팅/머천 전략

> README는 협업자와 미래의 나를 위한 **실행 매뉴얼 + 요약 보고서** 역할을 합니다.

---

## 🗂️ 프로젝트 구조

```text
project-root/
├─ README.md
├─ requirments.txt              # 의존성 목록(철자 주의)
├─ notebooks/
│  ├─ pr0_data_handling.ipynb   # 로드/정제/정합성 → *_clean, 번들 저장
│  ├─ pr1_RFM.ipynb             # RFM 점수화/세그먼트 라벨링
│  ├─ pr2_freight.ipynb         # 리드타임/지연율(물류 KPI)
│  ├─ pr3_trend.ipynb           # 월 매출, MoM/YoY, (옵션) 월간 CAGR
│  └─ pr4_cycle.ipynb           # Time‑to‑Peak, 라이프사이클 스택
├─ data/
│  ├─ raw/                      # 원본 데이터(비공개/용량 주의)
│  └─ processed/                # 전처리 산출물
├─ _snapshots/                  # 예: olist_clean_bundle.pkl 등 캐시/번들
├─ src/                         # (선택) 재사용 가능한 함수 모듈
├─ reports/                     # (선택) 그림/표/요약본
└─ assets/                      # (선택) 이미지/아이콘
```

> 노트북이 루트에 있다면 `notebooks/`로 이동 권장(가독성/재현성 향상).

---

## 🗃️ 데이터

- **출처**: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce?select=olist_orders_dataset.csv 브라질 이커머스 고객 행동 및 비즈니스 인사이트 분석
- **주요 필드(예시)**  
  `CustomerID`, `InvoiceNo`, `OrderDate`, `ProductID`, `Category`, `Quantity`, `UnitPrice`, `TotalAmount`,  
  `FreightValue`, `ReviewScore`, `SellerRegion`, `CustomerRegion`  
- **개인정보/윤리**: 식별자 마스킹, 민감정보 제외. 외부공개 시 **데이터 정책** 확인.
- **버전관리**: 가공 산출물은 `/data/processed` 또는 `/_snapshots`에 저장하여 재현성 확보.
---

## 🛠️ 설치 & 실행 방법

### 1) 저장소 클론
```bash
git clone <REPO_URL>
cd <REPO_FOLDER>
```

### 2) 가상환경 생성 & 활성화
```bash
python -m venv venv
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate
```

### 3) 패키지 설치
```bash
pip install -r requirements.txt
```

> **주요 의존성 예시**: pandas, numpy, matplotlib, seaborn, scikit-learn, scipy, jupyterlab (정확한 버전은 파일 참조)

### 4) 주피터 실행
```bash
jupyter lab
# 또는
jupyter notebook
```

### 5) 한글 폰트 설정 — Matplotlib
노트북 상단에 아래 스니펫을 넣으면 Windows/맥 모두 한글 깨짐을 방지할 수 있습니다.
```python
import matplotlib.pyplot as plt
import platform

if platform.system() == "Windows":
    plt.rcParams["font.family"] = "Malgun Gothic"
else:
    plt.rcParams["font.family"] = "AppleGothic"
plt.rcParams["axes.unicode_minus"] = False
```

---

## 📒 노트북 가이드

| 파일 | 핵심 내용 | 주요 산출물 |
|---|---|---|
| `pr0_data_handling.ipynb` | 데이터 로드, 정제, 정합성 점검, 조인/머지, 스키마 표준화 | `*_clean` 데이터프레임, (예) `_snapshots/olist_clean_bundle.pkl` |
| `pr1_RFM.ipynb` | R/F/M 계산, 분위 기반 점수화, 세그먼트 라벨링(VVIP/VIP/고액고객 등) | `rfm` 테이블, 세그먼트 분포·시각화 |
| `pr2_freight.ipynb` | 리드타임(평균·p90 등), 지연율, 월별 KPI 추이/대시보드 | 월별 KPI 표/그래프, SLA 관리 포인트 | 상위 세그먼트 매출비중, 세그먼트별 **Top3 카테고리**, 실행 인사이트 |
| `pr3_trend.ipynb` | 월 매출, MoM/YoY, (옵션) 월 단위 CAGR | 트렌드 라인/바, 성장률 표 |
| `pr4_cycle.ipynb` | **Time‑to‑Peak 로리팝**, **매출가중 라이프사이클 스택바** | 카테고리별 초기형/장기형 구분 그래프 |

> 시각화는 발표용 기준으로 구성(폰트·라벨·범례 최적화).

---

## 🔁 재현 파이프라인

1. **데이터 정리**: `notebooks/pr0_data_handling.ipynb` 실행 → `*_clean` 및 번들 생성  
2. **RFM 세분화**: `pr1_RFM.ipynb` 실행 → `rfm` 산출, 세그먼트 라벨 확정  
3. **물류 KPI**: `pr2_freight.ipynb` 실행 → 리드타임/지연율 추이 산출  
4. **트렌드**: `pr3_trend.ipynb` 실행 → 월 매출 & 성장률  
5. **사이클**: `pr4_cycle.ipynb` 실행 → Time‑to‑Peak & 라이프사이클

> 데이터 경로/환경 변수는 노트북 상단 셀(또는 `/src/config.py`)에서 관리하도록 권장.

---

## 🧩 결과 해석 가이드

- **RFM/세그먼트**: 상위 세그먼트(VVIP/VIP/고액고객)의 **매출 기여**와 **관심 카테고리** 확인 → 고가/반복구매/신속 응대 전략.
- **물류 KPI**: 리드타임 **평균 vs p90**을 함께 보고, **지연율 상승 구간**에 원인(셀러/지역/카테고리)을 트리로 파고들기.
- **Time‑to‑Peak**: **초기형**(초반 피크) 카테고리는 **런칭/이벤트 초반 화력 집중**, **장기형**은 **롱테일 운영·재진입 유도**.
- **트렌드**: MoM/YoY 분해로 **기저효과**와 **계절성**을 분리. 월 단위 CAGR은 **속도감** 체크용 보조지표.

---

## ⚠️ 한계 & 향후 과제

- **데이터 전처리**: 1회 구매 고객 비중이 높음 → **별도 기준 설정** 및 품질 리포트 자동화
- **분석 설계**: **사전 가설 수립** 후 E2E 흐름에 맞춘 분석(가설→실험→검증)
- **세부 분석 확장**: 배송비–리뷰점수, 리뷰점수별 평균 리뷰길이, 세그먼트 재지정/리뷰 행동 등 **심층 분석**
- **모듈화**: 전처리와 상관계수 계산을 **모듈화**하여 반복 분석 시간 단축
- **모델링 고도화**: 이탈 예측, Uplift 모델 → **A/B 테스트**로 인과효과 검증
- **SCM 연계**: 재고회전·안전재고·입고주기와 **리드타임** 동시 최적화

---

## 👥 만든이

- **배성우**: _SEONGU BAE_ (Solo Project)  

---

> 문의/피드백은 email로 남겨주세요. 🚀
