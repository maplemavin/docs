# Fullscreen Direct API V1
Use the Fullscreen Direct API to develop custom, standalone integrations with Fullscreen Direct.

### Under Development
The API is actively evolving - please check back here for updates and changes.

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

## /oauth2
`[POST]`  
This endpoint is used to get a request token.

username _(required)_

    the email or username of the user trying to authenticate

password _(required)_

    the password of the user trying to authenticate

response\_type _(required)_

    the response type you'd like to receive

    possible value is `code`

client\_id _(required)_

    the ID of the application this user is connecting to (applications can be created in the backend of Fullscreen Direct)

    possible values are any client ID that matches a registered application

redirect\_uri _(required)_

    the redirect URI tied to the application you're connecting the user to

    possible values are the URI that matches the application being used

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            "code": "<client ID here>"
        }
    }

## /oauth2/token
`[POST]`  
This endpoint is used to get an access token once a request token is received.

client\_id _(required)_

    the ID of the application this user is connecting to (applications can be created in the backend of Fullscreen Direct)

    possible values are any client ID that matches a registered application

client\_secret _(required)_

    the secret of the application this user is connecting to

    possible values are the client secret that matches the application of the passed client ID

grant\_type _(required)_

    the grant type being requested

    possible value is `authorization_code`

code _(required)_

    the request token code received after connecting with the `/oauth2` endpoint

    possible values are any string received during the authentication / connection process

include\_admin\_accounts

    whether or not to include the authenticated user's admin accounts with the `access_token` in the response

    accepted values are `true` or `false`

    defaults to `false`

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

# Accounts
These endpoints revolve around Fullscreen Direct accounts and their data. An account can have any number of admins, and all content on Fullscreen Direct is tied to an account.

## /account
`[POST]`  
Creates a new account and makes the currently authenticated user an admin for that account

### POST Parameters

`name` _(required)_  
`stagebloc_url` _(required)_  

	the URL of the account on Fullscreen Direct (i.e. www.fullscreendirect.com/fsd_url)

`description`  
`image`

	an image file to use for the account's image

## /accounts
`[GET]`  
Gets a listing of various types of accounts for a user

### GET Parameters

`order_by`  

	what to order the accounts by (possible values include "created", "modified", and "accountName")

`admin`  

	whether or not to include accounts the user is an admin of (defaults to true)

`following`  

	whether or not to include accounts the user is follow (defaults to true)

### Example Response

An array of accounts (see `/account` endpoint for structure)

## /account/{accountId}
`[GET]`  
Gets an account's information from its ID.

`[POST]`  
Updates an account by its ID. Only admins of the account can use this endpoint.

### POST Parameters

`name`  
`description`  
`stagebloc_url`

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
            "id": 1,
            "url": "http:\/\/customdomain.com",
            "stagebloc_url": "demo",
            "name": "Demo Account",
            "description": "Description here...",
            "type": "business",
            "stripe_enabled": true,
            "verified": true,
            "photo": {
                <see structure for a photo response>
            },
            "user_is_admin": true,
            "user_role": "read_only"
        }
    }

### Response Explanation

`user_is_admin`

	whether or not the user is an admin in some capacity for this account

`user_role`

	if the user is an admin, this will be what type of admin they are ("owner", "editor", "author", "read_only", or "fan_club_moderator")

## /{accountId}/follow
`POST /account/{accountId}/follow`  
This endpoint allows a user to follow an account.  
**Note:** If the `tier` the user is trying to follow is a paid tier, that functionality is not possible through the API.

`DELETE /account/{accountId}/follow`  
This will have a user unfollow an account regardless of the tier they are on

### POST Parameters

`tier` _(required)_  

	the tier the user wants to follow this account on

	must be either 1, 2, or 3

`expiration`  

	an expiration date to use for this membership

	must be a valid datetime string

	by default the system will determine a time to use for the expiration based on the tier's settings, but you can use this to override that

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

## /content
`[GET] /account/{accountId}/content`  
Gets an activity stream of recent content from the account.

## /fans
`[GET] /account/{accountId}/fans`  
Gets a list of fans for the account.

### GET Parameters

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

order_by

    how to order the returned results

    accepted values are "created", "name", "email", and "username"

    defaults to "created"

direction

    which order to return the results in

    accepted values are ASC or DESC

    defaults to DESC

### Example Response

This will be a listing of user objects, so check out the user endpoints for the structure

## /children/{type}
`[GET] /account/{accountId}/children/{type}`  
Gets the children accounts of a parent account. The `{type}` is optional and omitting it will return children accounts of all types. Otherwise, simply take the type of children accounts you are looking for, replace the spaces with dashes, and use that as the `{type}`.

