# Travel Dashboard Sketch

## 1. Goal

프로젝트명 `travel-agent` 에 맞는 1차 목표는 "여행 전후 의사결정에 필요한 정보를 한 화면에서 빠르게 판단할 수 있는 대시보드"를 만드는 것이다.

참고 코드베이스 `domain-k-evolver` 에서 가져올 핵심은 다음 두 가지다.

- 구조 분리: `docs / src / schemas / tests / scripts / dev`
- 도메인 엄격성: 카테고리, 필드, 상태, 검증 기준을 먼저 정하고 기능을 그 위에 쌓기

하지만 이 프로젝트의 중심은 `지식 진화 엔진`이 아니라 `사용자 의사결정 UX` 다. 따라서 구조는 참고하되, 구현 축은 아래 순서로 잡는다.

1. 사용자 질문 흐름 정의
2. 카테고리와 데이터 모델 정의
3. 화면 정보 구조 정의
4. 수집/정규화/표시 파이프라인 구현

## 2. Product Definition

### Primary user

- 출발 전 여행 준비를 하는 개인 사용자
- 도시/국가별 핵심 정보를 빠르게 비교하려는 사용자
- 교통, 숙소, 예산, 통신, 결제, 일정 리스크를 한 번에 보고 싶은 사용자

### Primary jobs-to-be-done

- 지금 여행 가능한지 빠르게 판단한다
- 예산과 이동 동선을 현실적으로 잡는다
- 교통패스, 통신, 결제 수단 중 무엇을 선택할지 결정한다
- 운영시간, 예약 필요 여부, 지역별 주의사항을 확인한다
- 오늘 기준으로 바뀌기 쉬운 정보를 놓치지 않는다

## 3. Reference Structure Mapping

`domain-k-evolver` 구조를 여행 대시보드에 맞게 다음처럼 매핑한다.

### Recommended repo structure

- `docs/`
  - 제품 스케치, IA, UX 플로우, 데이터 정책, 운영 문서
- `src/app/`
  - 라우팅, 페이지, 레이아웃
- `src/features/`
  - 대시보드, 검색, 필터, itinerary, alerts, compare
- `src/entities/`
  - destination, pass, transport, place, regulation, budget
- `src/shared/`
  - UI 컴포넌트, 유틸, 포맷터, 상수
- `src/data/`
  - mock 데이터, seed 데이터, 정규화 결과
- `schemas/`
  - destination, card, pass, place, advisory, dashboard-view schema
- `tests/`
  - schema, transform, selector, UX-critical rendering tests
- `scripts/`
  - seed 생성, 데이터 정규화, fixture 변환
- `dev/active/`
  - phase별 작업 계획과 결정 기록

### Mapping rationale

- 참고 프로젝트의 `state / schema / metrics` 개념은 여기서 `view model / content contract / freshness & coverage signals` 로 치환한다.
- 참고 프로젝트의 `bench` 는 여기서 `seed destinations` 와 `mock dashboard scenarios` 로 치환한다.
- 참고 프로젝트의 `deliverable contract` 개념은 각 단계의 산출물 정의에 그대로 사용한다.

## 4. Information Architecture

대시보드의 최상위 카테고리는 "데이터 공급자 기준"이 아니라 "여행자가 판단하는 순서" 기준이어야 한다.

### Top-level categories

1. `Trip Snapshot`
   - 여행 가능 상태, 날씨/시기, 핵심 경고, 예산 감각, 오늘 바뀔 수 있는 정보
2. `Entry & Rules`
   - 입국, 비자, 세관, 현지 규정, 예약/입장 제약
3. `Move`
   - 공항 이동, 도시 내 교통, 지역 간 이동, 패스/티켓
4. `Stay`
   - 숙소 유형, 체크인 규칙, 지역 선택, 숙박 팁
5. `Eat & Spend`
   - 식당 이용 팁, 결제 수단, 팁 문화, 평균 비용
6. `Connect`
   - eSIM, SIM, 포켓 와이파이, 충전/전원
7. `Plan Day`
   - 운영시간, 예약 필요 여부, 대표 일정 블록, 추천 동선
8. `Alerts`
   - 마감 임박, 변동 가능성, 확인 필요 항목

### Why this category model

- `교통/숙소/맛집` 같은 공급자 분류만으로는 준비 흐름이 끊긴다.
- 사용자는 먼저 "갈 수 있나", 다음 "어떻게 움직이나", 다음 "얼마 드나", 마지막 "오늘 무엇을 하나" 순서로 본다.
- 따라서 상단 네비게이션은 행위 중심이어야 하고, 세부 엔티티는 내부 데이터 모델에서 분리 관리한다.

