# Data Model: Pulse Platform

**Date**: 2026-01-27
**Feature**: 001-pulse-platform

## Entities

### User

**Purpose**: Represents a platform user with account, subscription tier, and data history.

**Attributes**:
- `id` (UUID, primary key)
- `email` (string, unique, indexed)
- `created_at` (timestamp)
- `updated_at` (timestamp)
- `subscription_tier` (enum: 'free', 'premium')
- `token_stake_amount` (decimal, nullable) - Amount of tokens currently staked
- `token_stake_transaction_hash` (string, nullable) - Blockchain transaction hash
- `token_stake_expires_at` (timestamp, nullable) - When premium access expires
- `preferences` (JSON) - User preferences (notifications, data retention, etc.)
- `consent_tracking` (JSON) - Data collection consent history

**Relationships**:
- One-to-many: User → DataEntry
- One-to-many: User → DailyPulse
- One-to-one: User → PersonalBaseline

**Validation Rules**:
- Email must be valid format
- Subscription tier defaults to 'free'
- Token stake amount must be >= minimum required for premium
- Consent tracking must be maintained for privacy compliance

**State Transitions**:
- `free` → `premium`: When token stake is successful
- `premium` → `free`: When token stake expires or is unstaked

### DataEntry

**Purpose**: Individual data submission (food photo, movement, body metric, lifestyle input) with timestamp and metadata.

**Attributes**:
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key → User.id, indexed)
- `data_type` (enum: 'food_photo', 'movement', 'body_metric', 'lifestyle')
- `submitted_at` (timestamp, indexed) - When user submitted
- `data_date` (date, indexed) - The date this data represents (may differ from submitted_at)
- `data_content` (JSON) - Type-specific data:
  - `food_photo`: `{ image_url: string, extracted_info: { meal_type, estimated_nutrition, etc. } }`
  - `movement`: `{ steps, activity_type, duration_minutes, intensity, calories_estimated }`
  - `body_metric`: `{ metric_type, value, unit }`
  - `lifestyle`: `{ category, value, notes }`
- `processed_at` (timestamp, nullable) - When backend processed this entry
- `processing_status` (enum: 'pending', 'processing', 'completed', 'failed')
- `ai_analysis` (JSON, nullable) - AI-extracted insights (for food photos, etc.)

**Relationships**:
- Many-to-one: DataEntry → User

**Validation Rules**:
- User must exist
- Data type must be valid enum value
- Data content must match data_type schema
- Submitted_at must be <= current timestamp
- Data_date can be in the past (backfill) or today

**Indexes**:
- `(user_id, data_date, data_type)` - For user trend queries
- `(user_id, submitted_at)` - For recent submissions
- `(processing_status, submitted_at)` - For processing queue

### DailyPulse

**Purpose**: A calculated score (0-100) representing daily wellness guidance signal based on user's submitted data.

**Attributes**:
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key → User.id, indexed)
- `pulse_date` (date, indexed, unique per user) - The date this pulse represents
- `score` (integer, 0-100) - The Daily Pulse score
- `calculated_at` (timestamp) - When calculation occurred
- `calculation_inputs` (JSON) - All data entries used in calculation:
  - `food_entries`: [DataEntry.id]
  - `movement_entries`: [DataEntry.id]
  - `body_metric_entries`: [DataEntry.id]
  - `lifestyle_entries`: [DataEntry.id]
- `calculation_weights` (JSON) - How each input type was weighted:
  - `food_weight`: decimal
  - `movement_weight`: decimal
  - `body_metric_weight`: decimal
  - `lifestyle_weight`: decimal
- `guidance` (JSON) - Practical, non-clinical recommendations:
  - `food`: { suggestions: string[], rationale: string }
  - `movement`: { suggestions: string[], rationale: string }
  - `recovery`: { suggestions: string[], rationale: string }
- `explanation` (text) - Human-readable explanation of how score was calculated
- `baseline_comparison` (JSON) - How this pulse compares to user's baseline:
  - `vs_baseline_score`: decimal (difference from baseline)
  - `trend_direction`: enum ('improving', 'stable', 'declining')

**Relationships**:
- Many-to-one: DailyPulse → User
- Many-to-many: DailyPulse ↔ DataEntry (via calculation_inputs)

**Validation Rules**:
- Score must be between 0 and 100
- Pulse_date must be unique per user (one pulse per day)
- Calculation_inputs must reference valid DataEntry records
- Weights must sum to 1.0
- Guidance must not contain medical advice (validated in service layer)
- Explanation must be non-empty