In the response, `child_account_types` will show all of the types regardless of the `{type}` requested.

### GET Parameters

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

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

	An array of strings of the different types of children accounts the parent account has setup

child_accounts

	An array of account objects (see account listing endpoint for structure). It will also add a `child_account_type` key to each account specifying the type it is

# Applications
These endpoints revolved around the application requests are being made on behalf of.

## /push/token
`[POST] /application/push/token`  
Allows an application to give Fullscreen Direct a push notification for a user so that they can be messaged via push notifications from the Fullscreen Direct backend.  
**Note:** For iOS development, you'll need to upload your Apple Push Notification Certificate so we can authenticate push notifications with your tokens. Please contact us to handle this.

### POST Parameters

`token` _(required)_  

	the push notification token for this user

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
	        "message": "Token successfully saved!"
	    }
	}

# Users
These endpoints revolve around Fullscreen Direct users and their data. A user on Fullscreen Direct can be an admin for any number of accounts, and their login is tied to their email address. A user can also be a fan of any number of accounts. These endpoints allow for management of both admin and fan relationships between users and their accounts.

## /users
`[POST]`  
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
Accepts "male" or "female"

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
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
    }

### Response Explanation

color

	the RGB value of the color the user has chosen in our backend

## /users/me
`[GET]`  
Gets the currently authenticated user's information.

`[POST]`  
Updates the currently authenticated user's information.

### POST Parameters

`bio`  
`birthday`  
`email`  
`username`  
`name`  
`gender`

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
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
            }
        }
    }

### Response Explanation

email

    this is only included for the currently authenticated user (i.e. won't show up for users other than the authenticated one)

birthday

    this is only included for the currently authenticated user (i.e. won't show up for users other than the authenticated one)

photo

    the user's photo

    the width and height are the dimensions of the originally uploaded photo

## /users/{userId}
`[GET]`  
Gets a user by their user ID

### Example Response

See the response for `/users/me`, it will be structured the same.

## /content/updates
`GET /users/{userId}/content/updates`  
This returns a listing of content that this user has created across any account they are a part of. Each content object returned in this response will have its `kind` value automatically expanded, and the structure of the content type will match it as if you were loading that single object via its own endpoint. Looking at the structure of a audio response, for example, will show you what an audio object in the array of content returned here will be formatted like.

### GET Parameters

`filter` _(required)_  
A comma separated string for the type(s) of content to be returned. Allowed values are audio, blog, event, photo, status, store, and video.

## /content/likes
`GET /users/{userId}/content/likes`  
This returns a listing of content that this user has liked across Fullscreen Direct.  Each content object returned in this response will have its `kind` value automatically expanded, and the structure of the content type will match it as if you were loading that single object via its own endpoint. Looking at the structure of a audio response, for example, will show you what an audio object in the array of content returned here will be formatted like.

### GET Parameters

`filter` _(required)_  
A comma separated string for the type(s) of content to be returned. Allowed values are audio, blog, event, photo, status, store, and video.

# Fan Clubs
Users on Fullscreen Direct can join Fan Clubs associated with accounts. By default an account doesn’t have a Fan Club set up, but creating one adds extra functionality such as having three different membership tiers and requiring payment for joining the Fan Club.

## /fanclub
`GET /account/{accountId}/fanclub`  
This will return the details about the Fan Club.

`POST /account/{accountId}/fanclub`  
This will create or update a Fan Club that belongs to the account with the given `accountId`.

### POST Parameters

`title`  
The title of the Fan Club as a whole (as opposed to any tier)

`description`  
The description of the Fan Club as a whole (as opposed to any tier)

`moderation_queue`  
Whether or not to have the moderation queue on for this Fan Club.

`tier_info` _(required)_  
An array structured similar to the return data for each array in the response below used to setup each tier. If one of the tier keys is missing from this array, that tier will be removed from the Fan Club. Instead of the two returned membership values, pass a `membership_length` parameter as a value in seconds that must match either zero, one month, three months, six months, or a year using number of seconds in a day (86,400) multiplied by the days in that interval (months are assumed to be 30 days in length).

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
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
    }

### Response Explanation

membership\_length\_interval and membership\_length\_unit

    together these represent how long the membership lasts

    the unit can be "once", "year", or "month"

discount

    the discount (in percentage points) that this tier gives fans off of store items for this account

allowed\_content\_sections

    the types of content fans are able to submit to this Fan Club

## /fanclub/dashboard
`GET /account/{accountId}/fanclub/dashboard`  
This endpoint will return various stats and information about the account's fan club (similar to the data shown on the fan club dashboard in the Fullscreen Direct backend)

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
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
	}

