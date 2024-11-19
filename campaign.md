# 1. Campaign

The onQ CMS API allows users to create, edit and delete campaign.<br>
At the moment, this API is for **internal use only** (Authentication: Session-Based Authentication is used.)<br>
*API authentication need to be implemented on every endpoint.*

# 2. TOC
- [1. Campaign](#1-campaign)
- [2. TOC](#2-toc)
  - [2.1. Campaign Type](#21-campaign-type)
    - [2.1.1. Create a new campaign type](#211-create-a-new-campaign-type)
    - [2.1.2. Fetch campaigns type by ID OR All](#212-fetch-campaigns-type-by-id-or-all)
      - [2.1.2.1. List All Campaign](#2121-list-all-campaign)
      - [2.1.2.2. Fetch a campaign type by ID](#2122-fetch-a-campaign-type-by-id)
    - [2.1.3. Update a campaign type by ID](#213-update-a-campaign-type-by-id)
    - [2.1.4. Delete single or multiple campaign types](#214-delete-single-or-multiple-campaign-types)
  - [2.2. Campaign Management](#22-campaign-management)
      - [Endpoints Overviews](#endpoints-overviews)
    - [2.2.1. Create a new campaign](#221-create-a-new-campaign)
    - [2.2.2. Fetch campaigns](#222-fetch-campaigns)
      - [2.2.2.1. Fetch all campaign by group](#2221-fetch-all-campaign-by-group)
      - [2.2.2.2. Fetch a campaign with sub divisions (campaigns)](#2222-fetch-a-campaign-with-sub-divisions-campaigns)
    - [2.2.3. Update a campaign by ID](#223-update-a-campaign-by-id)
    - [2.2.4. Delete single or multiple  campaigns](#224-delete-single-or-multiple--campaigns)
    - [2.2.5. Campaign Availability](#225-campaign-availability)

[Back to Home](README.md)

## 2.1. Campaign Type
Base URL: https://onqcms.com/api/campaign/type


### 2.1.1. Create a new campaign type
Endpoint: https://onqcms.com/api/campaign/type/create

**Request Parameters**

The CMS expects an array of template structure objects as the input

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company group ID |
| name                | string            | True      | Campaign type name  |
| template_structure  | array (template structure object) | True  | These objects specify details such as the campaign slot type, playback settings, duration, and additional attributes.|

***
<br>

**Template_Structure Asset JSON Object**
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| campaign-slot       | string            | If no 'stream'    | Campaign slot type|
| stream              | json              | If no 'campaign-slot'  | Playlist Json|
| playback            | string            | True      | Playback type             |
| time                | integer           | True      | Time for the slot         |
| truncate            | Boolean           | True      | Truncate option           |



`Example request:`
```
{
  "company_group_id": 3,
  "name": "David Jones Campaigns",
  "template_structure": [
    {"campaign-slot": "standard", "playback": "fixed", "time": 15, "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "time": 15, "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "time": 15, "truncate": true},
    {"stream": "internal_content_playlist", "playback": "fixed", "time": 15, "truncate": true}
  ]
}
```

`Success Response:`
```
{
  "campaign_type_id": 2,
  "company_group_id": 3,
  "name": "David Jones Campaigns",
  "template_structure": [
    {"campaign-slot": "standard", "playback": "fixed", "time": 15, "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "time": 15, "truncate": true},
    {"campaign-slot": "standard", "playback": "fixed", "time": 15, "truncate": true},
    {"stream": "internal_content_playlist", "playback": "fixed", "time": 15, "truncate": true}
  ]
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-01T10:00:00.000Z"
}
```

`Error Response:`

- Status: 400 Bad Request
```
{
  "error": "Invalid input data"
}
```
- Status: 500 Internal Server Error
```
{
  "error": "Failed to save campaign type"
}
```

[Top ↑](#1-campaign)


### 2.1.2. Fetch campaigns type by ID OR All
Endpoint: https://onqcms.com/api/campaign/type/fetch	

#### 2.1.2.1. List All Campaign

`Request:`
```
{
  "company_group_id": 1
}
```
`Success Response:`

- Status: 200 OK
```
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
#### 2.1.2.2. Fetch a campaign type by ID

`Request:`

```
{
  "campaign_type_id": 2,
  "company_group_id": 1
}
```
`Success Response:`
- Status: 200 OK
```
{
  "campaign_type_id": 2,
  "company_group_id": 1,
  "name": "New Campaign",
  "template_structure": ((JSON)),
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-01T10:00:00.000Z"
}
```
`Error Response:`
- Status: 404 Not Found
```
{
  "error": "Campaign type not found"
}
```
[Top ↑](#1-campaign)

### 2.1.3. Update a campaign type by ID
Endpoint: https://onqcms.com/api/campaign/type/edit	

`Request:`
```
{
  "campaign_type_id": 2,
  "company_group_id": 1,
  "name": "Updated Campaign type",
  "template_structure": ((JSON)),
}
```
`Response:`
- Satus: 200 OK
```
{
  "campaign_type_id": 2,
  "company_group_id": 1,
  "name": "Updated Campaign type",
  "template_structure": ((JSON)),
  "createdAt": "2024-10-01T10:00:00.000Z",
  "updatedAt": "2024-10-01T10:00:00.000Z"
}
```

`Error Response:`
- Status: 404 Not Found
```
{
  "error": "Campaign type not found"
}
```
[Top ↑](#1-campaign)

### 2.1.4. Delete single or multiple campaign types
Endpoint: https://onqcms.com/api/campaign/type/delete	

`Request:`
```
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
```
{
  "error": "Campaign type not found"
}
```

[Top ↑](#1-campaign)



## 2.2. Campaign Management
Base URL: https://onqcms.com/api/campaign

#### Endpoints Overviews
We have adopted the CRUD method but we do only use POST method with different endpoints
| Method | Endpoint | Description |
|---------|---------------------------|------------------------------|
| POST  | /campaign/create  | Create a new campaign |
| POST  | /campaign/fetch   | Retrieve a campaign by ID or ALL  |
| POST  | /campaign/edit    | Update a campaign by ID |
| POST  | /campaign/delete  | Delete single or multiple campaigns |
| POST  | /campaign/availability   |  Fetching Campaign Availability by Date and Players  |


### 2.2.1. Create a new campaign

Endpoint: https://onqcms.com/api/campaign/create

*Parameters*

| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| company_group_id    | integer           | True      | Company ID                |
| name                | string            | True      | Campaign name             |
| asset_folder_id     | integer           | True      | Default asset folder ID   |
| color_hex           | string            | True      | Campaign color code       |
| campaign_sub        | array (campaign object) | True | List of campaign object  |

---

*Campaign_sub Parameters*
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| order               | integer           | True      | position ID               |
| name                | string            | True      | division name             |
| start_date_time     | datetime          | False     | validity start date time  |
| end_date_time       | datetime          | False     | validity end date time    |
| content             | array             | True      | content ID in order       |
| mediaplayer_id      | array             | False     | assigned media player ID  |

`Request:`
```
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
      "mediaplayer_ids": [1144, 1153]
    },
    {
      "order": 2,
      "name": "division 2",
      "start_date_time": "2024-10-02 13:00:00",
      "end_date_time": "2024-11-30 20:00:00",
      "content": [56260, 56261, 56262, 56263],
      "mediaplayer_ids": [1415, 1416, 1417]
    }
  ]
}
```
`Success Response:`

- Status: 201 Created
```
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
```
{
  "error": "Invalid input data"
}
```
[Top ↑](#1-campaign)

### 2.2.2. Fetch campaigns

Endpoint: https://onqcms.com/api/campaign/fetch

*Parameters*

| Parameter           | Type      | Required  | Description               |
|---------------------|-----------|-----------|---------------------------|
| company_group_id    | integer   | True      | Company ID                |
| campaign_id         | string    | True      | Campaign ID               |
| start_date_time     | string    | True      | Start date time           |
| end_date_time       | string    | True      | End date time             |
| min_booking         | integer   | True      | (not used yet)            |
| max_booking         | integer   | True      | (not used yet)            |
| campaign_statuses   | array     | True      | Status requirement        |
| mediaplayer_ids     | array     | True      | Player ID, [] for all     |
| by_group            | integer   | True      | 1 for by group, 0 for division |

#### 2.2.2.1. Fetch all campaign by group
`Request:`
```
{
    "company_group_id": 3,
    "campaign_id": 49,
    "start_date_time": "2024-01-02 00:00:00",
    "end_date_time": "2024-12-02 23:59:59",
    "min_booking": null,
    "max_booking": null,
    "campaign_statuses": ["live","finished"],
    "mediaplayer_ids": [36],
    "by_group": 1
}
```
`Success Response:`
- Status: 200 OK
```
[
    {
        "campaign_id": 1,
        "company_group_id": 3,
        "name": "Campaign 1",
        "asset_folder_id": 1882,
        "color_hex": "#ABCDEF",
        "total_campaign_sub": 2,
        "start_date_time": null,
        "end_date_time": null,
        "status": "live",
        "total_assignments": 1,
        "createdAt": "2024-11-08 12:10:09",
        "updatedAt": "2024-11-08 12:10:09"
    },
    {
        "campaign_id": 8,
        "company_group_id": 3,
        "name": "Campaign 8",
        "asset_folder_id": 1882,
        "color_hex": null,
        "total_campaign_sub": 2,
        "start_date_time": "2024-09-08 00:00:00",
        "end_date_time": "2024-11-08 00:00:00",
        "status": "finished",
        "total_assignments": 0,
        "createdAt": "2024-11-08 12:10:09",
        "updatedAt": "2024-11-08 12:10:09"
    }
]
```

#### 2.2.2.2. Fetch a campaign with sub divisions (campaigns)
`Request:`
```
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
```
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

`Error Response:`

- Status: 404 Not Found
```
{
  "error": "Campaign not found"
}
```
[Top ↑](#1-campaign)

### 2.2.3. Update a campaign by ID

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

---

*Campaign_sub Parameters*
| Parameter           | Type              | Required  | Description               |
|---------------------|-------------------|-----------|---------------------------|
| order               | integer           | True      | position ID               |
| name                | string            | True      | division name             |
| start_date_time     | datetime          | False     | validity start date time  |
| end_date_time       | datetime          | False     | validity end date time    |
| content             | array             | True      | content ID in order       |
| mediaplayer_id      | array             | False     | assigned media player ID  |


To remove start_data, start_time, end_date and end_time values, set empty value for the key. Any unset key will keep existing values.

`Request:`
```
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
      "mediaplayer_ids": [1144, 1153]
    },
    {
      "order": 2,
      "name": "division 2",
      "start_date_time": "2024-10-02 13:00:00",
      "end_date_time": "2024-11-30 20:00:00",
      "content": [56260, 56261, 56262, 56263],
      "mediaplayer_ids": [1415, 1416, 1417]
    }
  ]
}
```
`Response:`

- Satus: 200 OK
```
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
```
{
  "error": "Campaign not found"
}
```

[Top ↑](#1-campaign)

### 2.2.4. Delete single or multiple  campaigns

Endpoint: https://onqcms.com/api/campaign/delete

`Request:`
```
{
  "campaign_id": 12
}
```
`Response:`

- Status: 204 No Content

- Response Body: None

`Error Response:`
   
- Status: 404 Not Found
```
{
  "error": "Campaign not found"
}
```
[Top ↑](#1-campaign)
### 2.2.5. Campaign Availability
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
```
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

```
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
[Top ↑](#1-campaign)

[Back to Home](README.md)