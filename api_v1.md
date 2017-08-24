# Fullscreen Direct API V1
Use the Fullscreen Direct API to develop custom, standalone integrations with Fullscreen Direct.

### Under Development
The API is actively evolving - please check back here for updates and changes.

#### Updates

##### 2017-02-15
- All urls have been updated to use singular resource names. Any plural endpoints will continue to work as they are now aliased to the singular version for backward compatibility.
- Endpoints between content items, such as Photos, Events, Audio, etc, have been updated to be more consistent with having the same abilities on each endpoint, such as updating or creating, and sub-resource lists now have the same features.
- Store Item Creation endpoint now treats the 'store_item' as an optional namespacer, making it behave like other REST endpoints.

### Fork us on GitHub!
All of Fullscreen Direct's documentation is up on GitHub for you to fork, modify, and improve. Join us over there to request features, add suggestions, and report bugs. What are you waiting for? [Git to it!](https://github.com/stagebloc/docs)

# General Information
The root URL of the API is `https://api.fullscreendirect.com/v1/`. It can generally be assumed that writing endpoints use `POST` requests and reading endpoints use `GET` requests.

### Authorization
Connecting with the Fullscreen Direct API uses the OAuth 2.0 standard. You must first [create a Fullscreen Direct account](https://www.fullscreendirect.com/signup) and then register your application in the Fullscreen Direct backend to receive a client ID and secret that will allow users to connect with your application. To do this, click on your account at the top of the page and then "Account Settings" followed by "Developer Center".

Requests can be be made with just a `client_id` parameter, but this is restricted to reading (listing) endpoints. In order to make use of any writing (editing) endpoints, an access token (i.e. validated user) must be present.

### Responses
All dates returned are in `GMT / UTC (+0000)` unless otherwise specified. The format of these dates follows PHP [`date() function`](http://php.net/date) function and is `Y-m-d H:i:s` (i.e. `2014-01-01 12:00:00`).

Responses are returned as `JSON`. To receive a `JSONP` response, include a `GET` parameter named `jsonp` specifying the name of your callback method.

Often times, large objects will be returned with just their ID instead of the whole object if they are nested within the main response. To have these objects expanded, just pass a `GET` parameter named `expand` with the comma separated string of the keys you want to expand. For instance, passing `expand=user` would then return the JSON for an entire user as opposed to just the ID of that user.

There are a lot of different model objects represented in the API. If you pass `expand=kind` with your requests, each object type will have a unique `kind` key in the JSON with a value of the object that the JSON structure represents. This can be useful for knowing what type of object to parse that JSON into if you're using native objects in your language as opposed to just arrays.

The general structure of a success response can be seen below.  The `data` key will contain the actual response data whereas the `metadata` key will contain informational content about the request.

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            ...
        }
    }

The general structure of an error response can be seen below.

    {
        "metadata": {
            "code": 400,
            "error_type": "InvalidData",
            "error": "The error string to present to the user",
            "dev_notes": "Some notes to help a developer debug the issue"
        },
        "data": null
    }

# Authentication
Fullscreen Direct uses the [OAuth2](http://oauth.net/2/) authentication process to get an access token from a request token. The access token is then used to make all subsequent requests.

Access tokens can be revoked on a per-application basis at any point in time by the user in their settings area in the Fullscreen Direct backend.

Depending on the endpoint, differing levels of authentication exist. Some endpoints require that the authenticated user be a fan while other require that they be an admin of the account. For those that require being an admin, some require a specific level of admin privileges, such as an editor or an author. Appropriate error messages will be shown if you try to access an endpoint you don't have access to.

A `client_id` must usually be passed with each request depending on the authentication level of that endpoint. However, if the request is made on behalf of an authenticated user, a `client_id` is not necessary.

Once an access token is received, it should be passed with the request as an HTTP header: `Authorization: OAuth <access token here>`

Account content:
> <p> requests without authentication or with non-fans will receive only non-exclusive content, except [audio](/developers/api/v1/audio/list) and [store item](/developers/api/v1/store-and-commerce/list--store-item) list endpoints which return all content.
> <p> requests identifying a fanclub member (with the `honor_exclusivity` query string parameter) will receive content exclusive to their fanclub tier as well as non-exclusive content.
> <p> requests identifying an account administrator will receive all content regardless of its exclusivity.


## Request Token
`[POST] /oauth2`

This endpoint is used to get a request token.

username _(required)_
> <p> the email or username of the user trying to authenticate

password _(required)_
> <p> the password of the user trying to authenticate

response\_type _(required)_
> <p> the response type you'd like to receive
> <p> possible value is `code`

client\_id _(required)_
> <p> the ID of the application this user is connecting to (applications can be created in the backend of Fullscreen Direct)
> <p> possible values are any client ID that matches a registered application

redirect\_uri _(required)_
> <p> the redirect URI tied to the application you're connecting the user to
> <p> possible values are the URI that matches the application being used

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            "code": "<client ID here>"
        }
    }

## Access Token
`[POST] /oauth2/token`

This endpoint is used to get an access token once a request token is received.

client\_id _(required)_
> <p> the ID of the application this user is connecting to (applications can be created in the backend of Fullscreen Direct)
> <p> possible values are any client ID that matches a registered application

client\_secret _(required)_
> <p> the secret of the application this user is connecting to
> <p> possible values are the client secret that matches the application of the passed client ID

grant\_type _(required)_
> <p> the grant type being requested
> <p> possible value is `authorization_code`

code _(required)_
> <p> the request token code received after connecting with the `/oauth2` endpoint
> <p> possible values are any string received during the authentication / connection process

include\_admin\_accounts
> <p> whether or not to include the authenticated user's admin accounts with the `access_token` in the response
> <p> accepted values are `true` or `false`
> <p> defaults to `false`

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            "access_token": "<access token here>",
            "scope": "non-expiring",
            "user": 1,
            "admin_accounts": [
                <admin accounts here if requested>
            ]
        }
    }

# Applications
These endpoints revolved around the application requests are being made on behalf of.

## Enable User Push Token
`[POST] /application/push/token`

Allows an application to give Fullscreen Direct a push notification for a user so that they can be messaged via push notifications from the Fullscreen Direct backend.
**Note:** For iOS development, you'll need to upload your Apple Push Notification Certificate so we can authenticate push notifications with your tokens. Please contact us to handle this.

### POST Parameters

`token` _(required)_
> <p> the push notification token for this user

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
	        "message": "Token successfully saved!"
	    }
	}

# Accounts
These endpoints revolve around Fullscreen Direct accounts and their data. An account can have any number of admins, and all content on Fullscreen Direct is tied to an account.

## Definition

    {
        "id": 2937,
        "url": "http://customdomain.dev/",
        "custom_domain": "customdomain.dev",
        "stagebloc_url": "customdomain_no_theme",
        "name": "customdomain no theme",
        "description": "",
        "type": "personal",
        "stripe_enabled": true,
        "color": "79,156,215",
        "verified": true,
        "photo": null,
        "user_is_admin": false
    }

`user_is_admin`
> <p> whether or not the user is an admin in some capacity for this account

`user_role`
> <p> if the user is an admin, this will be what type of admin they are (`owner`, `editor`, `author`, `read_only`, or `fan_club_moderator`)

## Create
`[POST] /account`

