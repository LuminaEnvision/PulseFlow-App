# Mobile API Integration Guide

**Date**: 2026-01-27
**Feature**: 001-pulse-platform

## Authentication

All API requests require JWT authentication via Bearer token in Authorization header:

```
Authorization: Bearer <jwt_token>
```

## Base URL

- Production: `https://api.pulse.app/v1`
- Development: `http://localhost:3000/v1`

## Key Endpoints

### Data Submission

**POST /data/entries**
- Submit food photos, movement data, body metrics, or lifestyle inputs
- Multipart form-data for images
- JSON for other data types
- Returns: Created DataEntry object

**GET /data/entries**
- Retrieve user's data entries
- Query params: `data_type`, `start_date`, `end_date`, `limit`
- Returns: Array of DataEntry objects

### Daily Pulse

**GET /pulse/daily?date=YYYY-MM-DD**
- Get Daily Pulse for specific date (defaults to today)
- Returns: DailyPulse object with score, guidance, explanation

**POST /pulse/daily**
- Trigger Daily Pulse calculation
- Body: `{ "date": "YYYY-MM-DD" }` (optional)
- Returns: Calculated DailyPulse object

**GET /pulse/history**
- Get Daily Pulse history
- Query params: `start_date`, `end_date`, `limit`
- Returns: Array of DailyPulse objects

### Trends

**GET /trends?analysis_type=basic|advanced**
- Get trend analysis (basic for free, advanced for premium)
- Query params: `start_date`, `end_date`
- Returns: TrendAnalysis object
- Premium required for `analysis_type=advanced`

### Premium

**POST /premium/stake**
- Stake tokens for premium access
- Body: `{ "amount": number, "transaction_hash": string, "network": "ethereum"|"polygon" }`
- Returns: Premium status

**POST /premium/unstake**
- Unstake tokens and revoke premium
- Returns: Status confirmation

### Baseline

**GET /baseline**
- Get user's personal baseline
- Returns: PersonalBaseline object
- 404 if insufficient data (< 14 days)

## Error Handling

All errors follow standard format:

```json
{
  "error": "Error code",
  "message": "Human-readable message",
  "code": 400
}
```

Common status codes:
- `200`: Success
- `201`: Created
- `400`: Bad Request (invalid input)
- `401`: Unauthorized (invalid/missing token)
- `403`: Forbidden (premium feature, insufficient tokens)
- `404`: Not Found
- `429`: Rate Limit Exceeded (free tier AI limit)
- `500`: Server Error

## Rate Limiting

- Free tier: 10 AI requests per day
- Premium: Unlimited AI requests
- Other endpoints: 100 requests per minute per user

Rate limit headers:
- `X-RateLimit-Limit`: Request limit
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Reset timestamp

## Mobile-Specific Considerations

### Image Upload
- Compress images before upload (max 5MB)
- Use multipart/form-data
- Show upload progress
- Handle network interruptions gracefully

### Offline Support
- Cache Daily Pulse and recent trends locally
- Queue data submissions for sync when online
- Show offline indicator when API unavailable

### Performance
- Lazy load trend data
- Paginate data entry lists
- Cache baseline data (recalculate on data submission)

### Error Recovery
- Retry failed requests with exponential backoff
- Handle token expiration (refresh token)
- Graceful degradation when AI services unavailable

## Example Requests

### Submit Food Photo
```typescript
const formData = new FormData();
formData.append('data_type', 'food_photo');
formData.append('data_date', '2026-01-27');
formData.append('image', photoFile);

const response = await fetch(`${API_BASE}/data/entries`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`
  },
  body: formData
});
```

### Get Daily Pulse
```typescript
const response = await fetch(`${API_BASE}/pulse/daily?date=2026-01-27`, {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
const pulse = await response.json();
```

### Submit Movement Data
```typescript
const response = await fetch(`${API_BASE}/data/entries`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data_type: 'movement',
    data_date: '2026-01-27',
    data_content: {
      steps: 8500,
      activity_type: 'walking',
      duration_minutes: 45,
      intensity: 'moderate'
    }
  })
});
```
