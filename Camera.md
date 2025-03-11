# Camera
Jetpack Compose does not have a built-in Camera API, so we use CameraX, which provides a modern and simple way to access the camera.

## Implementations
```
    implementation("androidx.camera:camera-core:1.3.0")
    implementation("androidx.camera:camera-lifecycle:1.3.0")
    implementation("androidx.camera:camera-camera2:1.3.0")
    implementation("androidx.camera:camera-view:1.3.0")
    implementation("androidx.compose.ui:ui-tooling-preview:1.5.1")
```
## [Permissions Handelling](Permission.md)
Before using the camera, request the camera permission in AndroidManifest.xml:
```
<uses-permission android:name="android.permission.CAMERA"/>
```
## Five Key Components
1. ### The CameraX(The Stage)
   * Think of CameraX as the overall system that manages your camera. It's like the stage where the camera's performance takes place.
   * It handles the low-level details of interacting with the camera hardware.
2. ### Preview Use Case(The performer)
   * The Preview use case is a specific feature of CameraX.
   * Its sole job is to take the camera's live feed and prepare it for display on the screen.
   * It is the primary actor on the stage.
3. ### PreviewView (The Screen):
    * This is the view on your app's screen where the camera feed will be shown.
    * It's like a TV screen that displays the camera's output.
    * CameraX provides this view to make things easier.
    * It handles the surface, and scaling the preview.
4. ### SurfaceProvider (The Connection):
    * This is the connection between the camera's output and the PreviewView.
    * It provides a "surface" (a place to draw) where the camera's images can be displayed.
    * PreviewView uses a SurfaceProvider internally.
5. ### Lifecycle Owner (The Director):
    * It tells CameraX when to start and stop the camera.
    * It's like the director of the show, controlling when the camera is active.
    * This prevents memory leaks.
## Preview in Camera
Call this function after the permission is granted
```
@Composable
fun CameraPreview() {
    val context = LocalContext.current 
    val lifecycleOwner = LocalLifecycleOwner.current 
    val cameraProviderFuture = remember { ProcessCameraProvider.getInstance(context) }

    AndroidView(
        factory = { ctx -> //context of this Android view
            val previewView = PreviewView(ctx) 

            val executor = ContextCompat.getMainExecutor(ctx)
            previewView.scaleType = PreviewView.ScaleType.FIT_CENTER // Or PreviewView.ScaleType.CENTER_INSIDE
            cameraProviderFuture.addListener({
                val cameraProvider = cameraProviderFuture.get()
                val preview = androidx.camera.core.Preview.Builder().build().also { 
                    it.setSurfaceProvider(previewView.surfaceProvider)
                }

                val cameraSelector = CameraSelector.DEFAULT_FRONT_CAMERA

                cameraProvider.unbindAll()
                cameraProvider.bindToLifecycle(lifecycleOwner, cameraSelector, preview)

            }, executor)

            previewView
        },Modifier.width(150.dp).height(50.dp)
    )
}
```
* Ctx here is like the Context of the AdnroidView
* We can edit the Preview by changing in the
     ```
     val preview = androidx.camera.core.Preview.Builder().build().also { 
                    it.setSurfaceProvider(previewView.surfaceProvider)
                }
      ```
* Options available are
     1. ``` Target Resolution (setTargetResolution(Size size))``` Example: .setTargetResolution(Size(1280, 720))
     2. ``` Target Aspect Ratio (setTargetAspectRatio(int aspectRatio))``` Example: .setTargetAspectRatio(AspectRatio.RATIO_16_9)
     3. ``` Target Rotation (setTargetRotation(int rotation)) ``` Example: .setTargetRotation(Surface.ROTATION_0)
     4. ```  Surface Provider (setSurfaceProvider(SurfaceProvider surfaceProvider)) ``` Example: .also { it.setSurfaceProvider(previewView.surfaceProvider) } *necessary*
* now some advance techniques :-
    1. Session Configuration
    2. View Port
    3. Default Config Modifications
* We can  also use to make changes in the PreviewView
