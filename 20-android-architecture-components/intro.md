# Architecture Components

This section is all about building the apps in a better and an easier way. 
Architecture components were launched in November 2017 with a prime motive of removing the difficulties faced by the developers in the common use cases scenarios which had been making their life tough since forever. Basically, architecture components are the fundamental components on the top of which, application can be built in an easier way. With the architecture components, the problems like difficulty in sharing of data between fragments and activities, loss of data due to orientation changes and other such problems are being handled.

The architecture components have been brought in keeping the folowing points in mind: 
* <b> Solving the right problems </b>
  To solve the problems that are faced by all the developers or things that are considered hard like the activity lifecycle for starters because if it is implemented incorrectly then nothing in the activity can work right. So, the need was observed and solutions to problems like these and other similar ones were developed to make the proces of development easier and enjoyable.
* <b> Playing well with others </b>
  Developers have too much investment in their code, hence it won't be practical to ask everybody to start afresh. Therefore the changes were made keeping in mind that they can be incorporated with the existing code with only a little bit of effort.
* <b> App needs to scale </b>
 The app must be work properly amidst the massive complexities of reality, it should not be like that it is working well on the emulator under the supervision of the developer but crashes when it goes out to the real people in the industry.
* <b> Reach </b>
To minimize the use of APIs by increasing the capabilities of support libraries.

Therefore, in simple words architecture components are a set of libaries that help in designing robust, testable, and maintainable apps. The components are as follows:
- <b> Lifecycle, LifecycleOwner and LifecycleObsever </b>
This components automatically responds to the lifecycle events, thereby solving most of the lifecycle related issues.
- <b> View Model </b>
This allows data to survive configuration changes because the data that is stored locally is unaffected by the UI of the app. Therefore, change in UI components when the app is rotated do not affect the data stored.
- <b> Room </b>
It is an object mapping library for SQLite.
- <b> Live Data </b>
They are the data objects that notify the views whenever the related database is changed.

All these components will be discussed in detail in the coming sections.

## Adding Architecture Components

Android Studio projects aren't configured to access this repository by default.

To add it to your project, open the build.gradle file for the project and add the google() repository as shown below:
```
allprojects {
    repositories {
        google()
        jcenter()
    }
}
```

In the build.gradle file of your app, add the following dependecies:

For Lifecycles, LiveData and ViewModel:
```
compile “android.arch.lifecycle:runtime:1.0.0-alpha9”
compile “android.arch.lifecycle:extensions:1.0.0-alpha9”
annotationProcessor “android.arch.lifecycle:compiler:1.0.0-alpha9”
```
For Room:
```
compile “android.arch.persistence.room:runtime:1.0.0-alpha9”
annotationProcessor “android.arch.persistence.room:compiler:1.0.0-alpha9”
```






  


