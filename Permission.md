# Permissions Handelling
Before using the camera, request the camera permission in AndroidManifest.xml:
```
<uses-permission android:name="android.permission.CAMERA"/>
```
## Requesting permission from code
There are 2 ways for asking permssion 
### 1. Manual (Recommended for hybrid apps where xml and jetpack compoase both are used)
### 2. Accompanist(Recommended for apps made in jetpack compose)
#### Benefits of using Accompanist
* ✅ Easy to use with rememberPermissionState().
* ✅ Handles UI recomposition when permission state changes.
* ✅ Manages single and multiple permissions smoothly.
* ⚠️ Dependency needed (accompanist-permissions).

### Dependency
```
implementation("com.google.accompanist:accompanist-permissions:0.31.1-alpha")
```
### For Single Permission
#### USE ```rememberPermissionState```
```
@OptIn(ExperimentalPermissionsApi::class)
@Composable
fun singlePermission(){
    val camerapermission= rememberPermissionState(Manifest.permission.CAMERA)
    LaunchedEffect(Unit) {
        if(!camerapermission.status.isGranted   ){
            camerapermission.launchPermissionRequest()
        }
    }
    when {
        camerapermission.status.isGranted -> {
            Text("Thanks for allowing the permission")
        }
        camerapermission.status.shouldShowRationale ->{
            Column {
                Text("we needed that permission can you please give that permission so that we can work")
                Button(onClick = {camerapermission.launchPermissionRequest()}) { Text("grant permission") }
            }

        }
        else -> {
            Text("permission Denied please enable from settings")
        }
    }
}
```
### For Multiple Permissions
#### USE ```rememberMultiplePermissionState```
```
@OptIn(ExperimentalPermissionsApi::class)
@Composable
fun Multiplepermission(){
    val permis= rememberMultiplePermissionsState(
        listOf(Manifest.permission.ACCESS_FINE_LOCATION,Manifest.permission.CAMERA))
    LaunchedEffect (Unit){
        permis.launchMultiplePermissionRequest()
    }
    Column {
        if (permis.permissions.all { it.status.isGranted }) {
            Text("✅ Thanks for accepting all the permissions")
        } else {
            Text("❌ Some permissions are denied")
        }
        permis.permissions.forEach{ prem ->
            when (prem.status){
                is PermissionStatus.Granted -> Text("$prem thanks for allowing this permsiion")
                is PermissionStatus.Denied -> Text("$prem permission is denied")
            }

        }
    }

}
```
### When the Permission is denied
#### We have to open the setting and let the user give the permission manually
```
@Composable
fun OpenSettingsScreen() {
    val context = LocalContext.current
    val cameraPermissionState = rememberPermissionState(Manifest.permission.CAMERA)

    Column {
        if (cameraPermissionState.status.isGranted) {
            Text("✅ Camera Permission Granted")
        } else {
            Text("❌ Camera Permission Denied")
            Button(onClick = {
                if (cameraPermissionState.status.shouldShowRationale) {
                    cameraPermissionState.launchPermissionRequest()
                } else {
                    val intent = Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS).apply {
                        data = Uri.fromParts("package", context.packageName, null)
                    }
                    context.startActivity(intent)
                }
            }) {
                Text("Grant Permission")
            }
        }
    }
}
```