Creates a new account and makes the currently authenticated user an admin for that account

### POST Parameters

`name` _(required)_

`stagebloc_url` _(required)_
> <p> the URL of the account on Fullscreen Direct (i.e. www.fullscreendirect.com/fsd_url)

`description`

`image`
> <p> an image file to use for the account's image

## List
`[GET] /account`

Gets a listing of various types of accounts for a user

### GET Parameters

`order_by`
> <p> what to order the accounts by (possible values include `created`, `modified`, and `accountName`)

`admin`
> <p> whether or not to include accounts the user is an admin of (defaults to true)

`following`
> <p> whether or not to include accounts the user is follow (defaults to true)

## Retrieve
`[GET] /account/{accountId}`

Gets an account's information from its ID.

## Update
`[POST] /account/{accountId}`

Updates an account by its ID. Only admins of the account can use this endpoint.

### POST Parameters

`name`

`description`

`stagebloc_url`

## User Follow
`[POST] /account/{accountId}/follow`

This endpoint allows a user to follow an account.

**Note:** If the `tier` the user is trying to follow is a paid tier, that functionality is not possible through the API.

## User Unfollow
`[DELETE] /account/{accountId}/follow`

This will have a user unfollow an account regardless of the tier they are on

### POST Parameters

`tier` _(required)_
> <p> the tier the user wants to follow this account on
> <p> must be either 1, 2, or 3

`expiration`
> <p> an expiration date to use for this membership
> <p> must be a valid datetime string
> <p> by default the system will determine a time to use for the expiration based on the tier's settings, but you can use this to override that

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            "user": { ... see the structure for a user object in /user endpoints ... },
            "account": {
					... see the structure for an account object in /account endpoints ...
				}
        }
    }

## List - Content
`[GET] /account/{accountId}/content`

Gets an activity stream of recent content from the account.

### GET Parameters

filter
> <p> a comma separated list of content types to include
> <p> in the returned stream of events.
> <p> accepted values are audio, audio_playlists, blog,
> <p> events, photos, photo_albums, statuses, store, videos,
> <p> and video_playlists
> <p> defaults to blog, statuses, photos, videos

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 20

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

## List - Fans
`[GET] /account/{accountId}/fans`

Gets a list of fans for the account.

### GET Parameters

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

order_by
> <p> how to order the returned results
> <p> accepted values are `created`, `name`, `email`, and `username`
> <p> defaults to `created`

direction
> <p> which order to return the results in
> <p> accepted values are ASC or DESC
> <p> defaults to DESC

## List - Child Accounts
`[GET] /account/{accountId}/children/{type}`

Gets the children accounts of a parent account. The `{type}` is optional and omitting it will return children accounts of all types. Otherwise, simply take the type of children accounts you are looking for, replace the spaces with dashes, and use that as the `{type}`.

In the response, `child_account_types` will show all of the types regardless of the `{type}` requested.

### GET Parameters

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            "child_account_types": [
                "artist",
                ...
            ],
            "child_accounts": [
                <child accounts listed here>
            ]
        }
    }

### Response Explanation

child\_account\_types
> <p> An array of strings of the different types of children accounts the parent account has setup

child_accounts
> <p> An array of account objects (see account listing endpoint for structure). It will also add a `child_account_type` key to each account specifying the type it is

# Users
These endpoints revolve around Fullscreen Direct users and their data. A user on Fullscreen Direct can be an admin for any number of accounts, and their login is tied to their email address. A user can also be a fan of any number of accounts. These endpoints allow for management of both admin and fan relationships between users and their accounts.

## Definition

    {
        "id": 8,
        "url": "https:\/\/www.fullscreendirect.com\/user\/testuser",
        "created": "2009-10-27 14:29:16",
        "name": "Test User",
        "username": "testuser",
        "bio": "Biography here...",
        "color": "70,170,255",
        "birthday": "1995-07-05",
        "gender": "male",
        "email": "testuser@sb.com",
	"phone_number": "262 555 1234",
        "photo": {
            "width": 525,
            "height": 500,
            "images": {
                "thumbnail_url": "http://placekitten.com/300/300",
                "small_url": "http://placekitten.com/300/300",
                "medium_url": "http://placekitten.com/300/300",
                "large_url": "http://placekitten.com/300/300",
                "original_url": "http://placekitten.com/300/300"
            }
        },
        "access_token": "<access_token_here>",
        "scope": "non-expiring"
    }

`color`
> <p> the RGB value of the color the user has chosen in our backend

