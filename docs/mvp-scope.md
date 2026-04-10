# MVP Scope

## 1. MVP 목표

**에이전트형 여행 결정 대시보드** — 도쿄 인바운드 단일 도시 기준.

카테고리를 나열하는 정보 포털이 아니다. 여행자가 실제로 결정을 내리는 순서에 따라 에이전트가 안내하고, 각 판단에 필요한 정보를 자동으로 합성해서 보여준다.

### 핵심 원칙 3가지

1. **결정 중심**: 사용자는 정보를 탐색하는 게 아니라 결정을 내리러 온다.
2. **데이터 투명성**: 모든 정보에 confidence + freshness 배지를 상시 노출한다.
3. **Gap 명시**: KU가 없으면 빈 카드 대신 "확인 필요" 상태로 명시한다.

---

## 2. 데이터 소스

**domain-k-evolver** 레포의 Knowledge Unit(KU) 아카이브를 기반 데이터로 사용한다.

KU 구조 (`knowledge-unit.json` 스키마 기반):

| 필드 | 설명 |
|---|---|
| `entity_key` | `{domain}:{category}:{slug}` 형식 식별자 |
| `field` | 해당 엔티티의 어떤 속성인지 |
| `value` | 실제 값 (문자열, 객체 등) |
| `confidence` | 0.0~1.0 신뢰도 |
| `observed_at` + `validity.ttl_days` | freshness 계산 기준 |
| `status` | `active` / `disputed` / `deprecated` |

### MVP entity_key 네임스페이스

```
travel:visa:japan-korea
travel:advisory:japan
travel:pass:ic-card
travel:pass:jr-pass
travel:transport:narita-express
travel:transport:limousine-bus
travel:transport:tokyo-metro
travel:payment:ic-card
travel:payment:credit-card
travel:budget:tokyo-daily
```

### Freshness 레벨 계산 규칙

```
observed_at + ttl_days >= today         → fresh   (정상 표시)
observed_at + ttl_days < today < +30일  → watch   (경계 배지)
초과 30일 이상                           → stale  (재확인 CTA + Alerts 포함)
KU 없음                                  → unknown (확인 필요 상태)
```

---

## 3. MVP 범위

### 대상

- **도시**: 도쿄 (Tokyo) 인바운드 단일 도시
- **사용자**: 한국 출발 기준 단기 여행자 (관광 목적)

### 온보딩: 2개 입력만

```
1. 어디로 가나요?   → 도쿄 (MVP 고정)
2. 언제 가나요?     → 출발일 + 귀국일
```

여행 스타일, 동행 유형, 예산 스타일 등 추가 입력은 받지 않는다.
에이전트가 KU를 조회한 후 핵심 판단 3~5개를 자동으로 합성한다.

---

## 4. 3단계 결정 흐름

에이전트는 아래 순서로 사용자를 안내한다. 단계 간 이동은 에이전트가 자동으로 진행하며, 각 단계에서 핵심 판단 카드를 먼저 보여준다.

### Step 1. "갈 수 있나?"

**목적**: 입국 가능 여부를 즉시 판단한다.

표시 항목:
- 비자 요건 (한국 여권 기준)
- 현재 여행경보 등급
- 입국 시 특이사항 (세관, 검역 등)

사용 엔티티: `advisory`, `destination`  
KU 예시: `travel:visa:japan-korea`, `travel:advisory:japan`

판단 결과 형식:
```
✅ 비자 불필요 (90일 무비자)   confidence: 0.95 | fresh
⚠️ 여행경보 1단계 (주의)       confidence: 0.90 | watch
```

### Step 2. "어떻게 가고 돌아다니나?"

**목적**: 공항 이동 수단과 도시 내 이동 방식을 결정한다.

표시 항목:
- 나리타/하네다 → 도쿄 시내 이동 옵션
- 도시 내 교통패스 추천
- 패스 구매 방법 및 주의사항

사용 엔티티: `transport_option`, `pass_product`  
KU 예시: `travel:transport:narita-express`, `travel:pass:ic-card`, `travel:pass:jr-pass`

판단 결과 형식:
```
🚆 나리타 익스프레스 권장   ¥3,070 | confidence: 0.92 | fresh
🪙 IC카드 필수 발급         confidence: 0.95 | fresh
```

### Step 3. "얼마 필요한가?"

**목적**: 현실적인 예산을 잡고 결제 수단을 결정한다.

표시 항목:
- 1일 평균 예산 범위 (숙박 제외)
- 교통비 추정 (패스 포함)
- 결제 수단 사용 가능성 (카드 vs 현금 vs IC카드)

사용 엔티티: `budget_profile`, `payment_method`  
KU 예시: `travel:budget:tokyo-daily`, `travel:payment:ic-card`, `travel:payment:credit-card`

판단 결과 형식:
```
💴 1일 예산 ¥8,000~15,000 (숙박 제외)   confidence: 0.80 | watch
💳 신용카드 사용 가능 (편의점/대형점 중심) confidence: 0.88 | fresh
```

