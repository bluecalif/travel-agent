# Information Architecture

## 1. Purpose

이 문서는 `travel-agent` 여행 정보 대시보드의 정보 구조를 정의한다.

목표는 다음 세 가지다.

- 사용자가 어디서 시작해야 하는지 바로 이해하게 한다
- 정보가 많아도 탐색 경로가 흔들리지 않게 한다
- `sketch.md`, `ux-flow.md`, `data-contract.md` 의 기준 축을 하나로 맞춘다

## 2. IA Principles

### Principle 1. 행동 중심 구조

상위 구조는 공급자 분류가 아니라 사용자의 결정 순서를 따른다.

### Principle 2. 요약 우선

모든 주요 페이지는 상세 정보보다 먼저 `현재 판단` 을 돕는 요약을 보여준다.

### Principle 3. 비교 가능성

교통, 패스, 통신, 숙소 지역처럼 선택 갈림길이 있는 정보는 항상 비교 가능한 구조로 둔다.

### Principle 4. 최신성 가시화

가격, 규정, 운영시간처럼 변동성이 높은 항목은 어느 화면에서든 최신성 신호를 함께 보여준다.

## 3. Top-Level Navigation

상단 1차 카테고리는 아래 8개로 고정한다.

1. `Trip Snapshot`
2. `Entry & Rules`
3. `Move`
4. `Stay`
5. `Eat & Spend`
6. `Connect`
7. `Plan Day`
8. `Alerts`

이 이름과 순서는 [`docs/sketch.md`](/C:/Projects-2026/active/travel-agent/docs/sketch.md) 와 동일해야 한다.

## 4. Site Map

### Level 0

- Landing
- Trip Setup
- Dashboard Home

### Level 1

- Trip Snapshot
- Entry & Rules
- Move
- Stay
- Eat & Spend
- Connect
- Plan Day
- Alerts
- Compare
- Saved Trip

### Level 2

#### Trip Snapshot

- Overall readiness
- Budget snapshot
- Weather and season
- Top decisions
- Change-sensitive items

#### Entry & Rules

- Entry requirements
- Customs and tax-free
- Local rules
- Reservation constraints

#### Move

- Airport transfer
- In-city transport
- Intercity transport
- Passes and tickets
- How to use

#### Stay

- Area guide
- Stay type
- Check-in rules
- Etiquette
- Best-for segments

#### Eat & Spend

- Food budget
- Payment methods
- Dining etiquette
- Reservation difficulty
- Spending tips

#### Connect

- eSIM
- SIM
- Pocket Wi-Fi
- Pickup and activation
- Charging and power

#### Plan Day

- Must-check places
- Hours and booking
- Sample day blocks
- Route ideas
- Time-of-day suggestions

#### Alerts

- Changed recently
- Verify before departure
- Booking urgency
- Weather and transport disruptions

#### Compare

- Airport transfer compare
- Pass compare
- Connectivity compare
- Stay area compare

#### Saved Trip

- Saved dashboard
- Checklist
- Notes
- Selected options

## 5. Core Page Templates

### 5.1 Dashboard Home

권장 블록 순서는 아래와 같다.

1. Hero summary
2. Alerts rail
3. Top decision cards
4. Budget snapshot
5. Mobility snapshot
6. Today or trip-day planner
7. Checklist and notes

### 5.2 Category Detail Page

모든 카테고리 상세 페이지는 동일한 뼈대를 가진다.

1. Category summary
2. Decision cards
3. Compare or option list
4. Practical tips
5. Latest changes
6. Related items

### 5.3 Compare Page

비교 페이지는 표 중심이 아니라 의사결정 중심으로 구성한다.

1. Compare summary
2. Best-for labels
3. Key trade-offs
4. Side-by-side facts
5. Friction and risks
6. Recommended next action

## 6. Entry Points

### First-time user entry

- Landing
- Trip Setup
- Dashboard Home

### Returning user entry

- Saved Trip
- Dashboard Home
- Alerts or changed items first

### Deep-link entry

- Category Detail
- Compare
- Specific card or entity detail

## 7. Navigation Rules

- 상단 네비게이션은 8개 카테고리만 노출한다
- 비교 기능은 독립 페이지로 두되, `Move`, `Stay`, `Connect` 에서 자연스럽게 진입하게 한다
- 상세 엔티티 페이지가 생기더라도 상위 카테고리 컨텍스트를 잃지 않게 breadcrumb 를 둔다
- 모바일에서는 상단 탭을 줄이고 `Snapshot`, `Move`, `Alerts` 우선 접근을 보장한다

## 8. Content Priority by Page

### Highest priority

- 지금 결정해야 하는 것
- 바뀌었을 가능성이 있는 것
- 비용과 이동에 직접 영향을 주는 것

### Medium priority

- 팁과 에티켓
- 추천 루트
- 스타일별 추천

### Lower priority

- 배경 설명
- 역사/문화 서술
- 장문 가이드

## 9. Entity-to-Page Mapping

| Entity | Primary page | Secondary page |
|---|---|---|
| `destination` | Trip Snapshot | Plan Day |
| `advisory` | Entry & Rules | Alerts |
| `transport_option` | Move | Compare |
| `pass_product` | Move | Compare |
| `stay_option` | Stay | Compare |
| `place` | Plan Day | Eat & Spend |
| `connectivity_product` | Connect | Compare |
| `payment_method` | Eat & Spend | Trip Snapshot |
| `budget_profile` | Trip Snapshot | Eat & Spend |
| `trip_plan` | Saved Trip | Plan Day |

## 10. Alignment Checklist

이 문서가 다른 문서와 맞아야 하는 기준은 아래와 같다.

- `ux-flow.md` 의 단계는 이 문서의 진입점과 페이지 이동 구조를 따라야 한다
- `data-contract.md` 의 엔티티 이름은 이 문서의 엔티티 매핑과 일치해야 한다
- 상위 카테고리 8개 이름은 세 문서에서 완전히 동일해야 한다
