# UX Flow

## 1. Purpose

이 문서는 여행 정보 대시보드의 주요 사용자 흐름을 정의한다.

이 문서의 기준은 아래 두 가지다.

- 상위 정보 구조는 [`docs/ia.md`](/C:/Projects-2026/active/travel-agent/docs/ia.md) 를 따른다
- 제품 방향과 카테고리 모델은 [`docs/sketch.md`](/C:/Projects-2026/active/travel-agent/docs/sketch.md) 를 따른다

## 2. UX Principles

- 첫 진입에서 빈 화면을 보여주지 않는다
- 사용자는 검색보다 `여행 컨텍스트 설정` 부터 시작한다
- 각 단계는 다음 결정을 돕는 한 문장 질문으로 이어져야 한다
- 비교가 필요한 선택지는 카드와 추천 문맥으로 보여준다
- 최신성 경고는 뒤가 아니라 앞단에서 보여준다

## 3. Primary User Journey

### Journey A. First-time trip setup

1. Landing 진입
2. `Start Planning` 선택
3. Trip Setup 진행
4. Dashboard Home 생성
5. Top decision cards 확인
6. 필요한 카테고리 상세로 이동
7. 비교 후 선택 저장
8. Checklist 와 itinerary 반영

### Journey B. Returning trip review

1. Saved Trip 진입
2. 최근 변경 항목 확인
3. Alerts 확인
4. 필요한 카테고리만 재확인
5. 기존 선택 수정 또는 유지

### Journey C. Decision compare

1. Dashboard Home 또는 Category Detail 진입
2. 비교 대상 선택
3. Compare 화면 이동
4. `best for / trade-off / friction` 확인
5. 선택 저장
6. 원래 여정으로 복귀

## 4. First-Time Flow

### Step 1. Landing

목표:

- 서비스가 무엇을 해결하는지 즉시 이해
- 시작 버튼으로 바로 진입

핵심 UI:

- 목적지 기반 hero
- 최근 많이 보는 여행지 또는 샘플
- `Start Planning`

### Step 2. Trip Setup

질문 순서는 아래와 같다.

1. 어디로 가나요
2. 언제 가나요
3. 얼마나 머무르나요
4. 누구와 가나요
5. 무엇이 가장 중요한가요

사용자 입력:

- destination
- date range
- trip length
- companion type
- priority tags

출력:

- `trip_context`
- 기본 추천 가중치

### Step 3. Instant Summary

Trip Setup 이 끝나면 바로 전체 대시보드를 보여주기 전에 핵심 요약을 먼저 보여준다.

표시 항목:

- 지금 확인할 경고 3개
- 바로 결정해야 할 것 3개
- 예산 범위
- 추천 이동수단 또는 패스
- 통신 추천

### Step 4. Dashboard Home

사용자 질문에 대응하는 순서로 블록을 구성한다.

1. 지금 여행 가능한가
2. 가장 먼저 정해야 하는 것은 무엇인가
3. 이동은 어떻게 잡는가
4. 예산은 어느 정도인가
5. 오늘 또는 해당 일차 일정은 어떻게 짜는가

## 5. Category Flows

### 5.1 Trip Snapshot

진입 질문:

- 이 여행은 지금 어떤 상태인가

사용자 액션:

- 핵심 경고 확인
- 예산 감각 확인
- 변동 가능 항목 확인

다음 이동:

- Entry & Rules
- Move
- Alerts

### 5.2 Entry & Rules

진입 질문:

- 입국과 규정 면에서 걸리는 것이 있는가

사용자 액션:

- 입국 요건 확인
- 세관/면세 규정 확인
- 예약 제약 확인

다음 이동:

- Alerts
- Move

### 5.3 Move

진입 질문:

- 어떻게 가장 덜 헤매고 이동할 것인가

사용자 액션:

- 공항 이동 수단 확인
- 도시 내 이동 방식 확인
- 패스 적합성 판단
- compare 진입

다음 이동:

- Compare
- Plan Day

### 5.4 Stay

진입 질문:

- 어느 지역에 머무는 것이 내 조건에 맞는가

사용자 액션:

- 지역 비교
- 숙소 유형 판단
- 체크인 규칙 확인

다음 이동:

- Compare
- Eat & Spend

### 5.5 Eat & Spend

진입 질문:

- 식비와 결제 측면에서 무엇을 준비해야 하는가

사용자 액션:

- 평균 비용 확인
- 카드/현금 사용성 확인
- 예약 난이도 확인

다음 이동:

- Connect
- Plan Day

### 5.6 Connect

진입 질문:

- 어떤 통신 수단이 가장 번거롭지 않은가

사용자 액션:

- eSIM / SIM / Pocket Wi-Fi 비교
- 개통 방식 확인
- 수령 방식 확인

다음 이동:

- Compare
- Saved Trip

### 5.7 Plan Day

진입 질문:

- 하루를 어떻게 현실적으로 짤 것인가

사용자 액션:

- 운영시간 확인
- 예약 필요 여부 확인
- 대표 루트 확인
- 일정 블록 저장

다음 이동:

- Saved Trip
- Alerts

### 5.8 Alerts

진입 질문:

- 출발 전 꼭 다시 확인해야 하는 것은 무엇인가

사용자 액션:

- 최근 변경 항목 확인
- 고변동 정보 확인
- 다시 검증할 항목 확인

다음 이동:

- 원래 보던 카테고리 복귀
- Saved Trip

## 6. Compare Flow

비교 UX는 다음 순서를 유지한다.

1. Compare summary
2. 사용자 조건 표시
3. 각 옵션의 `best for`
4. 핵심 차이점
5. 불편 요소와 리스크
6. 추천 선택
7. 저장

비교 대상 기본 우선순위는 아래와 같다.

1. airport transfer
2. pass products
3. connectivity products
4. stay areas

## 7. Returning User Flow

1. Saved Trip 또는 Dashboard Home 재진입
2. 마지막 방문 이후 변경된 항목 배지 확인
3. Alerts 우선 확인
4. 기존 선택이 여전히 유효한지 확인
5. 필요 시 compare 재실행
6. checklist 업데이트

## 8. Error and Empty-State Flow

### Empty dashboard 방지

- Trip Setup 이전에는 완성형 대시보드를 보여주지 않는다
- 샘플 여행 또는 최근 인기 목적지를 제안한다

### Missing data 대응

- 정보가 없으면 빈 카드 대신 `확인 필요` 상태를 보여준다
- 관련 카테고리 이동 CTA 를 함께 둔다

### Stale data 대응

- 오래된 정보는 숨기지 않고 `재확인 필요` 로 노출한다
- Alerts 에 자동 포함한다

## 9. Alignment Checklist

이 문서는 아래 기준으로 다른 문서와 정렬되어야 한다.

- 상위 카테고리 이름과 순서는 `ia.md`, `sketch.md` 와 동일해야 한다
- Trip Setup 입력값은 `data-contract.md` 의 `trip_context` 필드와 맞아야 한다
- Compare 기본 대상은 `data-contract.md` 의 compare view model 과 연결되어야 한다
