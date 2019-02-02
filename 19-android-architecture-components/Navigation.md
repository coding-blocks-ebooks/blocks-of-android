# Navigation Architecture Component
The Navigation Architecture Component helps you implement common, but complex navigation requirements in your app, enabling you to more easily provide consistent and predictable experiences for your users.

Navigation handles navigating between your app's destinations--that is, anywhere in your app to which users can navigate. While destinations are usually Fragments representing specific screens, the Navigation Architecture Component supports the other destination types listed below:

* Activities
* Navigation graphs and subgraphs - when the destination is a graph or subgraph, users navigate to the start destination of that graph or subgraph
* Custom destination types

These destinations are connected via actions, and a set of destinations and connecting actions comprise an app's navigation graph.
The Navigation Architecture Component provides a number of other benefits, including the following:

* Handling Fragment transactions
* Handling Up and Back actions correctly by default
* Providing standardized resources for animations and transitions
* Treating deep linking as a first-class operation
* Including Navigation UI patterns, such as navigation drawers and bottom navigation, with minimal additional work

The Navigation Architecture Component is based on the following design principles:
* Fixed start destination
* Navigation state should be represented via a stack of destinations
* The Up button never exits your app
* Up and Back are identical within your app's task
* Deep linking and navigating to a destination should yield the same stack

#### Implement Navigation for your project:
To include Navigation support in your project, add the following to your app's build.gradle file.
``` gradle
dependencies {
    def nav_version = "1.0.0-alpha11"

    implementation "android.arch.navigation:navigation-fragment:$nav_version" // use -ktx for Kotlin
    implementation "android.arch.navigation:navigation-ui:$nav_version" // use -ktx for Kotlin
}
```