**Indexes**:
- `(user_id, pulse_date)` - For user pulse history queries
- `(user_id, calculated_at)` - For recent pulses

### PersonalBaseline

**Purpose**: User-specific historical patterns and averages calculated from their data over time, used as reference point instead of generic benchmarks.

**Attributes**:
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key → User.id, unique, indexed)
- `calculated_at` (timestamp) - When baseline was last calculated
- `calculation_period_days` (integer) - Number of days of data used (minimum 14)
- `baseline_metrics` (JSON) - Calculated baseline values:
  - `average_daily_pulse`: decimal
  - `average_food_score`: decimal (derived from food entries)
  - `average_movement_score`: decimal (derived from movement entries)
  - `average_body_metric_trend`: JSON (trend direction for each metric type)
  - `average_lifestyle_patterns`: JSON (common lifestyle patterns)
- `data_points_count` (integer) - Total data entries used in calculation
- `confidence_level` (enum: 'low', 'medium', 'high') - How reliable baseline is:
  - 'low': < 30 days of data
  - 'medium': 30-90 days
  - 'high': > 90 days

**Relationships**:
- One-to-one: PersonalBaseline → User

**Validation Rules**:
- User must exist
- Calculation_period_days must be >= 14
- Data_points_count must match actual data entries used
- Confidence_level must be calculated based on data_points_count

**State Transitions**:
- Baseline recalculated when user has >= 14 new days of data
- Confidence level increases as more data accumulates

### TrendAnalysis

**Purpose**: Visualized representation of user data over time showing correlations, changes, and relationships relative to personal baseline (premium feature).

**Attributes**:
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key → User.id, indexed)
- `analysis_type` (enum: 'basic', 'advanced') - 'basic' for free, 'advanced' for premium
- `time_period_start` (date)
- `time_period_end` (date)
- `generated_at` (timestamp)
- `trend_data` (JSON) - Analysis results:
  - `data_type_trends`: JSON (trends per data type)
  - `correlations`: JSON (correlations between data types)
  - `pattern_insights`: JSON (identified patterns)
  - `baseline_deviations`: JSON (significant deviations from baseline)
- `visualization_config` (JSON) - Chart/visualization settings

**Relationships**:
- Many-to-one: TrendAnalysis → User

**Validation Rules**:
- User must have premium access for 'advanced' analysis_type
- Time_period_end must be >= time_period_start
- Time period must not exceed available user data range

### TokenStake

**Purpose**: User's staked tokens that unlock premium features, with staking/unstaking capabilities.

**Attributes**:
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key → User.id, indexed)
- `transaction_hash` (string, unique, indexed) - Blockchain transaction hash
- `stake_amount` (decimal) - Amount of tokens staked
- `staked_at` (timestamp)
- `unstaked_at` (timestamp, nullable)
- `status` (enum: 'active', 'unstaked', 'expired')
- `blockchain_network` (string) - 'ethereum' or 'polygon'
- `token_contract_address` (string) - ERC-20 token contract address

**Relationships**:
- Many-to-one: TokenStake → User

**Validation Rules**:
- Stake_amount must be >= minimum required for premium
- Transaction_hash must be unique and valid blockchain transaction
- Status transitions: 'active' → 'unstaked' or 'expired'
- Unstaked_at must be null when status is 'active'

**Indexes**:
- `(user_id, status)` - For active stakes per user
- `(transaction_hash)` - For blockchain verification

## Relationships Summary

```
User (1) ──< (many) DataEntry
User (1) ──< (many) DailyPulse
User (1) ──< (many) TrendAnalysis
User (1) ──< (many) TokenStake
User (1) ── (1) PersonalBaseline

DailyPulse (many) ── (many) DataEntry (via calculation_inputs JSON array)
```

## Data Retention

- **User data**: Retained as long as user account is active, user can delete
- **DataEntry**: Retained for trend analysis (1+ years), user can delete individual entries
- **DailyPulse**: Retained indefinitely for historical baseline comparison
- **PersonalBaseline**: Recalculated periodically, historical baselines archived
- **TokenStake**: Retained for audit purposes, even after unstaking

## Privacy Considerations

- All personal data encrypted at rest
- User consent tracked in User.consent_tracking
- Data export: Users can export all their data (JSON format)
- Data deletion: Users can delete data entries, which cascades to DailyPulse recalculation
- Anonymization: Historical data can be anonymized for aggregate analysis (future feature)
