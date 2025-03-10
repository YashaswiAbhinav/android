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
