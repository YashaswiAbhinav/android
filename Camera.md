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
## Analysis in CameraX
All the steps are almost simmilar as in the previous step just instead of `val preview` we will build `val imageAnalysis` and in the binding step we will send imageAnalysis instead of preview
### Key changes 
#### 1. ``` ImageAnalysis ``` Builder:
* `ImageAnalysis.Builder() ` is used to create the ImageAnalysis use case.
* `setBackpressureStrategy(ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST)` is used to handle backpressure.
* `setAnalyzer(...)` sets the analyzer that will process the frames.
#### 2. ` ImageAnalysis.Analyzer ` :
* The ImageAnalysis.Analyzer is implemented as a lambda expression.
* Inside the analyze() function, you can access the ImageProxy to process the image data.
* imageProxy.close() is crucial to release resources.
#### 3. Binding ImageAnalysis:
* imageAnalysis is added to the `cameraProvider.bindToLifecycle()` call, along with the preview use case.

### In simplified terms:

* "Create the analyzer": You create the instructions on what to do with each camera frame.
* "Tell CameraX to use the analyzer": You tell CameraX to run those instructions and when to start and stop.

```

@Composable
fun CameraAnalysis() {
    val context = LocalContext.current
    val lifecycleOwner = LocalLifecycleOwner.current
    val cameraProviderFuture = remember { ProcessCameraProvider.getInstance(context) }
    AndroidView(
        factory = { ctx ->
            val previewView = PreviewView(ctx)
            val executor = ContextCompat.getMainExecutor(ctx)
            previewView.scaleType = PreviewView.ScaleType.FIT_CENTER
            cameraProviderFuture.addListener({
                val cameraProvider = cameraProviderFuture.get()
                val imageAnalysis = ImageAnalysis.Builder()
                    .setBackpressureStrategy(ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST)
                    .build()
                    .also {
                        it.setAnalyzer(Executors.newSingleThreadExecutor(), ImageAnalysis.Analyzer { imageProxy ->
                            // Process the imageProxy here
                            val rotationDegrees = imageProxy.imageInfo.rotationDegrees
                            // Access image data: imageProxy.image, imageProxy.planes, etc.
                            // Perform your image analysis.
                            Log.d("ImageAnalysis", "Frame processed, rotation: $rotationDegrees")
                            imageProxy.close() // Important: Close the ImageProxy when done
                        })
                    }

                val cameraSelector = CameraSelector.DEFAULT_BACK_CAMERA

                cameraProvider.unbindAll()
                cameraProvider.bindToLifecycle(lifecycleOwner, cameraSelector, imageAnalysis) // Bind ImageAnalysis

            }, executor)

            previewView
        },
        Modifier.fillMaxSize()
    )
}
```
### changes we can make 
#### 1. Backpressure Strategy (setBackpressureStrategy()): This determines how CameraX handles situations where the analyzer can't keep up with the incoming frame rate.
Options:
* ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST: CameraX will discard older frames if the analyzer is busy, ensuring that the analyzer always receives the most recent frame. This is often the best choice for real-time analysis.
* ImageAnalysis.STRATEGY_BLOCK_PRODUCER: CameraX will block the camera from producing new frames until the analyzer has finished processing the current frame. This can lead to frame drops if the analyzer is slow.
Example :
```
ImageAnalysis.Builder()
    .setBackpressureStrategy(ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST)
    // ...
    .build()
```
#### 2. Analyzer (setAnalyzer()):This is where you define the logic for processing the image frames.
Parameters:

* An <strong> Executor </strong> to run the analyzer on a background thread.
* An `ImageAnalysis.Analyzer` interface implementation (usually a lambda).
Inside the Analyzer:You receive an ImageProxy object, which provides access to the image data.
Accessing Image Data:
    * imageProxy.image: Access the Image object (YUV format).
    * imageProxy.planes: Access the image planes (Y, U, V components).
    * imageProxy.width, imageProxy.height: Access the image dimensions.
    * imageProxy.imageInfo.rotationDegrees: Access the rotation of the image.
Processing Logic:
* Implement your image analysis algorithm here (e.g., object detection, barcode scanning).
* imageProxy.close(): Always call this when you're finished processing the image.
Example:
```
.also {
    it.setAnalyzer(Executors.newSingleThreadExecutor(), ImageAnalysis.Analyzer { imageProxy ->
        // Your analysis logic here
        val rotationDegrees = imageProxy.imageInfo.rotationDegrees
        Log.d("ImageAnalysis", "Frame processed, rotation: $rotationDegrees")
        imageProxy.close()
    })
}
```
#### 3. Target Resolution (setTargetResolution()):You can set the desired resolution of the image frames that are passed to the analyzer.
This can be useful for optimizing performance or for specific analysis requirements.
Example:
```
ImageAnalysis.Builder()
    .setTargetResolution(android.util.Size(640, 480))
    // ...
    .build()
```
#### 4. Target Aspect Ratio (setTargetAspectRatio()):You can set the desired aspect ratio of the image frames.
Example:
```
ImageAnalysis.Builder()
    .setTargetAspectRatio(AspectRatio.RATIO_4_3)
    // ...
    .build()
```
#### 5. Target Rotation (setTargetRotation()):Sets the target rotation of the analyzed images.
Example:
```
ImageAnalysis.Builder()
    .setTargetRotation(Surface.ROTATION_90)
    // ...
    .build()
```
#### 6. Image Queue Depth (setImageQueueDepth()):Sets the number of images that can be queued for analysis.
Example:
```
ImageAnalysis.Builder()
    .setImageQueueDepth(3)
    // ...
    .build()
```
#### 7. Setting the Image Reader Mode (setOutputImageFormat()):Sets the output image format. The default is OUTPUT_IMAGE_FORMAT_YUV_420_888.
Example:
```
ImageAnalysis.Builder()
    .setOutputImageFormat(ImageAnalysis.OUTPUT_IMAGE_FORMAT_RGBA_8888)
    // ...
    .build()
```
