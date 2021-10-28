---
layout: post
title:  "Jetpack Compose"
date:   2021-10-02 10:21:22 -0700
categories: android jetpack compose livedata
---

I've recently spent a lot of time learning and building app using Jetpack Compose. Here's my note of all the resources and learning from using Jetpack Compose.

### How to manage UI state inside Jetpack Compose

[Google Codelab on Jetpack Compose State](https://developer.android.com/codelabs/jetpack-compose-state#0) shows you how to build a todo list app using the Stateful UI that displays an interactive, editable todo list.

In Android, the typical event loop goes like this: event -> update -> display
- Event - An event occurred
- Update - An event handler changes the state that is used by the UI
- Display - The UI updated with the new state

In Android, unstructured state (like updating code in a textview using the by listening to the afterTextChange listener and update the textview inside the activity) will be a problem as your app grows.  Testing will be hard because the state of the UI is interwoven with the Views.  When the screen has a lot of events, things might not get updated correctly. It also makes the code complex as you bundle the logic inside the view, using this pattern.

Here's a typical example of an unstructured state in Android:

```kotlin

class HelloCodelabActivity : AppCompatActivity() {

   private lateinit var binding: ActivityHelloCodelabBinding
   var name = ""

   override fun onCreate(savedInstanceState: Bundle?) {
       /* ... */
       binding.textInput.doAfterTextChanged {text ->
           name = text.toString()
           updateHello()
       }
   }

   private fun updateHello() {
       binding.helloText.text = "Hello, $name"
   }
}

```

Android fixes this by using ViewModel and LiveData.

ViewModel lets you extract the state from the UI, and define events that the UI can call to update that state.  For example, here is how ViewModel would work in this case.   

```kotlin

class HelloCodelabViewModel: ViewModel() {

   // LiveData holds state which is observed by the UI
   // (state flows down from ViewModel)
   private val _name = MutableLiveData("")
   val name: LiveData<String> = _name

   // onNameChanged is an event we're defining that the UI can invoke
   // (events flow up from UI)
   fun onNameChanged(newName: String) {
       _name.value = newName
   }
}

class HelloCodeLabActivityWithViewModel : AppCompatActivity() {
   private val helloViewModel by viewModels<HelloCodelabViewModel>()

   override fun onCreate(savedInstanceState: Bundle?) {
       /* ... */

       binding.textInput.doAfterTextChanged {
           helloViewModel.onNameChanged(it.toString())
       }

       helloViewModel.name.observe(this) { name ->
           binding.helloText.text = "Hello, $name"
       }
   }
}

```

In the ViewModel, the state is represented by LiveData.  LiveData is basically an observable state holder -- it means that it provide a way for anyone to observe the change to the state of the data. When a state of a livedata gets updated, an observer of that data will be notified.

In Android, there are other observable types like LiveData, StateFlow, Flow and Observable.

In the previous example, ViewModel exposes one event `onNameChanged()` to let the view update the model `name` whenever there's an update to `name`.  This way, we keep `name` in a centralized place let the observers (fragment, activity) listen to the event and update when neccessary.

This pattern is called unidirectional data flow where state flows down and events flow up.



### Compose and Recomposition concept in Jetpack Compose

### Unidirectional Data Flow

![Unidirectional image](/haile.github.io/assets/unidirectional.png)


### Resources
[https://developer.android.com/codelabs/jetpack-compose-state#0](https://developer.android.com/codelabs/jetpack-compose-state#0)
[https://github.com/google/accompanist](https://github.com/google/accompanist)