`birthday`
> <p> this is only included for the currently authenticated user (i.e. won't show up for users other than the authenticated one)

`email`
> <p> this is only included for the currently authenticated user (i.e. won't show up for users other than the authenticated one)

`phone_number`
> <p> this is only included for the currently authenticated user (i.e. won't show up for users other than the authenticated one)

`photo`
> <p> the user's photo
> <p> the width and height are the dimensions of the originally uploaded photo

`access_token` & `scope`
> <p> Only returned when creating user.

## Create
`[POST] /user`

Creates a new user on Fullscreen Direct.

### POST Parameters

`email` _(required)_
The email address to use for the user

`password` _(required)_

`birthday` _(required)_

`source_account_id`
An account ID for an account to follow / join the Fan Club of during signup, this will then send a Fan Club welcome email instead of the generic Fullscreen Direct signup email

`name`

`username`
The username for this user, it must be unique across all of Fullscreen Direct and only accepts letters, numbers, and underscores.

`gender`
Accepts `male` or `female`

## Retrieve - Current User
`[GET] /user/me`

Gets the currently authenticated user's information.

## Update - Current User
`[POST] /user/me`

Updates the currently authenticated user's information.

### POST Parameters

`bio`

`birthday`

`email`

`username`

`name`

`gender`

## Retrieve
`[GET] /user/{userId}`

Gets a user by their user ID

## List - Content
`[GET] /user/{userId}/content/updates`

This returns a listing of content that this user has created across any account they are a part of. Each content object returned in this response will have its `kind` value automatically expanded, and the structure of the content type will match it as if you were loading that single object via its own endpoint. Looking at the structure of a audio response, for example, will show you what an audio object in the array of content returned here will be formatted like.

### GET Parameters

`filter` _(required)_
A comma separated string for the type(s) of content to be returned. Allowed values are audio, blog, event, photo, status, store, and video.

## List - Like
`[GET] /user/{userId}/content/like`

This returns a listing of content that this user has liked across Fullscreen Direct.  Each content object returned in this response will have its `kind` value automatically expanded, and the structure of the content type will match it as if you were loading that single object via its own endpoint. Looking at the structure of a audio response, for example, will show you what an audio object in the array of content returned here will be formatted like.

### GET Parameters

`filter` _(required)_
A comma separated string for the type(s) of content to be returned. Allowed values are audio, blog, event, photo, status, store, and video.

# Fan Clubs
Users on Fullscreen Direct can join Fan Clubs associated with accounts. By default an account doesn’t have a Fan Club set up, but creating one adds extra functionality such as having three different membership tiers and requiring payment for joining the Fan Club.

## Overview Dashboard
`[GET] /account/{accountId}/fanclub/dashboard`

This endpoint will return various stats and information about the account's fan club (similar to the data shown on the fan club dashboard in the Fullscreen Direct backend)

### Definition

    {
        "kind": "fan_club_dashboard",
        "totals": {
            "fans": 27,
            "likes": 17,
            "comments": 78,
            "posts": 21,
            "gender": {
                "male": 10,
                "female": 17,
                "unknown": 0
            },
            "tiers": {
                "1": {
                    "name": "Free Tier",
                    "fans": 10
                },
                "2": {
                    "name": "Basic Tier",
                    "fans": 5
                },
                "3": {
                    "name": "Premium Tier",
                    "fans": 12
                }
            }
        }
    }

## Definition

    {
        "title": "Awesome Fan Club",
        "description": "This fan club is so cool!",
        "account": 1,
        "moderation_queue": false,
        "tier_info": {
            "1": {
                "title": "Basic Membership",
                "description": "",
                "price": 0,
                "discount": 0,
                "membership_length_interval": 1,
                "membership_length_unit": "once",
                "renewal_price": null,
                "can_submit_content": false
            },
            "2": {
                "title": "Standard Membership",
                "description": "",
                "price": 10,
                "discount": 0,
                "membership_length_interval": 3,
                "membership_length_unit": "month",
                "renewal_price": null,
                "can_submit_content": true
            },
            "3": {
                "title": "Premium Membership",
                "description": "You get a T-Shirt with this tier!",
                "price": 50,
                "discount": 10,
                "membership_length_interval": 1,
                "membership_length_unit": "year",
                "renewal_price": 10,
                "can_submit_content": true
            }
        },
        "allowed_content_sections": {
            "blog": true,
            "statuses": false,
            "photos": true,
            "videos": true,
            "audio": true
        }
    }

membership\_length\_interval and membership\_length\_unit
> <p> together these represent how long the membership lasts
> <p> the unit can be `once`, `year`, or `month`

discount
> <p> the discount (in percentage points) that this tier gives fans off of store items for this account

allowed\_content\_sections
> <p> the types of content fans are able to submit to this Fan Club


## List
`[GET] /account/fanclub/{type}`

This endpoint retrieves a listing of Fan Clubs on Fullscreen Direct. The accepted values for `type` are `following`, `featured`, or `recent` with `featured` being the default. Note that for `featured` Fan Clubs, `limit` and `offset` are ignored as it will return all featured Fan Clubs at the time.

### GET Parameters

`limit`
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 20

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

## Retrieve
`[GET] /account/{accountId}/fanclub`

This will return the details about the Fan Club.

## Update
`[POST] /account/{accountId}/fanclub`

This will create or update a Fan Club that belongs to the account with the given `accountId`.

### POST Parameters

`title`
> <p> The title of the Fan Club as a whole (as opposed to any tier)

`description`
> <p> The description of the Fan Club as a whole (as opposed to any tier)

`moderation_queue`
> <p> Whether or not to have the moderation queue on for this Fan Club.

`tier_info` _(required)_
> <p> An array structured similar to the return data for each array in the response below used to setup each tier. If one of the tier keys is missing from this array, that tier will be removed from the Fan Club. Instead of the two returned membership values, pass a `membership_length` parameter as a value in seconds that must match either zero, one month, three months, six months, or a year using number of seconds in a day (86,400) multiplied by the days in that interval (months are assumed to be 30 days in length).

# Audio
These endpoints revolve around the ability to upload and stream audio through Fullscreen Direct. Audio consists of both individual tracks and those tracks being organized into various playlists.

## Definition


### Audio

    {
        "id": 773,
        "title": "The Best Track ever",
        "duration": 123,
        "description": "",
        "lyrics": "",
        "artist":"",
        "photo": 0,
        "account": 1,
        "created_by": 8,
        "created": "2014-11-14 17:48:40",
        "modified_by": 8,
        "modified": "2014-11-14 17:49:04",
        "short_url": "http:\/\/stgb.dev\/a\/ek",
        "stream_url": "https:\/\/stagebloc-audio.s3.amazonaws.com\/1\/stream\/607_20120327_192954_1_101.mp3",
        "embed_code": "\u003Ciframe src=\u0022https:\/\/widgets.fullscreendirect.com\/audio\/773\u0022 style=\u0022width:250px;height:70px;border-radius:6px\u0022\u003E\u003C\/iframe\u003E",
        "sticky": false,
        "exclusive": true,
        "exclusive_tiers": [1, 3],
        "private": false,
        "in_moderation": false,
        "custom_field_data": [ ],
        "is_fan_content": false,
        "comment_count": 0,
        "like_count": 0,
        "user": 8,
        "edit_url": "https:\/\/www.fullscreendirect.com\/demo\/admin\/audio\/edit\/773",
        "user_has_liked": false
    }

`duration`
> <p> the length (in seconds) of this track

`stream_url`
> <p> the URL to use to stream this audio file (could be a Fullscreen Direct URL or a third party one such as SoundCloud)

`in_moderation`
> <p> if this audio track was submitted by a fan, this handles if it is still in moderation or not

`is_fan_content`
> <p> whether or not this audio was submitted by a fan or not

`user_has_liked`
> <p> if the request as made with a logged in user, this will signify if that user has liked the track or not

`custom_field_data`
> <p> if you have custom data set on audio for your account, the slugs will show up as keys here with their values

### Audio Playlist

    {
        "id": 1,
        "title": "Best Music of the 90s",
        "description": "",
        "short_url": "http:\/\/stgb.dev\/ap\/4q",
        "embed_code": "\u003Ciframe src=\u0022https:\/\/widgets.fullscreendirect.com\/audio\/playlist\/198\u0022 style=\u0022width:250px;height:320px;border-radius:6px\u0022\u003E\u003C\/iframe\u003E",
        "created_by": 1,
        "created": "2014-11-14 11:17:50",
        "modified_by": 1,
        "modified": "2014-11-19 21:37:25",
        "comment_count": 0,
        "like_count": 0,
        "custom_field_data": [ ],
        "artist": "",
        "label": "",
        "audio": 4,
        "user_has_liked": false
    }

`embed_code`
> <p> Fullscreen Direct has audio playlist widgets for playlists that you can use by embedding this code on an HTML page if you so choose

`artist`
> <p> admins can add a special artist to a playlist if it something different than the name of the account

`audio`
> <p> the number of tracks in the playlist, or an array of audio objects if you pass specify to expand `audio` as a parameter

`custom_field_data`
> <p> if you have custom data set on events for your account, the slugs will show up as keys here with their values

## List
`[GET] /account/{accountId}/audio`

This endpoint can used to list all the audio for a certain account.

### GET Parameters

`honor_exclusivity`
> <p> whether to restrict content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to show all content and 1 to restrict to only exclusive content visible to the user.
> <p> defaults to 0

`playlist_id`
> <p> an audio playlist ID to limit the audio results to
> <p> accepted values are an ID of any playlist that belongs to the same account
> <p> defaults to none

`limit`
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

`offset`
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

`order_by`
> <p> how to order the returned items
> <p> accepted values are `created`, `modified`, and `recordedDate`
> <p> if `playlist_id` is provided this can also be `accountAudioSortOrder`
> <p> defaults to `created`

`direction`
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

## Create
`[POST] /account/{accountId}/audio`

This endpoint can be used to create a single audio track for an account. It currently only allows actual uploads as opposed to also allowing SoundCloud or other third party source URLs.

### POST Parameters

`audio` _(required)_
> <p> the contents of the audio file itself

`title` _(required)_
> <p> the title of the track

`description`
> <p> the description of the track

`public`
> <p> whether or not this audio track should be public (defaults to true)

`exclusive` _(deprecated)_
> <p> whether or not this audio track should be exclusive

`exclusive_tiers[]` _{array}_
> <p> what fan club tier(s) the audio track is exclusive to
> <p> Can be included multiple times to specify multiple tiers

## Retrieve
`[GET] /account/{accountId}/audio/{audioId}`

This endpoint can be used to get a single audio track from an account.

## Update
`[POST] /account/{accountId}/audio/{audioId}`

## Delete
`[DELETE] /account/{accountId}/audio/{audioId}`

## List Playlists
`[GET] /account/{accountId}/audio/playlists`

This endpoint can be used to list audio playlists that are available for an account.

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

order_by
> <p> how to order the returned items
> <p> accepted values are `created` and `modified`
> <p> defaults to `created`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

## Retrieve Playlist
`[GET] /account/{accountId}/audio/playlist/{playlistId}`

This endpoint can be used to get an individual audio playlist.

## Update Playlist
`[POST] /account/{accountId}/audio/playlist/{playlistId}`

`title`

`description`

`artists`

`label`

`exclusive_tiers[]`

`sticky`

## Delete Playlist
`[DELETE] /account/{accountId}/audio/playlist/{playlistId}`

## Add Audio to Playlist
`[POST] /account/{accountId}/audio/playlist/{playlistId}/audio`

`audio_id`
Integer or array of integers of audio ids.

# Blog
Endpoints around interacting with blog posts on Fullscreen Direct

## Definition

### Blog

    {
        "id": 7824,
        "account": 2937,
        "title": "Test Post, Please Ignore",
        "body": "<p>My best work so far!</p>,
        "category": "Random",
        "slug": "test-post-please-ignore",
        "short_url": "http://stgb.dev/b/3jU",
        "published": "2013-02-01 05:00:00",
        "created": "2013-02-01 05:00:00",
        "modified": "2016-07-06 13:20:13",
        "sticky": false,
        "exclusive": false,
        "exclusive_tiers": [],
        "in_moderation": false,
        "is_fan_content": false,
        "comment_count": 12,
        "like_count": 0,
        "user": 21,
        "user_has_liked": false
    }

## List
`[GET] /account/{accountId}/blog`

This endpoint can be used to list the blog posts for an account.

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

order_by
> <p> what to order the results by
> <p> accepted values are `published`, `created`, or `modified`
> <p> defaults to `published`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

## Create
`[POST] /account/{accountId}/blog`

## Retrieve
`[GET] /account/{accountId}/blog/{blogId}`

## Update
`[POST] /account/{accountId}/blog/{blogId}`

## Delete
`[DELETE] /account/{accountId}/blog/{blogId}`

# Events
Events on Fullscreen Direct can be many different things including shows, conferences, etc.

## Definition

### Event

    {
        "id": 1611,
        "title": "Awesome Thing Happening!",
        "description": "",
        "short_url": "http:\/\/stgb.dev\/e\/tM",
        "ticket_price": 0,
        "ticket_link": "",
        "start_date_time": "2014-10-12 11:00:00 -04:00",
        "end_date_time": "2014-10-12 14:00:00 -04:00",
        "timezone": "America\/New_York",
        "comment_count": 1,
        "like_count": 0,
        "attending_count": 0,
        "custom_field_data": {
            "is-sold-out": false
        },
        "location": {
            "name": "Fullscreen Direct Office",
            "street_address": "935 W Chestnut",
            "street_address_2": "",
            "city": "Chicago",
            "state": "IL",
            "postal_code": "12345",
            "country": "UA",
            "latitude": 0,
            "longitude": 0
        },
        "user_has_liked": false,
        "user_is_attending": "maybe"
    }

ticket_link
> <p> a link to a third party service where tickets for this events can be purchased

start\_date\_time & end\_date\_time
> <p> the start / end date and time of the event, unlike other timestamps they won't be in GMT time but will specify an offset from GMT

timezone
> <p> the timezone the event is in which is specified by the hour offset in the start and end time

user\_is_attending
> <p> if the request is made with a logged in user, this will specify `yes`, `no`, or `maybe`

custom\_field\_data
> <p> if you have custom data set on events for your account, the slugs will show up as keys here with their values

## List
`[GET] /account/{accountId}/event`

This endpoint can be used to list the events for an account.

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

direction
> <p> the direction to list results in
> <p> accepted values are `ASC` or `DESC`
> <p> defaults to `DESC`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

order_by
> <p> what to order the results by
> <p> accepted values are `created`, `startDateTime`, `endDateTime`
> <p> defaults to `startDateTime`

upcoming
> <p> whether or not to include upcoming events
> <p> accepted values are true or false
> <p> defaults to true

past
> <p> whether or not to include past events
> <p> accepted values are true or false
> <p> defaults to true

## Create
`[POST] /account/{accountId}/event`

`title`

`description`

`exclusive_tiers[]`

`sticky`

`start_date_time`

`end_date_time`

`timezone`

`public`

## Retrieve
`[GET] /account/{accountId}/event/{eventId}`

## Update
`[POST] /account/{accountId}/event/{eventId}`

`title`

`description`

`exclusive_tiers[]`

`sticky`

`start_date_time`

`end_date_time`

`timezone`

`public`

## Delete
`[DELETE] /account/{accountId}/event/{eventId}`

# Photos
These endpoints revolve around the ability to upload and view photos on Fullscreen Direct. Photos consist of both individual images as well as those images being organized into various photo albums.

## Definition

### Photo

    {
        "id": 3311,
        "title": "Super Cool Image",
        "created": "2014-12-07 23:37:32",
        "modified": "2014-12-07 23:37:36",
        "short_url": "http:\/\/stgb.dev\/p\/Z6",
        "description": "",
        "width": 534,
        "height": 654,
        "sticky": false,
        "exclusive": false,
        "exclusive_tiers": [],
        "in_moderation": false,
        "is_fan_content": false,
        "comment_count": 0,
        "like_count": 0,
        "images": {
            "thumbnail_url": "http:\/\/cdn.stagebloc.com\/production\/photos\/1\/thumbnail\/20141207_233732_1_3311.jpeg",
            "small_url": "http:\/\/cdn.stagebloc.com\/production\/photos\/1\/small\/20141207_233732_1_3311.jpeg",
            "medium_url": "http:\/\/cdn.stagebloc.com\/production\/photos\/1\/medium\/20141207_233732_1_3311.jpeg",
            "large_url": "http:\/\/cdn.stagebloc.com\/production\/photos\/1\/large\/20141207_233732_1_3311.jpeg",
            "original_url": "http:\/\/cdn.stagebloc.com\/production\/photos\/1\/original\/20141207_233732_1_3311.jpeg"
        },
        "user": 8,
        "custom_field_data": {
            "is-cool": true
        }
    }

width & height
> <p> the width / height in pixels of the originally uploaded image

images
> <p> an array of image URLs that are various sizes

### Photo Album

    {
        "id": 1724,
        "title": "Photo Album Title",
        "description": "",
        "short_url": "http:\/\/stgb.dev\/pa\/vJ",
        "created_by": 1,
        "created": "2014-12-04 13:44:26",
        "modified_by": 8,
        "modified": "2014-12-16 22:38:56",
        "like_count": 0,
        "custom_field_data": [],
        "photos": 1
    }

photos
> <p> the number of photos this photo album has
> <p> if you specify to expand the photos key, it will be an array of the photos for this album (structured the same as the photo listing endpoint)

## List
`[GET] /account/{accountId}/photo`

Lists photos from an account.

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

order_by
> <p> how to order the returned items
> <p> accepted values are `created` and `modified`
> <p> defaults to `created`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

## Create
`[POST] /account/{accountId}/photo`

Uploads a photo to an account.

### POST Parameters

`photo` _(optional)_
The photo file itself, if `photo_url` is not passed this parameter is required

`photo_url` _(optional)_
A url for a photo, if `photo` is not passed this parameter is required

`title` _(required)_
The title to use for the photo

`description`
A longer description of the photo

`fan_content`
If the photo is being posted by an admin as a fan photo, this can be set to `true`

## Retrieve
`[GET] /account/{accountId}/photo/{photoId}`

This endpoint can be used to get a single photo from an account. Note that if the photo is fan submitted or exclusive, it will require that the request be made by a logged in user who has access to that account as a fan or admin.

## Update
`[POST] /account/{accountId}/photo/{photoId}`

This endpoint can be used to update the descriptive data of a single photo from an account. Note that if the photo is fan submitted or exclusive, it will require that the request be made by a logged in user who has access to that account as a fan or admin.

`title`

`description`

`exclusive_tiers` / `exclusive`

## Delete
`[DELETE] /account/{accountId}/photo/{photoId}`

This endpoint can be used to delete a single photo from an account. Note that if the photo is fan submitted or exclusive, it will require that the request be made by a logged in user who has access to that account as a fan or admin.

## List Albums
`[GET] /account/{accountId}/photo/album`

Lists photos albums from an account.

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

order_by
> <p> how to order the returned items
> <p> accepted values are `created` and `modified`
> <p> defaults to `created`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

## Retrieve Album
`[GET] /account/{accountId}/photo/album/{albumId}`

This endpoint can be used to get a single photo album from an account.

## Add Photos to Album
`[POST] /account/{accountId}/photo/album/{albumId}/photo`

This endpoint can be used to add an existing photo to a photo album.

### POST Parameters

`photo_ids` _(required)_
> <p> an array of photo IDs to be added to the album

# Statuses
Statuses on Fullscreen Direct are shorter text updates that account's are able to schedule and post to both Fullscreen Direct itself and their connected social networks.

## Definition

    {
        "id": 1,
        "short_url": "http:\/\/stgb.dev\/s\/2",
        "text": "New status post!",
        "in_moderation": false,
        "is_fan_content": false,
        "published": "2014-08-19 20:31:29",
        "comment_count": 0,
        "like_count": 1,
        "user": 8
    }

## List
`[GET] /account/{accountId}/status`

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

order_by
> <p> how to order the returned items
> <p> accepted values are `created`, `modified`, and `price`
> <p> defaults to `created`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0


## Create
`[POST] /account/{accountId}/status`

This endpoint can be used to post a status to Fullscreen Direct.

### POST Parameters

`text` _(required)_
The text of the status to post

`latitude`
The latitude of the user posting the status to tie a location to it

`longitude`
The longitude of the user posting the status to tie a location to it

## Retrieve
`[GET] /account/{accountId}/status/{statusId}`

This endpoint can be used to get a single status post on Fullscreen Direct.

## Delete
`[DELETE] /account/{accountId}/status/{statusId}`

This endpoint can be used to delete a single status post on Fullscreen Direct. Only admins of the account with the right admin level or the original fan who posted the status are capable of deleting it.

## Likers
`[GET] /account/{accountId}/status/{statusId}/liker`

This endpoint can be used to list the users who have liked this status on Fullscreen Direct.

The response data will be an array of User objects.

# Store and Commerce
These endpoints revolve around Fullscreen Direct store and commerce data in the backend. They can be used for tasks including retrieving store items and orders, updating orders, or getting analytics from a store.

## Overview Dashboard
`[GET] /account/{accountId}/store/dashboard`

This endpoint is used to get stats and data regarding commerce and sales (very similar to the data shown on the Store dashboard in the Fullscreen Direct backend). It will provide all time, overall stats regarding your store and its revenue as well as results per country you've had at least one order in.

When dealing with monetary values, the currency will be USD.

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
	        "kind": "store_dashboard",
	        "totals": {
	            "revenue": 4054.27,
	            "shipping_handling": 1252.82,
	            "tax": 60.82,
	            "orders": 211
	        },
	        "revenue": {
	            "fan_club": 843.90,
	            "store": 3090.05
	        },
	        "averages": {
	            "order_price": 19.21,
	            "fan_value": 137.54
	        },
	        "top_buyers": [{
	            "user": {
                	... see the structure for a user object in /user endpoints ...
	            },
	            "amount_spent": 2731.59
	        }],
	        "countries": {
	            "USA": {
	                "name": "United States of America",
	                "total_orders": 131,
	                "total_revenue": 3158.57
	            },
	            ...
	        }
	    }
	}

## Definition - Store Item

    {
      "id": 26,
      "type": "physical",
      "account": 2937,
      "title": "Physical Product",
      "short_url": "http://stgb.dev/st/s",
      "description": "This is a physical product",
      "description_html": "<p>This is a physical product</p>",
      "sold_out": false,
      "exclusive": false,
      "exclusive_tiers": [],
      "featured": false,
      "created": "2017-01-25 11:20:14",
      "created_by": 8,
      "modified": "2017-01-25 11:20:14",
      "modified_by": 8,
      "fans_name_price": false,
      "category": "Cool Things",
      "price": 10.5,
      "sort_order": 0,
      "prices": [
        {
          "currency": "usd",
          "price": 10.5
        }
      ],
      "require_follow": false,
      "shipping_price_handlers": [
        {
          "id": 14,
          "name": "USPS",
          "price": 4.5
        },
        {
          "id": 15,
          "name": "Flat Rate",
          "price": 5.5
        }
      ],
      "fulfiller": {
        "id": 1,
        "type": 0,
        "name": "Main Warehouse",
        "settings": [],
        "postal_code": "53103",
        "address": {
          "id": 1,
          "name": "Domestic",
          "street_address": "S89 W22630 Loretto Lane",
          "street_address_2": "",
          "city": "Big Bend",
          "state": "WI",
          "postal_code": "53103",
          "country": "US"
        }
      },
      "on_sale": false,
      "options": [
        {
          "name": "an option",
          "sku": "ITM_1_OPT_1",
          "disabled": false,
          "upc": "UPC",
          "unlimited": true,
          "sold_out": false,
          "quantity": null,
          "low_stock_threshold": 10,
          "weight": 2.5,
          "weight_unit": "ounces",
          "height": 0,
          "width": 0,
          "length": 0,
          "additional_price": [
            {
              "currency": "usd",
              "price": 0
            }
          ]
        },
        {
          "name": "minimal option",
          "sku": "WE79XCW4",
          "disabled": false,
          "upc": null,
          "unlimited": true,
          "sold_out": false,
          "quantity": null,
          "low_stock_threshold": 10,
          "weight": 4.5,
          "weight_unit": "ounces",
          "height": 0,
          "width": 0,
          "length": 0,
          "additional_price": [
            {
              "currency": "usd",
              "price": 0
            }
          ]
        }
      ],
      "photo": 3309,
      "album_id": 443,
      "photos": 0
    }

type
> <p> the type of store item this is
> <p> possible values are `physical`, `experience`, `digital`, or `bundle`

shipping_providers
> <p> the available shipping methods for this store item, only shows up for items of type `physical`

options
> <p> the various SKUs available for this product and their related data

bundled_items
> <p> the store items that are in this bundle, only shows up for items of type `bundle`
> <p> other store items, audio tracks, or audio playlists can be bundled and will be listed under their respective section in this array

photos
> <p> the number of photos this store item has
> <p> if you specify to expand the photos key, it will be an array of the photos for this item

album_id
> <p> the ID for the photo album associated with this store item
> <p> only returned if a photo_url is passed and is successfully processed
> <p> this ID can be used to add additional photos for this store item via /photo/album/{albumId}/photo

## List - Store Item
`[GET] /account/{accountId}/store/item`

This endpoint is used to get a listing of store items belonging to an account.

### GET Parameters

`honor_exclusivity`
> <p> whether to restrict content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to show all content and 1 to restrict to only exclusive content visible to the user.
> <p> defaults to 0

order_by
> <p> how to order the returned items
> <p> accepted values are `created`, `modified`, and `price`
> <p> defaults to `created`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

## Create - Store Item
`[POST] /account/{accountId}/store/item`

This endpoint is used to create a new store item and, if relevant, its options. In the case of a physical store item it also configures the shipping price handlers for this new store item.

### Example Request

    {
      "type": "PHYSICAL",
      "price": 10.5,
      "title": "Physical Product",
      "description": "<p>This is a physical product</p>",
      "category": "Cool Things",
      "exclusive_tiers": [
        1,
        2
      ],
      "featured": false,
      "fulfiller_id": 1,
      "options": [
        {
          "name": "an option",
          "sku": "ITM_1_OPT_1",
          "upc": "UPC",
          "weight": 2.5,
          "quantity": null,
          "status": "NORMAL"
        },
        {
          "name": "minimal option",
          "weight": 4.5
        }
      ],
      "shipping_price_handlers": [
        {
          "name": "USPS",
          "price": 4.5,
          "media_mail": true
        },
        {
          "name": "FLAT_RATE",
          "price": 5.5
        }
      ],
      "photo_url": "http://placehold.it/350x350.png"
    }

### POST Parameters

`type` _(required)_
> <p> a string indicating the type of store item to create
> <p> accepted values are PHYSICAL or EXPERIENCE

`price` _(required)_
> <p> a decimal number indicating the base price for this store item

`title` _(required)_
> <p> a string (up to 150 characters) representing the title of the store item

`description`
> <p> a string representing the description of the store item (HTML supported)

`category`
> <p> a string representing a category in which to group this store item

`status`
> <p> a string representing the status of the store item
> <p> accepted values are OKAY or PRIVATE
> <p> defaults to OKAY

`exclusive`
> <p> a boolean, true if this should only be presented to users who are following the account
> <p> defaults to false

`exclusive_tiers`
> <p> an integer occurring 1 to 3 times specifying which fan club tier(s) an item is exclusive to
> <p> should be used _instead of_ [exclusive] above.
> <p> defaults to none

`featured`
> <p> a boolean, true if this should be considered a featured item

`fulfiller_id` _(required for type set to PHYSICAL)_
> <p> an integer ID for a fulfiller already configured in Fullscreen Direct which is associated with this account

`options` _(required for type set to PHYSICAL or EXPERIENCE)_
> <p> an array of objects representing each option (for a T-shirt each option may represent a size, a color, or a combination of the two, etc)
> <p> See _Options_ below for field information

`shipping_price_handlers` _(required for type set to PHYSICAL)_
> <p> an array of objects representing each shipping price handler to be offered to a user attempting to purchase this item. The object may include additional handling fees and options depending on the type of handler indicated.
> <p> See _Shipping Price Handlers_ below for field information

### Options

each element in this array may contain the following fields:

`name` _(required)_
> <p> the name to display when there are multiple options under the store item

`sku`
> <p> an identifier used to distinguish between this option and other options under the same store item
> <p> defaults to an auto-generated alphanumeric SKU

`upc`
> <p> an identifier used to submit download / sales information to SoundScan or Buzz Angle

`quantity`
> <p> null for unlimited or a non-negative integer representing how much stock is currently on hand
> <p> defaults to null

`additional_price`
> <p> a non-negative integer representing the additional price to charge if this option is selected by the user (useful if an XXL t-shirt is more expensive than other variations, for example)
> <p> defaults to no additional price

`weight` _(required for type set to PHYSICAL)_
> <p> a non-negative decimal number indicating the weight in ounces of this option (used for shipping price calculation)

`height` _(required for type set to PHYSICAL)_
> <p> a non-negative decimal number indicating the height in inches of this option (used for shipping price calculation)

`width` _(required for type set to PHYSICAL)_
> <p> a non-negative decimal number indicating the width in inches of this option (used for shipping price calculation)

`length` _(required for type set to PHYSICAL)_
> <p> a non-negative decimal number indicating the length in inches of this option (used for shipping price calculation)

`status`
> <p> a string representing the status for this option
> <p> accepted values are NORMAL or DISABLED
> <p> defaults to NORMAL

### Shipping Price Handlers 
Each element in this array may contain the following fields:

`name` _(required)_
> <p> a string identifying which Shipping Price Handler to enable, one of: FLAT_RATE, USPS, TOWNSEND, BELLTOWER, RLP, FEDEX, CUSTOM_TABLE, PICK_UP, DELIVERY_AGENT, UPS

`price`
> <p> a non-negative integer, an additional price on top of the charges indicated by APIs for USPS, UPS, and FEDEX. For FLAT_RATE, this is the amount to charge a user selecting this shipping method.

`media_mail` _(applies to USPS only)_
> <p> a boolean, true if you want to offer USPS Media Mail pricing for this item

## Update - Store Item
`[POST] /account/{accountId}/store/item/{storeItemId}`

## Retrieve - Store Item
`[GET] /account/{accountId}/store/item/{storeItemId}`

This endpoint is used to get a single store items belonging to an account.

## Definition - Fulfiller

    {
        "id": 15,
        "type": 0,
        "name": "Main Warehouse",
        "settings": {},
        "postal_code": "53103",
        "address": {
            "id": 113,
            "name": "Main Warehouse",
            "street_address": "S89 W22630 Loretto Lane",
            "street_address_2": "",
            "city": "Big Bend",
            "state": "WI",
            "postal_code": "53103",
            "country": "US"
        },
        "public_address": {
            "id": 112,
            "name": "Main Warehouse",
            "street_address": "Loretto Lane",
            "street_address_2": "",
            "city": "Big Bend",
            "state": "WI",
            "postal_code": "53103",
            "country": "US"
        }
    }

## List - Fulfiller
`[GET] /account/{accountId}/store/fulfiller`

This endpoint is use to get a listing of all fulfillers belonging to an account

## Create - Fulfiller

`[POST] /account/{accountId}/store/fulfiller`

This endpoint is used to create a new fulfiller. Many of the data requirements are specific to the type of fulfiller being created.

### POST Parameters

`name` _(required)_
> <p> a name for this fulfiller

`type` _(required)_
> <p> the type of fulfiller to create, one of: STAGEBLOC

`international_shipping_agreement`
> <p> a plain text string to which the user must agree if they are requesting shipment to a country which does not match the country for this fulfiller's private address

`private_address` _(required)_
> <p> Private addresses are used in all location if no public address is set. If a public address is set the private address only appears on Purchase Orders.
> <p> See _Address_ below for fields

`public_address` _(optional)_
> <p> See _Address_ below for fields

### Address

`name`
> <p> a string the name for the address

`street_address`
> <p> the street address

`street_address_2`
> <p> a second street address

`city`
> <p> the city

`country`
> <p> the 2 character country code as specified by ISO-3361-alpha 2

`postal_code`
> <p> the postal code appropriate to the country. If country is US the 5 digit zip code for the private address. If country is CA then you must provide a 6 character postal code (you may provide this with an additional space separator). For all other countries please use an appropriate postal code.

`state`
> <p> The region identifier appropriate for the country indicated. For US state codes please use the 2 character United States Postal Service abbreviations. For CA provinces please use the 2 character postal abbreviation for the province or territory. For all other countries please use a region identifier of your choice.

## Retrieve - Fulfiller
`[GET] /account/{accountId}/store/fulfiller/{fulfillerId}`

This endpoint is use to get information about a single fulfiller belonging to an account

## Definition - Coupon

    {
        "id": 2,
        "account": 1,
        "title": "Awesome Coupon",
        "discount_ype": "percent",
        "amount": 10,
        "apply_to": "order",
        "exclusive": false,
        "start_date": null,
        "end_date": null,
        "timezone": "America\/New_York",
        "created": "2014-09-09 19:40:52",
        "created_by": 8
    }

discount_type
> <p> the type of discount this is
> <p> possible values are `percent` or `amount`

apply_to
> <p> what part of the purchase this coupon applies to
> <p> possible values are `order`, `shipping`, or `membership`

start_date
> <p> if the coupon is only valid for a certain date range, this will be the start date of that range

## Retrieve - Coupon
`[GET] /account/{accountId}/store/coupon/{couponId}`

This endpoint is used to get a single store coupon belonging to an account.

## Definition - Order

    {
        "id": 28,
        "account": 2937,
        "receipt_url": "https://www.fullscreendirect.dev/receipt/28",
        "ordered": "2016-11-16 21:43:57",
        "email": "hosted_site_test@automated.test",
        "phone_number": "262 501 6621",
        "shipped": false,
        "currency": "usd",
        "total": 15.93,
        "total_usd": 15.93,
        "shipping_amount": 5,
        "handling_amount": 0,
        "tax_amount": 0.93,
        "status": "paid",
        "notes": null,
        "user": {
            "id": 1931,
            "url": "https://www.fullscreendirect.dev/user/hosted_site_test",
            "created": "2015-03-16 17:06:45",
            "name": "Hosted Site Test",
            "username": "hosted_site_test",
            "bio": "",
            "color": "70,170,255"
        },
        "address": {
            "id": 96,
            "name": "Josh Holat",
            "street_address": "2245 W North Ave",
            "street_address_2": "",
            "city": "Chicago",
            "state": "IL",
            "postal_code": "60647",
            "country": "US"
        },
        "transactions": [
            {
                "id": 31,
                "modified": "2016-11-16 21:44:00",
                "amount": 10,
                "status": "paid",
                "quantity": 1,
                "sku": "EJ_WRXHL",
                "item": {
                    "type": "store",
                    "object": {
                        "<see store item definition>"
                    }
                },
                "shipping": {
                    "method": "Flat Rate"
                }
            }
        ]
    }

user
> <p> this will be `null` if the order was a guest checkout

transactions
> <p> this will list the items in this order

transactions item type
> <p> this will list the type of item that was ordered
> <p> possible values are `audio`, `audio_playlist`, `store`, `theme`, and `fan_club_subscription`

## List - Order
`[GET] /account/{accountId}/store/order`

This endpoint is used to get retrieve orders that have been made in your store.

When dealing with monetary values, the currency will be USD unless otherwise specified.

### GET Parameters

order_by
> <p> how to order the returned items
> <p> accepted values are `ordered` and `totalAmount`
> <p> defaults to `ordered`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

## Retrieve - Order
`[GET] /account/{accountId}/store/order/{orderId}`

## Update - Order
`[POST] /account/{accountId}/store/order/{orderId}`

This endpoint can be used to update various elements about orders.

### POST Parameters

`shipped` _(required)_
> <p> A boolean that specifies if this order is now shipped

`weight` _(optional)_
> <p> The weight of the package sent, in ounces. May be omitted or left as null.

`tracking_number`
> <p> An optional tracking number for when this item was shipped

`carrier`
> <p> An optional carrier that this was shipped with for use with the `tracking_number`

`transaction_ids` _(optional)_
> <p> An array of transaction ids to indicate a partial shipment. If not supplied then all transactions and the order as a whole is marked as shipped.

## Resend Receipt - Order
`[POST] /account/{accountId}/order/{orderId}/receipt/resend`

This endpoint can be used to resend a receipt for an order.

## Refund - Order
`[POST] /account/{accountId}/store/order/{orderId}/refund`

This endpoint can be used to refund an entire order.

### POST Parameters

`adjust_stock`
> <p> whether or not to adjust the stock
> <p> defaults to `false`

`alert_user`
> <p> whether or not to email the user about this refund
> <p> defaults to `false`

`refund_reason`
> <p> a reasoning code for this refund
> <p> one of good_will, product_not_received, goods_services_returned_or_refused, correct_taxes, or other

`refund_reason_text`
> <p> a reason / description for this refund

## Retrieve - Settings
`[GET] /account/{accountId}/store/setting`

This endpoint is used to get information on how your store has been configured.

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
	        "return_policy": "",
	        "support_email": "support@example.com",
	        "shipping_minimum_price": "0",
	        "shipping_maximum_price": null,
	        "handling_fee_per_order": 0,
	        "handling_fee_per_item": 0,
	        "handling_fee_per_item_minimum_threshold": 0,
	        "cart_lock_time_seconds": 0,
	        "cart_require_billing_address": false,
	        "cart_require_phone_number": false,
	        "payment_preauth": false,
	        "payment_verification": false
	    }
	}

