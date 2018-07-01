--- 
draft: false
date: 2018-06-28T19:38:24+05:30
title: "Android Architecture Sandbox #3"
slug: "" 
tags: [tech,android]
categories: [tech]
---

So the task is to set up a Repository that'll interface between the database and the REST APIs. The Repository would have a LocalStore implementation that'll take care of handling the database CRUD, and a CloudStore implementation to take care of the RESTful APIs. We need an implementation of the Repository that'll handle switching between these two interfaces. Since we want an interface and all interactions with the module would be uniform, we need to decide on how the communication would take place. 

Since network calls obviously need to be asynchronous, we need to use callbacks. That is where we'll use our dear RxJava classes. We'll get our data as [Observables](http://reactivex.io/documentation/observable.html). But since Android Architecture components offers us [LiveData](https://developer.android.com/topic/libraries/architecture/livedata), we'll use LiveData to get our data. LiveData is an observable but is life-cycle aware, which is good because it'll send notifications to those Observers whose is in active state.

Ideally I'd prefer to keep a separate Repository for each model as the pattern describes. But I'd want to avoid code duplication at the moment and since I know the scope of this project is small and I don't have to worry about reaching method limits for a single Repository class, I'll keep all the model CRUD operations in a single Repository class. So we'll design our interface as such.

```kotlin
interface Repository {
    public getTransactions(): LiveData<List<RetailTransaction>>
    fun getTransaction(id: Long): LiveData<RetailTransaction>
    fun postTransaction(retailTransaction: RetailTransaction): Observable<Void>
    ...

    fun getCustomers(): Observable<List<Customer>>
    fun getCustomer(id: Long): Observable<Customer>
    fun postTransaction(customer: Customer): Observable<Void>
    ...
}
```

Let us start with Network implementation for Repository, we'll call it CloudStore.

```kotlin
class CloudStore: Repository{
    fun getTransactions(): LiveData<List<RetailTransaction>> {
        //Here is where we will need to interface with an instance of our Retrofit interface implementation
    }
}
```

So how do we get an instance of our Retrofit interface? We'll use a builder method from Retrofit for that. The builder will take a base url which we should have been saved as a variable in BuildConfig. We need to tell Retrofit use Gson as our serialiser and de-serialiser. We'll use `addConverterFactory()` for that. But we need to configure Gson to use a custom adapter that'll let Gson know how to serialise and de-serialise time data in string into LocalDateTime format from ThreeTenABP. The adapter is a class that implements `JsonSerializer<LocalDateTime>, JsonDeserializer<LocalDateTime>`. To get the data as an observable we'll use `addCallAdapterFactory()` and provide a RxCallAdapter by `RxJava2CallAdapterFactory.create()`. Finally we pass the interface to the `create()` to get the implementation.

```kotlin
class CloudStore: Repository {
    fun init() {
        val retrofit = Retrofit.Builder()
        .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
        .addConverterFactory(
            GsonConverterFactory.create(
                GsonBuilder().registerTypeAdapter(
                    LocalDateTime::class.java, LocalDateTimeConverter()
                ).create()
            )
        )
        .baseUrl(HOST)
        .build()

    restImp = retrofit.create(ApiStore::class.java) //This is our Retrofit interface
    }

    fun getTransactions(): LiveData<List<RetailTransaction>> {
        return restImp.getTransactions() //Error: But we expect our Observable as LiveData
    }

}
```

We need to convert the Observable to LiveData

```kotlin
private fun <T> getLiveData(observable: Observable<T>): MutableLiveData<T> {
    val mutableLiveData = MutableLiveData<T>()
    observable.subscribeOn(Schedulers.newThread())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({ mutableLiveData.postValue(it) },
            { it.printStackTrace() })
        return mutableLiveData
}
```

This should do the trick for the moment. May need to comeback if I find a better way. So we get


```
fun getTransactions(): LiveData<List<RetailTransaction>> {
    return getLiveData(restImp.getTransactions())
}
```

Now we need to set up LocalStore for managing the DataBase. That means its time we dive head first into *Room*. That'll be the next part.








