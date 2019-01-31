## Troubleshooting
OkHttp can be hard to troubleshoot when trying to step through the various layers of abstraction in the libraries. You can add the HttpLogInterceptor that can be added when using the OkHttp3 library, which will print HTTP requests/responses through LogCat. You can also leverage Facebook's Stetho project to use Chrome to inspect all network traffic.

### HttpLogInterceptor
To use HttpLogInterceptor, add this dependency to your Gradle configuration:
``` gradle
implementation 'com.squareup.okhttp3:logging-interceptor:3.6.0'
```
You will need to add a network interceptor for HttpLogInterceptor. See this doc for the different options that can be used.
``` kotlin
val builder = OkHttpClient.Builder()
val httpLoggingInterceptor = HttpLoggingInterceptor()
httpLoggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY)
builder.networkInterceptors().add(httpLoggingInterceptor)
builder.build()
```
### Using with Websockets
OkHttp v3.5 now includes support for bidirectional web sockets. The URL that should be used should be prefixed with ws:// or wss:// for the secure version. Although the connection ports are the same as HTTP (port 80 and port 443), the server still needs to be configured to support WebSockets since they are a completely different protocol.
``` kotlin
val request = Request.Builder().url(url).build()
client.newWebSocket(request, ) = object:WebSocketListener() {
  fun onOpen(webSocket:WebSocket, response:Response) {
    // connection succeeded
  }
  fun onMessage(webSocket:WebSocket, text:String) {
    // text message received
  }
  fun onMessage(webSocket:WebSocket, bytes:ByteString) {
    // binary message received
  }
  fun onClosed(webSocket:WebSocket, code:Int, reason:String) {
    // no more messages and the connection should be released
  }
  fun onFailure(webSocket:WebSocket, t:Throwable, response:Response) {
    // unexpected error
  }
}
val webSocket = client.newWebSocket(request, )
  ```
To send a message, simply use the send() function:
``` kotlin
websocket.send("hello");
```
Note that OkHttp handles all the work on a separate thread, so you don't have to worry about making Websocket calls on the main thread.

If you need to close the connection properly, make sure to use a status code of 1000. See this link for the different status codes.
``` kotlin
webSocket.close(1000, "closing");
```
#### some useful links:
(https://guides.codepath.com/android/Troubleshooting-API-calls)
