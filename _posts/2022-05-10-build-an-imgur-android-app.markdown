---
layout: post
title:  Building an imgur android app
date:   2022-05-11 10:21:22 -0700
categories: android
---

This post shows how you could build an Imgur Android app using the latest Android architecture and stacks. 

Here are the stacks: 

- Architecture
	- Android MVVM
	- LiveData
	- Kotlin Coroutines
	- Hilt Dependency Injection


- Networking
	- Retrofit
	- Moshi - JSON parsing
	- Sandwich - handle APIResponse


- Data Layer
	- Room

- Imgur API


# Imgur API
[https://apidocs.imgur.com/][1]

Each application has to get the 
- `client_id`
- `client_secret`

For the public read only api 

`Authorization: Client-ID <YOUR_CLIENT_ID>`

Client ID: ef236e476516602
Client Secret: c1fe74199678ed6127357e83946f87ce18eb5034

Sample request to query data from the earth porn

[https://api.imgur.com/3/gallery/r/earthporn/top/month/2][2]

Sample Response

```json
 "data": [
    {
      "id": "xiN4VpW",
      "title": "I hiked the Inca Trail to Machu Picchu in the Peruvian Andes this week. [4032x3024] [OC]",
      "description": null,
      "datetime": 1650852003,
      "type": "image\/jpeg",
      "animated": false,
      "width": 3226,
      "height": 2419,
      "size": 845971,
      "views": 328114,
      "bandwidth": 277574928694,
      "vote": null,
      "favorite": false,
      "nsfw": false,
      "section": "EarthPorn",
      "account_url": null,
      "account_id": null,
      "is_ad": false,
      "in_most_viral": false,
      "has_sound": false,
      "tags": [],
      "ad_type": 0,
      "ad_url": "",
      "edited": 0,
      "in_gallery": false,
      "link": "https:\/\/i.imgur.com\/xiN4VpW.jpg",
      "ad_config": {
        "safeFlags": [
          "not_in_gallery",
          "sixth_mod_safe",
          "subreddit"
        ],
        "highRiskFlags": [],
        "unsafeFlags": [],
        "wallUnsafeFlags": [],
        "showsAds": true,
        "showAdLevel": 2
      },
      "comment_count": null,
      "favorite_count": null,
      "ups": null,
      "downs": null,
      "points": null,
      "score": 319727,
      "is_album": false
    }
],
"success":true, 
"status": 200

```

# Android MVVM

# Hilt Dependency Injection


# Retrofit

[Retrofit][3] is a type-safe HTTP client for Android and Java. 

You can view the full documentation here [https://square.github.io/retrofit/][4]

**How to pass an authorization header into retrofit? **

There are a few ways that you could do this.  One is to use the `@Header` parameter every time you’re doing a retrofit request

```kt
  @GET(Constants.POSTS_URL)
    fun fetchPosts(@Header("Authorization") token: String): Call<PostsResponse>

```

But this is inconvenient because you have to pass the Header every time. 

The better way is to use an Retrofit Interceptor. 

**How do you set a timeout for Retrofit? **

**How to integrate Moshi with Retrofit? **

**How to do a GET request with query parameters using Retrofit?**

How to do a POST request using retrofit?

How to do a multipart request using retrofit? 

What is logging interceptor and how do you use it?

[https://medium.com/android-news/token-authorization-with-retrofit-android-oauth-2-0-747995c79720][5]

Adding an interceptor to Network Retrofit request

# Moshi

[https://github.com/square/moshi/][6]



# Sandwich API




[1]:	https://apidocs.imgur.com/
[2]:	https://api.imgur.com/3/gallery/r/earthporn/top/month/0
[3]:	https://github.com/square/retrofit
[4]:	https://square.github.io/retrofit/
[5]:	https://medium.com/android-news/token-authorization-with-retrofit-android-oauth-2-0-747995c79720
[6]:	https://github.com/square/moshi/