# CAMERA(advanced) 
## BUILD A CAMERA APP

In this lesson, we discuss how to control the camera hardware directly using the framework APIs.
Directly controlling a device camera requires a lot more code than requesting pictures or videos from existing camera applications. In this lesson, we will see how to build a specialized camera application or something fully integrated in your app UI.


### Open the Camera Object:
Getting an instance of the Camera object is the first step in the process of directly controlling the camera. As Android's own Camera application does, the recommended way to access the camera is to open Camera on a separate thread that's launched from onCreate(). This approach is a good idea since it can take a while and might bog down the UI thread. In a more basic implementation, opening the camera can be deferred to the onResume() method to facilitate code reuse and keep the flow of control simple. Calling Camera.open() throws an exception if the camera is already in use by another application, so we wrap it in a try block.

``` kotlin
private fun safeCameraOpen(id: Int): Boolean {
    return try {
        releaseCameraAndPreview()
        mCamera = Camera.open(id)
        true
    } catch (e: Exception) {
        Log.e(getString(R.string.app_name), "failed to open Camera")
        e.printStackTrace()
        false
    }
}
private fun releaseCameraAndPreview() {
    mPreview?.setCamera(null)
    mCamera?.also { camera ->
        camera.release()
        mCamera = null
    }
}
```


### Create the Camera Preview:
Taking a picture usually requires that your users see a preview of their subject before clicking the shutter. To do so, we will use a SurfaceView to draw previews of what the camera sensor is picking up.The preview requires an implementation of the android.view.SurfaceHolder.Callback interface, which is used to pass image data from the camera hardware to the application.

``` kotlin
class Preview(
        context: Context,
        val mSurfaceView: SurfaceView = SurfaceView(context)
) : ViewGroup(context), SurfaceHolder.Callback {

    var mHolder: SurfaceHolder = mSurfaceView.holder.apply {
        addCallback(this@Preview)
        setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS)
    }
    ...
}
```
### Set and Start the Preview:
A camera instance and its related preview must be created in a specific order, with the camera object being first. The process of initializing the camera is encapsulated so that Camera.startPreview() is called by the setCamera() method, whenever the user does something to change the camera. The preview must also be restarted in the preview class surfaceChanged() callback method.
``` kotlin
fun setCamera(camera: Camera?) {
    if (mCamera == camera) {
        return
    }
    stopPreviewAndFreeCamera()
    mCamera = camera
    mCamera?.apply {
        mSupportedPreviewSizes = parameters.supportedPreviewSizes
        requestLayout()
        try {
            setPreviewDisplay(mHolder)
        } catch (e: IOException) {
            e.printStackTrace()
        }
        // Important: Call startPreview() to start updating the preview
        // surface. Preview must be started before you can take a picture.
        startPreview()
    }
}
```

### Take a Picture:
Use the Camera.takePicture() method to take a picture once the preview is started. You can create Camera.PictureCallback and Camera.ShutterCallback objects and pass them into Camera.takePicture(). If you want to grab images continuously, you can create a Camera.PreviewCallback that implements onPreviewFrame(). For something in between, you can capture only selected preview frames, or set up a delayed action to call takePicture().


### Stop the Preview and Release the Camera:
Once your application is done using the camera, it's time to clean up. In particular, you must release the Camera object, or you risk crashing other applications, including new instances of your own application. Having your preview surface destroyed is a pretty good hint that it’s time to stop the preview and release the camera.
``` kotlin
override fun surfaceDestroyed(holder: SurfaceHolder) {
    // Surface will be destroyed when we return, so stop the preview.
    // Call stopPreview() to stop updating the preview surface.
    mCamera?.stopPreview()
}
/**
 * When this function returns, mCamera will be null.
 */
private fun stopPreviewAndFreeCamera() {
    mCamera?.apply {
        // Call stopPreview() to stop updating the preview surface.
        stopPreview()
        // Important: Call release() to release the camera for use by other
        // applications. Applications should release the camera immediately
        // during onPause() and re-open() it during onResume()).
        release()
    mCamera = null
    }
}
```

## Some useful links:
[a link] (https://github.com/CameraKit/camerakit-android)
[a link] (https://github.com/natario1/CameraView)