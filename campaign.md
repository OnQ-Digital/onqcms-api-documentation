# Campaign

The onQ CMS API allows users to create, edit and delete campaign.<br><br>
At the moment, **1. Campaign Type & 2. Campaign Management** are for **internal use only** (Authentication: Session-Based Authentication is used.)<br>
3, 4 & 5 are for both API key and Session-based Authentication.<br> 


# TOC
- [Campaign](#campaign)
- [TOC](#toc)
- [1. Campaign Type](#1-campaign-type)
  - [1.1. Create a new campaign type](#11-create-a-new-campaign-type)
  - [| slot\_duration       | integer           | True      | Time for slot             |](#-slot_duration--------integer------------true-------time-for-slot-------------)
  - [1.2. Fetch campaigns type by ID OR All](#12-fetch-campaigns-type-by-id-or-all)
    - [1.2.1. List All Campaign](#121-list-all-campaign)
    - [1.2.2. Fetch a campaign type by ID](#122-fetch-a-campaign-type-by-id)
  - [1.3. Update a campaign type by ID](#13-update-a-campaign-type-by-id)
  - [1.4. Delete single or multiple campaign types](#14-delete-single-or-multiple-campaign-types)
- [2. Campaign Management](#2-campaign-management)
    - [Endpoints Overviews](#endpoints-overviews)
  - [2.1. Create a new campaign](#21-create-a-new-campaign)
  - [2.2. Fetch campaigns](#22-fetch-campaigns)
    - [2.2.1. Fetch campaigns by group](#221-fetch-campaigns-by-group)
    - [2.2.2. Fetch a campaign with sub divisions (campaigns)](#222-fetch-a-campaign-with-sub-divisions-campaigns)
    - [2.2.3. Fetch a campaign detail with campaign ID](#223-fetch-a-campaign-detail-with-campaign-id)
  - [2.3. Update a campaign by ID](#23-update-a-campaign-by-id)
  - [2.4. Delete single or multiple  campaigns](#24-delete-single-or-multiple--campaigns)
  - [2.5. Campaign Availability](#25-campaign-availability)
  - [2.6. Campaign Report](#26-campaign-report)
- [3. Category](#3-category)
  - [3.1. Fetch Category](#31-fetch-category)
  - [3.2. Create a new category](#32-create-a-new-category)
  - [3.3. Edit category](#33-edit-category)
  - [3.4. Delete Category](#34-delete-category)
- [4. Tag](#4-tag)
  - [4.1. Fetch Tag](#41-fetch-tag)
  - [4.2. Create a new Tag](#42-create-a-new-tag)
  - [4.3. Edit Tag](#43-edit-tag)
  - [4.4. Delete Tag](#44-delete-tag)
- [5. Smart Group](#5-smart-group)
  - [5.1. Fetch Smart Group](#51-fetch-smart-group)
  - [5.2. Create a new Smart Group](#52-create-a-new-smart-group)
  - [5.3. Edit Smart Group](#53-edit-smart-group)
  - [5.4. Delete Smart Group](#54-delete-smart-group)



[Back to Home](README.md)

# 1. Campaign Type
Base URL: https://onqcms.com/api/campaign/type


## 1.1. Create a new campaign type
Endpoint: https://onqcms.com/api/campaign/type/create

**Request Parameters**

The CMS expects an array of template structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company group ID          |
| name                | string            | True      | Campaign type name        |
| mediaplayer_id      | integer           | True      | Media player ID           |
| template_structure  | array (template structure object) | True  | These objects specify details such as the campaign slot type, playback settings, duration, and additional attributes.|
| num_slots           | integer           | True      | Number of Slot            |
| slot_duration       | integer           | True      | Time for slot             |
---

<br>

**Template_Structure Asset JSON Object**
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| campaign-slot       | string            | If no 'stream'    | Campaign slot type|
| stream              | json              | If no 'campaign-slot'  | Playlist Json|
| playback            | string            | True      | Playback type             |
| truncate            | Boolean           | True      | Truncate option           |


`Example request:`

```json
{
  "company_group_id": 3,
  "name": "David Jones Campaigns",
  "mediaplayer_id": 1442,
  "num_slots": 4,
  "slot_duration" 15,
  "template_structure": [
    {"campaign-slot": "standard", "playback": "fixed", "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "truncate": true},
    {"stream": "internal_content_playlist", "playback": "fixed", "truncate": true}
  ]
}
```

`Success Response:`

```json
{
  "campaign_type_id": 2,
  "company_group_id": 3,
  "name": "David Jones Campaigns",
  "mediaplayer_id": 1442,
  "num_slots": 4, 
  "slot_duration" 15,
  "template_structure": [
    {"campaign-slot": "standard", "playback": "fixed", "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "truncate": true},
    {"stream": "internal_content_playlist", "playback": "fixed", "truncate": true}
  ]
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-01T10:00:00.000Z"
}

```

`Error Response:`

- Status: 400 Bad Request
```json
{
  "error": "Invalid input data"
}
```
- Status: 500 Internal Server Error
```json
{
  "error": "Failed to save campaign type"
}
```

[Top ↑](#campaign)


## 1.2. Fetch campaigns type by ID OR All
Endpoint: https://onqcms.com/api/campaign/type/fetch	

### 1.2.1. List All Campaign

`Request:`

```json
{
  "company_group_id": 1
}
```

`Success Response:`

- Status: 200 OK
```json
[
    {
        "campaign_type_id": 1,
        "company_group_id": 3,
        "name": "New Campaign Type",
        "template_structure": "{\"var\": [\"a\", \"b\", \"c\"], \"var1\": \"test\"}",
        "createdAt": "2024-10-07 16:25:17",
        "updatedAt": "2024-10-07 16:25:17"
    },
    {
        "campaign_type_id": 2,
        "company_group_id": 3,
        "name": "New Campaign Type",
        "template_structure": "{\"var\": [\"a\", \"b\", \"c\"], \"var1\": \"test\"}",
        "createdAt": "2024-10-07 16:25:27",
        "updatedAt": "2024-10-07 16:25:27"
    },
  ...
]
```

### 1.2.2. Fetch a campaign type by ID

`Request:`

```json
{
  "campaign_type_id": 2,
  "company_group_id": 1
}
```
`Success Response:`
- Status: 200 OK
```json
{
  "campaign_type_id": 2,
  "company_group_id": 1,
  "name": "New Campaign",
  "num_slots": 4,
  "slot_duration" 15,
  "template_structure": ((JSON)),
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-01T10:00:00.000Z"
}
```
`Error Response:`
- Status: 404 Not Found
```json
{
  "error": "Campaign type not found"
}
```
[Top ↑](#campaign)

## 1.3. Update a campaign type by ID
Endpoint: https://onqcms.com/api/campaign/type/edit	

`Request:`
```json
{
  "campaign_type_id": 2,
  "company_group_id": 1,
  "name": "Updated Campaign type",
  "template_structure": ((JSON)),
  "num_slots": 4,
  "slot_duration" 15
}
```
`Response:`
- Satus: 200 OK
```json
{
  "campaign_type_id": 2,
  "company_group_id": 1,
  "name": "Updated Campaign type",
  "num_slots": 4,
  "slot_duration" 15,
  "template_structure": ((JSON)),
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-01T10:00:00.000Z"
}
```

`Error Response:`
- Status: 404 Not Found
```json
{
  "error": "Campaign type not found"
}
```
[Top ↑](#campaign)

## 1.4. Delete single or multiple campaign types
Endpoint: https://onqcms.com/api/campaign/type/delete	

`Request:`
```json
{
  "campaign_type_id": 2,
  "company_group_id": 1
}
```
`Response:`

- Status: 204 No Content

- Response Body: None

`Error Response:`

- Status: 404 Not Found
```json
{
  "error": "Campaign type not found"
}
```

[Top ↑](#campaign)



# 2. Campaign Management
Base URL: https://onqcms.com/api/campaign

### Endpoints Overviews
We have adopted the CRUD method but we do only use POST method with different endpoints
| Method | Endpoint | Description |
|---------|---------------------------|------------------------------|
| POST  | /campaign/create  | Create a new campaign |
| POST  | /campaign/fetch   | Retrieve a campaign by ID or ALL  |
| POST  | /campaign/edit    | Update a campaign by ID |
| POST  | /campaign/delete  | Delete single or multiple campaigns |
| POST  | /campaign/availability   |  Fetching Campaign Availability by Date and Players  |


## 2.1. Create a new campaign

Endpoint: https://onqcms.com/api/campaign/create

*Parameters*

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company ID                |
| name                | string            | True      | Campaign name             |
| asset_folder_id     | integer           | True      | Default asset folder ID   |
| color_hex           | string            | True      | Campaign color code       |
| color_hex           | string            | True      | Campaign color code       |
| num_slots           | integer           | True      | Number of Slot            |
| asset_per_loop      | integer           | True      | Asset Plays per Loop      |
| fill_empty_slot     | integer           | False     | Fill Empty Slot           |
| fill_cond_limit     | integer           | False     | Fill Slot - Limit         |
| fill_cond_slot      | integer           | False     | Fill Slot - Condition     |
| campaign_sub        | array (campaign object) | True | List of campaign object  |

---

*Campaign_sub Parameters*
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| order               | integer           | True      | position ID               |
| name                | string            | True      | division name             |
| start_date_time     | datetime          | False     | validity start date time  |
| end_date_time       | datetime          | False     | validity end date time    |
| selected_days       | array             | False      | mon, tue, wed, thu, fri, sat, sun |
| content             | array             | True      | content ID in order       |
| mediaplayer_id      | array             | False     | assigned media player ID  |
| mediaplayer_category_id | array             | False     | Player Category ID        |
| mediaplayer_tag_id      | array             | False     | Player Tag ID             |

`Request:`
```json
{
  "company_group_id": 3,
  "name": "DJ Campaign",
  "asset_folder_id": 503,
  "color_hex": "#ff88dd",
  "campaign_sub": [
    {
      "order": 1,
      "name": "division 1",
      "start_date_time": "2024-10-01 09:00:00",
      "end_date_time": "2024-10-30 18:00:00",
      "content": [33361, 33362, 33363, 33369, 33370, 33371, 33374, 33375, 33376],
      "mediaplayer_ids": [1144, 1153],
      "mediaplayer_category_id": [123, 111],
      "mediaplayer_tag_id": [222,333]
    },
    {
      "order": 2,
      "name": "division 2",
      "start_date_time": "2024-10-02 13:00:00",
      "end_date_time": "2024-11-30 20:00:00",
      "content": [56260, 56261, 56262, 56263],
      "mediaplayer_ids": [1415, 1416, 1417],
      "mediaplayer_category_id": [123, 111],
      "mediaplayer_tag_id": [222,333]
    }
  ]
}
```
`Success Response:`

- Status: 201 Created
```json
{
  "campaign_id": 1
  "company_group_id": 3,
  "name": "DJ Campaign",
  "asset_folder_id": 503,
  "color_hex": "#ff88dd",
  "campaign_sub": [
    {
      "campaign_sub_id": 1,
      "order": 1,
      "name": "division 1",
      "start_date_time": "2024-10-01 09:00:00",
      "end_date_time": "2024-10-30 18:00:00",
      "content": [33361, 33362, 33363, 33369, 33370, 33371, 33374, 33375, 33376],
      "createdAt": "2024-10-01T10:00:00.000Z",
	  "updatedAt": "2024-10-01T10:00:00.000Z"
    },
    {
      "campaign_sub_id": 2
      "order": 2,
      "name": "division 2",
      "start_date_time": "2024-10-02 13:00:00",
      "end_date_time": "2024-11-30 20:00:00",
      "content": [56260,56261,56262,56263],
      "createdAt": "2024-10-01T10:00:00.000Z",
	  "updatedAt": "2024-10-01T10:00:00.000Z"
    }
  ]
}
```
`Error Response:`
- Status: 400 Bad Request
```json
{
  "error": "Invalid input data"
}
```
[Top ↑](#campaign)

## 2.2. Fetch campaigns

Endpoint: https://onqcms.com/api/campaign/fetch

*Parameters*

| Parameter           | Type      | Required  | Description                                     |
|---------------------|-----------|-----------|-------------------------------------------------|
| company_group_id    | integer   | True      | Company ID                                      |
| campaign_id         | string    | True      | Campaign ID                                     |
| start_date_time     | string    | True      | Start date time                                 |
| end_date_time       | string    | True      | End date time                                   |
| min_booking         | integer   | False     | Maximum campaign's total assignments            |
| max_booking         | integer   | False     | Minimum campaign's total assignments            |
| campaign_statuses   | array     | False     | Status requirement                              |
| mediaplayer_ids     | array     | False     | Player ID, [] for all                           |
| mediaplayer_tag_ids | array     | False     | Player's Tag ID, [] for all                     |
| mediaplayer_cat_ids | array     | False     | Player's Category ID, [] for all                |
| by_group            | integer   | True      | 1 for by group, 0 for division, 2 for details   |
| include_detail      | boolean   | False     | true, for including mediaplayers details. only used when by_group = 1 |

### 2.2.1. Fetch campaigns by group
`Request:`
```json
{
    "company_group_id": "3",
    "campaign_id": 7,
    "start_date_time": "2025-01-07 00:00:00",
    "end_date_time": "2029-01-06 00:00:00",
    "min_bookings": 1,
    "max_bookings": 10,
    "campaign_statuses": [
        "live"
    ],
    "mediaplayer_ids": [
        1144
    ],
    "mediaplayer_cat_ids": [
        3
    ],
    "mediaplayer_tag_ids": [
        150
    ],
    "include_detail": false,
    "by_group": 1,
}
```
`Success Response:`
- Status: 200 OK
```json
// example response, when "include_detail: false" in the request
[
    {
        "campaign_id": 7,
        "company_group_id": 3,
        "name": "Campaign 01",
        "asset_folder_id": 1882,
        "color_hex": "#8482d8",
        "is_draft": 0,
        "num_slots": 1,
        "asset_per_loop": 1,
        "fill_empty_slot": 1,
        "fill_cond_limit": 2,
        "fill_cond_slot": 2,
        "selected_days": [
            "mon",
            "tue",
            "wed",
            "thu",
            "fri",
            "sat",
            "sun"
        ],
        "start_date_time": "2025-01-01 00:00:00",
        "end_date_time": "2025-01-31 23:59:00",
        "status": "live",
        "total_campaign_sub": 2,
        "total_assignments": 3,
        "total_players": 2,
        "total_campaign_player_cats": 2,
        "total_campaign_player_tags": 2,
        "campaign_thumb_name": "3-457-1715297080-0a86dc5-thumb.jpg",
        "tag_campaign_ids": [
            151
        ],
        "category_clients": [
            {
                "smart_categories_object_id": "7",
                "smart_categories_id": 1,
                "smart_categories_value": "ISSEY MIYAKE"
            }
        ],
        "category_client_ids": [
            1
        ],
        "category_client_name": "ISSEY MIYAKE",
        "mediaplayers": [],
        "mediaplayers_by_tag": [],
        "mediaplayers_by_cat": [],
        "createdAt": "2025-01-06 10:40:59",
        "updatedAt": "2025-01-06 10:40:59"
    }
]
```

```json
// example response, when "include_detail: true" in the request
[
    {
        "campaign_id": 7,
        "company_group_id": 3,
        "name": "Campaign 01",
        "asset_folder_id": 1882,
        "color_hex": "#8482d8",
        "is_draft": 0,
        "num_slots": 1,
        "asset_per_loop": 1,
        "fill_empty_slot": 1,
        "fill_cond_limit": 2,
        "fill_cond_slot": 2,
        "selected_days": [
            "mon",
            "tue",
            "wed",
            "thu",
            "fri",
            "sat",
            "sun"
        ],
        "start_date_time": "2025-01-01 00:00:00",
        "end_date_time": "2025-01-31 23:59:00",
        "status": "live",
        "total_campaign_sub": 2,
        "total_assignments": 3,
        "total_players": 2,
        "total_campaign_player_cats": 2,
        "total_campaign_player_tags": 2,
        "campaign_thumb_name": "3-457-1715297080-0a86dc5-thumb.jpg",
        "tag_campaign_ids": [
            151
        ],
        "category_clients": [
            {
                "smart_categories_object_id": "7",
                "smart_categories_id": 1,
                "smart_categories_value": "ISSEY MIYAKE"
            }
        ],
        "category_client_ids": [
            1
        ],
        "category_client_name": "ISSEY MIYAKE",
        "mediaplayers": [
            {
                "campaign_id": 7,
                "mediaplayer_id": 1144,
                "mediaplayer_name": "a-frame"
            },
            {
                "campaign_id": 7,
                "mediaplayer_id": 1415,
                "mediaplayer_name": "Offset Test"
            }
        ],
        "mediaplayers_by_tag": [
            {
                "campaign_id": 7,
                "mediaplayer_id": 1153,
                "mediaplayer_name": "API Test 1",
                "tag_id": 150,
                "tag_name": "Group 1"
            },
            {
                "campaign_id": 7,
                "mediaplayer_id": 1416,
                "mediaplayer_name": "API Test 2",
                "tag_id": 152,
                "tag_name": "Group 2"
            }
        ],
        "mediaplayers_by_cat": [
            {
                "campaign_id": 7,
                "mediaplayer_id": 1153,
                "mediaplayer_name": "API Test 1",
                "smart_categories_id": 3,
                "smart_categories_key": "location",
                "smart_categories_value": "melbourne"
            },
            {
                "campaign_id": 7,
                "mediaplayer_id": 1416,
                "mediaplayer_name": "API Test 2",
                "smart_categories_id": 4,
                "smart_categories_key": "type",
                "smart_categories_value": "marketing"
            }
        ],
        "createdAt": "2025-01-06 10:40:59",
        "updatedAt": "2025-01-06 10:40:59"
    }
]
```

### 2.2.2. Fetch a campaign with sub divisions (campaigns)
`Request:`
```json
{
    "company_group_id": 3,
    "campaign_id": 49,
    "start_date_time": "2024-01-02 00:00:00",
    "end_date_time": "2024-12-02 23:59:59",
    "min_booking": null,
    "max_booking": null,
    "campaign_statuses": ["live","finished"],
    "mediaplayer_ids": [36],
    "by_group": 0
}
```
`Success Response:`

- Status: 200 OK
```json
[
    {
        "campaign_id": 9,
        "company_group_id": 3,
        "name": "DJ Campaign",
        "sub_order": 1,
        "sub_name": "division 1",
        "playlist_content": [
            33361,
            33362,
            33363,
            33369,
            33370,
            33371,
            33374,
            33375,
            33376
        ],
        "status": "finished",
        "start_date_time": "2024-10-01 09:00:00",
        "end_date_time": "2024-10-30 18:00:00",
        "total_assignments": 6,
        "createdAt": "2024-10-31 14:57:53",
        "updatedAt": "2024-10-31 14:57:53"
    },
    {
        "campaign_id": 9,
        "company_group_id": 3,
        "name": "DJ Campaign",
        "sub_order": 2,
        "sub_name": "division 2",
        "playlist_content": [
            56260,
            56261,
            56262,
            56263
        ],
        "status": "live",
        "start_date_time": "2024-10-02 13:00:00",
        "end_date_time": "2024-11-30 20:00:00",
        "total_assignments": 5,
        "createdAt": "2024-10-31 14:57:53",
        "updatedAt": "2024-10-31 14:57:53"
    }
]
```

### 2.2.3. Fetch a campaign detail with campaign ID
`Request:`
```json
{
    "company_group_id": 3,
    "campaign_id": 49,
    "start_date_time": "2024-01-02 00:00:00",
    "end_date_time": "2024-12-02 23:59:59",
    "min_booking": null,
    "max_booking": null,
    "campaign_statuses": ["live","finished"],
    "mediaplayer_ids": [36],
    "by_group": 0
}
```
`Success Response:`

- Status: 200 OK
```json
[
    {
        "campaign_id": 171,
        "campaign_name": "DJ Campaign 1",
        "asset_folder_id": 503,
        "color_hex": "#aadd33",
        "campaign_sub_id": 76,
        "campaign_sub_name": "dj division 1",
        "campaign_sub_order": 1,
        "playlist_content": [
            33361,
            33362,
        ],
        "playlist_thumbs": {
            "33361": {
                "file_title": "CR_May22_MumDay_1920x1080px_10.jpg",
                "file_thumb": "3-237-1651629762-74e0412-thumb.jpg"
            },
            "33362": {
                "file_title": "CR_May22_MumDay_1920x1080px_11.jpg",
                "file_thumb": "3-237-1651629763-304480f-thumb.jpg"
            }
        },
        "start_date_time": "2024-10-01 09:00:00",
        "end_date_time": "2024-10-30 18:00:00",
        "selected_days": [
            "wed",
            "thu",
            "fri"
        ],
        "created_at": "2024-12-12 09:41:57",
        "updated_at": "2024-12-12 09:41:57",
        "mediaplayer_ids": [
            1144,
            1153
        ],
        "mediaplayer_cat_ids": [
          111,
          222
        ],
        "mediaplayer_tag_ids": [
          123,
          345
        ],
        "tag_campaign_ids": [],
        "category_campaign_ids": [
            162
        ],
        "category_client_ids": [
            162
        ]
    },
    {
        "campaign_id": 171,
        "campaign_name": "DJ Campaign 1",
        "asset_folder_id": 503,
        "color_hex": "#aadd33",
        "campaign_sub_id": 77,
        "campaign_sub_name": "dj division 2",
        "campaign_sub_order": 2,
        "playlist_content": [
            56260,
            56261,
            56262,
            56263
        ],
        "playlist_thumbs": {
            "56260": {
                "file_title": "Demo Move.mp4",
                "file_thumb": "3-474-1713752722-7b76837-thumb.jpeg"
            },
            "56261": {
                "file_title": "Demo Move.mp4",
                "file_thumb": "3-474-1713753413-7b76837-thumb.jpeg"
            },
            "56262": {
                "file_title": "Demo Move.mp4",
                "file_thumb": "3-474-1713753562-7b76837-thumb.jpeg"
            },
            "56263": {
                "file_title": "Demo\nMove.mp4",
                "file_thumb": "3-474-1713753605-7b76837-thumb.jpeg"
            }
        },
        "start_date_time": null,
        "end_date_time": null,
        "created_at": "2024-12-12 09:41:57",
        "updated_at": "2024-12-12 09:41:57",
        "selected_days": [
            "wed",
            "thu",
            "fri"
        ],
        "mediaplayer_ids": [
            1413,
            1438,
            1439
        ],
        "mediaplayer_cat_ids": [
          111,
          222
        ],
        "mediaplayer_tag_ids": [
          123,
          345
        ],
        "tag_campaign_ids": [],
        "category_campaign_ids": [
            162
        ],
        "category_client_ids": [
            162
        ]
    }
]
```

`Error Response:`

- Status: 404 Not Found
```json
{
  "error": "Campaign not found"
}
```
[Top ↑](#campaign)

## 2.3. Update a campaign by ID

Endpoint: https://onqcms.com/api/campaign/edit


*Parameters*

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company ID                |
| campaign_id         | integer           | True      | Campaign ID                |
| name                | string            | True      | Campaign name             |
| asset_folder_id     | integer           | True      | Default asset folder ID   |
| color_hex           | string            | True     | Campaign color code       |
| campaign_sub        | array (campaign object) | True | List of campaign object  |


*Campaign_sub Parameters*
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| order               | integer           | True      | position ID               |
| name                | string            | True      | division name             |
| start_date_time     | datetime          | False     | validity start date time  |
| end_date_time       | datetime          | False     | validity end date time    |
| content             | array             | True      | content ID in order       |
| mediaplayer_ids      | array             | True     | assigned media player ID  |
| tag_campaign_ids      | array             | True     | assigned media player ID  |
| category_campaign_ids | array             | True     | assigned media player ID  |
| category_client_ids   | array             | True     | assigned media player ID  |

To remove start_data, start_time, end_date and end_time values, set empty value for the key. Any unset key will keep existing values.

`Request:`
```json
{
  "company_group_id": 3,
  "campaign_id": 12,
  "name": "DJ Campaign",
  "asset_folder_id": 503,
  "color_hex": "#ff88dd",
  "campaign_sub": [
    {
      "order": 1,
      "name": "division 1",
      "start_date_time": "2024-10-01 09:00:00",
      "end_date_time": "2024-10-30 18:00:00",
      "content": [33361, 33362, 33363, 33369, 33370, 33371, 33374, 33375, 33376],
      "mediaplayer_ids": [1144, 1153],
      "mediaplayer_category_id": [111,222],
      "mediaplayer_tag_id": [123,345],
      "tag_campaign_ids": [],
      "category_campaign_ids": [152],
      "category_client_ids": [162]
    },
    {
      "order": 2,
      "name": "division 2",
      "start_date_time": "2024-10-02 13:00:00",
      "end_date_time": "2024-11-30 20:00:00",
      "content": [56260, 56261, 56262, 56263],
      "mediaplayer_ids": [1415, 1416, 1417],
      "mediaplayer_category_id": [111,222],
      "mediaplayer_tag_id": [123,345],
      "tag_campaign_ids": [],
      "category_campaign_ids": [162],
      "category_client_ids": [162]
    }
  ]
}
```
`Response:`

- Satus: 200 OK
```json
{
  "campaign_id": 12,
  "company_group_id": 1,
  "name": "New Updated Campaign",
  "campaign_type_id": 2,
  "asset_folder_id": 5,
  "start_date": "2024-10-01",
  "start_time": "090000",
  "end_date": "2024-10-30",
  "end_time": "180000",
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-05T12:00:00.000Z"
}
```

`Error Response:`
- Status: 404 Not Found
```json
{
  "error": "Campaign not found"
}
```

[Top ↑](#campaign)

## 2.4. Delete single or multiple  campaigns

Endpoint: https://onqcms.com/api/campaign/delete

`Request:`
```json
{
  "campaign_id": 12,
  "company_group_id": 3
}
```
`Response:`

- Status: 204 No Content

- Response Body: None

`Error Response:`
   
- Status: 404 Not Found
```json
{
  "error": "Campaign not found"
}
```
[Top ↑](#campaign)
## 2.5. Campaign Availability
This endpoint returns availability sorted by date and players. If any campaign division overlaps with the specified date-time range, the main campaign is selected and counted.

Endpoint: https://onqcms.com/api/campaign/availability

*Parameters*

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company ID                |
| start_date_time     | datetime          | False     | start date time           |
| end_date_time       | datetime          | False     | end date time             |
| min_booking         | array             | False     | (Not used yet)            |
| max_booking         | array             | False     | (Not used yet)            |
| mediaplayer_id      | array             | False     | media player ID or null for all player |

`Request:`
```json
{
    "company_group_id": 3,
    "start_date_time": "2024-10-02 00:00:00",
    "end_date_time": "2024-11-02 23:59:59",
    "min_booking": null,
    "max_booking": null,
    "mediaplayer_ids": [36,542,906,980,1413,1143,1144,1453]
}
```

`Response:`

```json
{
    "2024-10-02": [
        {
            "mediaplayer_id": 36,
            "number_of_campaigns": 2,
            "total_slot": 4,
            "used_rate": 50,
            "available_rate": 50
        },
        {
            "mediaplayer_id": 906,
            "number_of_campaigns": 0,
            "total_slot": 4,
            "used_rate": 0,
            "available_rate": 100
        },
       ...
        {
            "mediaplayer_id": 1453,
            "number_of_campaigns": 0,
            "total_slot": 4,
            "used_rate": 0,
            "available_rate": 100
        }
    ],
    "2024-10-03": [
        {
            "mediaplayer_id": 36,
            "number_of_campaigns": 2,
            "total_slot": 4,
            "used_rate": 50,
            "available_rate": 50
        },
        ...
        ...
        {
            "mediaplayer_id": 1453,
            "number_of_campaigns": 0,
            "total_slot": 4,
            "used_rate": 0,
            "available_rate": 100
        }
    ],
    "2024-10-04": [
        ...
    ]
    ...
}
```
[Top ↑](#campaign)


## 2.6. Campaign Report
This endpoint returns mediaplayer reported data grouped by campaign id, including data for overview, files/assets breakdown and mediaplayer breakdown

Endpoint: https://onqcms.com/api/campaign/report

*Parameters*

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company ID                |
| campaign_id         | integer           | True      | Campaign ID               |

`Request:`
```json
{
    "company_group_id": "3",
    "campaign_id": 181
}
```

`Response:`

- Satus: 200 OK
```json
{
    "campaign_id": 181,
    "overview": {
        "campaign_id": 181,
        "total_counts": "466",
        "first_report_date": "2025-02-07 00:00:00",
        "final_report_date": "2025-02-11 00:00:00",
        "total_mediaplayers": 1,
        "campaign_name": "Dev - 20250203 - 1330"
    },
    "files": [
        {
            "campaign_id": 181,
            "file_id": 56785,
            "total_counts": "233",
            "file_name": "3-466-1735265739-b775422.jpg",
            "file_title": "asset-image-0005.jpg",
            "file_thumb_name": "3-466-1735265739-b775422-thumb.jpg",
            "campaign_subs": [
                {
                    "campaign_sub_name": "Dev - 20250203 - 1330",
                    "db_campaign_sub_name": "Dev - 20250203 - 1330"
                },
                {
                    "campaign_sub_name": "Dev - 20250203 - 1330",
                    "db_campaign_sub_name": null
                }
            ]
        },
        {
            "campaign_id": 181,
            "file_id": 56786,
            "total_counts": "233",
            "file_name": "3-466-1735265739-b1a52cf.jpg",
            "file_title": "asset-image-0002.jpg",
            "file_thumb_name": "3-466-1735265739-b1a52cf-thumb.jpg",
            "campaign_subs": [
                {
                    "campaign_sub_name": "Dev - 20250203 - 1330",
                    "db_campaign_sub_name": "Dev - 20250203 - 1330"
                },
                {
                    "campaign_sub_name": "Dev - 20250203 - 1330",
                    "db_campaign_sub_name": null
                }
            ]
        }
    ],
    "mediaplayers": [
        {
            "campaign_id": 181,
            "mediaplayer_id": 1428,
            "total_counts": "466",
            "mediaplayer_name": "Electron-Smart-2",
            "campaign_subs": [
                {
                    "campaign_sub_name": "Dev - 20250203 - 1330",
                    "db_campaign_sub_name": null
                },
                {
                    "campaign_sub_name": "Dev - 20250203 - 1330",
                    "db_campaign_sub_name": "Dev - 20250203 - 1330"
                }
            ]
        }
    ]
}
```

`Error Response:`

- Status: 400 Bad Request
- Status: 404 Not Found
```json
{
    "error": "Campaign report not found"
}
```

[Top ↑](#campaign)


# 3. Category

Base URL: https://onqcms.com/api/smart_category

## 3.1. Fetch Category

Endpoint: https://onqcms.com/api/smart_category/fetch

**Request Parameters**

The CMS expects an array of template structure objects as the input
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| category_id         | integer           | False      | Category ID              |
| category_key        | string            | False     | Category key              |
| category_val        | string            | False     | Category Value              |
| object_type         | string            | False      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | integer           | False     | Object ID              |

`Example request:`

```json
[
    {
        "category_id": "189",
        "category_key": "region",
        "category_value": "",
        "updated_at": "2025-01-03 16:25:36",
        "created_at": "2025-01-03 16:25:36"
    },
    {
        "category_id": "197",
        "category_key": "region",
        "category_value": "region",
        "updated_at": "2025-01-03 16:30:16",
        "created_at": "2025-01-03 16:30:16"
    },
    {
        "category_id": "237",
        "category_key": "region",
        "category_value": "apac",
        "updated_at": "2025-01-03 16:33:10",
        "created_at": "2025-01-03 16:33:10"
    },
    {
        "category_id": "238",
        "category_key": "region",
        "category_value": "melbourne",
        "updated_at": "2025-01-03 16:33:10",
        "created_at": "2025-01-03 16:33:10"
    },
    {
        "category_id": "239",
        "category_key": "region",
        "category_value": "victoria",
        "updated_at": "2025-01-03 16:33:10",
        "created_at": "2025-01-03 16:33:10"
    }
]

---

{
}
```
[Top ↑](#campaign)


## 3.2. Create a new category

Endpoint: https://onqcms.com/api/smart_category/create

**Request Parameters**

The CMS expects an array of template structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| categories          | array             | True      | Category key and array of values |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | integer           | True      | array of integers or strings |


`Example request:`

```json
{
    "categories": [
        { "key": "Client", "value": ["DJ-2"] },
        { "key": "Type",   "value": ["Marketing"] },
        { "key": "Region", "value": ["APAC", "MELBOURNE", "VICTORIA"] }
    ],
    "object_type": "campaign",
    "object_id": [111, 222, 333]
}

--

{
    "categories": [
        { "key": "Client", "value": ["DJ-2"] },
        { "key": "Type",   "value": ["Marketing"] },
        { "key": "Region", "value": ["APAC", "MELBOURNE", "VICTORIA"] }
    ],
    "object_type": "player",
    "object_id":[112,333,222]
}

-- 
{
    "categories": [
        { "key": "Client", "value": ["DJ-2"] },
        { "key": "Type",   "value": ["Marketing"] },
        { "key": "Region", "value": ["APAC", "MELBOURNE", "VICTORIA"] }
    ],
    "object_type": "playlist",
    "object_id": [111, 222, 333]
}

--
{
    "categories": [
        { "key": "Client", "value": ["DJ-2"] },
        { "key": "Type",   "value": ["Marketing"] },
        { "key": "Region", "value": ["APAC", "MELBOURNE", "VICTORIA"] }
    ],
    "object_type": "asset",
    "object_id": [111, 222, 333]
}

```

`Success Response:`

```json
{
  "category_id":162
}

```
[Top ↑](#campaign)

## 3.3. Edit category

Endpoint: https://onqcms.com/api/smart_category/create

**Request Parameters**

The CMS expects an array of template structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| category_key        | string            | True      | Category key name         |
| category_value      | string            | True      | Category key's value      |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | integer           | True      | array of integers or strings |


`Example request:`

```json
{
    "category_key":"Client",
    "category_value":"DJ-2",
    "object_type":"campaign",
    "object_id":[111,222,333]
}

--

{
    "category_key":"Brand",
    "category_value":"Hugo Boss",
    "object_type":"player",
    "object_id":[111,222,333]
}

-- 

{
    "category_key":"Brand",
    "category_value":"Hugo Boss",
    "object_type":"playlist",
    "object_id":[111,222,333]
}

--

{
    "category_key":"Brand",
    "category_value":"Hugo Boss",
    "object_type":"asset",
    "object_id":[111,222,333]
}

```

`Success Response:`

```json
{
..
}

```
## 3.4. Delete Category

Endpoint: https://onqcms.com/api/smart_category/delete


**Request Parameters**

The CMS expects an array of category structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| category_id         | integer           | True      | Category ID               |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |

`Example request:`

```json
{
..
}

```
[Top ↑](#campaign)

# 4. Tag
Base URL: https://onqcms.com/api/smart_tag

## 4.1. Fetch Tag
Endpoint: https://onqcms.com/api/smart_tag/fetch

**Request Parameters**

The CMS expects an array of tag structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| tag_id              | integer           | False     | Tag ID              |
| tag_name            | string            | False     | Tag name
| object_type         | string            | False     | 'player', 'playlist', 'asset', 'campaign' |
|object_id            | integer           | False     | Object Id

`Example request:`

```json
{
    "tag_id": 7,
    "object_type": "player"
}

---

{
    "tag_id": 7
}

---

{
}
```
[Top ↑](#campaign)

## 4.2. Create a new Tag
Endpoint: https://onqcms.com/api/smart_tag/create

**Request Parameters**

The CMS expects an array of tag structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| tag_name            | string            | True      | Tag name         |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | Array             | True      | array of strings          |


`Example request:`

```json
{
    "tag_name":"Hugo",
    "object_type":"player",
    "object_id":["testaaa001","testaaa002","offset-test1"]
}

---

{
    "tag_name":"Hugo",
    "object_type":"playlist",
    "object_id":["testaaa001","testaaa002","offset-test1"]
}

---

{
    "tag_name":"Hugo",
    "object_type":"asset",
    "object_id":["testaaa001","testaaa002","offset-test1"]
}
```

`Success Response:`

```json
{
  "tag_id":5
}

```
[Top ↑](#campaign)

## 4.3. Edit Tag
Endpoint: https://onqcms.com/api/smart_tag/create

This is to edit tag allocation

**Request Parameters**

The CMS expects an array of tag structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| tag_id              | integer/array     | True      | Tag id in either integer or array     |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | array             | True      | array of integers or strings |


`Example request:`

```json
{
    "tag_id":[144,145],
    "object_type":"player",
    "object_id":[307]
}

```

`Success Response:`

```json
{
    "removed":[
        [3,"146","campaign",307]
        ],
    "added":[
        [3,144,"campaign",307],
        [3,145,"campaign",307]
        ]
}
```
## 4.4. Delete Tag
Endpoint: https://onqcms.com/api/smart_tag/delete

**Request Parameters**

The CMS expects an array of tag structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| category_id         | integer           | True      | Category ID               |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |

`Example request:`

```json
{
    "tag_id": 7,
    "object_type": "player"
}

```
[Top ↑](#campaign)


# 5. Smart Group

*Note: We do not use Group for now*
***

Base URL: https://onqcms.com/api/smart_group

- This is smart playlst / Campaign group use. Do not confuse Comapny Group.

## 5.1. Fetch Smart Group

Endpoint: https://onqcms.com/api/smart_group/fetch

**Request Parameters**

The CMS expects an array of smart group structure objects as the input
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| smart_group_id      | integer           | False     | smart group ID              |
| object_type         | string            | False     | 'player', 'playlist', 'asset', 'campaign' |

`Example request:`

```json
{
    "smart_group_id":7,
    "object_type":"player"
}

---

{
    "smart_group_id":[]
}

---

{
}
```
[Top ↑](#campaign)

## 5.2. Create a new Smart Group

Endpoint: https://onqcms.com/api/smart_group/create

**Request Parameters**

The CMS expects an array of smart group structure objects as the input
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| smart_group_id      | string            | True      | smart group name         |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | Array             | True      | array of strings          |


`Example request:`

```json
{
    "group_name":"Hugo",
    "object_type":"player",
    "object_id":["testaaa001","testaaa002","offset-test1"]
}

---

{
    "group_name":"Hugo",
    "object_type":"playlist",
    "object_id":["testaaa001","testaaa002","offset-test1"]
}

---

{
    "group_name":"Hugo",
    "object_type":"asset",
    "object_id":["testaaa001","testaaa002","offset-test1"]
}
```

`Success Response:`

```json
{
  "tag_id":5
}

```
[Top ↑](#campaign)

## 5.3. Edit Smart Group

Endpoint: https://onqcms.com/api/smart_group/create

This is to edit tag allocation

**Request Parameters**

The CMS expects an array of smart group structure objects as the input
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| smart_group_id      | integer           | True      | smart group name          |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |
| object_id           | array             | True      | array of integers or strings |


`Example request:`

```json
{
    "smart_group_id":7,
    "object_type":"player",
    "object_id":["testaaa001","testaaa002","test12"]
}

```

`Success Response:`

```json
{
}

```
## 5.4. Delete Smart Group

Endpoint: https://onqcms.com/api/smart_group/delete

**Request Parameters**

The CMS expects an array of smart group structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| smart_group_id      | integer           | True      | smart group ID               |
| object_type         | string            | True      | 'player', 'playlist', 'asset', 'campaign' |

`Example request:`

```json
{
    "tag_id": 7,
    "object_type": "player"
}

```
[Top ↑](#campaign)

[Back to Home](README.md)