## 5. Domain Entities

참고 코드베이스의 `categories / fields / relations / axes` 개념을 간소화해 아래 엔티티로 정의한다.

### Core entities

- `destination`
  - 국가, 도시, 지역, 공항, 구역
- `advisory`
  - 입국 조건, 경보, 시즌 이슈, 운영 변경
- `transport_option`
  - 공항 이동, 지하철, 버스, 철도, 택시, 렌터카
- `pass_product`
  - 교통패스, 관광패스, 번들 티켓
- `stay_option`
  - 숙소 유형, 지역별 숙박 특징, 체크인 규칙
- `place`
  - 관광지, 식당, 쇼핑 장소, 시설
- `connectivity_product`
  - eSIM, SIM, 와이파이
- `payment_method`
  - 현금, 신용카드, 교통카드, 간편결제
- `budget_profile`
  - 1일 예산, 카테고리별 평균 비용
- `trip_plan`
  - 일정 블록, 동선, 저장된 선택

### Shared fields

- `title`
- `category`
- `geo_scope`
- `price`
- `hours`
- `booking_required`
- `eligibility`
- `how_to_use`
- `best_for`
- `risks`
- `last_verified_at`
- `confidence`

### Key relations

- `pass_product -> valid_for -> transport_option`
- `place -> located_in -> destination`
- `payment_method -> accepted_at -> place | transport_option`
- `advisory -> applies_to -> destination | category`
- `trip_plan -> includes -> place | transport_option | pass_product`

## 6. UX Flow

### Core UX principle

첫 화면에서 사용자가 "무엇을 클릭해야 할지" 고민하게 만들면 안 된다. 시작점은 검색창보다 `여행 컨텍스트 선택` 이다.

### Primary flow

1. 사용자 진입
2. 여행 컨텍스트 선택
   - 국가/도시
   - 여행 기간
   - 여행 스타일
   - 동행 유형
3. 대시보드 생성
4. 상단 Snapshot 확인
5. 핵심 결정 카드 확인
   - 입국/규정
   - 공항 이동
   - 교통패스
   - 통신
   - 결제
6. 세부 탭 탐색
7. 비교 또는 저장
8. 개인 itinerary / checklist 반영

### First-session UX

첫 방문에서는 빈 대시보드를 보여주지 않는다. 아래 두 단계가 필요하다.

1. `Quick Setup`
   - "어디로 가나요?"
   - "언제 가나요?"
   - "무엇이 가장 중요하나요?"  
     예: 예산, 이동 편의, 가족 여행, 먹거리, 사진 스팟
2. `Instant Summary`
   - 핵심 경고 3개
   - 지금 결정해야 할 것 3개
   - 예상 비용 범위
   - 가장 많이 쓰는 이동수단/패스 제안

### Returning-user UX

- 저장된 여행을 바로 열기
- 마지막 확인 이후 바뀐 항목만 상단 고정
- 오늘 해야 할 액션만 별도 묶음

## 7. Screen Strategy

### A. Dashboard Home

한 화면에서 아래 블록이 보여야 한다.

- Hero summary
- alerts rail
- decision cards
- budget snapshot
- mobility snapshot
- today plan
- saved notes / checklist

### B. Category Detail Pages

각 카테고리 페이지는 동일 패턴을 유지한다.

1. summary
2. compare cards
3. practical tips
4. latest changes
5. related items

### C. Compare Flow

가장 중요한 비교 축은 아래다.

- 공항 이동 수단 비교
- 교통패스 비교
- 통신 상품 비교
- 숙소 지역 비교

비교 UX는 표만 두지 말고, `누구에게 적합한지` 를 첫 줄에 보여줘야 한다.

## 8. Category Design Details

카테고리는 많아 보이지만 실제 UX에서는 아래처럼 묶어야 한다.

### 8.1 Snapshot

- 현재 여행 적합성
- 시즌/날씨
- 예산 감각
- 혼잡/예약 리스크
- 변동 가능성 높은 항목

### 8.2 Entry & Rules

- 입국 요건
- 세관/면세
- 현지 금지/주의사항
- 예약/신분 확인 필요 항목

### 8.3 Move

- 공항에서 시내 이동
- 도시 내 이동수단
- 도시 간 이동
- 패스/티켓 추천
- 초행자용 이용 방법

### 8.4 Stay

- 지역별 숙소 선택
- 체크인/체크아웃 팁
- 가족/커플/1인 기준 추천 지역
- 숙박 규칙과 에티켓

