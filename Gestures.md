# Gestures
Jetapack Toolbox contain
on the lowest level andrid has <strong>Pointer Events</strong> :- it handles raw pointer events using pointerInput modifier. for some common events like zooming pinching draging <strong> Gesture Recognizers </strong>. one level up we have <strong> Gesture Modifiers</strong> Add gesture handling and extra functionality .<strong> Components</strong> use built in gestures handling of comoponents like slides etc.
we will focus on Gesture Recognizers and Gesture Modifiers in this 
## 1. Gesture recognizers
	Modifier.pointerInput(lambda -> function){
	for different types of taps 
	* detectTapGestures(
		- onDoubleTap =..,
		- onLongTap = ..,
		- onPress = ..,
 		- onTap = ..
  	) 
	for different drag gestures 
	* detectDragGestures/detectVerticalDragGestures/detectHorizontalDragGestures/detectDragGesturesAfterLongPress (
  		- onDragStart =
  		- onDragCancel =
  		- onDragEnd =
  		- onDrag =
	)
	for multitouch Gestures
	* detectTransformGestures(
  		onGesture={center,pan,zoom,angle->})
	)
 ## 2. Gesture Modifers
 	- Modifier.clickable{..} //for single tap
  	- Modifier.combinedClickable( //for multiple taps
   		onClick = ,
	 	onLongClick= ,
   		onDoubleClick= 
	)
 	- val state = rememberDraggableState{delta -> ..} 
  	Modifier.draggable(
  		orientation = horizontal / vertical
		state = rememberDraggableState{
  			Delta->
		}
	)
 	- Modifer.scrollable() works the same as dragable be it holds the logic of scrolling and flinging
  	- Modifier.Transformable(
   		state = remmeberTransformableState{
	 		zoomChange,
			offsetChange,
   			rotationChange -> ..
		}
	)

Which one should we use if we can perform the same task from say detectTapGesture from Gesture Recognisers and Gesture Modifers
![image](https://github.com/user-attachments/assets/96ebb295-3628-4202-b122-828a19b4c578)
This is the code of the Modifier.clickable in which we can clearly see that not only includes gesture recognisers but also includes clicksemantics focusable to support keyboards and many more..
### Note :-In general try to use GestureModifier and use Gesture recogniser only there is a good reason
