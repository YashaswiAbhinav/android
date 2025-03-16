# Navigation
```
    val nav_version = "2.8.9"

    implementation("androidx.navigation:navigation-compose:$nav_version")
```
## 1. We need to create the navController ` val navController = rememberNavController() `
## 2. Now create a singleton Object for the Routes 
    ```
    object Routes {
    var screenA = "Screen_a"
    var screenB = "Screen_b"
    }
    ```
## 3. for hosting the navigation we have NavHost
  ### - Example without passing arguments 
  ```
   NavHost(navController = navController,startDestination = "Routes.screenA"){
      composable(Routes.screenA) { Screena(navController) }
      composable(Routes.screenB) { ScreenB(navController) }
   }
   ```
  ```
  screena(navController: NavController){
    Button(onClick(navController.navigate(Routes.screenB))){...}
  }
  ```
  ```
  screenb(navController: NavController){
   Button(onClick(navController.popBackStack()){...}
  }
  ```
  ### - Example with Arguments
  suppose we have to pass data from screena to screenb
  ```
