---
layout: post
title: MVI Architecture for Android
categories:
  - "Android"
  - "Architecture"
lang: en
---

Architecture patterns stand for creating scalable and maintainable apps. In android history, there are already **MVC**, **MVP** and **MVVM** patterns. **MVI** (Model, View, Intent) is the one of latest architecture approach in android world. MVVM and MVP have same footprint with MVC at some point. **Controller** in MVC has almost the same responsibility with **Presenter** in MVP and **Viewmodel** in MVVM. MVI has new ideas which are not present in other architecture patterns. So now time to ask;

<!--more-->
## What is the MVI?

MVI is including users in the software design steps. '**I**' for MVI stands for intent and it represents the intention of the user. Do not confuse it with intent in android that helps developers create new activities, services, etc.  Every action of the user can be considered as an intent. '**V**' represents the **view** as usual. In the Android world, it can be fragments of activities or custom views. '**M**' stands for the **model**. The model can be described as the state or data of the user interface. MVI provides unidirectional state flow by making this model immutable. By unidirectional flow, every intent matches its result. That provides fewer buggy applications.

![MVI Unidirectional Flow](/img/mvi-unidirection-flow.png)

## Dive into the MVI components

In MVI pattern; viewmodel takes an intent as an input and converts these intents to the actions. Then these actions goes through processor which responsible for interacting data layer. Processor returns results need to be reduced states. Reducing state is an important part of this path. Reducing is creating a new state with considering results and previous states of the view. The view takes a state from ViewModel to render itself and show it to the user. And also view gives intent to ViewModel to retrieve new states. So the process is basically repeating itself about handling intents and providing new states.

#### **`BaseMvi.kt`**
```kotlin
interface MviIntent

interface MviAction

interface MviResult

interface MviState

interface MviViewModel<I: MviIntent, S: MviState>{
  fun processIntents(intents: Observable<I>)
  fun states(): Observable<S>
}

interface MviView<I: MviIntent, in S: MviState>{
  fun intents():Observable<I>
  fun render(state: S)
}
```

<img style="float: left;" src="/img/login-demo.png">

I want to begin the process from start and show details step by step. Assume we have a login screen and we want to build it with MVI architecture. In our login screen, users have to log in with credentials or create an account to proceed.  So basically we expect two things from the user: Login and Signup. For login, the user has to do three things.

* Enter username,
* Enter password,
* Login with login button,
* Clear inside of editTexts.

With this information, we can create our **intents** like below.

#### **`LoginPageIntent.kt`**
```kotlin
sealed class LoginPageIntent: MviIntent {
  data class UsernameIntent(username: String): LoginPageIntent()
  data class PasswordIntent(password: String): LoginPageIntent()
  data class LoginIntent(username: String, password: String): LoginPageIntent()
  object ClearTextFieldsIntent: LoginPageIntent()
  object SignupIntent: LoginPageIntent()
}
```
Now we should create **actions** to correspond with these intents.

#### **`LoginPageAction.kt`**
```kotlin
sealed class LoginPageAction: MviAction {
  data class UsernameAction(username: String): LoginPageAction()
  data class PasswordAction(password: String): LoginPageAction()
  data class LoginAction(username: String, password: String): LoginPageAction()
  object ClearTextFieldsAction: LoginPageAction()
  object SignupAction: LoginPageAction()
}
```

Till now we wrote our actions and intents for the login page. This is the first half of the MVI cycle. We need to define our **results** for each action.

#### **`LoginPageResult.kt`**
```kotlin
sealed class LoginPageResult: MviResult {
  sealed class UsernameResult: LoginPageResult(){
    object Checking: UsernameResult()
    data class Success(val username: String): UsernameResult()
    data class Failure(var error: Throwable): UsernameResult()
  }
  sealed class PasswordResult: LoginPageResult(){
    object Checking: PasswordResult()
    data class Success(val password: String): PasswordResult()
    data class Failure(var error: Throwable): PasswordResult()
  }
   sealed class LoginResult: LoginPageResult(){
    object Loading: LoginResult()
    data class Success(val username: String, val password: String): LoginResult()
    data class Failure(var error: Throwable): LoginResult()
  }
   sealed class ClearTextFieldsResult: LoginPageResult(){
    object Loading: ClearTextFieldsResult()
    object Success: ClearTextFieldsResult()
    data class Failure(var error: Throwable): ClearTextFieldsResult()
  }
   sealed class SignupResult: LoginPageResult(){
    object Loading: SignupResult()
    object Success: SignupResult()
    data class Failure(var error: Throwable): SignupResult()
  }
}
```

