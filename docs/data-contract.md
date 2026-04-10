# Data Contract

## 1. Purpose

이 문서는 여행 대시보드에서 사용하는 핵심 데이터 계약을 정의한다.

목표는 다음과 같다.

- 화면과 데이터를 느슨하게 연결하지 않고 명시적으로 맞춘다
- `ia.md` 의 페이지 구조와 `ux-flow.md` 의 사용자 흐름을 지원한다
- 이후 `schemas/` 와 `src/entities/` 구현 기준이 되게 한다

## 2. Contract Principles

- 엔티티 이름은 문서 전체에서 고정한다
- 화면에서 필요한 최소 필드를 우선 정의한다
- 고변동 필드는 최신성 메타데이터를 반드시 가진다
- 비교 가능한 객체는 공통 비교 필드를 가진다
- `unknown` 과 `not_applicable` 를 구분한다

## 3. Shared Meta Fields

모든 주요 엔티티는 아래 메타 필드를 공통으로 가진다.

| Field | Type | Description |
|---|---|---|
| `id` | string | 전역 식별자 |
| `title` | string | 사용자 표시 이름 |
| `category` | string | 상위 카테고리 또는 엔티티 범주 |
| `geo_scope` | object | 국가, 도시, 지역 범위 |
| `summary` | string | 카드용 짧은 설명 |
| `last_verified_at` | string | 마지막 검증 시각 |
| `freshness_level` | enum | `fresh`, `watch`, `stale` |
| `confidence` | number | 0.0 ~ 1.0 |
| `sources` | array | 출처 목록 |

## 4. Core Entities

### 4.1 trip_context

Trip Setup 결과로 생성되는 사용자 컨텍스트다.

| Field | Type | Required |
|---|---|---|
| `destination_id` | string | yes |
| `start_date` | string | yes |
| `end_date` | string | yes |
| `trip_length_days` | number | yes |
| `companion_type` | enum | yes |
| `priority_tags` | string[] | yes |
| `budget_style` | enum | no |
| `mobility_preference` | enum | no |

### 4.2 destination

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `country` | string | yes |
| `city` | string | no |
| `region` | string | no |
| `timezone` | string | no |
| `season_summary` | string | no |
| `travel_readiness_summary` | string | no |
| `best_for` | string[] | no |

### 4.3 advisory

`Entry & Rules` 와 `Alerts` 에 공통으로 사용한다.

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `advisory_type` | enum | yes |
| `severity` | enum | yes |
| `applies_to` | object | yes |
| `details` | string | yes |
| `action_required` | string | no |
| `effective_from` | string | no |
| `effective_until` | string | no |
| `last_verified_at` | string | yes |

### 4.4 transport_option

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `transport_type` | enum | yes |
| `route_scope` | object | yes |
| `price` | object | no |
| `duration` | string | no |
| `how_to_use` | string | no |
| `best_for` | string[] | no |
| `friction_points` | string[] | no |
| `booking_required` | boolean | no |
| `operating_hours` | string | no |

### 4.5 pass_product

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `pass_type` | enum | yes |
| `price` | object | no |
| `duration` | string | no |
| `eligibility` | string | no |
| `valid_for_transport_ids` | string[] | no |
| `where_to_buy` | string | no |
| `how_to_use` | string | no |
| `best_for` | string[] | no |
| `tradeoffs` | string[] | no |

### 4.6 stay_option

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `stay_type` | enum | yes |
| `area_name` | string | no |
| `price_band` | string | no |
| `checkin_rules` | string[] | no |
| `etiquette` | string[] | no |
| `best_for` | string[] | no |
| `tradeoffs` | string[] | no |

### 4.7 place

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `place_type` | enum | yes |
| `location` | object | yes |
| `hours` | string | no |
| `booking_required` | boolean | no |
| `price` | object | no |
| `visit_duration_hint` | string | no |
| `tips` | string[] | no |

### 4.8 connectivity_product

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `connectivity_type` | enum | yes |
| `price` | object | no |
| `data_policy` | string | no |
| `pickup_method` | string | no |
| `activation_method` | string | no |
| `best_for` | string[] | no |
| `tradeoffs` | string[] | no |