## /fanclubs/{type}
`GET /account/fanclubs/{type}`  
This endpoint retrieves a listing of Fan Clubs on Fullscreen Direct. The accepted values for `type` are `following`, `featured`, or `recent` with `featured` being the default. Note that for `featured` Fan Clubs, `limit` and `offset` are ignored as it will return all featured Fan Clubs at the time.

### GET Parameters

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 20

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

# Audio
These endpoints revolve around the ability to upload and stream audio through Fullscreen Direct. Audio consists of both individual tracks and those tracks being organized into various playlists.

## /audio
`[GET] /account/{accountId}/audio`  
This endpoint can used to list all the audio for a certain account.

`[POST] /account/{accountId}/audio`  
This endpoint can be used to create a single audio track for an account. It currently only allows actual uploads as opposed to also allowing SoundCloud or other third party source URLs.

### GET Parameters

playlist_id

	an audio playlist ID to limit the audio results to

	accepted values are an ID of any playlist that belongs to the same account

	defaults to none

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

order_by

    how to order the returned items

    accepted values are `created`, `modified`, and `recordedDate`

    defaults to `created`

direction

    what direction to order the returned items

    accepted values are `ASC` and `DESC`

    defaults to `DESC`

### POST Parameters

`audio` _(required)_

	the contents of the audio file itself

`title` _(required)_

	the title of the track

description

	the description of the track

public

	whether or not this audio track should be public (defaults to true)

exclusive

	whether or not this audio track should be exclusive

exclusive_tiers

	what fan club tier(s) the audio track is exclusive to

### Example Response

	{
		"metadata": {
			"http_code":200
		},
		"data": [{
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
		}]
	}

### Response Explanation

duration

	the length (in seconds) of this track

stream_url

	the URL to use to stream this audio file (could be a Fullscreen Direct URL or a third party one such as SoundCloud)

in_moderation

	if this audio track was submitted by a fan, this handles if it is still in moderation or not

is\_fan\_content

	whether or not this audio was submitted by a fan or not

user\_has\_liked

	if the request as made with a logged in user, this will signify if that user has liked the track or not

custom\_field\_data

	if you have custom data set on audio for your account, the slugs will show up as keys here with their values

## /audio/{audioId}
`[GET] /account/{accountId}/audio/{audioId}`  
This endpoint can be used to get a single audio track from an account.

### Example Response

See the response for `/audio`, it will be structured the same except that it won't be in an array.

## /audio/playlists
`[GET] /account/{accountId}/audio/playlists`  
This endpoint can be used to list audio playlists that are available for an account.

### GET Parameters

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

order_by

    how to order the returned items

    accepted values are `created` and `modified`

    defaults to `created`

direction

    what direction to order the returned items

    accepted values are `ASC` and `DESC`

    defaults to `DESC`

### Example Response

	{
		"metadata": {
			"http_code":200
		},
		"data": [{
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
		}]
	}

### Response Explanation

embed_code

	Fullscreen Direct has audio playlist widgets for playlists that you can use by embedding this code on an HTML page if you so choose

artist

	admins can add a special artist to a playlist if it something different than the name of the account

audio

	the number of tracks in the playlist, or an array of audio objects if you pass specify to expand "audio" as a parameter

custom\_field\_data

	if you have custom data set on events for your account, the slugs will show up as keys here with their values

## /playlist/{playlistId}
`[GET] /account/{accountId}/audio/playlist/{playlistId}`  
This endpoint can be used to get an individual audio playlist.

### Example Response

See the response for `/audio/playlists`, it will be structured the same except that it won't be in an array.

# Photos
These endpoints revolve around the ability to upload and view photos on Fullscreen Direct. Photos consist of both individual images as well as those images being organized into various photo albums.

## /photo
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

### Example Response

See the endpoint for listing an individual photo for the response structure

## /photos
`[GET] /account/{accountId}/photos`  
Lists photos from an account.

### GET Parameters

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

order_by

    how to order the returned items

    accepted values are `created` and `modified`

    defaults to `created`

direction

    what direction to order the returned items

    accepted values are `ASC` and `DESC`

    defaults to `DESC`

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": [{
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
	    }]
	}

### Response Explanation

width & height

	the width / height in pixels of the originally uploaded image

images

	an array of image URLs that are various sizes

## /photo/{photoId}
`[GET] /account/{accountId}/photo/{photoId}`  
This endpoint can be used to get a single photo from an account. Note that if the photo is fan submitted or exclusive, it will require that the request be made by a logged in user who has access to that account as a fan or admin.

### Example Response

See the response for `/photos`, the photo object will be structured with the same keys / values (just not in an array).

## /photos/albums
`[GET] /account/{accountId}/photos/albums`  
Lists photos albums from an account.

