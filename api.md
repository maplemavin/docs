# Home
**NOTE: This version of the API is currently in the process of being deprecated. Any endpoint that exists in V1 should be used there instead.**

Use the StageBloc API to develop custom, standalone integrations with StageBloc.

# General Information
The root URL of the API is `https://api.stagebloc.com/3.0/`.

All `/edit` endpoints take `POST` parameters (as well as `/oauth2/token`) and all `/list` endpoints take `GET` parameters. It can generally be assumed that writing endpoints use `POST` and reading endpoints use `GET`.

All dates returned are in `GMT / UTC (+0000)`.

Responses can be formatted in either JSON or JSONP by simply changing the extension of the endpoint between `.json` or `.jsonp`. For instance, to list an account's blog entries, one could use either `/list.jsonp` or `/list.json`. This applies to all endpoints except `/oauth2/token` since it only returns JSON. Any request that specifies JSONP as the format must include a `GET` parameter named `callback` to handle the callback function.

### Authorization
Connecting with the StageBloc API uses the OAuth 2.0 standard. You must first [create a StageBloc account](http://stagebloc.com/signup) and then [register your application in the StageBloc backend](http://stagebloc.com/account/admin/management/developers/) to receive a client ID and secret that will allow users to connect with your application.

Requests can be be made with just a `client_id` parameter, but this is restricted to reading (listing) endpoints. In order to make use of any writing (editing) endpoints, an access token (i.e. validated user) must be present.

In order to get a validated user, check out the `oauth2/token` endpoint and / or look towards the bottom of this page to find a wrapper / framework in your language.

### Errors
Errors can be returned for any of the `/edit` endpoints and differ in content based on the endpoint you're using. However, they will have the following structure:

JSON Example

    {
        "response": [{
            "error": {
                "message": "Description of error."
            }
        }]
    }

# /blog
This endpoint is used for interacting with blog posts

## /blog/edit

This endpoint can be used for editing existing blog posts or adding new blog posts to a StageBloc account. Upon successful editing/creation, the blog post data will be returned in the same manner `/blog/list` would return it. Otherwise, an error message will be returned explaining what went wrong.

id _(required to edit an existing blog post)_
:	the id of the blog post to edit

	possible values are any blog post id that belongs to the authenticated account
	
title _(required)_
:	the title of the blog post

	possible values are any string <= 100 characters
	
body _(required)_
:	the body of the blog post

	possible values are any block of text (can contain HTML)
	
published
:	the published time of the post, setting it to a date in the future will cause it to be published at that date (assuming `status` is `1`)

	possible values are a formatted timestamp of the form `"Y-m-d H:i:s O"` (capital o, not zero) (see [PHP's date() function formatters](http://php.net/manual/en/function.date.php))
	
	defaults to a UTC date of the current time in timezone America/Chicago (UTC/GMT -5)

author_id
:	the id of the user who should be set as the author of the post

	possible values are any id of an admin for the currently authenticated account
	
	defaults to the authenticated user's id

status
:	whether or not this blog post should be published

	possible values are `1` (published) or `0` (draft)
	
	defaults to `1`
	
sticky
:	whether or not this blog post should be sticky

	possible values are `1` (sticky) or `0` (not sticky)
	
	defaults to `0`
		
exclusive
:	whether or not this photo should be marked as exclusive

	possible values are `1` (exclusive) or `0` (not exclusive)
	
	defaults to `0`

## /blog/list

### Returning a specific blog post

id
:	the id of the blog entry to return

	possible values are any blog id that belongs to the authenticated account
	
	no default

**_Important Note:_** If an id is passed, all other parameters will be ignored and only the requested blog entry will be returned

### Returning all blog posts for an account

order_by
:	how to order the results

	possible values are `published`, `created`, `modified`, or `title`
	
	defaults to `published`

direction
:	what direction to return the results in

	possible values are `ASC` or `DESC`
	
	defaults to `DESC`

limit
:	the limit of results to return

	defaults to `20`

offset
:	the offset of the results to return

	possible values are any number > 0

	defaults to `0`

status
:	comma seperated list of the status of blog entries to return

	possible values are `0` (draft), `1` (published), or `0,1` (both)
	
	defaults to `1`

year
:	the year to limit the published dates of the entries to

	no default

month
:	the month to limit the published dates of the entries to

	no default

ignore_sticky
:	whether or not to ignore sticky posts (i.e. posts that will be returned first regardless of the `order_by` and `direction` parameters)

	possible values are `1` (true) or `0` (false)
	
	defaults to `0`
	
### Explanation of Returned Data

status
:	a value of `1` means published and a value of `0` means draft
    
### Example Response (JSON)
	{
	    "response": {
	        "items": [{
	            "id": 7691,
	            "title": "New blog post",
	            "body": "<p>\r\n\tLorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam auctor quam nisl, nec tempor eros dictum ut. Nam dictum ligula eget risus iaculis, sit amet auctor lacus porttitor. Donec porttitor sem vel sem euismod, quis tempor est dignissim. Donec dapibus fringilla nibh ac viverra. Donec luctus orci dictum, varius est at, sagittis urna. Nullam quis vestibulum nulla. Donec suscipit dapibus velit eget iaculis. Proin sed odio sed nulla pellentesque consequat. Donec vitae lorem at masasss sdf sdfsdf\r\n<\/p>",
	            "category": "students",
	            "short_url": "http:\/\/stgb.dev\/b\/3hB",
	            "published": "2013-06-03 22:31:00",
	            "created": "2013-06-03 22:31:24",
	            "modified": "2013-07-25 14:35:29",
	            "sticky": true,
	            "exclusive": true,
	            "comment_count": 0,
	            "like_count": 0,
	            "repost_count": 0,
	            "author": {
	                "id": 8,
	                "url": "http:\/\/stagebloc.dev\/user\/joshholat62man\/",
	                "created": "2009-10-27 14:29:16",
	                "name": "JoshDude HolatMan",
	                "username": "joshholat62man",
	                "bio": "This is my bio!",
	                "following_count": 3,
	                "likes_count": 30,
	                "content_count": 1838,
	                "email": "hi@stagebloc.com",
	                "gender": "male",
	                "birthday": "1996-07-06",
	                "location": {
	                    "city": "Chicago",
	                    "state_province": "IL",
	                    "country": "US"
	                },
	                "photo": {
	                    "id": 74,
	                    "width": 720,
	                    "height": 720,
	                    "images": {
	                        "thumbnail_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/users\/8\/thumbnail\/20130826_233333_8_74.jpeg",
	                        "small_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/users\/8\/small\/20130826_233333_8_74.jpeg",
	                        "medium_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/users\/8\/medium\/20130826_233333_8_74.jpeg",
	                        "large_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/users\/8\/large\/20130826_233333_8_74.jpeg"
	                    }
	                }
	            },
	            "user_has_liked": false,
	            "account": {
	                "id": 1,
	                "name": "Demo",
	                "stagebloc_url": "demo",
	                "custom_domain": "",
	                "description": "<p>\r\n\t@StageBloc Co-Founder, Object Orienter, Git Pusher, Apple lover, College Dropout<\/p>\r\n",
	                "type": "business",
	                "color": "#ddb4ff",
	                "follower_count": 70,
	                "content_count": 607,
	                "user_role": 0,
	                "user_is_following": false,
	                "user_is_admin": true,
	                "photo": {
	                    "id": 2849,
	                    "title": "Account Image",
	                    "short_url": "http:\/\/stgb.dev\/p\/R8",
	                    "description": "Account Image",
	                    "width": 1600,
	                    "height": 1200,
	                    "comment_count": 0,
	                    "like_count": 0,
	                    "repost_count": 0,
	                    "images": {
	                        "thumbnail_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/thumbnail\/20130115_170951_1_2849.jpeg",
	                        "small_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/small\/20130115_170951_1_2849.jpeg",
	                        "medium_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/medium\/20130115_170951_1_2849.jpeg",
	                        "large_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/large\/20130115_170951_1_2849.jpeg"
	                    },
	                    "user_has_liked": false
	                }
	            }
	        }]
	    }
	}
    
# /photos
These endpoints are used for interacting with an account's photos.

## /photos/edit

This endpoint can be used for editing existing photos or adding new photos to a StageBloc account. When adding a photo, a JPG, GIF, or PNG file is required that is less than 10MB in size. Upon successful editing/creation, the photo data will be returned in the same manner `/photos/list` would return it. Otherwise, an error message will be returned explaining what was wrong.

id _(required to edit an existing photo)_
:	the id of the photo to edit

	possible values are any photo id that belongs to the authenticated account
	
title _(required)_
:	the title of the photo

	possible values are any string <= 200 characters
	
photo_data (required when adding a new photo (i.e. no `id` is given))
:	the file to upload

	possible values are any `JPG`, `GIF`, or `PNG` file < `10MB` in size

album_id
:	the ID of the photo album to add this photo to

	possible values are any photo album ID that belongs to the authenticated account
	
	defaults to none
	
album_type
:	an id representing which type of album to upload this photo to, only applicable if `album_id` isn't given, setting it to `5` will change the account's profile image

	possible values are `2` (mobile uploads), `5` (account images), `4` (blog images), or `6` (event images)
	
	defaults to `2`
	
description
:	the description of the photo

	possible values are any block of text (can contain HTML)
	
	default to none
	
exclusive
:	whether or not this photo should be marked as exclusive

	possible values are `1` (exclusive) or `0` (not exclusive)
	
	defaults to `0`
	
tags
:   the tags for the photo

    possible values are a comma sepearated string of tags (i.e. `tag1,tag2`)
    
    defaults to no tags
 
# /photos/albums
This endpoint is used for interacting with an account's photo albums.

## /photos/albums/list
id
:	the id of the photo album to return

	possible values are any photo album id that belongs to the authenticated account
	
	no default
	
**_Important Note:_** If an id is passed, all other parameters will be ignored and only the requested photo will be returned

order_by
:	how to order the results

	possible values are `created`, `modified`, or `title`
	
	defaults to `created`

direction
:	what direction to return the results in

	possible values are `ASC` or `DESC`
	
	defaults to `DESC`

limit
:	the limit of results to return

	defaults to `20`

offset
:	the offset of the results to return

	possible values are any number > 0

	defaults to `0`

### Explanation of returned data

images
:	the cover image used for the photo album
	
### Example Response (JSON)
	{
	    "response": {
	        "items": [{
	            "id": 359,
	            "title": "Album Name",
	            "description": "",
	            "short_url": "http:\/\/stgb.dev\/pa\/7c",
	            "created": "2013-02-27 16:05:49",
	            "modified": "2013-08-14 00:36:23",
	            "photo_count": 0,
	            "photo": {
	                "id": 2892,
	                "title": "TEST TITLE",
	                "short_url": "http:\/\/stgb.dev\/p\/RS",
	                "description": "",
	                "width": 1600,
	                "height": 1200,
	                "comment_count": 1,
	                "like_count": 0,
	                "repost_count": 0,
	                "images": {
	                    "thumbnail_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/thumbnail\/20130227_160549_1_2892.jpeg",
	                    "small_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/small\/20130227_160549_1_2892.jpeg",
	                    "medium_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/medium\/20130227_160549_1_2892.jpeg",
	                    "large_url": "http:\/\/cdn-staging.stagebloc.com\/local\/photos\/1\/large\/20130227_160549_1_2892.jpeg"
	                },
	                "user_has_liked": false
	            }
	        }]
	    }
	}
    
#/oauth2
This is used during the [OAuth2](http://oauth.net/2/) authentication process to get an access token from a request token for [StageBloc Connect](http://stagebloc.local/developers/connect/). Curious? Check out our OAuth2 [PHP](https://github.com/stagebloc/php-stagebloc-api) or [Objective-C](https://github.com/stagebloc/stagebloc-ios) wrapper on GitHub to get started.

### General Flow
In order to authenticate a user with our application, you must first send them to the `stagebloc.com/connect/` page along with a few `GET` parameters.

	https://stagebloc.com/connect/?client_id=<your_client_id>&redirect_uri=<your_redirect_uri>&response_type=code&scope=non-expiring&consumer_key=<your_client_secret>
	
Once they authorize on that page, they'll be redirected back to your redirect URI with a request token as a `GET` parameter named `code`. Using that `code`, you must submit a `POST` request to `oauth2/token`.

##/oauth2/token
This endpoint is used to get an access token once a request token is received from the `stagebloc.com/connect/` auth flow and the user is redirected back to your application.

client\_id _(required)_
:	the ID of the application an account is connecting to

	possible values are any string that matches a registered application

client\_secret _(required)_
:	the secret of the application an account is connecting to

	possible values are any string that matches a registered application
	
grant\_type _(required)_
:	the grant type being requested

	possible value is `authorization_code`
	
code _(required)_
:	the request token code received after connecting with a registered application via `https://stagebloc.com/connect/`

	possible values are any string received during the authentication / connection process
	
### Example Response (JSON)
    {"access_token":"<access_token_here>","scope":"non-expiring"}

### Example Response (JSON)
    {"response":{"message":"Token succesfully invalidated."}}

## /oauth2/token/info
This endpoint will return data about the currently authenticated user and account as well as the application for the given access token.

### Example Response (JSON)
    {
        "response": [{
            "items": [{
                "item": {
                    "access_token": "5badcaf789d3d1d09794d8f021f40f0e",
                    "account": {
                        "id": 1,
                        "name": "The Jonas Brothers",
                        "stagebloc_url": "jobros"
                    },
                    "user": {
                        "id": 8,
                        "name": "Carly Rae Jepsen"
                    },
                    "application": {
                        "name": "Music Maker",
                        "description": "Make all your music the bomb diggity!"
                    }
                }
            }]
        }]
    }
    
## /oauth2/token/edit
This endpoint will change the currently authenticated account for this access token. The response will be the same as `/oauth2/token/info`.

account_id
:	the ID of the account to use with this access token
    
# /statuses
This endpoint is used for interacting with an account's statuses.

## /statuses/list

### Returning a specific status

Pass the `id` of a status to return a specific status. If an `id `is passed, all other parameters will be ignored and only the requested status will be returned

id
:	the id of the status to return

	possible values are any status id that belongs to the authenticated account
	
	no default
	
### List the statuses the belong to an account

order_by
:	how to order the results

	possible values are `published`
	
	defaults to `published`

direction
:	what direction to return the results in

	possible values are `ASC` or `DESC`
	
	defaults to `DESC`

limit
:	the limit of results to return

	defaults to `20`

offset
:	the offset of the results to return

	possible values are any number > 0

	defaults to `0`
    
### Example Response (JSON)
	{
	    "response": {
	        "items": [{
	            "id": 223,
	            "short_url": "http:\/\/stgb.dev\/s\/4R",
	            "text": "Meow meow meow",
	            "published": "2013-07-01 14:44:01",
	            "comment_count": 0,
	            "like_count": 0,
	            "repost_count": 0,
	            "author": {
	                "id": 1676,
	                "url": "http:\/\/stagebloc.dev\/user\/jholat\/",
	                "created": "2013-07-01 14:42:12",
	                "name": "Josh",
	                "username": "jholat",
	                "bio": "",
	                "following_count": 0,
	                "likes_count": 0,
	                "content_count": 1
	            },
	            "user_has_liked": false
	        }]
	    }
	}
    
## /statuses/edit
This endpoint can be used for adding statuses to a StageBloc account (note: statuses cannot be edited, only deleted). Upon successful addition, the status data will be returned in the same manner `/statuses/list` would return it. Otherwise, an error message will be returned explaining what was wrong.

text _(required)_
:	the text of the status update

	possible values are any string (when sharing to services such as Twitter the text will be truncated to the character limit with a link to the status on StageBloc appended to the end)
	
account_id
:	a comma seperated list of additional account IDs to post this status to, don't incude the currently authorized account's ID in this string as it will always post to at least that account

	possible values are any IDs that the authenticated user has admin access to, see `/accounts/list`
	
profile_id
:	a comma seperated list of social profile IDs to post this status to

	possible values are IDs of social profiles (only Twitter or Facebook) that the authenticated user or account has connected to StageBloc, see `/accounts/social/list`

latitude
:   a string value of the latitude the status was posted at

longitude
:   a string value of the longitude the status was posted at