### 8.5 Eat & Spend

- 평균 식비 구간
- 현금/카드 사용성
- 팁 문화
- 예약 난이도
- 주문/이용 팁

### 8.6 Connect

- eSIM vs SIM vs 포켓 와이파이
- 공항 수령/온라인 개통
- 속도보다 편의가 중요한지 여부

### 8.7 Plan Day

- 대표 관광지 운영시간
- 예약 필요 여부
- 지역별 하루 동선 예시
- 오전/오후/야간 추천 블록

### 8.8 Alerts

- 오늘 확인해야 할 변경사항
- 예약 마감 임박
- 운영시간 변경
- 날씨/교통 이슈

## 9. Implementation Plan

참고 프로젝트의 phase 운영 방식을 그대로 차용해 구현한다.

### Phase 0. Foundation Spec

산출물:

- `docs/sketch.md`
- `docs/ia.md`
- `docs/ux-flow.md`
- `docs/data-contract.md`

작업:

- 카테고리 확정
- 핵심 엔티티/필드 정의
- 화면 흐름 정의
- freshness 정책 정의

완료 기준:

- 대시보드에 필요한 화면과 데이터가 문서로 닫혀 있어야 함

### Phase 1. App Skeleton

산출물:

- `src/app`
- `src/features`
- `src/entities`
- `src/shared`

작업:

- 기본 라우팅
- 레이아웃 시스템
- mock 데이터 기반 대시보드 렌더링
- 공통 카드/배지/테이블/필터 UI

완료 기준:

- destination 하나를 선택하면 정적 mock 대시보드가 열려야 함

### Phase 2. Data Contracts

산출물:

- `schemas/*.json`
- `src/entities/*/model`
- `tests/schema`

작업:

- destination, transport, pass, place, advisory schema 정의
- 카드별 view model 정의
- mock fixture 검증

완료 기준:

- fixture 데이터가 schema validation 을 통과해야 함

### Phase 3. Dashboard Experience

산출물:

- 홈 대시보드
- 카테고리 상세 페이지
- compare 페이지
- alert rail

작업:

- Quick Setup 플로우
- Snapshot 조합 로직
- 비교 카드 UI
- 저장/체크리스트 UX

완료 기준:

- 첫 방문 사용자가 3분 내 핵심 결정을 내릴 수 있어야 함

### Phase 4. Freshness & Trust Layer

산출물:

- last verified 표시
- 변경 가능성 배지
- 데이터 신뢰도 룰

작업:

- 변동성 높은 필드 정의
- stale 경고 로직
- source label / updated time 표시

완료 기준:

- 가격, 규정, 운영시간 같은 고변동 정보에 항상 freshness 신호가 보여야 함

### Phase 5. Personalization

산출물:

- itinerary 저장
- 여행 스타일 기반 추천
- 맞춤 alerts

작업:

- traveler profile
- saved trip state
- user preference 반영

완료 기준:

- 같은 도시라도 사용자 조건에 따라 추천 카드가 달라져야 함

## 10. MVP Scope

처음부터 모든 국가를 하려 하면 구조만 남고 제품이 안 나온다. MVP 는 도시/국가 1개로 시작한다.

### Recommended MVP

- 대상: 일본 또는 서울 인바운드 중 하나
- 화면:
  - dashboard home
  - move
  - pass compare
  - connect
  - alerts
- 기능:
  - 여행 컨텍스트 설정
  - mock 데이터 렌더링
  - compare
  - saved checklist

### Why this MVP

- 이동/패스/통신은 여행 대시보드의 효용이 가장 빨리 드러나는 영역이다.
- 참고 코드베이스에도 이미 `japan-travel` 도메인 구조가 있어 초기 데이터 모델링 참고가 쉽다.

## 11. UX Risks

### Risk 1. 카테고리가 공급자 중심으로 무너질 위험

- 대응: 상단 네비는 행위 중심 유지

### Risk 2. 정보량이 많아 뉴스 포털처럼 보일 위험

- 대응: 모든 페이지 첫 블록은 `summary + decision + action`

### Risk 3. 최신성 없는 정보가 자신감 있게 보일 위험

- 대응: `last_verified_at`, freshness badge, warning rail 필수

### Risk 4. 비교 화면이 표만 많은 문서가 될 위험

- 대응: 각 비교 카드에 `best for / tradeoff / friction` 필드 필수

## 12. Next Docs

다음 문서를 바로 이어서 작성한다.

1. `docs/ia.md`
2. `docs/ux-flow.md`
3. `docs/data-contract.md`
4. `docs/mvp-scope.md`
