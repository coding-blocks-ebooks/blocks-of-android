# Google Maps API

The Google Maps Android API includes built-in support for accessibility.

# Setting up the play console

In order to use the Google Maps API, you must register your application on the Google Developer Console and enable the API. To do this, start by going to the Google Developer Console. If you already have a project created, you can skip the next section. If not, you can follow along and create a new project for your maps application.

**Create a new project**

* Once you hit Create, a dialog appears in the lower right corner of the page with a loading indicator while the project is being created.
**Enable the Maps API**

* When the project has been created, or you have selected an existing project, you are taken to the project Overview screen. From here you will want to expand the APIs & auth item in the left navigation panel and click on APIs.

* This takes you to a screen where you can click on the Enable API button in order to enable the Maps API for your project.

**Creating an Android API Key**

* After you've enabled the Maps API, click on the Credentials item under APIs & auth in the side navigation to get a key to access the Maps API in your application. When you are presented with the Credentials dialog, press the blue Add Credentials button and select API Key.

* You need to select Android Key in the next dialog. If you were to create the same application using maps on different platforms, you could create a key for each platform.

* Click Add package name and fingerprint. This provides two fields, one for adding a package name and another for adding the SHA1 from your application signing key.


**SHA-1 Certificate**

* To get the SHA1 signature, you need to open a terminal or command prompt and navigate to the location of your application's signing key. This can be either your release key or debug.keystore.

```keytool -list -v -keystore debug.keystore```

* After you have created your SHA1 key and entered it into the text field, click on the Create button. You are then presented with a dialog containing the API key that you need to add to your Android app to access the Maps API.