## Retrieve - Secure Settings
`[GET] /account/{accountId}/store/setting/secure`

This endpoint is used to get secure information about your store. It can only be accessed using an authentication token for an admin with store order creation access.

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
	        "payworks_merchant_identifier": null,
	        "payworks_merchant_secret_key": null
	    }
	}

# Video
These endpoints revolve around the ability to upload and stream video through Fullscreen Direct. Video consists of both individual videos and those videos being organized into various playlists.

## Definition

    {
        "id": 1,
        "title": "Test API Upload",
        "description": "",
        "short_url": "http:\/\/stgb.dev\/v\/B",
        "video_url": null,
        "embed_code": "\u003Ciframe width=\u0022640\u0022 height=\u0022360\u0022 src=\u0022\/\/video.stagebloc.com\/e\/4jpzTNGy\u0022 frameborder=\u00220\u0022 allowfullscreen\u003E\u003C\/iframe\u003E",
        "created": "2014-12-03 17:38:50",
        "modified": "2014-12-03 17:38:50",
        "in_moderation": false,
        "is_fan_content": false,
        "comment_count": 0,
        "like_count": 0,
        "user": 8,
        "user_has_liked": false
    }

video_url
> <p> only relevant to videos not uploaded directly to StageBloc, this would be the link to the video on the third party site

