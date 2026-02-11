# External Import

The External Import API allows external systems (e.g., VAMS) to programmatically create, update, and deactivate campaigns on the CMS.

**Sync model**: Full-state. Each call sends the complete list of active bookings; the system reconciles to match. Expired campaigns are invisible to the sync process.

[Back to Home](README.md)

# TOC
- [1. Sync Endpoint](#1-sync-endpoint)
  - [1.1. Request Format](#11-request-format)
  - [1.2. Response Format](#12-response-format)
  - [1.3. Sync Behavior](#13-sync-behavior)
- [2. Status Endpoint](#2-status-endpoint)
  - [2.1. Request Format (GET)](#21-request-format-get)
  - [2.2. Request Format (POST)](#22-request-format-post)
  - [2.3. Response Format](#23-response-format)
- [3. Config Endpoints (Admin)](#3-config-endpoints-admin)
  - [3.1. Fetch Config](#31-fetch-config)
  - [3.2. Save Config](#32-save-config)

---

# 1. Sync Endpoint

**Endpoint**: `POST /api/external_import_sync.php`

**Authentication**: Bearer token (API key) or session-based authentication.

## 1.1. Request Format

**Headers**:
| Header | Value | Required |
|--------|-------|----------|
| Content-Type | application/json | Yes |
| Authorization | Bearer {api_key} | Yes (or session cookie) |

**Request Body**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| system | string | No | External system identifier. Default: `"vams"` |
| company_group_id | integer | No | Must match authenticated user's group |
| dry_run | boolean | No | If true, returns what would happen without making changes |
| items | array | Yes | Array of booking items from the external system |

**VAMS Item Schema**:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| orderNumber | string/integer | Yes | Order identifier. Items with the same orderNumber form one campaign. |
| code | string | Yes | Player/store code. Matched against `store_name` and `site_name` via LIKE search. |
| startDate | string | Yes | Start date in `YYYY-MM-DD` format |
| endDate | string | Yes | End date in `YYYY-MM-DD` format |
| slot | string/integer | Yes | Slot identifier |
| url | string or array | Yes | Content URL(s). CloudFront signed URLs are supported. The filename UUID is extracted as the stable content identifier. |
| brand | string | No | Brand/advertiser name |
| advertiserName | string | No | Alternative to `brand` |
| category | string | No | Category name |
| vaType | string | No | External slot type name (used for slot mapping) |
| name | string | No | Alternative external slot name |

**Example Request**:

```json
{
    "system": "vams",
    "items": [
        {
            "orderNumber": "3940",
            "code": "3201DS0061",
            "startDate": "2026-01-12",
            "endDate": "2026-02-15",
            "slot": 2,
            "vaType": "Behind POS Counter",
            "brand": "TOM FORD",
            "category": "Beauty",
            "url": "https://example.com/path/4vwhq2gg-zju2-lqlm-m1hn-e93xwgqrp9aa.mp4?Expires=..."
        },
        {
            "orderNumber": "3940",
            "code": "3201DS0072",
            "startDate": "2026-01-12",
            "endDate": "2026-02-15",
            "slot": 2,
            "vaType": "Behind POS Counter",
            "brand": "TOM FORD",
            "category": "Beauty",
            "url": "https://example.com/path/4vwhq2gg-zju2-lqlm-m1hn-e93xwgqrp9aa.mp4?Expires=..."
        }
    ]
}
```

## 1.2. Response Format

```json
{
    "result": "ok",
    "summary": {
        "total_items_received": 2,
        "campaigns_created": 1,
        "campaigns_updated": 0,
        "campaigns_ended": 0,
        "campaigns_deleted": 0,
        "skipped_linked": 0
    },
    "campaigns": [
        {
            "campaign_name": "VAMS #3940 - TOM FORD",
            "campaign_id": 456,
            "action": "created",
            "sub_campaigns": 1,
            "players_matched": 2
        }
    ],
    "warnings": [],
    "content_status": {
        "downloaded": 1,
        "failed": 0,
        "encoding_queued": 0,
        "details": [
            {
                "content_id": "4vwhq2gg-zju2-lqlm-m1hn-e93xwgqrp9aa",
                "file_content_id": 1234,
                "status": "ready"
            }
        ]
    },
    "errors": []
}
```

**Campaign Action Values**:

| Action | Description |
|--------|-------------|
| `created` | New campaign created |
| `updated` | Existing live managed campaign updated |
| `deleted` | Campaign hard-deleted (never played) |
| `ended` | Campaign end_date set to now (has playback history) |
| `skipped_linked` | Campaign is tagged as linked; not modified by sync |

**Content Status Values**:

| Status | Description |
|--------|-------------|
| `ready` | Content downloaded and available |
| `encoding_queued` | Video queued for h264 baseline encoding |
| `download_failed` | Download failed (see `error` field) |
| `rejected` | Unsupported format |

## 1.3. Sync Behavior

**Grouping rules**:
- Items with the same `orderNumber` become one CMS campaign (named `"VAMS #{orderNumber} - {brand}"`)
- Items with identical `startDate + endDate + slot + content` merge into one sub-campaign (players are merged)
- Items with different schedules, slots, or content become separate sub-campaigns
- If one external slot maps to multiple internal slot groups, one sub-campaign per group is created

**Lifecycle rules**:
- New orders with no matching live campaign: CREATE
- Orders matching a live managed campaign: UPDATE (rebuild all sub-campaigns)
- Orders matching a live linked campaign: SKIP
- Live managed campaigns missing from payload, never played: HARD DELETE
- Live managed campaigns missing from payload, has played: SET end_date = NOW
- Orders matching only expired campaigns: CREATE new (expired campaigns are invisible)

**Content dedup**: Content is identified by the filename UUID extracted from the URL path (the `content_id`). If the same `content_id` exists in `file_content.file_uid`, the existing content is reused without re-downloading.

---

# 2. Status Endpoint

**Endpoint**: `GET /api/external_import_status.php` or `POST /api/external_import_status.php`

**Authentication**: Bearer token (API key) or session-based authentication.

Check content download and encoding status after a sync.

## 2.1. Request Format (GET)

```
GET /api/external_import_status.php?content_ids=4vwhq2gg-zju2-lqlm-m1hn-e93xwgqrp9aa,a1b2c3d4-e5f6-7890-abcd-ef1234567890
```

## 2.2. Request Format (POST)

```json
{
    "content_ids": [
        "4vwhq2gg-zju2-lqlm-m1hn-e93xwgqrp9aa",
        "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
    ]
}
```

## 2.3. Response Format

```json
{
    "result": "ok",
    "content_status": [
        {
            "content_id": "4vwhq2gg-zju2-lqlm-m1hn-e93xwgqrp9aa",
            "file_content_id": 1234,
            "status": "ready",
            "width": 1920,
            "height": 1080,
            "format": "mp4"
        },
        {
            "content_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
            "file_content_id": 1235,
            "status": "encoding_queued",
            "width": 1920,
            "height": 1080,
            "format": "mp4"
        }
    ]
}
```

**Status Values**:

| Status | `encode_wait` value | Description |
|--------|---------------------|-------------|
| `ready` | 0 | Content is ready for playback |
| `encoding_queued` | 1 | Video is queued for encoding |
| `encoding_error` | 2 | Encoding failed |
| `encoding_timeout` | 3 | Encoding timed out |
| `not_found` | N/A | Content ID not found in system |

---

# 3. Config Endpoints (Admin)

These endpoints are for the admin UI. Authentication: session-based only (admin users).

## 3.1. Fetch Config

**Endpoint**: `POST /api/external_import_config_fetch.php`

**Request**:
```json
{
    "system_name": "vams"
}
```

Omit `system_name` to fetch all configs for the group.

**Response**:
```json
{
    "config": {
        "config_id": 1,
        "company_group_id": 42,
        "system_name": "vams",
        "slot_mapping": {
            "Behind POS Counter": ["pos-counter-left", "pos-counter-right"],
            "Main Entrance": ["entrance"]
        },
        "managed_tag_name": "vams-managed",
        "linked_tag_name": "vams-linked",
        "is_active": 1
    }
}
```

## 3.2. Save Config

**Endpoint**: `POST /api/external_import_config_save.php`

**Request**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| system_name | string | Yes | External system identifier |
| slot_mapping | object | Yes | `{"External Name": ["internal_group_1", ...]}` |
| managed_tag_name | string | Yes | Tag name for auto-managed campaigns |
| linked_tag_name | string | Yes | Tag name for user-managed (skipped) campaigns |
| is_active | integer | No | 1 = active, 0 = inactive. Default: 1 |

**Request Example**:
```json
{
    "system_name": "vams",
    "slot_mapping": {
        "Behind POS Counter": ["pos-counter-left", "pos-counter-right"],
        "Main Entrance": ["entrance"]
    },
    "managed_tag_name": "vams-managed",
    "linked_tag_name": "vams-linked",
    "is_active": 1
}
```

**Response**:
```json
{
    "result": "ok",
    "config": { ... }
}
```
