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
    screena(navController: NavController){
    Button(onClick(navController.navigate(Routes.screenB+"hello john"))){...}
  }
  ```
  ```
   NavHost(navController = navController,startDestination = "Routes.screenA"){
      composable(Routes.screenA) { Screena(navController) }
      composable(Routes.screenB+/"{greeting}") {
        val Greeting = it.arguments?.getString("greeting")
        Screenb(navController,Greeting?:"no Greeting") }
   }
   ```
here the greeting in the composable will serve as the key for finding it from the backstack and we can send it to the screenb
  ```
      screenb(navController: NavController,Greeting :String){
   Button(onClick(navController.popBackStack()){...}
  }
  ```
## Navigation Animation
![NavigationAnimation](NavigationAnimation.gif)
```
    NavHost(navController = navController, startDestination = Routes.permission,
        enterTransition={
        fadeIn(animationSpec = tween(time))+slideIntoContainer(
            AnimatedContentTransitionScope.SlideDirection.Left, tween(time)
        )
                        },
    exitTransition={
        fadeOut(animationSpec = tween(time))+slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Down,
            tween(time)
        )
                   },
    popEnterTransition = {
        fadeIn(animationSpec = tween(time))+slideIntoContainer(
            AnimatedContentTransitionScope.SlideDirection.Right, tween(time)
        )
                        },
    popExitTransition = {
        fadeOut(animationSpec = tween(time))+slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Up,
            tween(time)
        )
    }
        )
```
#### popEnter can accept all the animation of type enterTransition ans popEcit can accept all the Animation of popExit

