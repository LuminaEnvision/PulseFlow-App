# Tasks: Pulse Platform

**Input**: Design documents from `/specs/001-pulse-platform/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED - Constitution mandates test-first implementation (TDD). All tests must be written first and fail before implementation.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Mobile + API**: `api/src/`, `mobile/src/` at repository root
- Paths follow plan.md structure: api/ and mobile/ directories

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [ ] T001 Create project structure per implementation plan (api/ and mobile/ directories)
- [ ] T002 Initialize backend project: api/package.json with Node.js 20+, TypeScript, Express.js dependencies
- [ ] T003 Initialize mobile project: mobile/package.json with React Native, TypeScript, React Navigation dependencies
- [ ] T004 [P] Configure TypeScript for backend in api/tsconfig.json
- [ ] T005 [P] Configure TypeScript for mobile in mobile/tsconfig.json
- [ ] T006 [P] Setup Jest for backend testing in api/jest.config.js
- [ ] T007 [P] Setup Jest and React Native Testing Library for mobile in mobile/jest.config.js
- [ ] T008 [P] Configure ESLint and Prettier for backend in api/.eslintrc.json and api/.prettierrc
- [ ] T009 [P] Configure ESLint and Prettier for mobile in mobile/.eslintrc.json and mobile/.prettierrc
- [ ] T010 Create .env.example files for api/ and mobile/ with required environment variables

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T011 Setup PostgreSQL database with TimescaleDB extension
- [ ] T012 Create database migration framework in api/src/storage/migrations/
- [ ] T013 [P] Create database connection module in api/src/storage/database.ts
- [ ] T014 [P] Create S3-compatible object storage client in api/src/storage/object-storage.ts
- [ ] T015 [P] Setup Express.js server with basic middleware in api/src/server.ts
- [ ] T016 [P] Implement JWT authentication middleware in api/src/api/middleware/auth.ts
- [ ] T017 [P] Implement error handling middleware in api/src/api/middleware/error-handler.ts
- [ ] T018 [P] Setup logging infrastructure in api/src/utils/logger.ts
- [ ] T019 [P] Create environment configuration module in api/src/config/env.ts
- [ ] T020 Create User model in api/src/models/User.ts (foundational entity for all stories)
- [ ] T021 Create database migration for users table in api/src/storage/migrations/001_create_users.ts
- [ ] T022 [P] Create API route structure in api/src/api/routes/ with auth.ts route
- [ ] T023 [P] Setup React Native navigation in mobile/src/navigation/AppNavigator.tsx
- [ ] T024 [P] Create API client service in mobile/src/services/api-client.ts
- [ ] T025 [P] Create local storage service in mobile/src/services/storage.ts (AsyncStorage wrapper)
- [ ] T026 [P] Create authentication service in mobile/src/services/auth.ts
- [ ] T027 [P] Create TypeScript types/models in mobile/src/models/index.ts

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - Submit Personal Data and Receive Daily Pulse (Priority: P1) 🎯 MVP

**Goal**: Users can submit personal data (food photos, movement, body metrics, lifestyle) and receive Daily Pulse score (0-100) with practical, non-clinical guidance.

**Independent Test**: User submits at least one type of personal data and receives Daily Pulse score with guidance. Test validates data submission, Daily Pulse calculation, and non-clinical guidance presentation.

### Tests for User Story 1 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T028 [P] [US1] Contract test for POST /data/entries endpoint in api/tests/contract/test-data-entries.ts
- [ ] T029 [P] [US1] Contract test for POST /pulse/daily endpoint in api/tests/contract/test-pulse-daily.ts
- [ ] T030 [P] [US1] Contract test for GET /pulse/daily endpoint in api/tests/contract/test-pulse-get.ts
- [ ] T031 [P] [US1] Integration test for data submission flow in api/tests/integration/test-data-submission.ts
- [ ] T032 [P] [US1] Integration test for Daily Pulse calculation flow in api/tests/integration/test-pulse-calculation.ts
- [ ] T033 [P] [US1] Unit test for DataEntry model in api/tests/unit/models/test-DataEntry.ts
- [ ] T034 [P] [US1] Unit test for DailyPulse model in api/tests/unit/models/test-DailyPulse.ts
- [ ] T035 [P] [US1] Unit test for PulseCalculator service in api/tests/unit/services/test-PulseCalculator.ts
- [ ] T036 [P] [US1] Unit test for food photo analysis in api/tests/unit/ai/test-food-analysis.ts
- [ ] T037 [P] [US1] Unit test for guidance generator in api/tests/unit/ai/test-guidance-generator.ts
- [ ] T038 [P] [US1] Unit test for explainability module in api/tests/unit/ai/test-explainability.ts
- [ ] T039 [P] [US1] Mobile unit test for DataSubmission screen in mobile/tests/unit/screens/test-DataSubmission.tsx
- [ ] T040 [P] [US1] Mobile unit test for DailyPulse screen in mobile/tests/unit/screens/test-DailyPulse.tsx
- [ ] T041 [P] [US1] Mobile integration test for data submission in mobile/tests/integration/test-data-submission.ts

### Implementation for User Story 1

- [ ] T042 [P] [US1] Create DataEntry model in api/src/models/DataEntry.ts
- [ ] T043 [P] [US1] Create DailyPulse model in api/src/models/DailyPulse.ts
- [ ] T044 [P] [US1] Create PersonalBaseline model in api/src/models/PersonalBaseline.ts (needed for baseline comparison in Daily Pulse)
- [ ] T045 [P] [US1] Create database migration for data_entries table in api/src/storage/migrations/002_create_data_entries.ts
- [ ] T046 [P] [US1] Create database migration for daily_pulses table in api/src/storage/migrations/003_create_daily_pulses.ts
- [ ] T047 [P] [US1] Create database migration for personal_baselines table in api/src/storage/migrations/004_create_personal_baselines.ts
- [ ] T048 [US1] Create DataEntryService in api/src/services/DataEntryService.ts (depends on T042, T013)
- [ ] T049 [US1] Create ImageProcessingService in api/src/services/ImageProcessingService.ts (uses Sharp library)
- [ ] T050 [US1] Create FoodAnalysisService in api/src/ai/food-analysis.ts (modular AI component for food photo analysis)
- [ ] T051 [US1] Create PulseCalculatorService in api/src/services/PulseCalculatorService.ts (depends on T042, T043, T050)
- [ ] T052 [US1] Create GuidanceGeneratorService in api/src/ai/guidance-generator.ts (modular AI component for non-clinical guidance)
- [ ] T053 [US1] Create ExplainabilityService in api/src/ai/explainability.ts (modular AI component for explainable outputs)
- [ ] T054 [US1] Create BaselineService in api/src/services/BaselineService.ts (depends on T044, T042)
- [ ] T055 [US1] Implement POST /data/entries endpoint in api/src/api/routes/data.ts (depends on T048, T049, T050)
- [ ] T056 [US1] Implement GET /data/entries endpoint in api/src/api/routes/data.ts (depends on T048)
- [ ] T057 [US1] Implement POST /pulse/daily endpoint in api/src/api/routes/pulse.ts (depends on T051, T052, T053, T054)
- [ ] T058 [US1] Implement GET /pulse/daily endpoint in api/src/api/routes/pulse.ts (depends on T051)
- [ ] T059 [US1] Add validation and error handling for data submission in api/src/api/routes/data.ts
- [ ] T060 [US1] Add validation and error handling for Daily Pulse calculation in api/src/api/routes/pulse.ts
- [ ] T061 [US1] Add logging for data submission operations in api/src/services/DataEntryService.ts
- [ ] T062 [US1] Add logging for Daily Pulse calculation operations in api/src/services/PulseCalculatorService.ts
- [ ] T063 [US1] Create DataSubmission screen in mobile/src/screens/DataSubmission/DataSubmissionScreen.tsx
- [ ] T064 [US1] Create DailyPulse screen in mobile/src/screens/DailyPulse/DailyPulseScreen.tsx
- [ ] T065 [US1] Create data submission form components in mobile/src/components/DataSubmission/
- [ ] T066 [US1] Create Daily Pulse display components in mobile/src/components/DailyPulse/
- [ ] T067 [US1] Integrate data submission API calls in mobile/src/services/data-service.ts
- [ ] T068 [US1] Integrate Daily Pulse API calls in mobile/src/services/pulse-service.ts
- [ ] T069 [US1] Add image compression before upload in mobile/src/utils/image-compression.ts
- [ ] T070 [US1] Add offline data queue for data submissions in mobile/src/services/offline-queue.ts

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently. Users can submit data and receive Daily Pulse with guidance.

---

## Phase 4: User Story 2 - View Personal Patterns and Trends (Priority: P2)

**Goal**: Users can view their personal patterns and trends over time, seeing how data relates to their personal baseline (not generic benchmarks).

**Independent Test**: User with 7+ days of data views trends. Test validates trend visualization works, data is presented relative to personal baseline, and no generic benchmarks are shown.

### Tests for User Story 2 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T071 [P] [US2] Contract test for GET /trends endpoint in api/tests/contract/test-trends.ts
- [ ] T072 [P] [US2] Contract test for GET /baseline endpoint in api/tests/contract/test-baseline.ts
- [ ] T073 [P] [US2] Integration test for trend analysis flow in api/tests/integration/test-trend-analysis.ts
- [ ] T074 [P] [US2] Unit test for TrendAnalysis model in api/tests/unit/models/test-TrendAnalysis.ts
- [ ] T075 [P] [US2] Unit test for BaselineService trend calculations in api/tests/unit/services/test-BaselineService-trends.ts
- [ ] T076 [P] [US2] Mobile unit test for Trends screen in mobile/tests/unit/screens/test-Trends.tsx
- [ ] T077 [P] [US2] Mobile integration test for trends viewing in mobile/tests/integration/test-trends-viewing.ts

### Implementation for User Story 2

- [ ] T078 [P] [US2] Create TrendAnalysis model in api/src/models/TrendAnalysis.ts
- [ ] T079 [P] [US2] Create database migration for trend_analyses table in api/src/storage/migrations/005_create_trend_analyses.ts
- [ ] T080 [US2] Extend BaselineService with trend calculation methods in api/src/services/BaselineService.ts (depends on T054)
- [ ] T081 [US2] Create TrendAnalysisService in api/src/services/TrendAnalysisService.ts (depends on T078, T080)
- [ ] T082 [US2] Implement GET /trends endpoint in api/src/api/routes/trends.ts (depends on T081)
- [ ] T083 [US2] Implement GET /baseline endpoint in api/src/api/routes/baseline.ts (depends on T054)
- [ ] T084 [US2] Add validation and error handling for trends endpoint in api/src/api/routes/trends.ts
- [ ] T085 [US2] Add logging for trend analysis operations in api/src/services/TrendAnalysisService.ts
- [ ] T086 [US2] Create Trends screen in mobile/src/screens/Trends/TrendsScreen.tsx
- [ ] T087 [US2] Create trend visualization components in mobile/src/components/Trends/
- [ ] T088 [US2] Integrate trends API calls in mobile/src/services/trends-service.ts
- [ ] T089 [US2] Add local caching for trends data in mobile/src/services/trends-cache.ts
- [ ] T090 [US2] Add lazy loading for trend data in mobile/src/screens/Trends/TrendsScreen.tsx

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently. Users can submit data, receive Daily Pulse, and view trends.

---

## Phase 5: User Story 3 - Access Premium Features via Token Staking (Priority: P2)

**Goal**: Users can stake tokens to unlock premium features (advanced trend tracking, baseline modeling, multi-input reasoning, unlimited AI requests).

**Independent Test**: User stakes tokens and verifies premium features are unlocked. Test validates token staking works and premium features are properly gated.

### Tests for User Story 3 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T091 [P] [US3] Contract test for POST /premium/stake endpoint in api/tests/contract/test-premium-stake.ts
- [ ] T092 [P] [US3] Contract test for POST /premium/unstake endpoint in api/tests/contract/test-premium-unstake.ts
- [ ] T093 [P] [US3] Integration test for token staking flow in api/tests/integration/test-token-staking.ts
- [ ] T094 [P] [US3] Unit test for TokenStake model in api/tests/unit/models/test-TokenStake.ts
- [ ] T095 [P] [US3] Unit test for TokenService in api/tests/unit/blockchain/test-token-service.ts
- [ ] T096 [P] [US3] Unit test for premium access gating in api/tests/unit/services/test-premium-gating.ts
- [ ] T097 [P] [US3] Mobile unit test for Premium screen in mobile/tests/unit/screens/test-Premium.tsx
- [ ] T098 [P] [US3] Mobile integration test for premium upgrade flow in mobile/tests/integration/test-premium-upgrade.ts

### Implementation for User Story 3

- [ ] T099 [P] [US3] Create TokenStake model in api/src/models/TokenStake.ts
- [ ] T100 [P] [US3] Create database migration for token_stakes table in api/src/storage/migrations/006_create_token_stakes.ts
- [ ] T101 [US3] Create TokenService in api/src/blockchain/token-service.ts (uses Web3.js)
- [ ] T102 [US3] Create PremiumService in api/src/services/PremiumService.ts (depends on T099, T101)
- [ ] T103 [US3] Create premium access middleware in api/src/api/middleware/premium-check.ts (depends on T102)
- [ ] T104 [US3] Implement POST /premium/stake endpoint in api/src/api/routes/premium.ts (depends on T102, T101)
- [ ] T105 [US3] Implement POST /premium/unstake endpoint in api/src/api/routes/premium.ts (depends on T102)
- [ ] T106 [US3] Add premium gating to advanced trends endpoint in api/src/api/routes/trends.ts (depends on T082, T103)
- [ ] T107 [US3] Add rate limiting for free tier AI requests in api/src/api/middleware/rate-limit.ts
- [ ] T108 [US3] Add unlimited AI requests for premium users in api/src/api/middleware/rate-limit.ts (depends on T103)
- [ ] T109 [US3] Extend TrendAnalysisService with advanced analysis for premium users in api/src/services/TrendAnalysisService.ts (depends on T081)
- [ ] T110 [US3] Add multi-input reasoning for premium users in api/src/ai/multi-input-reasoning.ts (modular AI component)
- [ ] T111 [US3] Add validation and error handling for token staking in api/src/api/routes/premium.ts
- [ ] T112 [US3] Add logging for token staking operations in api/src/services/PremiumService.ts
- [ ] T113 [US3] Create Premium screen in mobile/src/screens/Premium/PremiumScreen.tsx
- [ ] T114 [US3] Create token staking components in mobile/src/components/Premium/
- [ ] T115 [US3] Integrate Web3 wallet connection in mobile/src/services/wallet-service.ts
- [ ] T116 [US3] Integrate premium API calls in mobile/src/services/premium-service.ts
- [ ] T117 [US3] Add premium feature indicators in mobile UI components

**Checkpoint**: At this point, all user stories should be independently functional. Users can submit data, receive Daily Pulse, view trends, and access premium features via token staking.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T118 [P] Add comprehensive error messages and user-friendly error handling across all API endpoints
- [ ] T119 [P] Add request/response logging middleware in api/src/api/middleware/request-logger.ts
- [ ] T120 [P] Add API rate limiting middleware (general) in api/src/api/middleware/rate-limit.ts
- [ ] T121 [P] Add data encryption at rest for sensitive fields in api/src/storage/encryption.ts
- [ ] T122 [P] Add consent tracking validation in api/src/services/ConsentService.ts
- [ ] T123 [P] Add data export functionality in api/src/api/routes/data-export.ts
- [ ] T124 [P] Add data deletion functionality with cascade handling in api/src/api/routes/data-deletion.ts
- [ ] T125 [P] Add mobile offline indicator component in mobile/src/components/OfflineIndicator.tsx
- [ ] T126 [P] Add mobile network error handling and retry logic in mobile/src/services/network-error-handler.ts
- [ ] T127 [P] Add mobile performance monitoring in mobile/src/utils/performance-monitor.ts
- [ ] T128 [P] Add comprehensive unit test coverage (aim for >80%) across all services
- [ ] T129 [P] Add integration test coverage for all user journeys
- [ ] T130 [P] Update API documentation with OpenAPI spec in api/docs/api-documentation.md
- [ ] T131 [P] Add mobile app documentation in mobile/docs/
- [ ] T132 [P] Run quickstart.md validation and update if needed
- [ ] T133 [P] Add non-clinical disclaimer components in mobile/src/components/Disclaimers/
- [ ] T134 [P] Add explainability UI components showing Daily Pulse calculation in mobile/src/components/Explainability/

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational (Phase 2) - Depends on US1 for DailyPulse data but can work with mock data for independent testing
- **User Story 3 (P2)**: Can start after Foundational (Phase 2) - Depends on US2 for premium gating but can work independently

### Within Each User Story

- Tests (REQUIRED) MUST be written and FAIL before implementation
- Models before services
- Services before endpoints
- Backend before mobile integration
- Core implementation before integration
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel (if team capacity allows)
- All tests for a user story marked [P] can run in parallel
- Models within a story marked [P] can run in parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Launch all tests for User Story 1 together:
Task: "Contract test for POST /data/entries endpoint in api/tests/contract/test-data-entries.ts"
Task: "Contract test for POST /pulse/daily endpoint in api/tests/contract/test-pulse-daily.ts"
Task: "Contract test for GET /pulse/daily endpoint in api/tests/contract/test-pulse-get.ts"
Task: "Integration test for data submission flow in api/tests/integration/test-data-submission.ts"
Task: "Integration test for Daily Pulse calculation flow in api/tests/integration/test-pulse-calculation.ts"

# Launch all models for User Story 1 together:
Task: "Create DataEntry model in api/src/models/DataEntry.ts"
Task: "Create DailyPulse model in api/src/models/DailyPulse.ts"
Task: "Create PersonalBaseline model in api/src/models/PersonalBaseline.ts"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Test User Story 1 independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1 (backend)
   - Developer B: User Story 1 (mobile)
   - Developer C: User Story 2 (backend)
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable
- **CRITICAL**: Verify tests fail before implementing (TDD requirement)
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently
- Avoid: vague tasks, same file conflicts, cross-story dependencies that break independence
- All AI components must be modular and swappable
- All guidance must be clearly marked as non-clinical
- Daily Pulse calculation must be explainable and testable