### GET Parameters

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

order_by

    how to order the returned items

    accepted values are `created` and `modified`

    defaults to `created`

direction

    what direction to order the returned items

    accepted values are `ASC` and `DESC`

    defaults to `DESC`

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": [{
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
	    }]
	}

### Response Explanation

photos

    the number of photos this photo album has

    if you specify to expand the photos key, it will be an array of the photos for this album (structured the same as the photo listing endpoint)

## /albums/{albumId}
`[GET] /account/{accountId}/photos/album/{albumId}`  
This endpoint can be used to get a single photo album from an account.

### Example Response

See the response for `/photos/albums`, it will be structured the same except that it won't be in an array.
## /albums/{albumId}/photos
`[POST] /account/{accountId}/photos/album/{albumId}/photos`  
This endpoint can be used to add an existing photo to a photo album.

### POST Parameters

`photo_ids` _(required)_

    an array of photo IDs to be added to the album

### Example Response

    See the response for `/photos/albums`, it will be structured the same except that it won't be in an array.

# Video
These endpoints revolve around the ability to upload and stream video through Fullscreen Direct. Video consists of both individual videos and those videos being organized into various playlists.

## /video
`[POST] /account/{accountId}/video/`  
This endpoint can be used to upload a video to an account. Once uploaded, there will be a slight delay before it can play while it converts. This request must be made by a logged in fan or admin of the account. Depending on the access level of the logged in user, it will become fan content or official content.

### POST Parameters

`video` _(required)_

	the contents of the video file itself

`title` _(required)_

	the title of the video

fan_content

	whether to force this content to be submitted as fan content (versus official content, only applies if the logged in user is an admin of the account)

description

	the description of the video

exclusive

	whether or not this video should be exclusive

exclusive_tiers

	what fan club tier(s) the video is exclusive to

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
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
	}

### Response Explanation

video_url

	only relevant to videos not uploaded directly to StageBloc, this would be the link to the video on the third party site

embed_code

	the embed code of the video to include in HTML

# Store and Commerce
These endpoints revolve around Fullscreen Direct store and commerce data in the backend. They can be used for tasks including retrieving store items and orders, updating orders, or getting analytics from a store.

## /store/dashboard
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

## /store/fulfillers
`[GET] /account/{accountId}/store/fulfillers`  
This endpoint is use to get a listing of all fulfillers belonging to an account

`[POST] /account/{accountId}/store/fulfillers`  
This endpoint is used to create a new fulfiller. Many of the data requirements are specific to the type of fulfiller being created.

### POST Parameters

name _(required)_

    a name for this fulfiller

type _(required)_

    the type of fulfiller to create, one of: STAGEBLOC

international\_shipping\_agreement

    a plain text string to which the user must agree if they are requesting shipment to a country which does not match the country for this fulfiller's private address

private\_address[name]

    a string the name of the recipient for the private address of the fulfiller. Private addresses are used in all location if no public address is set. If a public address is set the private address only appears on Purchase Orders.

private\_address[street\_address]

    the street address for the fulfiller's private address

private\_address[street\_address\_2]

    a second street address for the fulfiller's private address

private\_address[city]

    the city for the fulfiller's private address

private\_address[country]

    the 2 character country code as specified by ISO-3361-alpha 2

private\_address[postal\_code]

    the postal code appropriate to the country. If country is US the 5 digit zip code for the private address. If country is CA then you must provide a 6 character postal code (you may provide this with an additional space separator). For all other countries please use an appropriate postal code.

private\_address[state]

    The region identifier appropriate for the country indicated. For US state codes please use the 2 character United States Postal Service abbreviations. For CA provinces please use the 2 character postal abbreviation for the province or territory. For all other countries please use a region identifier of your choice.

public\_address[name]

    a string the name of the recipient for the public address of the fulfiller. Private addresses are used in all location if no public address is set. If a public address is set the private address only appears on Purchase Orders.

public\_address[street\_address]

    see private_address[street_address] above

public\_address[street\_address\_2]

    see private_address[street_address_2] above

public\_address[city]

    see private_address[city] above

public\_address[country]

    see private_address[country] above

public\_address[postal\_code]

    see private_address[postal_code] above

public\_address[state]

    see private_address[state] above

## /store/items
`[GET] /account/{accountId}/store/items`  
This endpoint is used to get a listing of store items belonging to an account.

`[POST] /account/{accountId}/store/items`  
This endpoint is used to create a new store item and, if relevant, its options. In the case of a physical store item it also configures the shipping price handlers for this new store item.

### GET Parameters

order_by

    how to order the returned items

    accepted values are `created`, `modified`, and `price`

    defaults to `created`