---

## 5. 에이전트 동작 정의

### KU 조회 및 합성

에이전트는 아래 절차로 판단 카드를 생성한다:

1. 입력된 여행 컨텍스트(목적지, 날짜)를 기반으로 대상 `entity_key` 목록을 결정한다
2. KU 아카이브에서 해당 `entity_key`의 활성 KU를 조회한다
3. `confidence`와 freshness를 기준으로 KU를 우선순위 정렬한다
4. 같은 엔티티에 여러 KU가 있으면 최신 + 고신뢰도 KU를 선택한다
5. 핵심 판단 3~5개로 합성해 카드로 노출한다

### Gap Detection

| 상태 | 조건 | 표시 |
|---|---|---|
| `fresh` | TTL 내 active KU 존재 | 정상 카드 |
| `watch` | TTL 임박 KU | 경계 배지 + 재확인 권장 |
| `stale` | TTL 초과 KU | 재확인 CTA + Alerts 포함 |
| `unknown` | 해당 KU 없음 | "확인 필요" 상태 카드 명시 |
| `disputed` | 충돌 KU 존재 | "정보 불일치 — 직접 확인 필요" |

---

## 6. MVP 엔티티 Scope

MVP에서 사용하는 엔티티는 6개로 제한한다.

| 엔티티 | 설명 | 사용 단계 |
|---|---|---|
| `destination` | 도시/국가 기본 정보 | Step 1, 3 |
| `advisory` | 여행경보, 비자, 입국 조건 | Step 1 |
| `transport_option` | 공항 이동, 도시 내 교통 | Step 2 |
| `pass_product` | IC카드, JR Pass 등 패스 | Step 2 |
| `payment_method` | 카드, 현금, IC카드 결제 | Step 3 |
| `budget_profile` | 1일 예산 범위, 카테고리별 평균 | Step 3 |

`data-contract.md`의 공통 메타 필드(`last_verified_at`, `freshness_level`, `confidence`, `sources`)는 6개 엔티티 전부에 적용한다.

---

## 7. MVP 제외 항목

| 항목 | 제외 이유 | 이연 단계 |
|---|---|---|
| 숙소 카테고리 (`stay_option`) | KU 수 부족(5개), 조건 변수 많음 | Phase 3 |
| 맛집 카테고리 (`place`) | KU 수 부족(3개), freshness 리스크 높음 | Phase 3 |
| 통신 카테고리 (`connectivity_product`) | KU 수 적음(5개), 빠른 변동성 | Phase 3 |
| Compare 기능 | UX 복잡도 증가, Step 2 이후 필요 | Phase 3 |
| 자연어 질의 | LLM 파이프라인 별도 필요 | Phase 3 |
| 서울 등 다도시 확장 | 도메인 스켈레톤 재정의 필요 | Phase 4 |
| 여행 스타일 개인화 | Trip Setup 입력 최소화 원칙에 위배 | Phase 5 |

---

## 8. 성공 기준

### 기능 기준

| 기준 | 조건 |
|---|---|
| **30초 판단** | 목적지+날짜 입력 후 30초 내에 핵심 판단 3개가 화면에 표시되어야 한다 |
| **Freshness 상시 노출** | 모든 정보 카드에 confidence 수치와 freshness 레벨 배지가 함께 표시되어야 한다 |
| **출처 명시** | 모든 카드에 KU의 `sources` 또는 `observed_at` 정보가 노출되어야 한다 |
| **Gap 명시** | KU가 없는 항목은 "확인 필요" 상태로 표시되어야 한다 (빈 카드 금지) |

### 데이터 기준

| 기준 | 조건 |
|---|---|
| KU 커버 | Step 1~3의 핵심 판단 항목이 모두 KU로 커버되어야 한다 |
| Fixture 통과 | MVP 엔티티 6개의 mock fixture가 `schemas/` 스키마 검증을 통과해야 한다 |

---

## 9. Phase 계획과의 연결

```
Phase 0 (현재)  — Foundation Spec 완료 ✅
Phase 1         — App Skeleton: 라우팅, 레이아웃, mock 데이터 기반 3단계 흐름 렌더링
Phase 2         — Data Contracts: 6개 엔티티 스키마 + KU fixture 연동
Phase 3         — MVP 제외 항목 순차 추가 (Stay, Eat, Connect, Compare, 자연어)
Phase 4         — 다도시 확장 (서울 등)
Phase 5         — 개인화 (traveler profile, saved trip)
```

---

## 10. 다음 작업

Phase 1 시작 전 완료해야 할 항목:

- [ ] `src/app` 기본 라우팅 구성 (Landing → Trip Setup → Dashboard)
- [ ] 3단계 결정 흐름 페이지 스캐폴딩
- [ ] KU fixture mock 데이터 작성 (6개 엔티티, 도쿄 기준)
- [ ] freshness 계산 유틸리티 구현
- [ ] 판단 카드 컴포넌트 (confidence 배지 + freshness 배지 포함)