embed_code
> <p> the embed code of the video to include in HTML

## List
`[GET] /account/{accountId}/video`

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

## Create
`[POST] /account/{accountId}/video`

This endpoint can be used to upload a video to an account. Once uploaded, there will be a slight delay before it can play while it converts. This request must be made by a logged in fan or admin of the account. Depending on the access level of the logged in user, it will become fan content or official content.

### POST Parameters

`video` _(required)_
> <p> the contents of the video file itself

`title` _(required)_
> <p> the title of the video

fan_content
> <p> whether to force this content to be submitted as fan content (versus official content, only applies if the logged in user is an admin of the account)

description
> <p> the description of the video

exclusive
> <p> whether or not this video should be exclusive

exclusive_tiers
> <p> what fan club tier(s) the video is exclusive to

## Retrieve
`[GET] /account/{accountId}/video/{videoId}`

## Update
`[POST] /account/{accountId}/video/{videoId}`

## Delete
`[DELETE] /account/{accountId}/video/{videoId}`

## List - Playlist
`[GET] /account/{accountId}/video/playlist`

### GET Parameters

`honor_exclusivity`
> <p> whether to include content exclusive to a specific tier based on the active user's permissions
> <p> accepted values are 0 to only show public content and 1 to also include exclusive content.
> <p> defaults to 0

