---
layout: post
title:  “Understand paging, data source and jetpack compose”
date:   2024-02-01 10:21:22 -0700
categories: android
---

This post captures my understanding about pagination using jetpack compose components and android data source, which is use to fetch chunk of data from an api and display them on the UI 

# How do you structure the code to use paging data?

```kt

fun GalleryScreen(galleryViewModel: GalleryViewModel = hiltViewModel()) {
    GalleryScreen(
        galleryViewModel.plantPictures
    )
}

```

The gallery screen display the data from plantPictures, which is coming as a flow 
`Flow<PagingData<UnsplashPhoto>>` from the repository

```kt

// paging items 
val pagingItems: LazyPagingItems<UnsplashPhoto> = plantPictures.collectAsLazyPagingItems()


```

```kt

val pagingItems: LazyPagingItems<UnsplashPhoto> = plantPictures.collectAsLazyPagingItems()

```

The actual work is happening inside the repository, in this case, the `UnsplashRepository` which is used to fetch the data from the Unsplash Image Service 

```kt

 fun getSearchResultStream(query: String): Flow<PagingData<UnsplashPhoto>> {
        return Pager(
            config = PagingConfig(enablePlaceholders = false, pageSize = NETWORK_PAGE_SIZE),
            pagingSourceFactory = { UnsplashPagingSource(service, query) }
        ).flow
    }


```

The repository returns a Pager, which passed in a paging config and a paging source factory.  

The `UnsplashPagingSource` is the data source for the paging data.  It implements the `PagingSource`  abstract class and provides the implementation of two key methods for the pagination service.  The first method is `load` and the second method is `getRefreshKey`

```kt

    public abstract suspend fun load(params: LoadParams<Key>): LoadResult<Key, Value>

```

This is the sample implementation of the `getRefreshKey()`

```kt
override fun getRefreshKey(state: PagingState<Int, UnsplashPhoto>): Int? {
        return state.anchorPosition?.let { anchorPosition ->
            // This loads starting from previous page, but since PagingConfig.initialLoadSize spans
            // multiple pages, the initial load will still load items centered around
            // anchorPosition. This also prevents needing to immediately launch prepend due to
            // prefetchDistance.
            state.closestPageToPosition(anchorPosition)?.prevKey
        }
    }
```

**What is the purpose of the `getRefreshKey()` in PagingSource?**

The getRefreshKey function in a Paging Source in Android is an important part of the Paging 3 library, which is used for efficiently loading and displaying large datasets in a RecyclerView. This function plays a critical role in handling refreshes and reloads of data, ensuring that the correct data is loaded when a user requests a refresh of the content.

Here's a detailed explanation of its purpose:

**State Restoration on Refresh:** When the data displayed in a RecyclerView needs to be refreshed (for example, due to a swipe-to-refresh action), the getRefreshKey function helps to determine the position in the dataset from where to start loading data again. This is particularly important for maintaining the user's position in the dataset after a refresh, so they don't lose their place.

**Handling Process Death and Recreating State:** In Android, apps may undergo process death, where the operating system kills the app to reclaim resources. When the app comes back from process death, getRefreshKey is used to restore the last visible state of the list. It provides the Paging library with information about the dataset's key that should be used to reinitialize the loader.

**Error Recovery:** If an error occurs while loading data (like a network error), the Paging library may need to retry loading data. getRefreshKey helps in determining the starting point for the next load attempt.

**Configuring Initial Load Position:** It allows the developer to configure the PagingData's initial load position. For example, if your list should start from a specific item (based on a key) when first loaded, getRefreshKey can be used to provide that key.

It's important to implement getRefreshKey properly in your Paging Source to ensure a smooth and user-friendly experience, especially in scenarios involving data refreshes and app state restoration. The exact implementation can vary based on the structure of your dataset and your specific use case.

### Examples of `getRefreshKey()` implementation

Simple Integer Key: 

Assuming you have a simple integer-based paging where each page has a fixed number of items, the getRefreshKey method might look like this:

```kt


override fun getRefreshKey(state: PagingState<Int, MyItem>): Int? {
    // Get the anchor position from the state.
    val anchorPosition = state.anchorPosition ?: return null

    // Look up the closest page to the anchor position.
    val page = state.closestPageToPosition(anchorPosition)
    return page?.prevKey?.plus(1) ?: page?.nextKey?.minus(1)
}


```

In this example, MyItem is your data type. This method calculates the refresh key based on the item closest to the current user's viewport.

Network-based paging:

```kt

override fun getRefreshKey(state: PagingState<Int, MyItem>): Int? {
    return state.anchorPosition?.let { anchorPosition ->
        val anchorPage = state.closestPageToPosition(anchorPosition)
        anchorPage?.prevKey?.plus(1) ?: anchorPage?.nextKey?.minus(1)
    }
}

```

Dynamic dataset paging: 

If your dataset is dynamic (e.g., items can be added or removed), you may need to implement a more robust `getRefreshKey` method:

```kt

override fun getRefreshKey(state: PagingState<Int, MyItem>): Int? {
    val anchorPosition = state.anchorPosition ?: return null

    val itemAtIndex = state.closestItemToPosition(anchorPosition)
    return itemAtIndex?.id // Assuming each MyItem has a unique ID.
}

```