Now we need a **processor** for converting our actions to results. The processor should keep the repository instance for communicating with the database or remote server. Let's assume we have a login endpoint that accepts username and password and returns a response for the login process. We can define this processor like below;

#### **`LoginProcessorHolder.kt`**
```kotlin
class LoginPageActionProcessHolder @Inject constructor(
    private val repository: AppRepository
) {
   val loginProcessor =
        ObservableTransformer<LoginPageAction.LoginAction,
         LoginPageResult.LoginResult> { action ->
            action.flatMap {
                repository.invoke(it.username, it.password)
                    .map { result -> LoginPageAction.LoginAction.Success }
                    .cast(LoginPageAction.LoginAction::class.java)
                    .onErrorReturn(LoginPageAction.LoginAction::Error)
                    .subscribeOn(Schedulers.io())
                    .observeOn(AndroidSchedulers.mainThread())
                    .startWith(LoginPageAction.LoginAction.Loading)
            }
        }
}
```

With results coming from processors, in ViewModel, we need to reduce results to new states. But first, define our **login state**;

#### **`LoginPageState.kt`**
```kotlin
data class LoginPageState(
  val isProcessing: Boolean,
  val username: String,
  val password: String,
  val isLoggedIn: Boolean,
  val isCleared: Boolean,
  val isRedirectSignup: Boolean,
  val error: Throwable?
) : MviState {
  fun idle(): LoginPageState = LoginPageState(
    false,
    Constants.EMPTY_STRING,
    Constants.EMPTY_STRING,
    false,
    false,
    false,
    null
    )
}
```

Reducing is evaluating the previous state and current result to conclude the new state of the page. We do this process in ViewModel and pass the new state to view.

#### **`LoginViewModel.kt`**
```kotlin
class LoginViewModel @Inject constructor(
    private val actionProcessorHolder: LoginPageActionProcessHolder
) : ViewModel(), MviViewModel<LoginPageIntent, LoginPageState> {
  
   override fun states(): Observable<LoginPageState> {...}
   
   override fun processIntents(intents: Observable<LoginPageIntent>) {...}
    
   companion object {
      private val reducer = ...
   }
}
```

Last but not less important, our views should be able to receive new states for rendering and also be able to send new intents to ViewModel;

#### **`LoginFragment.kt`**
```kotlin
class LoginPageFragment : Fragment(), MviView<LoginPageIntent, LoginPageState> {
 
  override fun intents(): Observable<LoginPageIntent> {...}
  
  override fun render(state: LoginPageState) {...}
}
```

We went through each component in MVI so far. There is a sample project below. You can debug and run it to see how it works all pieces together. It was written in Kotlin and used RxJava. I hope you'll find it useful. Now look at the pros and cons of MVI:

## Advantages of MVI
* State gets things easier to understand the view logic. And easy to maintain state because every action of user returns its state.
* Easy to expand. If a new feature requires new abilities about the screen, then just add new states and intents to cover all new features.
* The view can not modify the state, so it provides a single source of truth(SSOT). Since the state is immutable from the view, it ensures thread safety.
* Easier to debug and raise the testability of the codebase.

## Disadvantages of MVI
* It causes us to write much boilerplate code since we have to maintain every state for each user intent.
* Creating states for each user action can be a problem for app memory management. And also can cost too much for simple screens.

I've developed simple movie app for demonstrating MVI architecture. You can check out [**this repository**](https://github.com/EyyupAlkis/MovieShow/)  to find out more.

Many thanks for reading, I hope you find it useful. 

