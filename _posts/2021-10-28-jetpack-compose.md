---
layout: post
title:  "Jetpack Compose"
date:   2021-10-02 10:21:22 -0700
categories: android
---

I've recently spent a lot of time learning and building app using Jetpack Compose. Here's my note of all the resources and learning from using Jetpack Compose.

### How to manage UI state inside Jetpack Compose

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

### Livedata observable example

Here's an example of how you observe the data change in Composable

```kotlin
val items: List<TodoItem> by todoViewModel.todoItems.observeAsState(listOf())
```

There are a lot of things going here, so let's explain:

- `val items: List<TodoItem>` - declares a variable `items` of type `List<TodoItem>`
- `todoViewModel.todoItems` - is a LiveData<List<TodoItem>> from the `ViewModel`
- `.observeAsState` observes a `LiveData<T>` and converts it into `State<T>` object.  It's here that Compose can react to the value change.  
- `listOf()` - an initial value to avoid the possible `null()` results before the `LiveData`is initialized, if it wasn't passed `items` would be List<TodoItem>? which is nullable.
- `by` is the property delegate syntax in Kotlin, it lets us automatically unwrap `State<List<TodoItem>>` from `observeAsState` into the regular `List<TodoItem>`


### Compose and Recomposition concept in Jetpack Compose

One interesting thing that I learned when working with Jetpack Compose is the side effect of the Recomposition process.

Composition means drawing things on the screen. It builds a tree of every composable that was called.  For example, the Composable function will draw a list view on the screen, which will include drawing the LazyColumn and all the Row inside that LazyColumn.  What will happen when the list changes?  The UI gets redraw.  This process of redrawing is called Recomposition.

But when working Jetpack Compose, you should be careful about not introducing a `side-effect`.   A `side effect` is any change that is visible outside of a visible function.  An example of side-effects are updating state in a `ViewModel`,  calling `Random.nextInt()` or writing to a database.

The trick is to `remember` the value that was previously used in when the Row recomposes.   Compose function lets us store values in the composition tree, so we can update the Row to store the value in the composition tree.

`val iconAlpha: Float = remember(todo.id) { randomTint() }`

[remember](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#remember(kotlin.Function0))
* gives a composable function memory
* a value computed by `remember` will be stored in the composition tree, and only be recomputed if the keys to `remember` change.
* You can think of `remember` as giving storage for a single object to a function the same way a `private val` property does to an object.

The `remember(todo.id) { randomTint() }` call has two parts:

- `key arguments` - the `key` that this remember uses, this is the part that is passed parentheses.  In this case, we pass in the `todo.id`.  Some sort of id here is good.

- `calculation` - a lambda that computes a new value to be remembered.

Very important note -- **Recomposition should be side-effect free**

An **idempotent** composable always produces the same result for the same inputs and has no side-effects on recomposition.

The other thing to consider when adding memory to a composable is always ask yourself "will some caller reasonably want to control this?"

* if the answer is yes, makes it a parameter instead.
* if the answer is no, keep it as a local variable.

Remember stores values in the Composition, and will forget them if the Composable that called `remember` is removed.  The problem with this is you cannot rely upon `remember` to store important things inside of composables that add and remove children such as `LazyColumn`.  The typical problem when you scroll the list view, the invisible items will be removed and reconstructed depending on its position.  When this happens, all the previously stored `remember` values will be lost, and you might end up with weird bugs.

To fix this, we'll use the state and state hoisting.

### Unidirectional Data Flow

![image](/assets/images/unidirectional.png)

### Resources
- [https://developer.android.com/codelabs/jetpack-compose-state#0](https://developer.android.com/codelabs/jetpack-compose-state#0)

- [https://github.com/google/accompanist](https://github.com/google/accompanist)

- [Google Codelab on Jetpack Compose State](https://developer.android.com/codelabs/jetpack-compose-state#0) shows you how to build a todo list app using the Stateful UI that displays an interactive, editable todo list.
