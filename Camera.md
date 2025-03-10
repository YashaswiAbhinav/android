# Camera
Jetpack Compose does not have a built-in Camera API, so we use CameraX, which provides a modern and simple way to access the camera.
### Implementations
```
    implementation("androidx.camera:camera-core:1.3.0")
    implementation("androidx.camera:camera-lifecycle:1.3.0")
    implementation("androidx.camera:camera-camera2:1.3.0")
    implementation("androidx.camera:camera-view:1.3.0")
    implementation("androidx.compose.ui:ui-tooling-preview:1.5.1")
```
### [Permissions Handelling](https://github.com/LUAMICIFER/android/blob/main/Permission.md)
Before using the camera, request the camera permission in AndroidManifest.xml:
```
<uses-permission android:name="android.permission.CAMERA"/>
```

