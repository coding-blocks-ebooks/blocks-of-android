# OkHttp

Downloading images from the internet or fetching data from an API server or any other operation that involves sending or receiving data from the web requires networking. Earlier a lot of boilerplate code had to be prepared for doing networking but nowadays third party libraries like OkHttp, Retrofit, Moshi and Picasso have come into existence which not only makes the task easier but also faster and more efficient. In this tutorial, we will talk about the OKHttp library.

OkHttp is a third party library that is used for sending and receing HTTP-based network requests. This open source library has been created by developers at Square. It is very easy to use and has the follwoing benefits:
1. Performs the operation faster and also saves bandwidth.
2. OkHttp allows sharing of socket, in case the calls are to the same host.
3. Connection pooling reduces request latency (if HTTP/2 isn’t available).
4. Transparent GZIP shrinks download sizes.
5. Response caching avoids the network completely for repeat requests.

## Setup

Firstly, ask for Internet permission in the Manifest file
```
<uses-permission android:name="android.permission.INTERNET"/>
```

Add the following dependency in the `app/build.gradle` file:
```
dependencies {
  implementation 'com.squareup.okhttp3:okhttp:3.11.0'
}
```
## Making network calls

First of all, instantiate an OkHttp client and create a request object

```
val client = OkHttpClient()

val request = Request.Builder()
              .url(url)
              .build()
```

You can also add query parameters:

```
 val urlBuilder = HttpUrl.parse("https://api.github.com").newBuilder()
 urlBuilder.addQueryParameter("v", "1.0")
 urlBuilder.addQueryParameter("user", username)
 val url = urlBuilder.build().toString()

 val request = Request.Builder()
                .url(url)
                .build()
```

The next step is to get the response from the server, this can be in asynchronus as well as synchronus manner.
 ### Synchronous call
 
The synchronous call to the network is made with the help of a an asyn task because android does not allow making network calls on the main thread. However, this is not the preferred way of doing the task because it leaks the context of the activity and also cancelling of requests is not suppported.
 ```
 val response = client.newCall(request).execute()
 ```
 
 ### Asynchronous call
 
This is the preferred way of asking calls to the network as it allows making multiple requests and cancelling them all with a single command.

The point to be noted here is that the call is made via some other thread and the same thread is used to handle the response that is received. However, android does not allow any changes to be made to the UI of the application from any other thread except the main thread. Therefore, in order to publish the response of the newtwork call or to do any other change with the UI of the thread, use runOnUiThread method as shown in the snippet below:  

```
fun makeNetworkCall(url: String) {
        val client = OkHttpClient()
        val request = Request.Builder().url(url).build()

        client.newCall(request).enqueue(object : Callback {
            
            override fun onResponse(response: Response?) {
                val result = response!!.body().string()
                this@Main3Activity.runOnUiThread(java.lang.Runnable {
                    textView.text = result
                })
            }
            override fun onFailure(request: Request?, e: IOException?) {
                e!!.printStackTrace()
            }
        })
  }
  ```

 
 
 