### 4.9 payment_method

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `title` | string | yes |
| `payment_type` | enum | yes |
| `acceptance_summary` | string | no |
| `fees` | string | no |
| `tips` | string[] | no |
| `best_for` | string[] | no |
| `risks` | string[] | no |

### 4.10 budget_profile

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `destination_id` | string | yes |
| `daily_range` | object | yes |
| `transport_range` | object | no |
| `food_range` | object | no |
| `stay_range` | object | no |
| `notes` | string[] | no |

### 4.11 trip_plan

| Field | Type | Required |
|---|---|---|
| `id` | string | yes |
| `trip_context_id` | string | yes |
| `selected_transport_ids` | string[] | no |
| `selected_pass_ids` | string[] | no |
| `selected_connectivity_ids` | string[] | no |
| `saved_place_ids` | string[] | no |
| `checklist_items` | array | no |
| `notes` | string[] | no |

## 5. Page View Models

### 5.1 dashboard_home_view

| Field | Type | Description |
|---|---|---|
| `trip_context` | object | 현재 여행 컨텍스트 |
| `hero_summary` | object | 핵심 요약 |
| `alerts` | advisory[] | 상단 경고 |
| `top_decisions` | array | 바로 결정할 카드 |
| `budget_snapshot` | budget_profile | 예산 요약 |
| `mobility_snapshot` | array | 이동 요약 |
| `today_plan` | array | 일정 블록 |
| `saved_state` | object | 저장 상태 |

### 5.2 category_page_view

모든 상위 카테고리 페이지는 같은 골격을 가진다.

| Field | Type | Description |
|---|---|---|
| `category_key` | string | 상위 카테고리 키 |
| `summary` | object | 카테고리 요약 |
| `decision_cards` | array | 핵심 선택 카드 |
| `options` | array | 선택지 또는 목록 |
| `practical_tips` | string[] | 실전 팁 |
| `latest_changes` | advisory[] | 최근 변경 |
| `related_items` | array | 연관 항목 |

### 5.3 compare_view

| Field | Type | Description |
|---|---|---|
| `compare_type` | enum | airport-transfer, pass, connectivity, stay-area |
| `trip_context` | object | 사용자 조건 |
| `options` | array | 비교 대상 |
| `summary` | object | 비교 요약 |
| `recommended_option_id` | string | 추천 옵션 |

## 6. Category-to-Entity Mapping

| Category | Primary entities |
|---|---|
| `Trip Snapshot` | destination, budget_profile, advisory |
| `Entry & Rules` | advisory |
| `Move` | transport_option, pass_product |
| `Stay` | stay_option |
| `Eat & Spend` | payment_method, place, budget_profile |
| `Connect` | connectivity_product |
| `Plan Day` | place, trip_plan |
| `Alerts` | advisory |

## 7. Freshness Policy

고변동 필드는 아래 규칙을 따른다.

### High-volatility fields

- `price`
- `hours`
- `operating_hours`
- `details` on advisory
- `acceptance_summary`

### Required freshness data

- `last_verified_at`
- `freshness_level`
- `sources`

### Freshness behavior

- `fresh`: 일반 표시
- `watch`: 경계 배지 표시
- `stale`: Alerts 포함 및 재확인 CTA 표시

## 8. Compare Contract Rules

비교 가능한 객체는 아래 필드를 우선 가져야 한다.

- `title`
- `price`
- `best_for`
- `tradeoffs` or `friction_points`
- `how_to_use` or `activation_method`
- `last_verified_at`

## 9. Suggested Schema Files

- `schemas/trip-context.json`
- `schemas/destination.json`
- `schemas/advisory.json`
- `schemas/transport-option.json`
- `schemas/pass-product.json`
- `schemas/stay-option.json`
- `schemas/place.json`
- `schemas/connectivity-product.json`
- `schemas/payment-method.json`
- `schemas/budget-profile.json`
- `schemas/trip-plan.json`
- `schemas/dashboard-home-view.json`
- `schemas/category-page-view.json`
- `schemas/compare-view.json`

## 10. Alignment Checklist

- `trip_context` 필드는 `ux-flow.md` 의 Trip Setup 입력과 동일해야 한다
- `category_page_view` 구조는 `ia.md` 의 카테고리 상세 페이지 템플릿과 동일해야 한다
- 상위 카테고리 이름 8개는 `sketch.md`, `ia.md`, `ux-flow.md` 와 동일해야 한다