direction

    what direction to order the returned items

    accepted values are `ASC` and `DESC`

    defaults to `DESC`

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": [{
            "id": 131,
            "type": "physical",
            "account": 1,
            "title": "Test Shirt",
            "short_url": "http:\/\/stgb.dev\/st\/3g",
            "description": "",
            "sold_out": false,
            "exclusive": true,
            "exclusive_tiers": [2, 3],
            "featured": false,
            "created": "2014-08-03 19:11:19",
            "created_by": 8,
            "modified": "2014-08-03 19:27:45",
            "modified_by": 8,
            "fans_name_price": true,
            "category": null,
            "prices": [{
                "currency": "usd",
                "price": 12
            }],
            "shipping_providers": [{
                "id": 1,
                "name": "Flat Rate",
                "price": 3
            }, {
                "id": 2,
                "name": "USPS",
                "origin_zip_code": "53103",
                "handling_fee": 2
            }],
            "on_sale": false,
            "options": [{
                "name": "Main",
                "sku": "7HWE49JA",
                "upc": null,
                "unlimited": true,
                "sold_out": false,
                "quantity": null,
                "low_stock_threshold": 10,
                "weight": 2,
                "weight_unit": "ounces",
                "height": 0,
                "width": 0,
                "length": 0
            }],
            "photo": 3231,
            "photos": 5
        }, {
            "id": 124,
            "type": "bundle",
            "account": 1,
            "title": "All sorts of stuff",
            "short_url": "http:\/\/stgb.dev\/st\/39",
            "description": "",
            "sold_out": false,
            "exclusive": true,
            "exclusive_tiers": [2],
            "featured": false,
            "created": "2014-07-31 11:59:17",
            "created_by": 8,
            "modified": "2014-08-05 16:30:15",
            "modified_by": 8,
            "fans_name_price": false,
            "category": null,
            "prices": [{
                "currency": "usd",
                "price": 20
            }],
            "living_bundle": false,
            "bundled_items": {
                "store_items": [ ... ],
                "audio": [ ... ],
                "audio_playlists": [ ... ]
            },
            "on_sale": false,
            "photo": null,
            "photos": 0
        }]
    }

### POST Parameters

store\_item _(required)_

    a parent object containing all information to be associated with this store item

store\_item[options] _(required for type set to PHYSICAL or EXPERIENCE)_

    an array of objects representing each option (for a T-shirt each option may represent a size, a color, or a combination of the two, etc)

    each element in this array may contain the following fields:

    `name` _(required)_

        the name to display when there are multiple options under the store item

    `sku`

        an identifier used to distinguish between this option and other options under the same store item

        defaults to an auto-generated alphanumeric SKU

    `upc`

        an identifier used to submit download / sales information to SoundScan or Buzz Angle

    `quantity`

        null for unlimited or a non-negative integer representing how much stock is currently on hand

        defaults to null

    `additional_price`

        a non-negative integer representing the additional price to charge if this option is selected by the user (useful if an XXL t-shirt is more expensive than other variations, for example)

        defaults to no additional price

    `weight` _(required for type set to PHYSICAL)_

        a non-negative decimal number indicating the weight in ounces of this option (used for shipping price calculation)

    `height` _(required for type set to PHYSICAL)_

        a non-negative decimal number indicating the height in inches of this option (used for shipping price calculation)

    `width` _(required for type set to PHYSICAL)_

        a non-negative decimal number indicating the width in inches of this option (used for shipping price calculation)

    `length` _(required for type set to PHYSICAL)_

        a non-negative decimal number indicating the length in inches of this option (used for shipping price calculation)

    `status`

        a string representing the status for this option

        accepted values are NORMAL or DISABLED

        defaults to NORMAL

store\_item[shipping\_price\_handlers] _(required for type set to PHYSICAL)_

    an array of objects representing each shipping price handler to be offered to a user attempting to purchase this item. The object may include additional handling fees and options depending on the type of handler indicated.

    Each element in this array may contain the following fields:

    `name` _(required)_

        a string identifying which Shipping Price Handler to enable, one of: FLAT_RATE, USPS, TOWNSEND, BELLTOWER, RLP, FEDEX, CUSTOM_TABLE, PICK_UP, DELIVERY_AGENT, UPS

    `price`

        a non-negative integer, an additional price on top of the charges indicated by APIs for USPS, UPS, and FEDEX. For FLAT_RATE, this is the amount to charge a user selecting this shipping method.

    `media_mail` _(applies to USPS only)_

        a boolean, true if you want to offer USPS Media Mail pricing for this item

store\_item[type] _(required)_

    a string indicating the type of store item to create

    accepted values are PHYSICAL or EXPERIENCE

