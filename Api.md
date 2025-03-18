# API
## Dependencies
```
dependencies {
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
}
```
## 1. Create a Data Class of you Single Object you are returning
```
data class Post(
    val userId: Int,
    val id: Int,
    val title: String,
    val body: String
)
```
## 2. import retrofit2.Retrofit 
```
import retrofit2.converter.gson.GsonConverterFactory
import retrofit2.http.GET

interface ApiService {
    @GET("posts/1")  // Example API
    suspend fun getPost(): Post
}

object RetrofitInstance {
    val api: ApiService by lazy {
        Retrofit.Builder()
            .baseUrl("https://jsonplaceholder.typicode.com/")
            .addConverterFactory(GsonConverterFactory.create())
            .build()
            .create(ApiService::class.java)
    }
}
```
## 3. In the `@GET` we pass the endpoints
## 4. Fetching the Data from the internet
```
    val scope = rememberCoroutineScope()
    var post by remember { mutableStateOf<Post?>(null) }
    var error by remember { mutableStateOf<String?>(null) }

    LaunchedEffect(Unit) {
        scope.launch {
            try {
                post = RetrofitInstance.api.getPost()
            } catch (e: Exception) {
                error = e.message
            }
        }
    }
```
