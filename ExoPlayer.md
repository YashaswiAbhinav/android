# ExoPlayer
### Dependencies
```


```
Building a exoplayer contains minimum of 3 things
1. ExoPlayer Builder
2. Android View 
3. Disposable effect
## 1. ExoPlayer Builder
example of a simple ExoBuilder
```
    val exoplayer = remember {
        ExoPlayer.Builder(context).build().apply {
            val uri = Uri.parse(source)
            setMediaItem(MediaItem.fromUri(uri))
            prepare()
            playWhenReady = true
        }
    }
```