order_by
> <p> how to order the returned items
> <p> accepted values are `created`, `modified`, and `price`
> <p> defaults to `created`

direction
> <p> what direction to order the returned items
> <p> accepted values are `ASC` and `DESC`
> <p> defaults to `DESC`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0


## Create - Playlist
`[POST] /account/{accountId}/video/playlist`

## Retrieve - Playlist
`[GET] /account/{accountId}/video/playlist/{playlistId}`

## Update - Playlist
`[POST] /account/{accountId}/video/playlist/{playlistId}`

## Delete - Playlist
`[DELETE] /account/{accountId}/video/playlist/{playlistId}`

# Comments
Comments can be made on almost all types of content in Fullscreen Direct.

## Definition

### Comment

    {
        "id": 6,
        "text": "This is my awesome comment!",
        "user": 204,
        "created": "2013-02-27 19:51:46",
        "account": 1,
        "reply_to": 0,
        "reply_count": 0,
        "short_url": "http:\/\/stgb.dev\/c\/b\/7",
        "in_moderation": false,
        "content": {
            "id": 7724,
            "content_type": "blog"
        }
    }

content
> <p> the content object this comment was made on (will match the same JSON structure as listing these objects in their respective endpoints), by default this will be collapsed unless expand=content is used

content_type
> <p> a string specifying what type of content this comment was made on (i.e. what type of content the `content` key conforms to)

