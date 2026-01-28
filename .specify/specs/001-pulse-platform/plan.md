# Implementation Plan: Pulse Platform

**Branch**: `001-pulse-platform` | **Date**: 2026-01-27 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-pulse-platform/spec.md`

## Summary

Build Pulse, a cross-platform mobile application with supporting backend that enables users to submit personal wellness data (food photos, movement, body metrics, lifestyle inputs) and receive a Daily Pulse score (0-100) with practical, non-clinical guidance. The system calculates personal baselines from historical data, supports token-based premium access, and provides explainable AI reasoning. The architecture prioritizes mobile-first performance, privacy-first data handling, minimal services, and modular AI components.

## Technical Context

**Language/Version**: 
- Mobile: React Native with Expo (latest stable) and TypeScript - Cross-platform mobile app (iOS 14+, Android 8.0+ API 26+)
- Backend: Node.js 20+ with TypeScript and Express.js

**Primary Dependencies**: 
- Mobile: React Native with Expo, React Navigation, AsyncStorage, Expo Camera, Expo ImagePicker, react-native-health (HealthKit/Google Fit), Web3 libraries for wallet connection
- Backend: Express.js, Sharp (image processing), OpenAI API (GPT-4 Vision for food/fridge analysis), Anthropic Claude API (fallback), Web3.js (token staking)
- Database: PostgreSQL 14+ with TimescaleDB extension for time-series analysis
- Object Storage: S3-compatible storage (AWS S3 or MinIO) for food photos and fridge images
- AI/ML: OpenAI API (primary) with Anthropic Claude API (fallback) - GPT-4 Vision for food/fridge photo analysis, GPT-4 for Daily Pulse reasoning and guidance
- Token/Blockchain: Web3.js library for Ethereum/Polygon token staking integration

**Storage**: 
- PostgreSQL for user data, personal baselines, data entries, token stakes
- Object storage (S3-compatible) for food photos and media
- Time-series database or PostgreSQL extensions for trend analysis

**Testing**: 
- Mobile: Jest/React Native Testing Library or Flutter Test
- Backend: Jest/Vitest (Node.js) or pytest (Python)
- Integration: Supertest/httpx for API testing

**Target Platform**: 
- Mobile: iOS 14+ (iPhone + iPad), Android 8.0+ (API 26+) - Fully cross-platform compatible
- Backend: Linux server (containerized deployment)
- App Stores: App Store and Play Store compliant (especially health data policies)

**Project Type**: Mobile + API (React Native with Expo cross-platform mobile app with Node.js backend API)

**Performance Goals**: 
- Mobile: App launch < 2 seconds, Daily Pulse calculation display < 3 seconds on 4G
- Backend: API response time < 200ms p95, image processing < 5 seconds
- Database: Query performance suitable for trend analysis on 1+ year of user data

**Constraints**: 
- Mobile-first: Optimize for battery life, network efficiency, offline capability for data viewing
- Privacy-first: End-to-end encryption for sensitive data, minimal data collection, user data control
- Explainable AI: Daily Pulse calculation must be testable and auditable (no black-box models)
- Token staking: Integration with blockchain for token management (gas optimization, transaction reliability)
- Modular AI: AI components must be swappable without affecting core functionality

**Scale/Scope**: 
- Initial: 1,000-10,000 users, 100K data entries/month
- Growth: 100K users, 10M data entries/month
- Data retention: Long-term storage for trend analysis (1+ years of historical data per user)

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

### Principle Compliance

✅ **Specification-First Development**: Plan derived from complete specification (spec.md)

✅ **Test-First Implementation**: Testing framework specified, TDD approach required

✅ **Library-First Architecture**: Modular AI components, clear boundaries between mobile/backend

✅ **Non-Clinical, Non-Diagnostic**: All guidance clearly marked as informational, no medical advice

✅ **Clarity Over Complexity**: User-facing outputs prioritize simplicity (Daily Pulse 0-100 score)

✅ **Mobile Performance**: Performance goals specified for mobile devices, battery/network optimized

✅ **Privacy-First Data Handling**: Privacy constraints specified, encryption, user data control

✅ **Explainable AI Outputs**: Daily Pulse must be explainable and testable (no black-box)

✅ **Minimal Abstractions**: Architecture favors direct framework capabilities, minimal services

### Gate Status: **PASS** - All constitution principles addressed in technical context

## Project Structure

### Documentation (this feature)

```text
specs/001-pulse-platform/
├── plan.md # This file (/speckit.plan command output)
├── research.md # Phase 0 output (/speckit.plan command)
├── data-model.md # Phase 1 output (/speckit.plan command)
├── quickstart.md # Phase 1 output (/speckit.plan command)
├── contracts/ # Phase 1 output (/speckit.plan command)
│   ├── api-spec.json
│   └── mobile-api.md
└── tasks.md # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

