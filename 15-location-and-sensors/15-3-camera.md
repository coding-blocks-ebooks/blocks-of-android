# **Camera**

The functioning of camera is a bit different as compared to other sensors on the device and it has a special set of APIs designed to utilize its functionalities.

In this lesson, we will use the camera app or the internal memory store of images to get an image and display it in an ImageView.

## Steps to be followed : 

* Create a Layout with a button and an imageView

* Add the relevant storage Permissions that allow us to store the image file onto the local storage:

```
<uses-permission android:name= "android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name= "android.permission.WRITE_EXTERNAL_STORAGE" />
```

* Create a function to display a dialog on button click and perform the required tasks:

```
private void selectImage() {
    finalCharSequence[] items = {"Take Photo","Choose from Library","Cancel"};
    AlertDialog.Builder builder =newAlertDialog.Builder(CameraPhoto.this);
    builder.setTitle("Add Photo!");
    builder.setItems(items,newDialogInterface.OnClickListener() {
        @Override
        public voidonClick(DialogInterface dialog,intitem) {
            if(items[item].equals("Take Photo")) {
                Intent intent =newIntent(MediaStore.ACTION_IMAGE_CAPTURE);
                intent.putExtra(MediaStore.EXTRA_OUTPUT, getPhotoFileUri(photoFileName));
                // set the image file name
                    if(intent.resolveActivity(getPackageManager())!=null) {
                        startActivityForResult(intent,CAPTURE_IMAGE_ACTIVITY_REQUEST_CODE);
                    }
                }else if(items[item].equals("Choose from Library")) {
                    Intent intent =newIntent(Intent.ACTION_PICK, android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
                    intent.setType("image/*");
                    if(intent.resolveActivity(getPackageManager())!=null) {
                    // Bring up gallery to select a photo
                        startActivityForResult(intent,SELECT_FILE);
                }else if(items[item].equals("Cancel")) {
                    dialog.dismiss();
                }
            }
        });
    builder.show();
}
```

* Required Constants:

```
public final String APP_TAG = "MyCustomApp";
public final static int  C APTURE_IMAGE_ACTIVITY_REQUEST_CODE = 1034;
public final static int SELECT_FILE = 1011;
public String photoFileName = "photo.jpg";
```

* Required Extra Functions for Camera App, used to get the Uri of the stored image and to check if external storage is available or not:

    public Uri getPhotoFileUri(String fileName){
    // Only continue if the SD Card is mounted

        if(isExternalStorageAvailable()){

            /* Get safe storage directory for photos
        Use `getExternalFilesDir` on Context to access package-specific directories.
        This way, we don't need to request external read/write runtime permissions.*/

            File mediaStorageDir = new File(getExternalFilesDir(Environment.DIRECTORY_PICTURES) , APP_TAG);

            // Create the storage directory if it does not exist

            if(!mediaStorageDir.exists() && !mediaStorageDir.mkdirs()){
                Log.d(APP_TAG,"failed to create directory");
            }

            // Return file target for the photo based on filename

            returnUri.fromFile(newFile(mediaStorageDir.getPath()+File.separator+fileName));
        }

    return null;
    }

    // Returns true if external storage for photos is available

    private boolean isExternalStorageAvailable(){
        String state = Environment.getExternalStorageState();
    return state.equals(Environment.MEDIA_MOUNTED);
    }

* Implement onActivityResult for both cases and display the thumbnail in the imageView:

```
@Override
public void onActivityResult(int requestCode , int resultCode, Intent data) {
    if(requestCode == CAPTURE_IMAGE_ACTIVITY_REQUEST_CODE) {
        if(resultCode == RESULT_OK) {
            Uri takenPhotoUri = getPhotoFileUri(photoFileName);
            // by this point we have the camera photo on disk
            Bitmap takenImage = BitmapFactory.decodeFile(takenPhotoUri.getPath());
            // Load the taken image into a preview
            ImageView ivPreview = (ImageView) findViewById(R.id.display_image);
            ivPreview.setImageBitmap(takenImage);
        }else{// Result was a failure
            Toast.makeText(this,"Picture wasn't taken!", Toast.LENGTH_SHORT).show();
        }
    }else if(requestCode ==SELECT_FILE){
        if(resultCode ==RESULT_OK){
            if(data !=null) {
                Uri photoUri = data.getData();
                // Do something with the photo based on Uri
                Bitmap selectedImage =null;
                try{
                    selectedImage = MediaStore.Images.Media.getBitmap(this.getContentResolver(), photoUri);
                }catch(IOException e) {
                    e.printStackTrace();
                }
                // Load the selected image into a preview
                ImageView ivPreview = (ImageView) findViewById(R.id.display_image);
                ivPreview.setImageBitmap(selectedImage);
            }
        }
    }
}
```



