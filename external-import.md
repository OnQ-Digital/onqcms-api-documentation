# External Import

The External Import API allows external systems (e.g., VAMS) to programmatically create, update, and deactivate campaigns on the CMS.

**Sync model**: Full-state. Each call sends the complete list of active bookings; the system reconciles to match. Expired campaigns are invisible to the sync process.

[Back to Home](README.md)

# TOC
- [1. Sync Endpoint](#1-sync-endpoint)
  - [1.1. Request Format](#11-request-format)
  - [1.2. Response Format](#12-response-format)
  - [1.3. Warnings](#13-warnings)
  - [1.4. Errors](#14-errors)
  - [1.5. Sync Behavior](#15-sync-behavior)
- [2. Status Endpoint](#2-status-endpoint)
  - [2.1. Request Format (GET)](#21-request-format-get)
  - [2.2. Request Format (POST)](#22-request-format-post)
  - [2.3. Response Format](#23-response-format)

---

# 1. Sync Endpoint

**Endpoint**: `POST /api/external_import_sync.php`

**Authentication**: Bearer token (API key).

```
Authorization: Bearer {api_key}
```

## 1.1. Request Format

**Headers**:
| Header | Value | Required |
|--------|-------|----------|
| Content-Type | application/json | Yes |
| Authorization | Bearer {api_key} | Yes |

**Request Body**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| system | string | No | External system identifier. Default: `"vams"` |
| company_group_id | integer | No | Must match authenticated user's group |
| dry_run | boolean | No | If `true`, returns what would happen without making any changes. Useful for testing. |
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

**Dry Run Example**:

```json
{
    "system": "vams",
    "dry_run": true,
    "items": [ ... ]
}
```

A dry run performs translation and classification (which campaigns would be created, updated, or deactivated) but does not make any changes. The response will have `"result": "dry_run"` instead of `"ok"`.

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

**Result Values**:

| Value | Description |
|-------|-------------|
| `ok` | Sync completed successfully |
| `dry_run` | Dry run completed; no changes made |
| `error` | Sync could not run (e.g., no config found) |

**Campaign Action Values**:

| Action | Description |
|--------|-------------|
| `created` | New campaign created |
| `updated` | Existing live managed campaign updated (all sub-campaigns rebuilt) |
| `deleted` | Campaign hard-deleted (campaign had never been played) |
| `ended` | Campaign end_date set to now (campaign has playback history, cannot be deleted) |
| `skipped_linked` | Campaign is tagged as "linked" (user-managed); not modified by sync |

**Content Status Values**:

| Status | Description |
|--------|-------------|
| `ready` | Content downloaded and available for playback |
| `encoding_queued` | Video accepted but queued for h264 baseline encoding. Use the Status endpoint to poll for completion. |
| `download_failed` | Content URL could not be downloaded. See `error` field for details (e.g., `"HTTP 403"`, `"timeout"`). |
| `rejected` | File format is not supported (not an image or video) |
| `upload_failed` | File downloaded successfully but failed to upload to storage |
| `db_error` | File processed but database record could not be created |

## 1.3. Warnings

Warnings indicate non-fatal issues. The sync continues despite warnings, but the affected items may not behave as expected. Warnings are returned in the top-level `warnings` array.

### Unmatched Player

A player code from the payload could not be matched to any player in the CMS (via `store_name` or `site_name` LIKE search). The campaign is still created, but without this player assigned.

```json
{
    "type": "unmatched_player",
    "code": "UNKNOWN123",
    "campaign": "VAMS #3940 - TOM FORD"
}
```

**Action required**: Verify the player code is correct and that the player exists in the CMS with a matching `store_name` or `site_name`.

### Unmapped Slot

An external slot name (e.g., `vaType`) has no mapping configured in the CMS admin. The sub-campaign is still created but assigned to the `"default"` slot group.

```json
{
    "warning": "Unmapped external slot",
    "slot": "Digital Screen - Escalator",
    "campaign": "VAMS #3940 - TOM FORD"
}
```

**Action required**: Add the slot mapping in the CMS admin under External Import configuration.

### Content ID Extraction Failed

A URL in the payload could not be parsed to extract the content identifier (filename UUID). The content item is skipped for that sub-campaign.

```json
{
    "item_index": 3,
    "warning": "Could not extract content_id from URL",
    "url": "https://example.com/invalid-path",
    "orderNumber": "3940"
}
```

**Action required**: Check that the URL has a valid file path with a filename and extension (e.g., `.mp4`, `.png`).

### Ended Not Deleted

A managed campaign was removed from the payload but has playback history, so it cannot be hard-deleted. Instead, its end_date was set to now, making it "finished".

```json
{
    "type": "ended_not_deleted",
    "campaign": "VAMS #3941 - DIOR",
    "campaign_id": 457,
    "reason": "has playback history"
}
```

**Note**: This is expected behavior. Campaigns with playback history are preserved for reporting purposes.

### Dimension Mismatch

Content dimensions (aspect ratio) do not match an assigned player's dimensions, even accounting for the player's configured tolerance. The campaign is still created — the CMS handles playback scaling — but this may indicate the wrong content was assigned.

```json
{
    "type": "dimension_mismatch",
    "campaign": "VAMS #3940 - TOM FORD",
    "sub_campaign": "TOM FORD - promo (2026-01-12 to 2026-02-15)",
    "content_id": 1234,
    "content_dimensions": "1920x1080",
    "player_id": 567,
    "player_store": "3201DS0061",
    "player_dimensions": "1080x1920",
    "tolerance": 5
}
```

**Action required**: Verify the content was intended for this player. Portrait content on landscape players (or vice versa) will be letterboxed or scaled. If the mismatch is expected, no action is needed.

## 1.4. Errors

Errors indicate items that were rejected during translation and not processed. Errors are returned in the top-level `errors` array.

### Missing Required Fields

A booking item is missing one or more required fields and was skipped entirely.

```json
{
    "item_index": 5,
    "error": "Missing required fields: startDate, url",
    "orderNumber": "3940"
}
```

### No Valid Content URLs

All URLs for a booking item failed content_id extraction. The item was skipped.

```json
{
    "item_index": 7,
    "error": "No valid content URLs found",
    "orderNumber": "3940"
}
```

## 1.5. Sync Behavior

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

**Content dedup**: Content is identified by the filename UUID extracted from the URL path (the `content_id`). If the same `content_id` already exists in the CMS, the existing content is reused without re-downloading.

---

# 2. Status Endpoint

**Endpoint**: `GET /api/external_import_status.php` or `POST /api/external_import_status.php`

**Authentication**: Bearer token (API key).

```
Authorization: Bearer {api_key}
```

Check content download and encoding status after a sync. Use this to poll for video encoding completion.

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

| Status | Description |
|--------|-------------|
| `ready` | Content is ready for playback |
| `encoding_queued` | Video is queued for encoding to h264 baseline |
| `encoding_error` | Encoding failed |
| `encoding_timeout` | Encoding timed out |
| `not_found` | Content ID not found in the system (not yet downloaded or invalid) |
