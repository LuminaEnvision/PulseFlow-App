# PulseFlowApp Development Guidelines

Auto-generated from all feature plans. Last updated: 2026-01-27

## Active Technologies

### Mobile
- **React Native with Expo** (latest stable) - Cross-platform mobile development (iOS 14+, Android 8.0+)
- **TypeScript** - Type safety
- **React Navigation** - Navigation framework
- **AsyncStorage** - Local data persistence
- **Expo Camera** - Camera integration for food/fridge photos
- **Expo ImagePicker** - Image selection
- **react-native-health** - HealthKit (iOS) and Google Fit (Android) integration
- **Web3 libraries** - Wallet connection for token staking
- **Jest** + **React Native Testing Library** - Testing framework

### Backend
- **Node.js 20+** with TypeScript - Runtime and language
- **Express.js** - HTTP server framework
- **PostgreSQL 14+** with **TimescaleDB** extension - Database and time-series analysis
- **Sharp** - Image processing library
- **AWS SDK** / **S3-compatible storage** - Object storage for images
- **Web3.js** - Blockchain/token integration
- **Jest** - Testing framework

### AI/ML
- **OpenAI API** (GPT-4 Vision for food/fridge photo analysis, GPT-4 for Daily Pulse and guidance) - Primary AI service
- **Anthropic Claude API** - Fallback AI service
- **Computer Vision** - Ingredient detection from fridge photos (GPT-4 Vision)
- **Recipe Generation** - AI-generated recipes from detected ingredients

### Blockchain
- **Ethereum** / **Polygon** networks - Token staking
- **ERC-20 tokens** - Token standard

## Project Structure

```text
api/
├── src/
│   ├── models/          # Data models
│   ├── services/        # Business logic
│   ├── api/             # REST API endpoints
│   ├── ai/              # Modular AI components
│   ├── storage/         # Data access layer
│   └── blockchain/      # Token staking
└── tests/

mobile/
├── src/
│   ├── screens/         # App screens (Onboarding, Dashboard/DailyPulse, Food/Fridge, Movement, Body, Insights, Profile, Premium)
│   ├── components/      # UI components (DailyPulse, Disclaimers, Explainability, OfflineIndicator)
│   ├── services/        # API clients, HealthKit/Google Fit, wallet
│   ├── models/          # TypeScript types
│   ├── utils/           # Helpers, image compression
│   └── navigation/      # React Navigation
├── app.json            # Expo configuration
└── tests/
```

## Commands

### Backend (Node.js/Express)
- `npm install` - Install dependencies
- `npm run dev` - Start development server
- `npm test` - Run tests
- `npm run migrate` - Run database migrations
- `npm run build` - Build for production

### Mobile (React Native)
- `npm install` - Install dependencies
- `npm run ios` - Run on iOS simulator
- `npm run android` - Run on Android emulator
- `npm test` - Run tests
- `cd ios && pod install` - Install iOS dependencies

### Database
- `psql pulse_db -c "CREATE EXTENSION IF NOT EXISTS timescaledb;"` - Enable TimescaleDB
- `npm run migrate` - Run migrations

## Code Style

### TypeScript
- Use strict mode
- Prefer interfaces over types for object shapes
- Use async/await over promises
- Type all function parameters and return values

### React Native
- Functional components with hooks
- Use TypeScript for all components
- Extract reusable logic to custom hooks
- Keep components focused and small

### Node.js/Express
- Use async/await for async operations
- Error handling middleware
- Type all request/response objects
- Use environment variables for configuration

### Testing
- Write tests first (TDD)
- Test business logic in isolation
- Mock external services (AI, blockchain, storage)
- Aim for >80% code coverage

## Recent Changes

### 001-pulse-platform (2026-01-27)
- Added cross-platform mobile app structure (React Native with Expo) - iOS 14+, Android 8.0+
- Added backend API structure (Node.js/Express/TypeScript)
- Added PostgreSQL with TimescaleDB for time-series data
- Added modular AI components (OpenAI GPT-4 Vision for food/fridge analysis, GPT-4 for Daily Pulse, Anthropic fallback)
- Added Fridge → Meals feature (ingredient detection, recipe generation from detected items)
- Added Movement → Performance tracking (HealthKit/Google Fit integration, fitness trend analysis)
- Added Body → Feedback tracking (correlation views, pattern-first approach)
- Added token staking integration (Web3.js, Ethereum/Polygon) - read-only wallet connection
- Added image processing (Sharp, S3 storage)
- Added data models: User, DataEntry, DailyPulse, PersonalBaseline, TrendAnalysis, TokenStake
- Added REST API endpoints for data submission, Daily Pulse, trends, premium features, fridge/meals
- Added mobile-first performance optimizations
- Added privacy-first data handling (encryption, consent tracking)
- Added explainable AI outputs for Daily Pulse calculation
- Added dark-first UI design system (#050505 background, Indigo/electric blue accents)
- Added App Store and Play Store compliance considerations

 
