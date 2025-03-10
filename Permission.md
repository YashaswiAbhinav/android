# Permissions Handelling
Before using the camera, request the camera permission in AndroidManifest.xml:
```
<uses-permission android:name="android.permission.CAMERA"/>
```
### Requesting permission from code
 ```
       val context = LocalContext.current
        if (ContextCompat.checkSelfPermission(
                context,
                Manifest.permission.CAMERA
            ) == PackageManager.PERMISSION_GRANTED
        ) {
              // code goes here if permission is granted
          }
       else {
            ActivityCompat.requestPermissions(
                context as MainActivity,
                arrayOf(Manifest.permission.CAMERA),
                101
            )
        }
```
