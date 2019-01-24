## Processing Network Responses
Assuming the request is not canceled and there are no connectivity issues, the onResponse() method will be fired. It passes a Response object that can be used to check the status code, the response body, and any headers that were returned. Calling isSuccessful() for instance if the code returned a status code of 2XX (i.e. 200, 201, etc.)
``` kotlin
if (!response.isSuccessful())
{
  throw IOException("Unexpected code " + response)
}
```
The headers can also be access directly using response.header():
``` kotlin
var header = response.header("Date")
```
We can also get the response data by calling response.body() and then calling string() to read the entire payload. Note that response.body() can only be run once and should be done on a background thread.
``` kotlin
Log.d("DEBUG", response.body().string());
```
### Processing JSON data
Suppose we make a call to the GitHub API, which returns JSON-based data:
``` kotlin
var request = Request.Builder()
              .url("https://api.github.com/users/codepath")
              .build()
```             
We can also decode the data by converting it to a JSONObject or JSONArray, depending on the response data:
``` kotlin
client.newCall(request).enqueue(object:Callback() {
  @Throws(IOException::class)
  fun onResponse(call:Call, response:Response) {
    try
    {
      val responseData = response.body().string()
      val json = JSONObject(responseData)
      val owner = json.getString("name")
    }
    catch (e:JSONException) {
    }
  }
})
```
### Processing JSON data with Gson
Note that the string() method on the response body will load the entire data into memory. To make more efficient use of memory, it is recommended that the response is processed as a stream by using charStream() instead. This approach, however, requires using the Gson library. See this guide for setup instructions.
To use the Gson library, we first must declare a class that maps directly to the JSON response:
``` kotlin
internal class GitUser {
  var name:String
  var url:String
  var id:Int = 0
}
```
We can then use the Gson parser to convert the data directly to a Java model:
``` kotlin
val gson = Gson()
client.newCall(request).enqueue(object:Callback() {
  // Parse response using gson deserializer
  @Throws(IOException::class)
  fun onResponse(call:Call, response:Response) {
    // Process the data on the worker thread
    val user = gson.fromJson(response.body().charStream(), GitUser::class.java)
    // Access deserialized user object here
  }
})
```
### Sending Authenticated Requests
OkHttp has a mechanism to modify outbound requests using interceptors. A common use case is the OAuth protocol, which requires requests to be signed using a private key. The OkHttp signpost library works with the SignPost library to use an interceptor to sign each request. This way, the caller does not need to remember to sign each request:
``` kotlin
val consumer = OkHttpOAuthConsumer(CONSUMER_KEY, CONSUMER_SECRET)
consumer.setTokenWithSecret(token, secret)
okHttpClient.interceptors().add(SigningInterceptor(consumer))
```
### Caching Network Responses
We can setup network caching by passing in a cache when building the OkHttpClient:
``` kotlin
var cacheSize = 10 * 1024 * 1024 // 10 MiB
var cache = Cache(File(getApplication().getCacheDir(), "cacheFileName"), cacheSize)
var client = OkHttpClient.Builder().cache(cache).build()
```
We can control whether to retrieve a cached response by setting the cacheControl property on the request. For instance, if we wish to only retrieve the request if data is cached, we could construct the Request object as follows:
``` java
var request = Request.Builder()
              .url("http://publicobject.com/helloworld.txt")
              .cacheControl(CacheControl.Builder().onlyIfCached().build())
              .build()
```                
We can also force a network response by using noCache() for the request:
``` kotlin
  .cacheControl(new CacheControl.Builder().noCache().build())        
```
We can also specify a maximum staleness age for the cached response:
``` kotlin
   .cacheControl(new CacheControl.Builder().maxStale(365, TimeUnit.DAYS).build())
   ```
To retrieve the cached response, we can simply call cacheResponse() on the Response object:
``` kotlin
val call = client.newCall(request)
call.enqueue(object:Callback() {
  fun onFailure(call:Call, e:IOException) {
  }
  @Throws(IOException::class)
  fun onResponse(call:Call, response:Response) {
    val text = response.cacheResponse()
    // if no cached object, result will be null
    if (text != null)
    {
      Log.d("here", text.toString())
    }
  }
})
```
