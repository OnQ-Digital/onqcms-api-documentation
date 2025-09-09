# Seedooh

The Seedooh API provides integration endpoints for the Seedooh platform to interact with the onQ CMS. These endpoints handle campaign data retrieval, player information, and playlog generation for Seedooh's digital out-of-home advertising platform.

- [Seedooh](#seedooh)
  - [Campaigns Fetch](#campaigns-fetch)
    - [Endpoint: seedooh/campaigns\_fetch](#endpoint-seedoohcampaigns_fetch)
  - [Players Fetch](#players-fetch)
    - [Endpoint: seedooh/players\_fetch](#endpoint-seedoohplayers_fetch)
  - [Campaign Playlogs Generator](#campaign-playlogs-generator)
    - [Endpoint: seedooh/campaign\_playlogs\_generator](#endpoint-seedoohcampaign_playlogs_generator)
  - [Campaign Playlogs Fetch](#campaign-playlogs-fetch)
    - [Endpoint: seedooh/campaign\_playlogs\_fetch](#endpoint-seedoohcampaign_playlogs_fetch)

[Back to Home](README.md)

## Campaigns Fetch

### Endpoint: seedooh/campaigns_fetch

Retrieves campaign data for Seedooh Front End with player associations and campaign details.

**Request Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| campaign_id | integer | False | Campaign ID to fetch specific campaign |
| start_date_time | string | False | Start date time filter (YYYY-MM-DD HH:MM:SS) |
| end_date_time | string | False | End date time filter (YYYY-MM-DD HH:MM:SS) |
| min_bookings | integer | False | Minimum campaign bookings filter |
| max_bookings | integer | False | Maximum campaign bookings filter |
| campaign_statuses | array | False | Array of campaign statuses to filter |
| campaign_tag_ids | array | False | Array of campaign tag IDs to filter |
| mediaplayer_ids | array | False | Array of media player IDs to filter |
| mediaplayer_tag_ids | array | False | Array of media player tag IDs to filter |
| mediaplayer_cat_ids | array | False | Array of media player category IDs to filter |
| sort_field | string | False | Field to sort by (campaign_id, campaign_name, start_date, end_date, created_at) |
| sort_order | string | False | Sort order (asc, desc) |
| by_group | integer | False | Group by type (0 for division, 1 for group, 2 for details) |

**Request Example**

```json
{
  "campaign_id": 123,
  "start_date_time": "2024-01-01 00:00:00",
  "end_date_time": "2024-12-31 23:59:59",
  "campaign_statuses": ["live"],
  "mediaplayer_ids": [1144, 1153],
  "sort_field": "campaign_id",
  "sort_order": "asc",
  "by_group": 1
}
```

**Success Response**

- Status: 200 OK

```json
[
  {
    "campaign_id": 123,
    "campaign_title": "Summer Campaign 2024",
    "advertiser": "Brand Name",
    "media_buyer": "Agency Name",
    "campaign_categories": [],
    "campaign_creative_schedules": [
      {
        "campaign_sub_id": 456,
        "start_date": "2024-01-01",
        "end_date": "2024-12-31",
        "player_ids": [1144, 1153],
        "playlist_content": [33361, 33362]
      }
    ],
    "seedooh_verified": true,
    "filter_request_data": {
      "record_output_type": {
        "Seedooh": 123
      },
      "datetime_range": {
        "start": "2024-01-01T00:00:00",
        "end": "2024-12-31T23:59:59"
      },
      "player_ids": [1144, 1153],
      "playlist_ids": [456],
      "asset_ids": null,
      "event_types": null,
      "active_hours_record_only": true
    }
  }
]
```

**Error Response**

- Status: 400 Bad Request

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "campaign_id": "Campaign ID must be a positive integer"
  },
  "code": 400
}
```

- Status: 401 Unauthorized

```json
{
  "success": false,
  "error": "User is not logged in",
  "code": 401
}
```

- Status: 404 Not Found

```json
{
  "success": false,
  "error": "Campaign not found",
  "code": 404
}
```

[Top ↑](#seedooh)

## Players Fetch

### Endpoint: seedooh/players_fetch

Retrieves player data for Seedooh Front End with campaign associations and screenshot information.

**Request Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| name | string | False | Player name filter (partial match) |
| uid | string | False | Player unique ID (exact match) |
| folder | object | False | Folder filter object |
| folder.path | array | False | Array of folder path strings |
| folder.recurse | boolean | False | Include subfolders (default: true) |
| tags | array | False | Array of tag names to filter by |
| timezone | string | False | Timezone for date formatting (default: UTC) |
| company_group_id | integer | False | Company group ID for validation |

**Request Example**

```json
{
  "name": "demo",
  "folder": {
    "path": ["Australia", "Melbourne"],
    "recurse": true
  },
  "tags": ["demo", "AU"],
  "timezone": "Australia/Melbourne"
}
```

**Success Response**

- Status: 200 OK

```json
[
  {
    "mediaplayer_id": 1144,
    "player_id": "demo123",
    "site_name": "Demo Player",
    "store_name": "Demo Store",
    "resolution_width": 1920,
    "resolution_height": 1080,
    "last_update": "2024-01-15T10:35:53+10:00",
    "screenshot_update": "2024-01-15T10:35:53+10:00",
    "screenshot": "https://onqcms.com/api/seedooh/userfiles/user123/SCREENSHOT/screenshot.jpg",
    "folder_path": ["Australia", "Melbourne"],
    "tags": ["demo", "AU"],
    "campaigns": [
      {
        "campaign_id": 123,
        "campaign_name": "Summer Campaign 2024",
        "status": "live"
      }
    ]
  }
]
```

**Error Response**

- Status: 400 Bad Request

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "folder_path": "Folder path must be an array"
  },
  "code": 400
}
```

- Status: 401 Unauthorized

```json
{
  "error": "User is not logged in."
}
```

[Top ↑](#seedooh)

## Campaign Playlogs Generator

### Endpoint: seedooh/campaign_playlogs_generator

Initiates playlog generation for a specific campaign by communicating with the external generator service via NATS.

**Request Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| record_output_type | object | True | Record output type configuration |
| record_output_type.Seedooh | integer | True | Campaign ID for Seedooh output |
| datetime_range | object | True | Date range for playlog generation |
| datetime_range.start | string | True | Start date time (YYYY-MM-DD HH:MM:SS) |
| datetime_range.end | string | True | End date time (YYYY-MM-DD HH:MM:SS) |

**Request Example**

```json
{
  "record_output_type": {
    "Seedooh": 123
  },
  "datetime_range": {
    "start": "2024-01-01 00:00:00",
    "end": "2024-01-31 23:59:59"
  }
}
```

**Success Response**

- Status: 200 OK

```json
{
  "id": "report_20240115_143022_a1b2c3d4"
}
```

**Error Response**

- Status: 400 Bad Request

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "validation_errors": {
      "record_output_type": "record_output_type.Seedooh is required and must be numeric"
    }
  },
  "request_id": "req_20240115_143022_a1b2c3d4"
}
```

- Status: 401 Unauthorized

```json
{
  "success": false,
  "error": "Unauthorized",
  "request_id": "req_20240115_143022_a1b2c3d4"
}
```

- Status: 404 Not Found

```json
{
  "success": false,
  "error": "Campaign not found or access denied",
  "request_id": "req_20240115_143022_a1b2c3d4"
}
```

- Status: 503 Service Unavailable

```json
{
  "success": false,
  "error": "Failed to initiate report generation with external generator service",
  "request_id": "req_20240115_143022_a1b2c3d4"
}
```

[Top ↑](#seedooh)

## Campaign Playlogs Fetch

### Endpoint: seedooh/campaign_playlogs_fetch

Retrieves generated playlog data by report ID, checking status and returning parsed playlog data.

**Request Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | True | Report ID returned from playlogs generator |

**Request Example**

```json
{
  "id": "report_20240115_143022_a1b2c3d4"
}
```

**Success Response**

The endpoint returns a JSON array of playlog data when the report status is "Complete", or a status object when "InProgress". For all other states, the response will be an error.

- Status: 200 OK (Complete)

```json
[
  {
    "player_id": 1144,
    "asset_id": 33361,
    "play_time": "2024-01-15 10:30:00",
    "duration": 10,
    "campaign_id": 123,
    "campaign_name": "Summer Campaign 2024"
  },
  {
    "player_id": 1144,
    "asset_id": 33362,
    "play_time": "2024-01-15 10:30:10",
    "duration": 15,
    "campaign_id": 123,
    "campaign_name": "Summer Campaign 2024"
  }
]
```

- Status: 200 OK (InProgress)

```json
{
  "id": "report_20240115_143022_a1b2c3d4",
  "state": "InProgress",
  "url": null
}
```

**Error Response**

- Status: 400 Bad Request

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "id": "Report ID is required"
  },
  "code": 400
}
```

- Status: 401 Unauthorized

```json
{
  "success": false,
  "error": "User is not logged in",
  "code": 401
}
```

- Status: 500 Internal Server Error

```json
{
  "error": "Failed to read report file"
}
```

- Status: 503 Service Unavailable

```json
{
  "success": false,
  "error": "Report status service is currently unavailable",
  "code": 503
}
```

[Top ↑](#seedooh)

[Back to Home](README.md)
