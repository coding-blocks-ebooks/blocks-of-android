# **Intent**

Intents in Android are used as messaging objects. You will use intents whenever you want to get a task done by another part of the system. These tasks can range from starting another activity to starting a background service to download some data. We haven’t discussed Services and Broadcasts yet, so I will restrict these notes to starting new activities.

Intents can be broadly classified into two categories:

### **Explicit Intents: **

When we specify the exact activity class \(or component\) that should handle the intent. We will use this methodology to launch new activities within the app. For example, in our stocks app, when the user clicks on a company in the list we would like to show a detailed page to user. For this, we can create an explicit intent.

Here is the sample code for that:

```
Intent i =newIntent();
i.setClass(getApplicationContext(), DetailedActivity.class);
startActivity(i);
```

Here in this code we created an intent and set the DetailedActivity class as the class, which should handle the intent. startActivity function is used to pass the intent to Android and start the transition. Instead of using setClass function, you could have used the setComponent, setClassName functions as well. To make this work, you would need to create a DetailedActivity class and add that activity to your manifest.xml file.

### **Implicit Intents:**

Sometimes we don’t care which activity takes care of our intent. We just want some activity to handle the task at hand. For example, if user clicks on a website link in our app, we would like the link to be opened by a browser. We don’t really care which app is used to open the link, as long as it can take care of the task at hand.  
In these cases, we won’t set the class that should handle the intent, instead we tell the task to be done and Android finds all the activities, which can handle it.

Here are some attributes, which we can use to describe a task.

**Action: **This string specifies the generic action type. Action can be View or Send. For example, if you want to open a link in browser it will be **ACTION_VIEW** whereas to send an email you should use **ACTION_SEND**.

**Data: **Data is a Uri type object. It contains the details of the task to be done as well as the MIME \(http://en.wikipedia.org/wiki/MIME\) type of the task. For example, to open a website, we will set the data Uri to be “http://www.google.com”.

Here http:// is the MIME type, which tells Android that activities that can handle http data should be used to complete this task. Also [www.google.com](http://www.google.com) is the data that tells the target activity which website should be opened.

**Category: **This contains additional information about the activity to be launched.  
For example, **CATEGORY_DEFAULT** tells that the activity to be launched should be an activity, which opens in standard full screen mode whereas **CATEGORY_TAB** tells that the activity should be launched as a tab in a TabActivity.

**Type: **This is the MIME type that we have already discussed while talking about data.  
In case you don’t have any data to be passed you can use the setType function and just set the type of intent.

Here are few examples of implicit Intents:

Opening a link:

```
Intent i =newIntent();
i.setAction(Intent.ACTION_VIEW);
Uri a = Uri.parse("http://www.google.com");
i.setData(a);
startActivity(i);
```

Email:

```
Intent i =newIntent (Intent.ACTION_SENDTO);
i.setData(Uri.parse("mailto:ankush@codingblocks.com"));
i.putExtra(Intent.EXTRA_SUBJECT,"subject");
if(i.resolveActivity(getPackageManager()) !=null) {
    startActivity(i);
}
```

In the second example, we first checked if the intent can be resolved by any of the applications installed by the user or not. We start the activity only if intent can be resolved, otherwise our app would have crashed.

### **Receiving Intents \(Intent Filters\): **

As discussed while using implicit intents you can tell Android the kind of task you want to get done and it will find the activities that can take care of the task.  
Now, if we build an activity which can take care of tasks, we would like Android to show our application as one of the options to the user.  
In order to achieve this, we will be using Intent Filters.

Intent Filters are added in the Manifest.xml file and from there Android can find out what kind of tasks can be taken care by which activity of our application.  
For example, if we were building a browser and BrowserActivity is the class which can take open webpages, we can add following code in the manifest file.

```
<activity android:name=".BrowserActivity" android:label="Browser">
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:scheme="http"/>
    </intent-filter>
</activity>
```

Here we are telling Android that we have an activity BrowserActivity that can take care of view action on data with MIME type http.  
Default category is the category used by intents when we don’t mention any other category.  
We can similarly add intent filters for various actions, MIME types and categories.

### **Passing data between activities**

**Caller to callee: **

A lot of times you would like to pass additional data to the target activity and the data can range from the database id of an object or other attributes that can be used by the target activity to show data.  
The Intent class provides a bunch of putExtra functions, which can be used to pass different kind of data.  
For example, in a stocks app we might want to pass the company’s ticker symbol to the detailed activity.

Here is the sample code on caller side:

```java
Intent i =newIntent();
i.setClass(getApplicationContext(), DetailedActivity.class);
i.putExtra("company_name", c.name);
startActivity(i);
```

On the callee side we can use the getExtras function to get an object of class Bundle that contains all the extra data that was passed by the caller.

Here is the sample code on callee side:

```java
Intent i = getIntent();
Bundle b = i.getExtras();
String companyName = b.getString("company_name");
```

**Callee to Caller: **

Not as common as the first case but sometimes we want the target activity to complete a task and return the result to us. For example, we may want to create a new activity where user can add a new company to list of companies. We might want this activity to return the data that user has entered and save the data in the original activity.  
For cases like these, the caller need to use startActivityForResult function instead of the startActivity function.  
Caller also needs to implement onActivityResult method, which gets called once the target activity finishes.

Here are the functions:

```java
startActivityForResult(intent, requestCode);
onActivityResult(int requestCode , int resultCode, Intent data);
```

Here the caller sets the requestCode and the callee sets the resultCode. These codes are used to differentiate between various possible intents that could have finished and to differentiate between different outcomes \(like success or failure\).  
For example, our main activity could have called Activity A in one function and Activity B in some other function and Activity A in third function but this time for some other task. We could use the requestCode to differentiate between these three cases.

On the callee side, you need to set the resultCode and data to be sent back.  
You can add any kind of data to be sent back in intent extras, similar to what we did while sending data from caller to callee.

```java
Intent data =newIntent();
data.putExtras(“extra_data”, “done”);
setResult(resultCode, data);
```

You can add this code to finish\(\) method of activity, which is called every time the activity is about to be finished.
