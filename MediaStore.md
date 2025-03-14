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
2. 
