
# Ml kit face detection
### implementation 
```  implementation "com.google.mlkit:face-detection:16.1.7" ```
### Options
| Options | attributes available |
|---------|----------------------|
|setPerformanceMode |`PERFORMANCE_MODE_FAST` (default) / `PERFORMANCE_MODE_ACCURATE` <br> Favor speed or accuracy when detecting faces. |
|setLandmarkMode|`LANDMARK_MODE_NONE` (default) / `LANDMARK_MODE_ALL` <br> Whether to attempt to identify facial "landmarks": eyes, ears, nose, cheeks, mouth, and so on.
|setContourMode|`CONTOUR_MODE_NONE` (default) / `CONTOUR_MODE_ALL` <br> Whether to detect the contours(lines) of facial features. Contours are detected for only the most prominent face in an image.
|setClassificationMode|`CLASSIFICATION_MODE_NONE`(default) / `CLASSIFICATION_MODE_ALL` <br> Whether or not to classify faces into categories such as "smiling", and "eyes open".|
|setMinFaceSize|float (default: 0.1f) <br> Sets the smallest desired face size, expressed as the ratio of the width of the head to width of the image.|
|enableTracking|false (default) \ true <br> Whether or not to assign faces an ID, which can be used to track faces across images. <br> Note that when contour detection is enabled, only one face is detected, so face tracking doesn't produce useful results. For this reason, and to improve detection speed, don't enable both contour detection and face tracking.|

### Example of builder
#### remember to put these in LaunchedEffect
```
val options = FaceDetectorOptions.Builder()
    .setPerformanceMode(FaceDetectorOptions.PERFORMANCE_MODE_FAST) // FAST or ACCURATE
    .setLandmarkMode(FaceDetectorOptions.LANDMARK_MODE_ALL) // Detect eyes, nose, etc.
    .setClassificationMode(FaceDetectorOptions.CLASSIFICATION_MODE_ALL) // Smiling, eyes open
    .build()

val detector = FaceDetection.getClient(options)
```
### Image
```
        val inputStream = context.resources.openRawResource(R.raw.mine)
        val bitmap = BitmapFactory.decodeStream(inputStream)
        val image = InputImage.fromBitmap(bitmap, 0)
```
### Example of Extracting Information from the given Face
```
        detector.process(image)
            .addOnSuccessListener { faces ->
                if (faces.isNotEmpty()) {
                    val face = faces.first() // Assuming only one face in the image
                    //you can apply different features in here according to use

                    val leftEyeOpenProb = face.leftEyeOpenProbability ?: 0f
                    val rightEyeOpenProb = face.rightEyeOpenProbability ?: 0f

                    Log.d("FaceDetection", "Left Eye: $leftEyeOpenProb, Right Eye: $rightEyeOpenProb")

                    if (leftEyeOpenProb > 0.5 && rightEyeOpenProb > 0.5) {
                        eyeStatus = "Both eyes are open 👀"
                        Log.d("FaceDetection", eyeStatus)
                    } else {
                        eyeStatus = "One or both eyes are closed 😴"
                        Log.d("FaceDetection", eyeStatus)
                    }
                } else {
                    eyeStatus = "No face detected"
                    Log.d("FaceDetection", eyeStatus)
                }
            }
            .addOnFailureListener { e ->
                eyeStatus = "Face detection failed: ${e.message}"
                Log.e("FaceDetection", "Face detection failed", e)
            }
```
### Different things we can obtain from Face
```
    val bounds = face.boundingBox // for rectangular bound around the face
    val rotY = face.headEulerAngleY // Head is rotated to the right rotY degrees
    val rotZ = face.headEulerAngleZ // Head is tilted sideways rotZ degrees
   // If landmark detection was enabled (mouth, ears, eyes, cheeks, and
    // nose available):
    val leftEar = face.getLandmark(FaceLandmark.LEFT_EAR)
    leftEar?.let {
        val leftEarPos = leftEar.position
    }

    // If contour detection was enabled:
    val leftEyeContour = face.getContour(FaceContour.LEFT_EYE)?.points
    val upperLipBottomContour = face.getContour(FaceContour.UPPER_LIP_BOTTOM)?.points

    // If classification was enabled:
    if (face.smilingProbability != null) {
        val smileProb = face.smilingProbability
    }
    if (face.rightEyeOpenProbability != null) {
        val rightEyeOpenProb = face.rightEyeOpenProbability
    }

    // If face tracking was enabled:
    if (face.trackingId != null) {
        val id = face.trackingId
    }
```
