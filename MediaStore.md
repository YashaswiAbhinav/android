# MediaStore
## MediaType enum class(optional)
```
enum class MediaType{
    IMAGE,
    VIDEO,
    AUDIO
}
```
## Create a data class
```
data class MediaFile(
    val uri : Uri,
    val name : String,
    val Type: MediaType 
)
```
### Media Store is an API that helps to access the External Storage. It does not contain all the data instead it Stores the metadata of all the files. or we can call it a Content Provider

# Main Reader Function
```
fun getAllFiles(context: Context): List<MediaFile>{ //MediaFile datatype hai
    val mediaFiles =  mutableListOf<MediaFile>()
    val queryUri = if(Build.VERSION.SDK_INT>=29){
        MediaStore.Video.Media.getContentUri(MediaStore.VOLUME_EXTERNAL)//specific video ke liye ho gaya hai
    }else MediaStore.Video.Media.EXTERNAL_CONTENT_URI  // we have just created and uri for the query we will give to the system below
    val projection = arrayOf(
        MediaStore.Video.Media._ID,    // yahan pr MediaStore.Video kar denge to videos ka projection banega warna sara files ka hoga
        MediaStore.Video.Media.DISPLAY_NAME,
        MediaStore.Video.Media.DURATION
    ) // we have created projecion for the query
    val sortOrder = "${MediaStore.Video.Media.DATE_MODIFIED} DESC"

    context.contentResolver.query(queryUri,projection,null,null,sortOrder)?.use{cursor->
        val idColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media._ID)
        val nameColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DISPLAY_NAME)
        val durationColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DURATION)
        while (cursor.moveToNext()){
            val id = cursor.getLong(idColumn)
            val name = cursor.getString(nameColumn)
            val duration = cursor.getLong(durationColumn)
                val contentUri  = ContentUris.withAppendedId(queryUri,id)
                mediaFiles.add(
                    MediaFile(
                        uri = contentUri,
                        name = name,
                        duration = duration,
                    )
                )


        }
    }
    return mediaFiles.toList()
}
```
Steps involved : 
1. As we have to return the list of custom data class we've created a `val mediaFiles =  mutableListOf<MediaFile>()`
2. now we have to call this query to get the data `context.contentResolver.query(Uri,projection,selection,selecetionArgument,sortOrder)?.use{}`
3. only the Uri part is mandatory rest of it can be null 
4. As we dont have Uri so we can create by
    ```
    val queryUri = if(Build.VERSION.SDK_INT>=29){
        MediaStore.Files.getContentUri(MediaStore.VOLUME_EXTERNAL) if APi is above 29
    }else MediaStore.Files.getContentUri("external")  for Api Below that
   ```
5. Now Projection is the array of data we want to extract
    ```
   val projection = arrayOf(
        MediaStore.Files.FileColumns._ID,    // yahan pr MediaStore.Video kar denge to videos ka projection banega warna sara files ka hoga
        MediaStore.Files.FileColumns.DISPLAY_NAME,
        MediaStore.Files.FileColumns.MIME_TYPE,
           {...} //you can add multiple types of data here that you want to retrieve
    )
    ```
6. Call the query
   ```
   context.ContextResolver.query(Uri,projection,null,null,null)?.use{cursor -> // it will iterate to all the rows and . it is typically given in database operations 
        val idColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media._ID)
        val nameColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DISPLAY_NAME)
        val mimeTypeColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.MIME_TYPE)
       while(cursor.moveToNext(){
            val id = cursor.getLong(idColumn)
            val name = cursor.getString(nameColumn)
            val mimeType = cursor.getString(mimeTypeColumn)
            //optional Step
           if(name != null && mimeType!= null){ //systemfiles ko filter karne ke liye
               //Step 7
           } 
       }
   }.
7. Adding in the mediaFiles List
    ```
    if({...}){
    mediaFiles.add(
        uri = Step 8,
        name = name,
        type =Step 8 
    )
        }
    ```
8. Uri is created by queryUri(it is like parent path) appending with the id `val contentUri = ContentUris.withAppendedId(queryUri,id)` `val mediaType = when{mimeType.StartsWith("audio/")->MediaType.AUDIO {..}same for image and audio}`
9. `return mediaFiles.toList()`
