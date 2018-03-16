\#Retrofit

Retrofit is a REST Client for Android and Java by Square. It makes it relatively easy to retrieve and upload JSON \(or other structured data\) via a REST based webservice. In Retrofit you configure which converter is used for the data serialization. Typically for JSON you use GSon, but you can add custom converters to process XML or other protocols. Retrofit uses the OkHttp library for HTTP requests.

Retrofit 2 by default leverages OkHttp as the networking layer and is built on top of it.

## Implementation of a Retrofit 2

* Build a new project open your `build.grade`.

```java
compile 'com.squareup.retrofit2:retrofit:2.1.0'
compile 'com.google.code.gson:gson:2.6.2'
compile 'com.squareup.retrofit2:converter-gson:2.1.0'
```

* OkHttp dependency is already shipped with Retrofit 2 dependency. If you wish to use a separate OkHttp dependency, you should exclude the OkHttp dependency from Retrofit 2 as:

```java
compile ('com.squareup.retrofit2:retrofit:2.1.0') {
// exclude Retrofit’s OkHttp dependency module and define your own module import
exclude module: 'okhttp'
}
compile 'com.google.code.gson:gson:2.6.2'
compile 'com.squareup.retrofit2:converter-gson:2.1.0'
compile 'com.squareup.okhttp3:logging-interceptor:3.4.1'
compile 'com.squareup.okhttp3:okhttp:3.4.1'
```

* The logging-interceptor generates a log string of the entire response that’s returned.

* Add the permission to access internet in the AndroidManifest.xml file.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

# OkHttp Interceptors

Interceptors are a powerful mechanism present in OkHttp that can monitor, rewrite, and retry calls.  
Interceptors can be majorly divided into two categories:

* Application Interceptors : To register an application interceptor, we need to call addInterceptor\(\) on OkHttpClient.Builder

* Network Interceptors : To register a Network Interceptor, invoke addNetworkInterceptor\(\) instead of addInterceptor\(\)

# Setting Up the Retrofit Interface

The getClient\(\) method in the below code will be called every time while setting up a Retrofit interface. Retrofit provides with a list of annotations for each of the HTTP methods: @GET, @POST, @PUT, @DELETE, @PATCH or @HEAD.

```java
package Stonedcoder.coding-blocks.retrofit;

import Stonedcoder.coding-blocks.retrofit.pojo.MultipleResource;
import okhttp3.OkHttpClient;
import okhttp3.logging.HttpLoggingInterceptor;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

class APIClient {
    private static Retrofit retrofit = null;
    static Retrofit getClient() { 
        HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();
        interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);

        OkHttpClient client=newOkHttpClient.Builder().addInterceptor(interceptor).build();
        retrofit = new Retrofit.Builder() .baseUrl("https://reqres.in") .addConverterFactory(GsonConverterFactory.create()).client(client).build();
        return retrofit;
    }
}
```

* Create a class `APIInterface.java` .

```java
import Stonedcoder.coding-blocks.retrofit.pojo.MultipleResource;
import Stonedcoder.coding-blocks.retrofit.pojo.User;
import Stonedcoder.coding-blocks.retrofit.pojo.UserList;

import retrofit2.Call;
import retrofit2.http.Body;
import retrofit2.http.Field;
import retrofit2.http.FormUrlEncoded;
import retrofit2.http.GET;
import retrofit2.http.POST;
import retrofit2.http.Query;

interface APIInterface {

@GET("/api/unknown")
Call<MultipleResource> doGetListResources();

@POST("/api/users")
Call<User> createUser(@Body User user);

@GET("/api/users?")
Call<UserList> doGetUserList(@Query("page") String page);

@FormUrlEncoded
@POST("/api/users?")
Call<UserList> doCreateUserWithField(@Field("name") String name, @Field("job") String job);
}
```

* In the above class, we’ve defined some methods that perform HTTP requests with annotation.