## Create
`[POST] /account/{accountId}/{contentType}/{contentId}/comment`

`contentType`
> <p> can be one of `audio`, `blog`, `photo`, `status`, `video`, `event`, or `store`.

This endpoint will create a comment on a piece of content. The user must be a fan of the account the content belongs to.

### POST Parameters

`text` _(required)_
> <p> the text of the comment

`reply_to_id`
> <p> if this is a reply to another comment, this would be the ID of the original comment

## List
`[GET] /account/{accountId}/{contentType}/{contentId}/comment`

`contentType`
> <p> can be one of `audio`, `blog`, `photo`, `status`, `video`, `event`, or `store`.

Retrieves comments for a particular piece of content on Fullscreen Direct.

### GET Parameters

direction
> <p> the direction to list results in
> <p> accepted values are `ASC` or `DESC`
> <p> defaults to `DESC`

limit
> <p> the number of items to limit the response to
> <p> accepted values are any positive number
> <p> defaults to 50

offset
> <p> how much to offset the returned items by
> <p> accepted values are any number greater than or equal to zero
> <p> defaults to 0

# Content Actions
Fullscreen Direct as a network has a lot of social functionality built in that allows fans to interact with content. Those endpoints are outlined here.

## List Likes
`[POST] /account/{accountId}/{contentType}/{contentId}/like`

