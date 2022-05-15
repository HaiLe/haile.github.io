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

What are the benefits of using MVVM? 

What is an example of using a sample MVVM activity and view model?


# Kotlin Coroutines

# LiveData

How to use LiveData in the app?

What is LiveData good for?

# RecyclerView

# Fragments

# Flow

# Hilt Dependency Injection

Why Hilt Dependency Injection?

How do you do dependency injection using Hilt?


# Retrofit

[Retrofit][3] is a type-safe HTTP client for Android and Java. 

You can view the full documentation here [https://square.github.io/retrofit/][4]

Add the dependency to project `build.gradle` file

```bash
implementation 'com.squareup.retrofit2:retrofit:(insert latest version)'
```

**How to pass an authorization header into retrofit? **

There are a few ways that you could do this.  One is to use the `@Header` parameter every time you’re doing a retrofit request

```kt
  @GET(Constants.POSTS_URL)
    fun fetchPosts(@Header("Authorization") token: String): Call<PostsResponse>

```

But this is inconvenient because you have to pass the Header every time. 

The better way is to use an Retrofit Interceptor.  

### Adding an authorization Header

First, implement the interceptor class and add headers to the network request

```kt
package network

import okhttp3.Interceptor

class ImgurAuthInterceptor : Interceptor {

    // api constants
    val IMGUR_CLIENT_ID = "ef236e476516602"
    val IMGUR_CLIENT_SECRET = "c1fe74199678ed6127357e83946f87ce18eb5034"

    override fun intercept(chain: Interceptor.Chain): okhttp3.Response {
        var request = chain.request()
        request = request.newBuilder().header("Authorization", "Client-ID $IMGUR_CLIENT_ID").build()
        return chain.proceed(request)
    }
}
```

In your network module, add the interceptor 

```kt
package network

import com.google.gson.GsonBuilder
import com.skydoves.sandwich.adapters.ApiResponseCallAdapterFactory
import dagger.Module
import dagger.Provides
import dagger.hilt.InstallIn
import dagger.hilt.components.SingletonComponent
import me.haile.androidmvvm.main.PopularRepoRepository
import okhttp3.OkHttpClient
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory
import javax.inject.Singleton

@Module
@InstallIn(SingletonComponent::class)
object NetworkManager {

    private const val IMGUR_BASE_URL = "https://api.imgur.com/3/"
    private var imgurApiService: ImgurApiService? = null

    @Singleton
    @Provides
    fun getImgurApiService(): ImgurApiService {
        return imgurApiService ?: kotlin.run {
            buildImgurNetworkLayer().create(ImgurApiService::class.java)
        }
    }


    private fun buildImgurNetworkLayer(): Retrofit {

        val client = OkHttpClient.Builder().addInterceptor(ImgurAuthInterceptor()).build()
        val gsonBuilder = GsonBuilder()
        val gson = gsonBuilder.create()

        return Retrofit.Builder().baseUrl(IMGUR_BASE_URL)
            .addConverterFactory(GsonConverterFactory.create(gson))
            .addCallAdapterFactory(ApiResponseCallAdapterFactory.create())
            .client(client)
            .build()
    }

    @Singleton
    @Provides
    fun providesPopularRepository(githubAPIService: GithubAPIService) =
        PopularRepoRepository(githubAPIService)
}
```


Here is the code that add the interceptor into the request header, so you don’t have to use the `@Header` every time you’re making a request. 

```kt
val client = OkHttpClient.Builder().addInterceptor(ImgurAuthInterceptor()).build()
```

**How do you set a timeout for Retrofit? **

```kt
val client = OkHttpClient
            .Builder()
            .addInterceptor(ImgurAuthInterceptor())
            .connectTimeout(Constants.CONNECTION_TIMEOUT_IN_MILLIS, TimeUnit.MILLISECONDS)
            .readTimeout(Constants.CONNECTION_TIMEOUT_IN_MILLIS, TimeUnit.MILLISECONDS)
            .build()
```


Also, you could add the retryOnConnectionFailure to the request

```kt
val client = OkHttpClient
            .Builder()
            .addInterceptor(ImgurAuthInterceptor())
            .connectTimeout(Constants.CONNECTION_TIMEOUT_IN_MILLIS, TimeUnit.MILLISECONDS)
            .readTimeout(Constants.CONNECTION_TIMEOUT_IN_MILLIS, TimeUnit.MILLISECONDS)
            .retryOnConnectionFailure(true)
            .build()
```

**How to integrate Moshi with Retrofit? **

**How to do a GET request with query parameters using Retrofit?**

How to do a POST request using retrofit?

How to do a multipart request using retrofit? 

What is logging interceptor and how do you use it?

[https://medium.com/android-news/token-authorization-with-retrofit-android-oauth-2-0-747995c79720][5]

Adding an interceptor to Network Retrofit request

# Moshi

Questions: 

Why is Moshi better than GSON?
How to integrate Moshi and use it with Retrofit?
Who developed Moshi?


[https://github.com/square/moshi/][6]



# Sandwich API

Why should you use the Sandwich API?
How do you enable timeout and caching using Sandwich API?

# Glide

What is Glide good for?


# Resources

[https://www.javacodemonk.com/retrofit-basic-authentication-in-android-a47245fb][7]



[1]:	https://apidocs.imgur.com/
[2]:	https://api.imgur.com/3/gallery/r/earthporn/top/month/0
[3]:	https://github.com/square/retrofit
[4]:	https://square.github.io/retrofit/
[5]:	https://medium.com/android-news/token-authorization-with-retrofit-android-oauth-2-0-747995c79720
[6]:	https://github.com/square/moshi/
[7]:	https://www.javacodemonk.com/retrofit-basic-authentication-in-android-a47245fb