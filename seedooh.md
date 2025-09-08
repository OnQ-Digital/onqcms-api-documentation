# Seedooh Integration APIs

The Seedooh integration provides four specialized RESTful APIs that enable external access to campaign, player, and playlog data. These APIs are designed specifically for third-party integrations and follow modern API design principles with Bearer token authentication.

**Base URL**: `https://onqcms.com/api/seedooh/`

- [Campaigns API](#campaigns-api)
- [Players API](#players-api)
- [Campaign Playlogs APIs](#campaign-playlogs-apis)
  - [Campaign Playlogs Generator API](#campaign-playlogs-generator-api)
  - [Campaign Playlogs Fetch API](#campaign-playlogs-fetch-api)
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

### Campaign Endpoint

`POST /api/seedooh/campaigns_fetch`

### Campaign Request Method

**POST**: JSON body required

### Headers

- `Is-Active: true` (optional) - Filter to return only active campaigns

### Campaign Request Parameters

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

### Campaign Response Parameters

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
curl -X POST "https://onqcms.com/api/seedooh/campaigns_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{}'
```

#### Get Active Campaigns Only

```bash
curl -X POST "https://onqcms.com/api/seedooh/campaigns_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -H "Is-Active: true" \
  -d '{}'
```

#### Get Specific Campaign

```bash
curl -X POST "https://onqcms.com/api/seedooh/campaigns_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "campaign_id": 43
  }'
```

#### Advanced Filtering

```bash
curl -X POST "https://onqcms.com/api/seedooh/campaigns_fetch" \
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

### Player Endpoint

`POST /api/seedooh/players_fetch`

### Player Request Method

**POST**: JSON body required

### Player Request Parameters

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

### Player Response Parameters

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

### Player Example Requests

#### Get All Players

```bash
curl -X POST "https://onqcms.com/api/seedooh/players_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{}'
```

#### Filter by Name

```bash
curl -X POST "https://onqcms.com/api/seedooh/players_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Central Station",
    "company_group_id": 48,
    "timezone": "Australia/Sydney"
  }'
```

#### Filter by Company Group and Timezone

```bash
curl -X POST "https://onqcms.com/api/seedooh/players_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "company_group_id": 48,
    "timezone": "Australia/Sydney"
  }'
```

---

## Campaign Playlogs APIs

The Campaign Playlogs system consists of two endpoints that work together to provide asynchronous report generation and retrieval. This architecture enables efficient handling of large datasets through background processing.

### Workflow Overview

1. **Generator API**: Initiates report generation and returns a report ID
2. **Fetch API**: Uses the report ID to retrieve the completed playlogs data

---

## Campaign Playlogs Generator API

Initiates asynchronous report generation for campaign playlogs data.

### Generator Endpoint

`POST /api/seedooh/campaign_playlogs_generator`

### Generator Request Method

**POST**: JSON body required

### Generator Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| campaign_id | integer | Yes | Campaign ID (numeric) |
| start_date | string | Yes | Start date (YYYY-MM-DD) |
| end_date | string | Yes | End date (YYYY-MM-DD) |

### Response Structure

```json
{
  "success": true,
  "timestamp": "2025-09-08T04:33:49+00:00",
  "data": {
    "success": true,
    "report_id": "report_68be5cad96587",
    "status": "processing",
    "message": "Report generation started by service 1",
    "estimated_completion": "2025-09-08 04:38:49",
    "request_id": "req_20250908043349_d1921b8c18cd53d5"
  },
  "message": "Report generation initiated successfully",
  "request_id": "req_20250908043349_8c03f70a",
  "metadata": {
    "campaign_name": "temp campaign",
    "date_range": {
      "start": "2024-01-01",
      "end": "2024-01-31"
    }
  }
}
```

### Generator Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| success | boolean | Overall operation success status |
| timestamp | string | Response timestamp (ISO 8601) |
| data | object | Report generation details |
| data.report_id | string | Unique report identifier for fetching |
| data.status | string | Report status ("processing", "completed", "failed") |
| data.message | string | Status message |
| data.estimated_completion | string | Estimated completion time |
| request_id | string | Unique request identifier |
| metadata | object | Campaign and date range information |

### Generator Example Request

```bash
curl -X POST "https://onqcms.com/api/seedooh/campaign_playlogs_generator" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "campaign_id": 43,
    "start_date": "2024-01-01",
    "end_date": "2024-01-31"
  }'
```

---

## Campaign Playlogs Fetch API

Retrieves completed playlogs data using a report ID from the generator.

### Fetch Endpoint

`POST /api/seedooh/campaign_playlogs_fetch`

### Fetch Request Method

**POST**: JSON body required

### Fetch Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| report_id | string | Yes | Report ID from generator API |

### Fetch Response Structure

#### Success Response (Report Ready)

```json
{
  "success": true,
  "timestamp": "2025-09-08T04:35:00+00:00",
  "data": {
    "status": "completed",
    "playlogs": [
      {
        "campaign_id": "43",
        "schedule_id": "sch_456",
        "player_id": "P-001",
        "timestamp": "2024-01-15T14:30:00+10:00",
        "duration": 15,
        "creative_name": "Summer Banner"
      }
    ],
    "total_playlogs": 50,
    "total_duration": 8232,
    "date_range": {
      "start": "2024-01-01",
      "end": "2024-01-31"
    }
  },
  "message": "Playlogs retrieved successfully"
}
```

#### Processing Response (Report Not Ready)

```json
{
  "success": true,
  "timestamp": "2025-09-08T04:33:50+00:00",
  "data": {
    "status": "processing",
    "message": "Report is still being generated",
    "estimated_completion": "2025-09-08 04:38:49"
  },
  "message": "Report is not ready yet"
}
```

### Fetch Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| success | boolean | Overall operation success status |
| timestamp | string | Response timestamp (ISO 8601) |
| data | object | Playlogs data or status information |
| data.status | string | Report status ("completed", "processing", "failed") |
| data.playlogs | array | Array of playlog objects (when completed) |
| data.total_playlogs | integer | Total number of playlogs |
| data.total_duration | integer | Total duration in seconds |
| message | string | Status message |

### Playlog Object Structure

| Parameter | Type | Description |
|-----------|------|-------------|
| campaign_id | string | Campaign identifier |
| schedule_id | string | Schedule identifier (prefixed with "sch_") |
| player_id | string | Player identifier |
| timestamp | string | Playlog timestamp (ISO 8601) |
| duration | integer | Play duration in seconds |
| creative_name | string | Creative asset name |

### Fetch Example Request

```bash
curl -X POST "https://onqcms.com/api/seedooh/campaign_playlogs_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "report_id": "report_68be5cad96587"
  }'
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

#### Standard Error Response

```json
{
  "error": "Description of the error"
}
```

#### Detailed Error Response (New APIs)

```json
{
  "success": false,
  "timestamp": "2025-09-08T04:33:49+00:00",
  "error": "Detailed error message",
  "request_id": "req_20250908043349_8c03f70a",
  "validation_errors": {
    "campaign_id": "Campaign ID is required and must be numeric",
    "start_date": "Start date is required and must be in YYYY-MM-DD format"
  }
}
```

### Common Errors

- "Invalid or missing API key"
- "User is not logged in"
- "Malformed JSON"
- "Invalid company group ID"
- "campaign_id is required and must be numeric"
- "Only POST method is allowed"
- "Empty input"
- "Invalid report ID format"
- "Report not found"
- "Report generation failed"

---

## Technical Architecture

### NATS Integration

The Campaign Playlogs APIs use NATS messaging for asynchronous communication with external services:

- **Generator API**: Sends requests to external report generation service via NATS
- **Fetch API**: Communicates with external report status service via NATS
- **Subjects**:
  - `GENERATOR_SUBJECT`: For report generation requests
  - `RETRIEVER_SUBJECT`: For report status and retrieval requests

### Azure Blob Storage

Completed reports are stored in Azure Blob Storage as SQL files:

- **Storage**: Azure Blob Storage container for report files
- **Format**: SQL files containing INSERT statements with playlog data
- **Processing**: SQL files are parsed and converted to JSON for API responses
- **Security**: Files are accessed using Azure Storage connection strings

### Asynchronous Processing

The playlogs system is designed for handling large datasets:

1. **Request Initiation**: Generator API creates a report request
2. **Background Processing**: External service processes the request asynchronously
3. **Status Monitoring**: Fetch API checks report status periodically
4. **Data Retrieval**: Once complete, playlogs are retrieved from Azure Blob Storage

---

## Security Features

### API Authentication

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

Use POST requests with JSON body for testing:

```bash
# Test Campaigns API
curl -X POST "https://onqcms.com/api/seedooh/campaigns_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{"campaign_id": 43}'

# Test Players API
curl -X POST "https://onqcms.com/api/seedooh/players_fetch" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{"company_group_id": 48}'

# Test Playlogs Generator
curl -X POST "https://onqcms.com/api/seedooh/campaign_playlogs_generator" \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -d '{"campaign_id": 43, "start_date": "2024-01-01", "end_date": "2024-01-31"}'
```

### API Testing Tools

- **Postman**: Use POST method with JSON body
- **curl**: Examples provided above
- **JavaScript fetch**: Use proper POST method and JSON body

### Development Testing

Session-based authentication works in browser when logged into the CMS. All APIs support both Bearer token and session authentication.

### Complete Workflow Testing

1. **Generate Report**: Call generator API to get report ID
2. **Check Status**: Use fetch API to check if report is ready
3. **Retrieve Data**: Once status is "completed", playlogs data will be returned

---

[Home](README.md)
