# CAMERA (basics)

You want to take photos but do not want to reinvent the camera. Luckily, most Android-powered devices already have at least one camera application installed.Android provides full access to the device camera hardware so you can build a wide range of camera or vision-based apps. Or if you just need a way for the user to capture a photo, you can simply request an existing camera app to capture a photo and return it to you. This lesson teaches how to capture a photo by using another camera app on the device 


Request the camera feature:
To tell that your application depends on having a camera, put a <uses-feature> tag in your manifest file:

```xml
<manifest ... >
    <uses-feature android:name="android.hardware.camera"
                  android:required="true" />
    ...
</manifest>
```

Take a photo with a camera app:
The Android way of delegating actions to other applications is to invoke an Intent that describes what you want to be done. This process involves three pieces: The Intent itself, a call to start the external Activity, and some code to handle the image data when focus returns to your activity.
Here's a function that invokes an intent to capture a photo:

```kotlin
val REQUEST_IMAGE_CAPTURE = 1
private fun dispatchTakePictureIntent() {
    Intent(MediaStore.ACTION_IMAGE_CAPTURE).also { takePictureIntent ->
        takePictureIntent.resolveActivity(packageManager)?.also {
            startActivityForResult(takePictureIntent, REQUEST_IMAGE_CAPTURE)
        }
    }
}
```

Add the photo to a gallery:
When you create a photo through an intent, you should know where your image is located, because you said where to save it in the first place. For everyone else, perhaps the easiest way to make your photo accessible is to make it accessible from the system's Media Provider.
The following example method demonstrates how to invoke the system's media scanner to add your photo to the Media Provider's database, making it available in the Android Gallery application and to other apps.

private fun galleryAddPic() {
    Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE).also { mediaScanIntent ->
        val f = File(mCurrentPhotoPath)
        mediaScanIntent.data = Uri.fromFile(f)
        sendBroadcast(mediaScanIntent)
    }
}
