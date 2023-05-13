# Suspending Functions
1. Functions that can be paused and resumed at later time, they wait
without blocking.
2. Syntax same as normal function has an additional suspend keyword
3. Suspending functions can be invoked by another *suspending functions*
    or from within a *coroutine*

Initial
    |
    V

```kotlin
suspend fun backgroundTask(param: Int): Int {
     // long running operation
}
```
Final (After compilation)
  |
  V
```kotlin
fun backgroundTask(param: Int, callback: Continuation<Int>): Int {
   // long running operation
}
```
continuation is an interface which contains two function
resume -> it is used to resume the coroutine
resumeWithException -> it is used when the error has occured while the functions
    is suspended


body of the continuation function is turned into a state machine

[stackoverflow-discussion](https://stackoverflow.com/questions/47871868/what-does-the-suspend-function-mean-in-a-kotlin-coroutine)

# Coroutines
1. Code in the coroutine scope is sequential by default





what is the difference between the coroutine and a suspending function?
coroutine is basically a lightweight thread and suspending functions are functions that can be suspended

# Analogy with thread
block -> suspend
thread -> coroutine

# Coroutine Builders
There are three major coroutine builders other libraries can define their own coroutine builders
launch, async and runBlocking

async -> returns Deferred<T> and we can await the return data
launch -> returns Job,  It is used to start a computation that does not return any result  
    we can wait for the computation inside the launch coroutine builder to finish by using Job.join() function

Deferred basically extends Job
what are coroutine-scopes?? basically the scope in which the coroutine runs 
every thing between the curley braces of a scope{}


# Coroutine Scopes
what are coroutine-scopes?? basically the scope in which the coroutine runs 
every thing between the curley braces of a scope{}

coroutine scopes are responsible for maintaining the parent child relationship -> this is pretty
necessary because the parent needs to wait for all the coroutines to finish before the parent's scope 
finishes

coroutine-context -> coroutine context stores the additional information to run a given coroutine
example -> name of the coroutine the dispatcher that defines the thread pool from which it can 
take the threads on which coroutines can be scheduled


# Channels
Channels are communication primitive that allow data to be passed between the coroutines
one or many coroutines can send data (producers)
one or many coroutines can listen to the data (consumers)

when there are multiple consumers each element in the channel is entertained only once 

A channel can **suspend send() and recieve() operation** this happens when the channel is full or empty
so basically it will suspend the send() operation when the channel is full that makes sense because it 
does not have any space to store more elements
it suspends the recieve() operation when the channel is empty thus saving the CPU cycles of continuously hitting the channel

there are 3 channel interfaces -> sendChannel, recieveChannel, channel
channel extends the first two

you create a channel and then pass as sendChannel to producers so that they can only send the data
and pass it as recieveChannel to consumers so that they only consume the data
both send and recieve functions are suspending -> this makes sense because a channel can suspend both of the operations

```kotlin
interface SendChannel<in E> {
    suspend fun send(element: E)
    fun close(): Boolean
    // the close function indicates that there are no more data to be send
}

interface ReceiveChannel<out E> {
    suspend fun receive(): E
}

interface Channel<E> : SendChannel<E>, ReceiveChannel<E>
```
there can be many types of channels defined in the library and by default the channel is Rendezvous channel

#TODO -> 