`@GET("/api/unknown") calls doGetListResources();`

`doGetListResources()` is the method name. MultipleResource.java is a Model POJO class for our response object that’s used to map the response parameters to their respective variables. These POJO class act as the method return type.

* create a new POJO class for `MultipleResources.java`

```java
package Stonedcoder.coding-blocks.retrofit.pojo;
import com.google.gson.annotations.SerializedName;

import java.util.ArrayList;
import java.util.List;

public class MultipleResource {

@SerializedName("page")
public Integer page;
@SerializedName("per_page")
public Integer perPage;
@SerializedName("total")
public Integer total;
@SerializedName("total_pages")
public Integer totalPages;
@SerializedName("data")
public List data = null;

public class Datum {

@SerializedName("id")
public Integer id;
@SerializedName("name")
public String name;
@SerializedName("year")
public Integer year;
@SerializedName("pantone_value")
public String pantoneValue;

}
}
```

`@SerializedName` annotation is used to specify the name of the field that’s in the JSON Response.

To create a POJO class for each response, we can go to [http://www.jsonschema2pojo.org/](http://www.jsonschema2pojo.org/) and paste the json response structure as shown below.

```json
{
"page": 1,
"per_page": 3,
"total_pages": 4,
"data": [
{
"id": 1,
"name": "cerulean",
"year": 2000,
"pantone_value": "19-2031"
},
{
"id": 2,
"name": "fuchsia rose",
"year": 2001,
"pantone_value": "20-2031"
},
{
"id": 3,
"name": "true red",
"year": 2002,
"pantone_value": "21-2031"
}
]
}
```

* The POJO classes are wrapped into a typed Retrofit Call class.

* Method Parameters : There are a wide variety of possible options of parameters to pass inside a method:

```java
@Body – Sends Java objects as request body.
@Url – use dynamic URLs.
@Query – We can simply add a method parameter with @Query() and a query parameter name, describing the type. To URL encode a query use the form:
@Query(value = "auth_token",encoded = true) String auth_token
@Field – send data as form-urlencoded. This requires a @FormUrlEncoded annotation attached with the method.
```

The @Field parameter works only with a POST

* Note: @Field requires a mandatory parameter. In cases when `@Field` is optional, we can use `@Query` instead and pass a null value.

The pojo package defines four model classes for each of the API endpoint responses defined in the `APIInterface.java` class.

* Create a class `User.java`

```java
package Stonedcoder.coding-blocks.retrofit.pojo;
import com.google.gson.annotations.SerializedName;

public class User {

@SerializedName("name")
public String name;
@SerializedName("job")
public String job;
@SerializedName("id")
public String id;
@SerializedName("createdAt")
public String createdAt;

public User(String name, String job) {
this.name = name;
this.job = job;
}
}
```

The above class is used to create the Response Body for the `createUser()` method.

* Create a class `UserList.java`

```java
package Stonedcoder.coding-blocks.retrofit.pojo;
import com.google.gson.annotations.SerializedName;

import java.util.ArrayList;
import java.util.List;

public class UserList {

@SerializedName("page")
public Integer page;
@SerializedName("per_page")
public Integer perPage;
@SerializedName("total")
public Integer total;
@SerializedName("total_pages")
public Integer totalPages;
@SerializedName("data")
public List data = new ArrayList();

public class Datum {

@SerializedName("id")
public Integer id;
@SerializedName("first_name")
public String first_name;
@SerializedName("last_name")
public String last_name;
@SerializedName("avatar")
public String avatar;

}
}
```

* Create `UserResponse.java`

```java
package Stonedcoder.coding-blocks.retrofit.pojo;
import com.google.gson.annotations.SerializedName;

public class CreateUserResponse {

@SerializedName("name")
public String name;
@SerializedName("job")
public String job;
@SerializedName("id")
public String id;
@SerializedName("createdAt")
public String createdAt;
}
```

* The `MainActivity.java` is where we call each of the API endpoints defined in the Interface class and display each of the fields in a Toast/TextView.

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;
import android.widget.Toast;

import Stonedcoder.coding-blocks.retrofit.pojo.CreateUserResponse;
import Stonedcoder.coding-blocks.retrofit.pojo.MultipleResource;
import Stonedcoder.coding-blocks.retrofit.pojo.User;
import Stonedcoder.coding-blocks.retrofit.UserList;

import java.util.List;

import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;

public class MainActivity extends AppCompatActivity {

TextView responseText;
APIInterface apiInterface;

@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
responseText = (TextView) findViewById(R.id.responseText);
apiInterface = APIClient.getClient().create(APIInterface.class);


/**
GET List Resources
**/
Call call = apiInterface.doGetListResources();
call.enqueue(new Callback() {
@Override
public void onResponse(Call call, Response response) {

Log.d("TAG",response.code()+"");

String displayResponse = "";

MultipleResource resource = response.body();
Integer text = resource.page;
Integer total = resource.total;
Integer totalPages = resource.totalPages;
List datumList = resource.data;

displayResponse += text + " Page\n" + total + " Total\n" + totalPages + " Total Pages\n";

for (MultipleResource.Datum datum : datumList) {
displayResponse += datum.id + " " + datum.name + " " + datum.pantoneValue + " " + datum.year + "\n";
}

responseText.setText(displayResponse);

}

@Override
public void onFailure(Call call, Throwable t) {
call.cancel();
}
});

/**
Create new user
**/
User user = new User("morpheus", "leader");
Call call1 = apiInterface.createUser(user);
call1.enqueue(new Callback() {
@Override
public void onResponse(Call call, Response response) {
User user1 = response.body();

Toast.makeText(getApplicationContext(), user1.name + " " + user1.job + " " + user1.id + " " + user1.createdAt, Toast.LENGTH_SHORT).show();

}

@Override
public void onFailure(Call call, Throwable t) {
call.cancel();
}
});

/**
GET List Users
**/
Call call2 = apiInterface.doGetUserList("2");
call2.enqueue(new Callback() {
@Override
public void onResponse(Call call, Response response) {

UserList userList = response.body();
Integer text = userList.page;
Integer total = userList.total;
Integer totalPages = userList.totalPages;
List datumList = userList.data;
Toast.makeText(getApplicationContext(), text + " page\n" + total + " total\n" + totalPages + " totalPages\n", Toast.LENGTH_SHORT).show();

for (UserList.Datum datum : datumList) {
Toast.makeText(getApplicationContext(), "id : " + datum.id + " name: " + datum.first_name + " " + datum.last_name + " avatar: " + datum.avatar, Toast.LENGTH_SHORT).show();
}


}

@Override
public void onFailure(Call call, Throwable t) {
call.cancel();
}
});


/**
POST name and job Url encoded.
**/
Call call3 = apiInterface.doCreateUserWithField("morpheus","leader");
call3.enqueue(new Callback() {
@Override
public void onResponse(Call call, Response response) {
UserList userList = response.body();
Integer text = userList.page;
Integer total = userList.total;
Integer totalPages = userList.totalPages;
List datumList = userList.data;
Toast.makeText(getApplicationContext(), text + " page\n" + total + " total\n" + totalPages + " totalPages\n", Toast.LENGTH_SHORT).show();

for (UserList.Datum datum : datumList) {
Toast.makeText(getApplicationContext(), "id : " + datum.id + " name: " + datum.first_name + " " + datum.last_name + " avatar: " + datum.avatar, Toast.LENGTH_SHORT).show();
}

}

@Override
public void onFailure(Call call, Throwable t) {
call.cancel();
}
});

}
}
```

`apiInterface = APIClient.getClient().create(APIInterface.class);` is used to instantiate the APIClient.  
To map the Model class to the response we use:

`MultipleResource resource = response.body();`

