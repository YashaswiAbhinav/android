# Firebase
## Set-up your app first 
1. Go to [firebase console](https://console.firebase.google.com/u/0/)
2. create new project![image](https://github.com/user-attachments/assets/e9286dae-154a-4913-af10-633e68faeba0)
3. give any name to your project ![image](https://github.com/user-attachments/assets/65b227a2-2be0-45c6-bcf9-87def22fefbd)
4. press on the android icon ![image](https://github.com/user-attachments/assets/7880877b-be3e-4170-b2db-a2af26d4e720)
5. you'll get the name here ![image](https://github.com/user-attachments/assets/f72fd947-ba21-40cd-b787-d213a3db61f5)
6. Download the file you get ![image](https://github.com/user-attachments/assets/9d448451-bcbf-4d1e-ac2f-13e63f86a61b)
7. Switch to project ![image](https://github.com/user-attachments/assets/8a0aaf90-3dc5-4326-9930-d808c27c29f0)
8. Put the downlaoded file in the app folder ![image](https://github.com/user-attachments/assets/1613055d-b22b-44d9-a4c6-e81e71d41a3f)
9. go back to the android and open ![image](https://github.com/user-attachments/assets/1185efdf-bc6d-439d-b381-8c59c842a58b)
10. paste `id("com.google.gms.google-services") version "4.4.2" apply false`
11. Now in the app level gradle.kts ![image](https://github.com/user-attachments/assets/a34952b5-dcba-4f74-b2a4-5dd88d574bed) put this `id("com.google.gms.google-services")`
12. add the implementations in the dependencies
```
implementation(platform("com.google.firebaseLfirebase-bom:33.6.8"))
```
13. now add implementation accordingly whatever you are using for database you have a different one and for firestore you have a different one

## As the firebase setup is done now we can move on to the different features that fireabse provides
#### [1. Authentication – Login with Email, Google, or Phone.](https://github.com/LUAMICIFER/android/blob/main/firebase.md#1-authentication)
#### 2. Firestore Database – Store and retrieve structured data.
#### 3. Realtime Database – Sync data in real time.
#### 4. Cloud Storage – Store files (images, videos, etc.).
#### 5. Firebase Messaging – Push notifications.
### 1. Authentication 
```
dependencies {
    implementation("com.google.firebase:firebase-auth-ktx")
}
```
#### Step 1: Enable Google Sign-In in Firebase
Open Firebase Console → Authentication → Sign-in method → Add new provider.
Enable Google and save changes.
#### Step 2: Add Dependencies
```
//    firebase auth
    implementation("com.google.firebase:firebase-auth-ktx:22.3.0")
```
#### Step 3: Setup Navhost in MainActivity
```
  EyeCAdminTheme {
                val navController = rememberNavController()
                NavHost(navController = navController, startDestination = "login") {
                    composable("login") { login(navController) }
                    composable("sign-up") { SignUp(navController) }
                    composable("home") { home() }
                }
            }
```
#### Step 4: Login page
```
@Composable
fun login(navController: NavController) {
    val auth: FirebaseAuth = FirebaseAuth.getInstance()

    var email by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    Column(
        Modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text("Login Page", fontSize = 32.sp)
        Spacer(Modifier.height(16.dp))

        OutlinedTextField(value = email, onValueChange = { email = it }, label = { Text("Email") })
        Spacer(Modifier.height(8.dp))

        OutlinedTextField(value = password, onValueChange = { password = it }, label = { Text("Password") })
        Spacer(Modifier.height(16.dp))

        Button(onClick = {
            if (email.isNotEmpty() && password.isNotEmpty()) {
                signInUser(auth, email, password) { success ->
                    if (success) {
                        navController.navigate("home")
                    } else {
                        errorMessage = "Login failed. Check your email and password."
                    }
                }
            } else {
                errorMessage = "Fields cannot be empty."
            }
        }) {
            Text("Login")
        }

        Spacer(Modifier.height(8.dp))
        errorMessage?.let { Text(it, color = Color.Red) }
        TextButton(onClick = { navController.navigate("sign-up") }) { Text("Create an account (Sign-Up)") }
    }
}

fun signInUser(auth: FirebaseAuth, email: String, password: String, onResult: (Boolean) -> Unit) {
    auth.signInWithEmailAndPassword(email, password).addOnCompleteListener { task ->
        onResult(task.isSuccessful)
    }
}
```

` val auth: FirebaseAuth = FirebaseAuth.getInstance()` we first create an instance of the firebase auth 
#### Step 5: Sign-up Page
```
@Composable
fun SignUp(navController: NavController) {
    val auth: FirebaseAuth = FirebaseAuth.getInstance()

    var email by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }
    var passkey by remember { mutableStateOf("") }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    Column(Modifier.fillMaxSize(),horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.Center) {
        Text("Sign-Up Page", fontSize = 32.sp)
        Spacer(Modifier.height(16.dp))

        OutlinedTextField(value = email, onValueChange = { email = it }, label = { Text("Email") })
        Spacer(Modifier.height(8.dp))

        OutlinedTextField(value = password, onValueChange = { password = it }, label = { Text("Password") })
        Spacer(Modifier.height(8.dp))

        OutlinedTextField(value = passkey, onValueChange = { passkey = it }, label = { Text("Passkey") })
        Spacer(Modifier.height(16.dp))

        Button(onClick = {
            if (email.isNotEmpty() && password.isNotEmpty() && passkey == "Ke-Shav") {
                createUser(auth, email, password) { success ->
                    if (success) {
                        navController.navigate("home")
                    } else {
                        errorMessage = "Sign-Up failed. Try again."
                    }
                }
            } else {
                errorMessage = "Fields cannot be empty or passkey incorrect."
            }
        }) {
            Text("Sign-Up")
        }

        Spacer(Modifier.height(8.dp))
        errorMessage?.let { Text(it, color = Color.Red) }
        TextButton(onClick = { navController.navigate("login") }) { Text("Already have an account? Login") }
    }
}

fun createUser(auth: FirebaseAuth, email: String, password: String, onResult: (Boolean) -> Unit) {
    auth.createUserWithEmailAndPassword(email, password).addOnCompleteListener { task ->
        if (task.isSuccessful) {
            Log.d("SignUp", "User registered successfully")
        } else {
            Log.e("SignUp", "Error: ${task.exception?.message}")
        }
        onResult(task.isSuccessful)

    }
}
```





