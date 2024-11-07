# Playlist

Playlists define how content is to be played on any player assigned to the playlist. 

- [Bulk Playlist](#create-a-bulk-playlist)

## Create a Bulk Playlist

### Endpoint: playlist_bulk/create

The bulk playlist request creates a special type of playlist that is unique to each assigned player. This playlist can be viewed, edited and deleted, but not reassigned via the CMS. If the bulk playlist request is called on a player that already has an API playlist assigned, the playlist is deleted and recreated with the settings as per the new request.

### Request Parameters
The CMS expects an array of playlist objects as the input.

### Playlist JSON Object

| Parameter | Type                          | Required | Description |
|-----------|-------------------------------|----------|-------------|
| players   | array (strings - player IDs)  | True     | The media players to be assigned to the playlist |
| playlist  | array (playlist asset object) | True     | A list of assets to playback. See below table for parameters. Assets will play back in the order that they are in the array, restarting playback from the start once the end of the playlist is reached.

### Playlist Asset JSON Object

| Parameter | Type                     | Required | Description |
|-----------|--------------------------|------------|-------------|
| uid       | string                   | If no file | The unique ID of an asset on the CMS to play back. Must not define file if using uid. |
| file      | object (see table below) | If no uid  | The details of an asset for the CMS to fetch from a remote url, if it hasn't been fetched already. Must not define uid if using file. |
| duration  | integer                  | False      | The playback duration for images, defaults to 10 seconds. |
| tags      | object (see table below) | False      | Tags to control which players play back this asset.
| validity  | object (see table below) | False      | An object that defines whether an asset is valid to play within this playlist. See table below for values. |

### File JSON Object

| Parameter  | Type                     | Required | Description |
|------------|--------------------------|----------|-------------|
| url        | string                   | True     | The unique ID of an asset on the CMS to play back. Must not define file if using uid. |
| name       | object (see table below) | False    | The details of an asset for the CMS to fetch from a remote url, if it hasn't been fetched already. Must not define uid if using file. |
| hash       | string                   | False    | The md5 hash of the asset. An error is returned if the hash of the downloaded file doesn't match this value. |
| folder     | array (string)           | False    | The folder that the asset is stored in. Contains the full folder path, with the 'leftmost' item being the closest to root level. For example, an asset stored in a 'campaigns' folder, which is itself stored in a 'marketing' folder would have a folder value of ["marketing","campaigns"]. Asset will be stored at the root level if not present.
| references | array (string)           | False    | A list of additional names that refer to this asset. Used to provide an additional name/tag against the asset in the proof of play reports.
| validity   | object (see table below) | False    | An object that defines whether an asset is valid to play, regardless of whether it is an active playlist or not. See table below for values. |

### Validity JSON Object

| Parameter             | Type    | Description |
|-----------------------|---------|-------------|
| start_date            | string  | The asset will not play before this date. YYYY-MM-DD format, eg 2022-07-15. |
| end_date              | string  | The asset will not play after this date. YYYY-MM-DD format, eg 2022-09-15. |
| start_time            | string  | The asset will not play before this time. HH:MM format, 24 hour time, eg 07:00. |
| end-time              | string  | The asset will not play after this time. HH:MM format, 24 hour time, eg 17:00. |
| date_time_independent | boolean | If true, the date and time conditions are evaluated separately - the asset will only play between the valid times between the valid dates. If false, the asset will play constantly after the valid time and date until the end time and date. |

### Tags JSON Object

| Parameter | Type           | Description |
|-----------|----------------|-------------|
| all       | array (string) | The player must have all of these tags to play the asset. |
| any       | array (string) | The player needs any one of these tags to play the asset. |
| none      | array (string) | The player must *not* have any of these tags to play the asset. |

### Example Request
```
curl -H "Authorization: bearer demo123" -X POST -d '[
	{
		"players":["demo123"],
		"playlist":[
			{
				"file":{
					"url": "https://onqcms.com/api-test/test-pic.jpg",
					"hash": "00000000000000",
					"name":"demo image",
					"folder":["marketing","campaigns"],
					"references":["demo","demo_pic"],
					"validity":{
						"start_date":"2022-07-15",
						"end_date":"2022-09-15",
						"start_time":"07:00",
						"end_time":"15:00",
						"date_time_independent":true
					}
				},
				"duration":5,
				"tags":{
                    "all":["all 1","all 2"]
					"any":["any tag1","any tag2", "any tag3"],
                    "none":["exclude"]
				},
				"validity":{
					"start_date":"2022-07-15",
					"end_date":"2022-09-15",
					"start_time":"07:00",
					"end_time":"15:00",
					"date_time_independent":true
				}
			}
		]
	}
]' \
https://onqcms.com/api/playlist_bulk/create
```
The above snippet creates (or deletes and recreates) and assigns an API playlist for the player **demo123**. The playlist will loop over one piece of content that has been fetched from the url https://onqcms.com/api-test/test-pic.jpg while the validity is true. The asset will play on any player with all the below:
- either the **any tag1**, **any tag2** or **any tag3** tags; and 
- which do not have the **exclude** tag; and
- which have both the **all1** and **all2** tags.

[Home](README.md)