`contentType`
> <p> can be one of `audio`, `audio/playlist`, `blog`, `event`, `photo`, `photo/album`, `status`, `store`, `video`, `video/playlist`.

This will return a list of users who have liked this content.


## Like
`[POST] /account/{accountId}/{contentType}/{contentId}/like`

`contentType`
> <p> can be one of `audio`, `audio/playlist`, `blog`, `event`, `photo`, `photo/album`, `status`, `store`, `video`, `video/playlist`.

This endpoint is used for liking content.

## Unlike
`[DELETE] /account/{accountId}/{contentType}/{contentId}/like`

`contentType`
> <p> can be one of `audio`, `audio/playlist`, `blog`, `event`, `photo`, `photo/album`, `status`, `store`, `video`, `video/playlist`.

This will remove a like from a piece of content.

Upon a successful like (or removal of a like), the JSON returned will simply be the same you'd receive from a `/account/{accountId}/{contentType}/{contentId}` `GET` request.

## Flag Content
`[POST] /account/{accountId}/{contentType}/{contentId}/flag`

`contentType`
> <p> can be one of `audio`, `blog`, `photo`, `status`, `video`.

Flag content for account moderation. This mostly applies to fan submitted content within Fan Clubs to ensure the content is kept at a high standard.

### POST Parameters

`type`
> <p> The type of flagging this is.
> <p> Accepted values are `duplicate`, `offensive`, `copyright`, or `prejudice`.

`reason`
> <p> This specifies why the user is flagging this content and should be a sentence or two provided by the end user using your application.

# Devices
These endpoints revolve around Fullscreen Direct Checkout and the devices used for the checkout process. They allow the application to label a device identifier with a convenient human readable label.

## Definition

    {
        "identifier": "abcdefghijklmnopqrstuvwxyz",
        "label": "The dude's ipad"
    }

identifier
> <p> The identifier for the device.

label
> <p> The current label assigned to this device under the current account context. Null if the identfier was used in an order but no label is currently assigned to it.

## List
`[GET] /account/{accountId}/device`

## Retrieve
`GET /account/{accountId}/device/{deviceIdentifier}`

## Create
`POST /account/{accountId}/device`

### POST Parameters

`deviceIdentifier` _(required)_
> <p> the device identifier to register with the system.

`label`
> <p> the label to associate with the deviceIdentifier indicated.

## Update
`POST /account/{accountId}/device/{deviceIdentifier}`

### POST Parameters

`label`
> <p> the label to associate with the deviceIdentifier indicated.

## Delete
`DELETE /account/{accountId}/device/{deviceIdentifier}`

Removes a device's label from our system. All orders associated with this device will remain, only the label currently associated with the device identifier will be removed.