```text
# Mobile + API structure
api/
├── src/
│   ├── models/          # Data models (User, DataEntry, DailyPulse, etc.)
│   ├── services/        # Business logic (PulseCalculator, BaselineService, etc.)
│   ├── api/             # REST API endpoints
│   │   ├── routes/
│   │   │   ├── data.ts        # Data submission endpoints
│   │   │   ├── pulse.ts       # Daily Pulse endpoints
│   │   │   ├── trends.ts      # Trends and patterns
│   │   │   ├── premium.ts     # Token staking, premium features
│   │   │   ├── auth.ts        # Authentication
│   │   │   ├── fridge.ts      # Fridge → Meals endpoints
│   │   │   └── baseline.ts    # Personal baseline
│   │   └── middleware/
│   ├── ai/              # Modular AI components
│   │   ├── food-analysis.ts      # Food photo analysis
│   │   ├── fridge-analysis.ts     # Fridge ingredient detection (computer vision)
│   │   ├── recipe-generator.ts    # Recipe suggestions from detected ingredients
│   │   ├── pulse-calculator.ts    # Daily Pulse score calculation
│   │   ├── guidance-generator.ts  # Non-clinical guidance generation
│   │   ├── explainability.ts      # Explainable AI outputs
│   │   └── multi-input-reasoning.ts # Premium: Multi-input synthesis
│   ├── storage/         # Data access layer
│   │   ├── database.ts
│   │   └── object-storage.ts
│   └── blockchain/      # Token staking integration
│       └── token-service.ts
└── tests/
    ├── contract/
    ├── integration/
    └── unit/

mobile/
├── src/
│   ├── screens/         # App screens
│   │   ├── Onboarding/
│   │   ├── Dashboard/   # Daily Pulse (hero feature)
│   │   ├── Food/        # Fridge → Meals feature
│   │   ├── Movement/    # Movement → Performance
│   │   ├── Body/        # Body → Feedback
│   │   ├── Insights/    # Trends and patterns
│   │   ├── Profile/     # Token access, settings
│   │   └── Premium/     # Premium features
│   ├── components/      # Reusable UI components
│   │   ├── DailyPulse/  # Pulse score display
│   │   ├── Disclaimers/ # Non-clinical disclaimers
│   │   ├── Explainability/ # AI explanation UI
│   │   └── OfflineIndicator/
│   ├── services/        # API clients, local storage, HealthKit/Google Fit
│   ├── models/          # TypeScript types/models
│   ├── utils/           # Helpers, formatters, image compression
│   └── navigation/      # React Navigation setup
├── app.json            # Expo configuration
└── tests/
    ├── unit/
    └── integration/
```

**Structure Decision**: Mobile + API structure selected because the specification requires a React Native with Expo cross-platform mobile app (iOS 14+, Android 8.0+) with a supporting Node.js backend. The backend handles data processing, AI reasoning (food/fridge analysis, Daily Pulse calculation), and token management, while the mobile app provides the user interface with native integrations (HealthKit, Google Fit, camera). Clear separation enables independent development and testing of mobile and backend components. The mobile app uses dark-first UI design system (#050505 background, Indigo/electric blue accents) with glass-morphism sparingly and rounded corners (24-32px).

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

No violations - architecture aligns with constitution principles:
- Library-first: Modular AI components with clear boundaries
- Minimal abstractions: Direct framework capabilities, minimal service layer
- Privacy-first: Encryption and data control built into architecture
- Explainable AI: Dedicated explainability module for Daily Pulse