store\_item[price] _(required)_

    a decimal number indicating the base price for this store item

store\_item[title] _(required)_

    a string (up to 150 characters) representing the title of the store item

store\_item[description]

    a string representing the description of the store item (HTML supported)

store\_item[category]

    a string representing a category in which to group this store item

store\_item[status]

    a string representing the status of the store item

    accepted values are OKAY or PRIVATE

    defaults to OKAY

store\_item[exclusive]

    a boolean, true if this should only be presented to users who are following the account

    defaults to false

store\_item[exclusive_tiers]

    an integer occurring 1 to 3 times specifying which fan club tier(s) an item is exclusive to

    should be used _instead of_ [exclusive] above.

    defaults to none

store\_item[featured]

    a boolean, true if this should be considered a featured item

store\_item[fulfiller\_id] _(required for type set to PHYSICAL)_

    an integer ID for a fulfiller already configured in Fullscreen Direct which is associated with this account

### Example Request

	{
	    "store_item": {
	        "price": 10.5,
	        "title": "Physical Product",
	        "description": "<p>This is a physical product</p>",
	        "category": "Cool Things",
	        "type": "PHYSICAL",
	        "exclusive_tiers": [1, 2],
	        "featured": false,
	        "options": [
	            {
	                "name": "an option",
	                "sku": "ITM_1_OPT_1",
	                "upc": "UPC",
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
	                "name":"USPS",
	                "price":4.5,
	                "media_mail":true
	            },
	            {
	                "name":"FLAT_RATE",
	                "price":5.5
	            }
	        ],
	        "photo_url": "https://21cif.com/tutorials/micro/mm/urls/images/url2.gif"
	    }
	}

### Response Explanation

type

    the type of store item this is

    possible values are "physical", "experience", "digital", or "bundle"

shipping_providers

    the available shipping methods for this store item, only shows up for items of type "physical"

options

    the various SKUs available for this product and their related data

bundled_items

    the store items that are in this bundle, only shows up for items of type "bundle"

    other store items, audio tracks, or audio playlists can be bundled and will be listed under their respective section in this array

photos

    the number of photos this store item has

    if you specify to expand the photos key, it will be an array of the photos for this item

album_id

    the ID for the photo album associated with this store item

    only returned if a photo_url is passed and is successfully processed

    this ID can be used to add additional photos for this store item via /photos/albums/{albumId}/photos

## /store/items/{itemId}
`[GET] /account/{accountId}/store/items/{itemId}`  
This endpoint is used to get a single store items belonging to an account.

## /coupon/{couponId}
`[GET] /account/{accountId}/store/coupon/{couponId}`  
This endpoint is used to get a single store coupon belonging to an account.

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
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
	}

### Response Explanation

discount_type

	the type of discount this is

	possible values are "percent" or "amount"

apply_to

	what part of the purchase this coupon applies to

	possible values are "order", "shipping", or "membership"

start_date

	if the coupon is only valid for a certain date range, this will be the start date of that range

## /store/orders
`[GET] /account/{accountId}/store/orders`  
This endpoint is used to get retrieve orders that have been made in your store.

When dealing with monetary values, the currency will be USD unless otherwise specified.

### GET Parameters

order_by

    how to order the returned items

    accepted values are `ordered` and `totalAmount`

    defaults to `ordered`

direction

    what direction to order the returned items

    accepted values are `ASC` and `DESC`

    defaults to `DESC`

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": [{
            "id": 580,
            "account": 1,
            "ordered": "2014-08-10 23:18:02",
            "shipped": false,
            "currency": "usd",
            "total": 26.62,
            "total_usd": 26.62,
            "shipping_amount": 5.6,
            "tax_amount": 1.02,
            "stripe_charge_id": "<Stripe charge ID>",
            "email": "testfan@stagebloc.com",
            "user": { <user object> },
            "address": {
                "street_address": "12345 Main Street",
                "street_address_2": "",
                "city": "Chicago",
                "state": "IL",
                "postal_code": "60647",
                "country": "US"
            },
            "transactions": [{
                "id": 2968,
                "modified": "2014-08-10 23:18:03",
                "amount": 0,
                "quantity": 1,
                "item": {
                    "type": "store",
                    "object": { <store object> }
                },
                "shipping": {
                    "method": "USPS Priority Mail",
                },
                "shipment": {
                    "id": 12,
                    "shipped": null,
                    "tracking_number": "HS2334JFGS0"
                }
            }, {
                "id": 2969,
                "modified": "2014-08-10 23:18:03",
                "amount": 0,
                "quantity": 1,
                "item": {
                    "type": "audio",
                    "object": { <audio obkect> }
                }
            }]
        }, {
            "id": 579,
            "account": 1,
            "ordered": "2014-08-10 20:25:55",
            "shipped": false,
            "currency": "usd",
            "total": 31.17,
            "total_usd": 31.17,
            "shipping_amount": 7,
            "tax_amount": 1.17,
            "stripe_charge_id": "<Stripe charge ID>",
            "email": "fakemail@stagebloc.com",
            "user": null,
            "address": {
                "street_address": "555 Main Street",
                "street_address_2": "",
                "city": "Chicago",
                "state": "IL",
                "postal_code": "60642",
                "country": "US"
            },
            "transactions": [{
                "id": 2966,
                "modified": "2014-08-10 20:25:57",
                "amount": 23,
                "status": "Paid",
                "quantity": 1,
                "item": {
                    "type": "store",
                    "object": { <store object> }
                },
                "shipping": {
                    "provider": "USPS",
                    "method": "USPS Priority Mail",
                    "shipped": null,
                    "tracking_number": null
                }
            }]
        }]
    }

