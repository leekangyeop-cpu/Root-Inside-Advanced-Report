# Root Inside — AI-Native Consulting Report Pipeline
### Version 4.0 | Full-Auto Extraction & Report Generation
### Last Updated: 2026-02-12

> **본 README는 AI 에이전트가 읽고 즉시 실행할 수 있는 '완전 자동화 지침서'입니다.**  
> 새 프로젝트에서 이 README + 리서치 원본 파일만 제공하면, AI가 파일 트리 생성 → 원문 추출 → 구조화 → 검증 → 초안 리포트까지 **원스텝으로 완성**합니다.

---

## 📋 목차
1. [시스템 개요](#1-시스템-개요)
2. [보안 및 격리 규칙](#2-보안-및-격리-규칙)
3. [프로젝트 디렉토리 구조](#3-프로젝트-디렉토리-구조)
4. [에이전트 절대 준수 규칙](#4-에이전트-절대-준수-규칙)
5. [PHASE 1: 프로젝트 초기화](#5-phase-1-프로젝트-초기화)
6. [PHASE 2: 원문 추출 및 Markdown 변환](#6-phase-2-원문-추출-및-markdown-변환)
7. [PHASE 3: 구조화 데이터 생성 (Semi-DB)](#7-phase-3-구조화-데이터-생성-semi-db)
8. [PHASE 4: 무결성 검증 (QA)](#8-phase-4-무결성-검증-qa)
9. [PHASE 5: 초안 리포트 자동 생성](#9-phase-5-초안-리포트-자동-생성)
10. [PHASE 6: 최종 윤문](#10-phase-6-최종-윤문)
11. [원스텝 실행 프롬프트](#11-원스텝-실행-프롬프트)
12. [데이터베이스 스키마 상세](#12-데이터베이스-스키마-상세)
13. [참조 사례: cozymom_2025](#13-참조-사례-cozymom_2025)

---

## 1. 시스템 개요

이 저장소는 **루트인사이드(Root Inside)** 경영 컨설팅 리포트의 전체 1단계 공정을 담당하는 **AI 네이티브 파이프라인**입니다.

| 구성 요소 | 역할 | 도구 |
|-----------|------|------|
| **VS Code Agent** | 데이터 파서 (Data Parser) — 원문에서 구조화 데이터 추출 | VS Code + AI Agent |
| **Python Scripts** | 자동 검증(QA) + 초안 렌더링 | `ops/run_validate.py`, `ops/run_report.py` |
| **Claude Desktop** | 수석 컨설턴트 — 최종 리포트 윤문 및 포맷팅 | Claude Desktop Project |

**핵심 원칙:** 환각(Hallucination) 제로 — 원문에 없는 내용은 절대 생성하지 않는다.

---

## 2. 보안 및 격리 규칙

1. **Private Repository 강제:** 클라이언트의 민감한 재무/전략 정보가 포함되므로 반드시 **Private(비공개)** 상태를 유지한다.
2. **저장소 분리 원칙:** 본 저장소는 Template으로만 유지하고, 실제 프로젝트는 클라이언트별로 Private Repository를 Clone하여 작업한다.
3. **원본 파일 보호:** `00_sources/` 내 원본 파일은 절대 수정·삭제하지 않는다. 추출 결과만 별도 파일로 생성한다.

---

## 3. 프로젝트 디렉토리 구조

AI 에이전트는 새 프로젝트 시작 시 반드시 아래 구조를 **그대로** 생성해야 한다.

```
Root-Inside-Advanced-Report/
│
├── README.md                          ← 본 파일 (AI 지침서 겸 스키마 정의)
├── LICENSE
│
├── ops/                               ← 자동화 스크립트
│   ├── run_validate.py                ← QA 검증 스크립트
│   └── run_report.py                  ← 초안 리포트 렌더링 스크립트
│
└── reports/
    └── {client_name}_{year}/          ← 클라이언트별 프로젝트 폴더
        │
        ├── 00_sources/                ← [INPUT] 원본 리서치 파일 + 추출된 텍스트
        │   ├── (원본파일).docx/.pdf/.xlsx  ← 인간이 투입하는 원본 파일
        │   ├── {파일명}_extracted.txt      ← AI가 원본에서 추출한 Markdown 텍스트
        │   └── ...
        │
        ├── 01_structured/             ← [SEMI-DB] 구조화된 JSON/CSV 데이터
        │   ├── sources.json           ← 출처 목록 (메타데이터)
        │   ├── references.json        ← 개별 참조/인용 목록 (URL 포함)
        │   ├── claims.json            ← 핵심 주장 목록
        │   ├── evidences.json         ← 근거/인용문 목록 (출처 추적 포함)
        │   ├── metrics.json           ← 정량 지표 목록
        │   └── claim_evidence.csv     ← 주장↔근거 매핑 테이블
        │
        ├── 02_checks/                 ← [QA] 무결성 검증 결과
        │   └── validation_report.json
        │
        └── 03_report/                 ← [OUTPUT] 최종 산출물
            └── report_draft.md        ← AI가 생성한 초안 리포트
```

**명명 규칙:**
- 클라이언트 폴더: `{영문소문자회사명}_{서비스연도}` (예: `cozymom_2025`, `samsung_2026`)
- 추출 텍스트: `{원본파일의_식별가능한_이름}_extracted.txt`

---

## 4. 에이전트 절대 준수 규칙

> **AI 에이전트는 작업 시작 전, 자신의 역할을 '감정 없고 정확한 데이터 파서(Data Parser)'로 엄격히 제한하라.**

### 4.1 창작 및 추론 절대 금지
- `00_sources/` 원문에 **없는** 내용, 숫자, 의미를 절대 지어내지 마라.
- 원문의 수치를 임의로 반올림하거나 단위를 변환하지 마라.
- "~로 추정된다", "~일 가능성이 있다" 등의 추론적 표현을 에이전트가 자체 생성하지 마라.

### 4.2 선택적 추출 (No Noise)
- 원문 전체를 복사하는 것이 아니다. 원문 전체 보관은 `00_sources/`에서 충족한다.
- **'최종 리포트 결론에 사용될 핵심 데이터와 근거'**만을 `01_structured/`에 구조화하여 추출하라.
- 추출 기준: ① 주요 재무지표 ② 시장 규모/성장률 ③ 핵심 주장의 근거 ④ 리스크 요인 ⑤ 전략적 시사점

### 4.3 스키마 강제 (Schema Strictness)
- 섹션 12에 정의된 JSON/CSV 스키마를 **단 하나의 필드라도** 임의로 변경하거나 누락해서는 안 된다.
- 스키마에 정의되지 않은 필드를 임의로 추가하지 마라 (단, `description` 등 보조 필드는 예외).

### 4.4 출처 추적 의무화 (Citation-Enforced)
- 모든 Evidence(근거)는 반드시 `ref_ids`를 통해 개별 참조(Reference)에 연결되어야 한다.
- 모든 Evidence는 `citation` 필드에 사람이 읽을 수 있는 인용 표기를 포함해야 한다.
- 출처 없는 근거는 **에러**로 처리된다.

---

## 5. PHASE 1: 프로젝트 초기화

### AI에게 주는 지시:
```
이 README를 읽고, reports/ 아래에 "{client_name}_{year}" 폴더와 하위 디렉토리 구조
(00_sources, 01_structured, 02_checks, 03_report)를 생성해줘.
```

### 에이전트 행동:
1. `reports/{client_name}_{year}/` 폴더 생성
2. 하위 4개 디렉토리 생성: `00_sources/`, `01_structured/`, `02_checks/`, `03_report/`
3. `01_structured/` 내 빈 JSON/CSV 파일 스캐폴딩 (빈 배열 `[]` 또는 빈 CSV 헤더)

### 스캐폴딩 초기 파일 내용:
```json
// sources.json, claims.json, evidences.json, metrics.json, references.json
[]
```
```csv
// claim_evidence.csv
claim_id,evidence_id
```

---

## 6. PHASE 2: 원문 추출 및 Markdown 변환

### 전제 조건:
인간이 `00_sources/`에 원본 리서치 파일(`.docx`, `.pdf`, `.xlsx`, `.txt` 등)을 투입한 상태.

### AI에게 주는 지시:
```
00_sources/ 안의 모든 원본 파일을 읽고, 각 파일의 전체 내용을 Markdown 형식의 
텍스트 파일로 변환하여 같은 폴더에 "{파일명}_extracted.txt"로 저장해줘. 
원본의 모든 수치, 표, 출처 번호를 빠짐없이 보존해야 해.
```

### 에이전트 행동:
1. `00_sources/` 내 모든 원본 파일을 순회하며 읽기
2. 각 파일의 내용을 Markdown 형식으로 정리:
   - 제목/소제목 → `#`, `##`, `###` 헤딩
   - 숫자 데이터가 포함된 표 → Markdown 테이블 (`| col1 | col2 |`)
   - 원문의 출처 번호(각주) → 그대로 보존 (예: `[1]`, `[REF-01]`)
   - 강조 텍스트 → `**볼드**`, `*이탤릭*`
3. `{파일명}_extracted.txt` 파일로 저장
4. 추출 완료 후 파일 목록과 각 파일의 분량(줄 수)을 보고

### 변환 규칙:
| 원본 요소 | Markdown 변환 |
|-----------|---------------|
| 문서 제목 | `# 제목` |
| 섹션 제목 | `## 섹션명`, `### 하위섹션명` |
| 데이터 표 | Markdown 테이블 (파이프 구분) |
| 각주/출처 번호 | 원문 그대로 보존 (예: `[1]`, `출처3`) |
| 수치 데이터 | 원문 그대로 (반올림/단위변환 금지) |
| 볼드/강조 | `**텍스트**` |
| 목록 | `- 항목` 또는 `1. 항목` |

---

## 7. PHASE 3: 구조화 데이터 생성 (Semi-DB)

### AI에게 주는 지시:
```
00_sources/의 _extracted.txt 파일들을 모두 읽고, README 섹션 12의 스키마에 맞춰 
01_structured/ 내부의 JSON 파일들과 CSV를 채워줘. 
모든 근거에는 반드시 개별 출처(ref_ids)를 연결하고, citation 필드를 작성해야 해.
```

### 에이전트 행동 (순서 엄수):

#### Step 1: `sources.json` 생성
- 각 원본 파일(또는 분석 보고서)을 하나의 Source로 등록
- ID 형식: `SRC-01`, `SRC-02`, ...
- 해당 Source에서 파생되는 개별 참조(REF)의 ID 목록을 `ref_ids`에 기록

#### Step 2: `references.json` 생성
- Source 내에서 인용된 **개별 출처**(논문, 보도자료, 정부통계 등)를 각각 Reference로 등록
- ID 형식: `REF-01`, `REF-02`, ... 또는 `REF-FIN`(재무제표 자체인 경우)
- 반드시 `publisher`, `url` 포함 (없으면 "N/A"로 표기하되 경고 발생)

#### Step 3: `claims.json` 생성
- 리포트의 핵심 결론이 될 주장을 추출
- `importance`: 재무 건전성·사업 존속에 직결되면 `high`, 보조적이면 `med`, 참고치면 `low`
- `requires_rebuttal`: 반대 논거나 리스크가 예상되면 `true`

#### Step 4: `evidences.json` 생성
- 각 주장을 뒷받침하는 원문 인용을 추출
- **반드시** `ref_ids` 배열에 해당 인용의 개별 참조 ID를 연결
- `citation` 필드에 사람이 읽을 수 있는 인용 표기 작성
  - 형식: `"{발행기관}, '{제목}' ({날짜})"` 또는 `"{보고서명} §{섹션} ({작성자}, {날짜})"`

#### Step 5: `metrics.json` 생성
- 정량적 수치 데이터를 추출
- `value`: 원문의 수치를 **그대로** 기록 (반올림 금지)
- `unit`, `period` 반드시 기입

#### Step 6: `claim_evidence.csv` 생성
- 주장(Claim)과 근거(Evidence)의 매핑 테이블 작성
- 하나의 Claim에 여러 Evidence가 연결될 수 있음 (1:N)
- 하나의 Evidence가 여러 Claim에 재사용될 수 있음 (N:M)

---

## 8. PHASE 4: 무결성 검증 (QA)

### AI에게 주는 지시:
```
터미널에서 python ops/run_validate.py reports/{client_name}_{year} 를 실행해줘.
에러가 있으면 01_structured/ 파일들을 수정하고 다시 검증해줘.
```

### 검증 규칙 (10대 QA 하드 룰):

| # | 규칙명 | 유형 | 설명 |
|---|--------|------|------|
| 1 | Evidence 부족 | ERROR | `importance: high` Claim에 매핑된 Evidence가 2개 미만 |
| 2 | 고아 데이터 | ERROR | `source_id`가 `sources.json`에 없는 Evidence나 Metric 존재 |
| 3 | 단위/기간 누락 | ERROR | Metric의 `unit` 또는 `period` 값이 비어있음 |
| 4 | 수치 모순 | ERROR | 동일 Metric 이름이 서로 다른 Value로 중복 존재 |
| 5 | 기간 혼재 | WARNING | 서로 다른 Period가 명시 없이 혼재 |
| 6 | 최신성 경고 | WARNING | `sources.json`의 Date가 1년 이상 경과 |
| 7 | 가정 누락 | WARNING | 미래 추정 Metric에 관련 가정이 없음 |
| 8 | 리스크 부재 | WARNING | `requires_rebuttal` 미정의 Claim 존재 |
| 9 | 출처 미연결 | ERROR | Evidence에 `ref_ids`가 없거나 빈 배열 |
| 10 | 참조 미존재 | ERROR | Evidence의 `ref_ids`에 있는 ID가 `references.json`에 없음 |

### 검증 결과 형식 (`02_checks/validation_report.json`):
```json
{
  "validation_summary": {
    "total_errors": 0,
    "total_warnings": 0,
    "evidences_checked": 22,
    "references_checked": 32,
    "citation_coverage": "22/22 (100%)"
  },
  "errors": [],
  "warnings": []
}
```

**에러가 0이 될 때까지 PHASE 3 ↔ PHASE 4를 반복한다.**

---

## 9. PHASE 5: 초안 리포트 자동 생성

### AI에게 주는 지시:
```
터미널에서 python ops/run_report.py reports/{client_name}_{year} 를 실행해줘.
```

### 리포트 구조 (자동 생성):
`run_report.py`는 `01_structured/` 데이터를 읽어 아래 구조의 Markdown 리포트를 자동 생성한다.

```markdown
# Root Inside 초안 리포트

## 1. 핵심 주장 및 근거
   - 각 Claim별 근거 테이블 (인용문 + 신뢰도 + 각주)

## 2. 핵심 지표
   - Metrics 전체 테이블 (ID, 지표명, 수치, 단위, 기간)

## 3. 전체 근거 목록 (출처 추적 상세)
   - 모든 Evidence의 개별 출처 citation 표시

## 4. 각주 (References)
   - 사용된 참조의 각주 번호 → 발행기관, 제목, URL

## 5. 데이터 출처 요약
   - Source별 참조 수 + 기관별 분류
```

### 출처 추적 체계 (V2.0):
```
Claim → Evidence → ref_ids → Reference (publisher, title, URL)
  ↑                    ↑
  └── claim_evidence.csv ──┘
```
모든 주장은 근거를 통해 원본 URL까지 역추적 가능해야 한다.

---

## 10. PHASE 6: 최종 윤문

### Claude Desktop에게 주는 지시:
```
03_report/report_draft.md를 기반으로, KPMG/McKinsey 스타일의 전문 경영 컨설팅 
리포트로 재구성해 주세요. 데이터와 수치는 절대 변경하지 말고, 서술 흐름과 
가독성만 개선해 주세요. 각주와 출처 추적 체계는 반드시 유지하세요.
```

---

## 11. 원스텝 실행 프롬프트

> **아래 프롬프트 하나로 PHASE 1~5를 한 번에 실행할 수 있다.**
> 인간은 `00_sources/`에 원본 파일만 넣고 아래 프롬프트를 AI에게 전달하면 된다.

### 🚀 마스터 프롬프트 (복사하여 사용)

```
당신은 Root Inside AI Data Parser입니다. 
이 프로젝트의 README.md를 먼저 읽고, 아래 작업을 순서대로 수행하세요.

[사전 정보]
- 클라이언트명: {client_name}
- 서비스 연도: {year}
- 원본 파일 위치: reports/{client_name}_{year}/00_sources/

[실행 순서]
1. README의 섹션 3에 따라 프로젝트 폴더 구조가 없으면 생성하세요.
2. 00_sources/ 안의 모든 원본 파일을 읽고, 각각을 Markdown 형식으로 
   변환하여 "{파일명}_extracted.txt"로 같은 폴더에 저장하세요.
   (README 섹션 6의 변환 규칙을 준수)
3. 변환된 _extracted.txt 파일들을 읽고, README 섹션 12의 스키마에 맞춰 
   01_structured/ 내부의 JSON/CSV 파일들을 생성하세요.
   (README 섹션 7의 Step 1~6 순서를 엄수)
4. 터미널에서 python ops/run_validate.py reports/{client_name}_{year} 를 
   실행하여 검증하세요. 에러가 있으면 수정 후 재검증하세요.
5. 에러가 0이 되면, python ops/run_report.py reports/{client_name}_{year} 를 
   실행하여 초안 리포트를 생성하세요.
6. 완료되면 최종 결과 요약을 보고하세요:
   - 추출된 Sources 수
   - 생성된 References 수
   - 생성된 Claims/Evidences/Metrics 수
   - 검증 결과 (에러/경고 수)
   - 초안 리포트 경로

[절대 준수]
- README 섹션 4의 에이전트 규칙을 반드시 따르세요.
- 원문에 없는 내용을 절대 창작하지 마세요.
- 모든 근거에 개별 출처(ref_ids + citation)를 반드시 연결하세요.
```

---

## 12. 데이터베이스 스키마 상세

### 12.1 `sources.json` — 출처 문서 목록
```json
[
  {
    "id": "SRC-01",                          // 필수 | 형식: SRC-{순번}
    "title": "문서 제목",                      // 필수 | 원본 문서의 정식 제목
    "type": "재무분석보고서",                    // 필수 | 문서 유형 (재무분석보고서, 산업분석보고서, 인터뷰록 등)
    "date": "2026-02-10",                     // 필수 | ISO 8601 형식
    "path_or_url": "reports/.../파일명.docx",   // 필수 | 원본 파일 경로 또는 URL
    "ref_ids": ["REF-01", "REF-02"],          // 필수 | 이 문서에서 파생된 개별 참조 ID 목록
    "ref_count": 2,                           // 필수 | ref_ids의 개수
    "description": "문서에 대한 간략 설명"       // 선택 | 보조 설명
  }
]
```

### 12.2 `references.json` — 개별 참조/인용 목록
```json
[
  {
    "ref_id": "REF-01",                       // 필수 | 형식: REF-{순번} 또는 REF-{식별자}
    "source_id": "SRC-01",                    // 필수 | 이 참조가 속한 Source ID
    "title": "참조 문헌/기사 제목",              // 필수
    "publisher": "발행 기관명",                  // 필수 | 예: 한국건강기능식품협회, KDI 등
    "type": "정부통계",                         // 필수 | 정부통계, 협회보고서, 언론보도, 리서치리포트, 영상자료, 법률분석 등
    "date": "2026-02-11",                     // 필수 | ISO 8601
    "url": "https://..."                      // 필수 | 원본 출처 URL (없으면 "N/A")
  }
]
```

### 12.3 `claims.json` — 핵심 주장 목록
```json
[
  {
    "id": "CLM-01",                           // 필수 | 형식: CLM-{순번}
    "text": "핵심 주장 전문",                    // 필수 | 하나의 완결된 문장
    "importance": "high",                     // 필수 | high / med / low
    "requires_rebuttal": true                 // 필수 | true: 반박/리스크 존재, false: 팩트 기반
  }
]
```

**importance 분류 기준:**
| 등급 | 기준 |
|------|------|
| `high` | 재무 건전성, 사업 존속, 핵심 전략에 직결 |
| `med` | 시장 동향, 부수적 기회/위협 |
| `low` | 참고 정보, 배경 지식 |

### 12.4 `evidences.json` — 근거/인용문 목록
```json
[
  {
    "id": "EV-01",                            // 필수 | 형식: EV-{순번}
    "source_id": "SRC-01",                    // 필수 | 이 근거가 속한 Source ID
    "ref_ids": ["REF-FIN"],                   // 필수 | 개별 참조 ID 배열 (1개 이상)
    "quote": "원문에서 직접 인용한 문장",         // 필수 | 원문 그대로 (수정 금지)
    "location": "섹션 3.3 매출액 성장 분석",     // 필수 | 페이지/섹션/장 정보
    "reliability": "high",                    // 필수 | high / med / low
    "citation": "코지맘바이오 제11기 재무제표 분석 보고서 §3.3 (Root Inside AI, 2026.02.10)"
                                              // 필수 | 사람이 읽을 수 있는 인용 표기
  }
]
```

**reliability 분류 기준:**
| 등급 | 기준 |
|------|------|
| `high` | 공식 재무제표, 정부 통계, 감사보고서 등 1차 자료 |
| `med` | 내부 분석 추정, 시뮬레이션, 전문가 의견 |
| `low` | 언론 보도, 비공식 자료, 간접 인용 |

### 12.5 `metrics.json` — 정량 지표 목록
```json
[
  {
    "id": "MET-01",                           // 필수 | 형식: MET-{순번}
    "name": "매출액",                          // 필수 | 지표명
    "value": "24,340,000,000",                // 필수 | 원문 수치 그대로 (문자열)
    "unit": "원",                             // 필수 | 단위 (원, %, 억 원, 건, 일 등)
    "period": "2025(연간)",                    // 필수 | 기간 (형식: {연도}({범위}))
    "source_id": "SRC-01",                    // 필수 | 출처 Source ID
    "location": "섹션 3.3 매출액 성장 분석"     // 필수 | 원문 내 위치
  }
]
```

**period 작성 규칙:**
- 연간: `2025(연간)`
- 분기: `2025_Q1(분기)`
- 비교: `2025 vs 2024`
- 전망: `2030(전망)`
- 특정 시점: `2025(11월 기준)`

### 12.6 `claim_evidence.csv` — 주장↔근거 매핑
```csv
claim_id,evidence_id
CLM-01,EV-01
CLM-01,EV-02
CLM-02,EV-03
CLM-02,EV-04
```
- UTF-8 인코딩 필수
- 헤더 행 필수: `claim_id,evidence_id`
- 1개 Claim : N개 Evidence 관계
- 1개 Evidence가 여러 Claim에 매핑 가능

---

## 13. 참조 사례: cozymom_2025

본 저장소의 `reports/cozymom_2025/`는 실제 작업 완료된 참조 사례입니다.

### 프로젝트 개요:
| 항목 | 내용 |
|------|------|
| 클라이언트 | (주)코지맘바이오 |
| 분석 기간 | 제11기 (2025.01.01 ~ 2025.12.31) |
| 원본 문서 | 재무제표 분석 보고서 + 건강기능식품 산업 분석 보고서 |

### 데이터 규모:
| 파일 | 건수 |
|------|------|
| Sources | 2개 (재무분석 + 산업분석) |
| References | 32개 (1개 재무 + 31개 산업) |
| Claims | 12개 (8 high, 4 med) |
| Evidences | 22개 (전체 출처 추적 100%) |
| Metrics | 33개 |

### 작업 결과:
- 검증 에러: **0건** ✅
- 출처 추적 커버리지: **22/22 (100%)** ✅
- 초안 리포트: `03_report/report_draft.md` (295줄)

**새 프로젝트에서 이 사례의 JSON 파일 구조와 필드 값을 참조 기준으로 삼아라.**

---

*Root Inside AI Data Parser | Pipeline V4.0 | Citation-Enforced, Full-Auto Report Generation*  
*Last Updated: 2026-02-12*
