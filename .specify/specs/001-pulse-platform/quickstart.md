# Quickstart Guide: Pulse Platform

**Date**: 2026-01-27
**Feature**: 001-pulse-platform

## Overview

This guide provides step-by-step instructions to set up and test the Pulse Platform locally.

## Prerequisites

- Node.js 20+ installed
- PostgreSQL 14+ with TimescaleDB extension
- S3-compatible object storage (AWS S3 or MinIO)
- OpenAI API key (or Anthropic API key as fallback)
- Web3 provider access (Infura, Alchemy, or local node) for token staking
- React Native development environment (Xcode for iOS, Android Studio for Android)

## Backend Setup

### 1. Install Dependencies

```bash
cd api
npm install
```

### 2. Database Setup

```bash
# Create database
createdb pulse_db

# Enable TimescaleDB extension
psql pulse_db -c "CREATE EXTENSION IF NOT EXISTS timescaledb;"

# Run migrations
npm run migrate
```

### 3. Environment Configuration

Create `.env` file:

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/pulse_db

# Object Storage (S3-compatible)
S3_ENDPOINT=https://s3.amazonaws.com
S3_BUCKET=pulse-storage
S3_ACCESS_KEY=your_access_key
S3_SECRET_KEY=your_secret_key

# AI Services
OPENAI_API_KEY=sk-your-key
ANTHROPIC_API_KEY=your-key (fallback)

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=7d

# Blockchain
ETHEREUM_RPC_URL=https://mainnet.infura.io/v3/your-key
POLYGON_RPC_URL=https://polygon-mainnet.infura.io/v3/your-key
TOKEN_CONTRACT_ADDRESS=0x...

# Server
PORT=3000
NODE_ENV=development
```

### 4. Start Backend

```bash
npm run dev
```

Backend should be running at `http://localhost:3000`

## Mobile App Setup

### 1. Install Dependencies

```bash
cd mobile
npm install
```

### 2. iOS Setup

```bash
cd ios
pod install
cd ..
```

### 3. Android Setup

Ensure Android SDK is configured in `android/local.properties`

### 4. Configure API Endpoint

Update `mobile/src/config/api.ts`:

```typescript
export const API_BASE_URL = __DEV__ 
  ? 'http://localhost:3000/v1'
  : 'https://api.pulse.app/v1';
```

### 5. Start Mobile App

```bash
# iOS
npm run ios

# Android
npm run android
```

## Testing Workflow

### Test 1: User Registration & Authentication

1. Register a new user via mobile app
2. Verify JWT token is received and stored
3. Test authenticated API calls

**Expected**: User can register and authenticate successfully

### Test 2: Submit Data Entry (Movement)

1. Submit movement data via mobile app:
   ```json
   {
     "data_type": "movement",
     "data_date": "2026-01-27",
     "data_content": {
       "steps": 8500,
       "activity_type": "walking",
       "duration_minutes": 45,
       "intensity": "moderate"
     }
   }
   ```
2. Verify data entry is created in database
3. Check processing status updates to "completed"

**Expected**: Data entry is stored and processed successfully

### Test 3: Submit Food Photo

1. Take/select a food photo in mobile app
2. Submit via multipart form-data
3. Verify image is stored in S3
4. Check AI analysis extracts meal information
5. Verify data entry includes extracted info

**Expected**: Food photo is processed, stored, and analyzed

### Test 4: Calculate Daily Pulse

1. Submit multiple data types (movement, body metric, lifestyle)
2. Trigger Daily Pulse calculation via API
3. Verify score is between 0-100
4. Check guidance includes food, movement, recovery suggestions
5. Verify explanation shows how score was calculated
6. Confirm all guidance is marked as non-clinical

**Expected**: Daily Pulse calculated with explainable score and non-clinical guidance

### Test 5: View Trends (Free Tier)

1. Submit data for 7+ days
2. Request basic trend analysis
3. Verify trends show patterns relative to personal baseline
4. Confirm no generic benchmarks are displayed

**Expected**: Basic trends displayed relative to personal baseline

### Test 6: Token Staking & Premium Access

1. Connect wallet in mobile app
2. Stake required tokens (testnet tokens for development)
3. Verify premium status is activated
4. Request advanced trend analysis
5. Verify advanced features are accessible
6. Test unlimited AI requests (no rate limit)

**Expected**: Premium features unlocked after token staking

### Test 7: Personal Baseline Calculation

1. Submit data for 14+ days
2. Trigger baseline calculation
3. Verify baseline metrics are calculated
4. Check confidence level (should be "low" for 14-30 days)
5. Verify Daily Pulse compares to baseline

**Expected**: Personal baseline calculated and used for comparisons

### Test 8: Explainable Daily Pulse

1. Calculate Daily Pulse
2. Review explanation field
3. Verify calculation_inputs show all data entries used
4. Check calculation_weights show how inputs were weighted
5. Confirm explanation is human-readable

**Expected**: Daily Pulse calculation is fully explainable and auditable

## Integration Test Scenarios

### Scenario 1: New User Journey

1. Register → Submit first data entry → Calculate Daily Pulse (may have limited data)
2. Submit data for 7 days → View basic trends
3. Submit data for 14 days → Personal baseline calculated
4. View Daily Pulse with baseline comparison

**Expected**: Smooth progression from new user to established user

### Scenario 2: Premium Upgrade Journey

1. Free user with 30+ days of data
2. View basic trends
3. Stake tokens → Premium activated
4. View advanced trends with multi-input reasoning
5. Use unlimited AI requests

**Expected**: Seamless upgrade to premium features

### Scenario 3: Data Submission & Daily Pulse

1. Submit food photo in morning
2. Submit movement data after workout
3. Submit body metric (weight)
4. Submit lifestyle input (sleep quality)
5. Calculate Daily Pulse
6. Verify all inputs are included in calculation
7. Review guidance for food, movement, recovery

**Expected**: All data types contribute to Daily Pulse calculation

## Performance Validation

### Mobile Performance

- App launch: < 2 seconds
- Daily Pulse display: < 3 seconds on 4G
- Image upload: < 5 seconds for compressed image
- Trend loading: < 2 seconds for 30 days

### Backend Performance

- API response: < 200ms p95
- Daily Pulse calculation: < 5 seconds
- Image processing: < 5 seconds
- Database queries: < 100ms for user data

## Privacy Validation

- All data encrypted at rest
- User consent tracked
- Data export works (JSON format)
- Data deletion works (cascades properly)

## Constitution Compliance Check

- ✅ Non-clinical guidance: All recommendations marked as informational
- ✅ Privacy-first: Encryption, consent, user control
- ✅ Explainable AI: Daily Pulse calculation is auditable
- ✅ Mobile performance: Targets met
- ✅ Test-first: All features have tests before implementation

## Troubleshooting

### Database Issues
- Verify TimescaleDB extension is enabled
- Check database connection string
- Ensure migrations have run

### AI Service Issues
- Verify API keys are correct
- Check rate limits (free tier: 10/day)
- Test fallback to Anthropic if OpenAI fails

### Blockchain Issues
- Use testnet for development
- Verify RPC endpoints are accessible
- Check token contract address

### Image Processing Issues
- Verify S3 credentials
- Check image format support
- Test image compression

## Next Steps

After quickstart validation:
1. Run full test suite: `npm test`
2. Check code coverage: `npm run coverage`
3. Review constitution compliance
4. Proceed to `/speckit.tasks` for task breakdown