### Response Explanation

user

    this will be `null` if the order was a guest checkout

transactions

    this will list the items in this order

transactions item type

    this will list the type of item that was ordered

    possible values are "audio", "audio_playlist", "store", "theme", and "fan_club_subscription"

## /store/orders/{orderId}
`[POST] /account/{accountId}/store/orders/{orderId}`  
This endpoint can be used to update various elements about orders.

### POST Parameters

`shipped`  _(required)_  
A date string that specifies when this order was shipped

`tracking_number`  
An optional tracking number for when this item was shipped

`carrier`  
An optional carrier that this was shipped with for use with the `tracking_number`

## /{orderId}/receipt/resend
`[POST] /account/{accountId}/store/orders/{orderId}/receipt/resend`  
This endpoint can be used to resend a receipt for an order.

## /{orderId}/refund
`[POST] /account/{accountId}/store/orders/{orderId}/receipt/resend`  
This endpoint can be used to refund an entire order.

### POST Parameters

`adjust_stock`  

    whether or not to adjust the stock

    defaults to `false`

`alert_user`  

    whether or not to email the user about this refund

    defaults to `false`

`refund_reason`  

    a reasoning code for this refund

    one of good_will, product_not_received, goods_services_returned_or_refused, correct_taxes, or other

`refund_reason_text`  

    a reason / description for this refund

# Statuses
Statuses on Fullscreen Direct are shorter text updates that account's are able to schedule and post to both Fullscreen Direct itself and their connected social networks.

## /status
`[POST] /account/{accountId}/status`  
This endpoint can be used to post a status to Fullscreen Direct.

### POST Parameters

`text` _(required)_  
The text of the status to post

`latitude`  
The latitude of the user posting the status to tie a location to it

`longitude`  
The longitude of the user posting the status to tie a location to it

### Examples Response

See the response for listing a single status post

## /status/{statusId}
`[GET] /account/{accountId}/status/{statusId}`  
This endpoint can be used to get a single status post on Fullscreen Direct.

`[DELETE] /account/{accountId}/status/{statusId}`  
This endpoint can be used to delete a single status post on Fullscreen Direct. Only admins of the account with the right admin level or the original fan who posted the status are capable of deleting it.

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": {
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
    }

## /likers
`[GET] /account/{accountId}/status/{statusId}/likers`  
This endpoint can be used to list the users who have liked this status on Fullscreen Direct.

### Example Response

    {
        "metadata": {
            "http_code": 200
        },
        "data": [
            <array of user objects here>
        ]
    }

# Blog
Endpoints around interacting with blog posts on Fullscreen Direct

## /blog
`[GET] /account/{accountId}/blog`  
This endpoint can be used to list the blog posts for an account.

### GET Parameters

order_by

    what to order the results by

    accepted values are "published", "created", or "modified"

    defaults to "published"

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

### Example Response

	{
		"data": [
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
		],
		"metadata": {
			"http_code": 200
		}
	}

# Events
Events on Fullscreen Direct can be many different things including shows, conferences, etc.

## /events
`[GET] /account/{accountId}/events`  
This endpoint can be used to list the events for an account.

### GET Parameters

direction

    the direction to list results in

    accepted values are "ASC" or "DESC"

    defaults to "DESC"

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

order_by

    what to order the results by

    accepted values are "created", "startDateTime", "endDateTime"

    defaults to "startDateTime"

upcoming

	whether or not to include upcoming events

	accepted values are true or false

	defaults to true

past

	whether or not to include past events

	accepted values are true or false

	defaults to true

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": [{
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
	    }]
	}

### Response Explanation

ticket_link

	a link to a third party service where tickets for this events can be purchased

