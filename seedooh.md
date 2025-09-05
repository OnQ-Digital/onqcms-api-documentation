# Seedooh Integration APIs

The Seedooh integration provides three specialized RESTful APIs that enable external access to campaign, player, and playlog data. These APIs are designed specifically for third-party integrations and follow modern API design principles with Bearer token authentication.

**Base URL**: `https://onqcms.com/api/seedooh/`

- [Campaigns API](#campaigns-api)
- [Players API](#players-api)
- [Campaign Playlogs API](#campaign-playlogs-api)
- [Authentication](#authentication)
- [Error Handling](#error-handling)

[Home](README.md)

## Authentication

All Seedooh APIs use Bearer token authentication with API keys stored in the database. Unlike standard OnQ APIs, these endpoints also support session-based authentication for browser access.

### Headers Required

- `Authorization: Bearer <api_key>`
- `Content-Type: application/json`

### Session Fallback

APIs also support session-based authentication for browser access when no Bearer token is provided.

## Campaigns API

Retrieves campaign data with creative schedules and metadata. Supports active campaign filtering.

### Endpoint

`GET/POST /api/seedooh/campaigns`

### Request Methods

- **GET**: Simple request returning all campaigns
- **POST**: Advanced filtering with JSON body

### Headers

- `Is-Active: true` (optional) - Filter to return only active campaigns

### Request Parameters (POST)

| Parameter | Type | Description |
|-----------|------|-------------|
| campaign_id | integer | Specific campaign ID |
| start_date_time | string | Start date filter (YYYY-MM-DD HH:MM:SS) |
| end_date_time | string | End date filter (YYYY-MM-DD HH:MM:SS) |
| campaign_statuses | array | Campaign status filters ["live", "planned"] |
| campaign_tag_ids | array | Campaign tag ID filters [1, 2, 3] |
| mediaplayer_ids | array | Media player ID filters [101, 102] |
| mediaplayer_tag_ids | array | Media player tag ID filters [5, 6] |
| mediaplayer_cat_ids | array | Media player category ID filters [7, 8] |
| sort_field | string | Sort field (default: campaign_id) |
| sort_order | string | Sort order: asc/desc (default: asc) |
| by_group | integer | Group by type: 0/1 (default: 1) |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| campaign_id | integer | Unique campaign identifier |
| campaign_title | string | Campaign title/name |
| advertiser | string | Advertiser name |
| media_buyer | string | Media buyer name |
| campaign_categories | array | Campaign categories |
| campaign_creative_schedules | array | Creative schedule objects |
| seedooh_verified | boolean | Seedooh verification status |

### Creative Schedule Object

| Parameter | Type | Description |
|-----------|------|-------------|
| schedule_id | string | Schedule identifier (prefixed with "sch_") |
| start_date | string | Schedule start date (YYYY-MM-DD) |
| end_date | string | Schedule end date (YYYY-MM-DD) |
| creative_id | string | Creative identifier (prefixed with "cr_") |
| creative_name | string | Creative asset name |
| creative_url | string | URL to creative asset |
| creative_width | integer | Creative width in pixels |
| creative_height | integer | Creative height in pixels |
| saturation_frequency | integer | Frequency per loop |
| purchase_type | string | Purchase type (e.g., "Paid") |
| player_ids | array | Array of player IDs |

### Campaign JSON Object

```json
{
  "campaign_id": 123,
  "campaign_title": "Summer Campaign 2025",
  "advertiser": "ACME Corp",
  "media_buyer": "Zenith Media",
  "campaign_categories": [],
  "campaign_creative_schedules": [
    {
      "schedule_id": "sch_456",
      "start_date": "2025-09-01",
      "end_date": "2025-09-30",
      "creative_id": "cr_789",
      "creative_name": "Summer Banner",
      "creative_url": "https://cdn.example.com/creative.jpg",
      "creative_width": 1920,
      "creative_height": 1080,
      "saturation_frequency": 8,
      "purchase_type": "Paid",
      "player_ids": ["P-001", "P-002"]
    }
  ],
  "seedooh_verified": true
}
```

### Example Requests

#### Get All Campaigns

```bash
curl "https://onqcms.com/api/seedooh/campaigns" \
  -H "Authorization: Bearer <api_key>"
```

#### Get Active Campaigns Only

```bash
curl "https://onqcms.com/api/seedooh/campaigns" \
  -H "Authorization: Bearer <api_key>" \
  -H "Is-Active: true"
```

#### Advanced Filtering (POST)

```bash
curl -X POST "https://onqcms.com/api/seedooh/campaigns" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "by_group": 1,
    "sort_order": "desc",
    "campaign_statuses": ["live", "planned"]
  }'
```

---

## Players API

Retrieves player (panel) information with location metadata and operational details.

### Endpoint

`GET/POST /api/seedooh/players`

### Request Methods

- **GET**: Returns all players for the authenticated user's group
- **POST**: Filtered request with optional parameters

### Request Parameters (POST)

| Parameter | Type | Description |
|-----------|------|-------------|
| name | string | Player name filter |
| uid | string | Specific player UID |
| folder | object | Folder filter object |
| folder.path | array | Folder path array ["Transport", "Indoor"] |
| folder.recurse | boolean | Include subfolders (default: true) |
| tags | array | Tag filters ["transport", "indoor"] |
| company_group_id | integer | Company group ID |
| folder_name | string | Specific folder name |
| timezone | string | Timezone (e.g., "Australia/Sydney") |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| player_name | string | Player display name |
| player_id | string | Unique player identifier |
| address | string | Player physical address |
| region | string | Geographic region |
| state | string | State/province |
| format | string | Display format type |
| category | string | Player category |
| longitude | string | GPS longitude coordinate |
| latitude | string | GPS latitude coordinate |
| pixel_width | integer | Display width in pixels |
| pixel_height | integer | Display height in pixels |
| max_slots_per_loop | integer | Maximum slots per playback loop |
| default_slot_duration | integer | Default slot duration in seconds |
| n_screens | integer | Number of screens |
| operating_hours | array | Operating hours array |

### Player JSON Object

```json
{
  "player_name": "Central Station Billboard",
  "player_id": "P-001",
  "address": "123 Railway Parade, Sydney",
  "region": "Metro",
  "state": "NSW",
  "format": "Digital Large",
  "category": "Transport",
  "longitude": "151.2093",
  "latitude": "-33.8688",
  "pixel_width": 1920,
  "pixel_height": 1080,
  "max_slots_per_loop": 8,
  "default_slot_duration": 15,
  "n_screens": 1,
  "operating_hours": ["06:00-23:00"]
}
```

### Example Requests

#### Get All Players

```bash
curl "https://onqcms.com/api/seedooh/players" \
  -H "Authorization: Bearer <api_key>"
```

#### Filter by Name (POST)

```bash
curl -X POST "https://onqcms.com/api/seedooh/players" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Central Station",
    "company_group_id": 48,
    "timezone": "Australia/Sydney"
  }'
```

---

## Campaign Playlogs API

Retrieves timestamped playlogs showing when and where campaigns were played.

### Endpoint

`GET /api/seedooh/campaign_playlogs`

### Request Method

**GET**: Query parameters only

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| campaign_id | integer | Yes | Campaign ID (numeric) |
| start_datetime | string | Yes | Start datetime (ISO 8601) |
| end_datetime | string | Yes | End datetime (ISO 8601) |
| schedule_id | integer | No | Specific schedule ID |
| player_id | string | No | Specific player ID |
| sort_field | string | No | Sort field (default: timestamp) |
| sort_order | string | No | Sort order: asc/desc (default: desc) |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| campaign_id | string | Campaign identifier (prefixed with "cmp_") |
| schedule_id | string | Schedule identifier (prefixed with "sch_") |
| player_id | string | Player identifier |
| timestamp | string | Playlog timestamp (ISO 8601) |

### Playlog JSON Object

```json
{
  "campaign_id": "cmp_123",
  "schedule_id": "sch_456",
  "player_id": "P-001",
  "timestamp": "2025-09-01T14:30:00+10:00"
}
```

### Example Request

```bash
curl "https://onqcms.com/api/seedooh/campaign_playlogs?campaign_id=123&start_datetime=2025-09-01T00:00:00%2B10:00&end_datetime=2025-09-30T23:59:59%2B10:00" \
  -H "Authorization: Bearer <api_key>"
```

---

## Error Handling

All APIs return appropriate HTTP status codes and JSON error responses.

### Status Codes

- `200 OK` - Success
- `400 Bad Request` - Invalid parameters
- `401 Unauthorized` - Authentication failed
- `404 Not Found` - Resource not found
- `500 Internal Server Error` - Server error

### Error Response Format

```json
{
  "error": "Description of the error"
}
```

### Common Errors

- "Invalid or missing API key"
- "User is not logged in"
- "Malformed JSON"
- "Invalid company group ID"
- "campaign_id is required and must be numeric"

---

## Security Features

### Authentication

- **Bearer Token**: Primary authentication method using API keys from database
- **Session Fallback**: Browser-based session authentication for development
- **API Key Validation**: Keys validated against users table

### Authorization

- **Company Group Isolation**: All data filtered by user's company group
- **Session Validation**: Proper session management for browser access

### Input Validation

- **Parameter Validation**: All inputs validated for type and format
- **SQL Injection Prevention**: Prepared statements used throughout
- **JSON Validation**: Proper JSON parsing with error handling

---

## Performance Considerations

### Optimization

- **Prepared Statements**: All database queries use prepared statements
- **Query Limits**: 1000 record limit on playlogs API
- **Indexing**: Ensure indexes on company_group_id, campaign_id, date fields

### Monitoring

- **NATS Events**: All API calls logged to NATS for monitoring
- **Error Logging**: Comprehensive error logging for debugging
- **Performance Metrics**: Track query execution times

---

## Testing

### Browser Testing

Use URL parameters for easy testing:

```
https://onqcms.com/api/seedooh/campaigns?is_active=true
```

### API Testing Tools

- **Postman**: Use `Is-Active: true` header format
- **curl**: Examples provided above
- **JavaScript fetch**: Use proper header names

### Development Testing

Session-based authentication works in browser when logged into the CMS.

---

[Home](README.md)