start\_date\_time & end\_date\_time

	the start / end date and time of the event, unlike other timestamps they won't be in GMT time but will specify an offset from GMT

timezone

	the timezone the event is in which is specified by the hour offset in the start and end time

user\_is_attending

	if the request is made with a logged in user, this will specify "yes", "no", or "maybe"

custom\_field\_data

	if you have custom data set on events for your account, the slugs will show up as keys here with their values

# Comments
Comments can be made on almost all types of content in Fullscreen Direct.

## /comment
`POST /account/{accountId}/{contentType}/{contentId}/comment`  
This endpoint will create a comment on a piece of content. The user must be a fan of the account the content belongs to.  
**Note:** `contentType` can be one of audio, blog, photo, status, video, event, or store.

### POST Parameters

`text` _(required)_  

	the text of the comment

`reply_to_id`  

	if this is a reply to another comment, this would be the ID of the original comment

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": {
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
	}

## /comments
`GET /account/{accountId}/{contentType}/{contentId}/comments`  
Retrieves comments for a particular piece of content on Fullscreen Direct.  
**Note:** `contentType` can be one of audio, blog, photo, status, video, event, or store.

### GET Parameters

direction

    the direction to list results in

    accepted values are "ASC" or "DESC"

    defaults to "DESC"

limit

    the number of items to limit the response to

    accepted values are any positive number

    defaults to 50

offset

    how much to offset the returned items by

    accepted values are any number greater than or equal to zero

    defaults to 0

### Example Response

	{
	    "metadata": {
	        "http_code": 200
	    },
	    "data": [{
	        "id": 1,
	        "text": "I made a comment on this really cool thing!",
	        "user": 8,
	        "created": "2014-09-15 15:38:36",
	        "account": 1,
	        "reply_to": 0,
	        "reply_count": 0,
	        "short_url": "http:\/\/stgb.dev\/c\/a\/2",
	        "in_moderation": false,
	        "content": {
	            "id": 768,
	            "title": "An audio track!",
	            "description": "",
	            "lyrics": "",
	            "artist": "",
	            "photo": 0,
	            "account": 1,
	            "created_by": 8,
	            "created": "2014-11-11 15:13:52",
	            "modified_by": 8,
	            "modified": "2014-11-11 15:14:05",
	            "short_url": "http:\/\/stgb.dev\/a\/ef",
	            "stream_url": "https:\/\/stagebloc-audio.s3.amazonaws.com\/dev\/1\/stream\/975_20141111_151352_1_768.mp3?response-content-type=audio%2Fmpeg",
	            "embed_code": "\u003Ciframe src=\u0022https:\/\/widgets.stagebloc.dev\/audio\/768\u0022 style=\u0022width:250px;height:70px;border-radius:6px\u0022\u003E\u003C\/iframe\u003E",
	            "sticky": false,
	            "exclusive": false,
	            "exclusive_tiers": [],
	            "in_moderation": false,
	            "is_fan_content": false,
	            "comment_count": 3,
	            "like_count": 0,
	            "user": 8,
	            "custom_field_data": {
	                "episodenumber": "1"
	            },
	            "content_type": "audio"
	        }
	    }]
	}

### Response Explanation

content

	the content object this comment was made on (will match the same JSON structure as listing these objects in their respective endpoints), by default this will be collapsed unless expand=content is used

content_type

	a string specifying what type of content this comment was made on (i.e. what type of content the `content` key conforms to)

# Actions
Fullscreen Direct as a network has a lot of social functionality built in that allows fans to interact with content. Those endpoints are outlined here.

## /{contentId}/like
`POST /account/{accountId}/{contentType}/{contentId}/like`  
This endpoint is used for liking content in the API. Allowed content types (for the `contentType` parameter) include the main content types you'll find across our endpoints: `audio`, `blog`, `event`, `photo`, `status`, `store`, `video`.

`DELETE /account/{accountId}/{contentType}/{contentId}/like`  
This will remove a like from a piece of content.

Upon a successful like (or removal of a like), the JSON returned will simply be the same you'd receive from a `/account/{accountId}/{contentType}/{contentId}` `GET` request.

## /{contentId}/flag
`POST /account/{accountId}/{contentType}/{contentId}/flag`  
This endpoint is used for flagging content in the API. This mostly applies to fan submitted content within Fan Clubs to ensure the content is kept at a high standard. Allowed content types (for the `contentType` parameter) include the main content types that fans are able to submit: `audio`, `blog`, `photo`, `status`, `video`.

### POST Parameters

`type`  
The type of flagging this is. Accepted values are `duplicate`, `offensive`, `copyright`, or `prejudice`.

`reason`  
This specifies why the user is flagging this content and should be a sentence or two provided by the end user using your application.
