The Android framework provides two animation systems: property animation and view animation. Both animation systems are viable options, but the property animation system, in general, is the preferred method to use, because it is more flexible and offers more features. In addition to these two systems, you can utilize Drawable animation, which allows you to load drawable resources and display them one frame after another.  

#Property Animation
Introduced in Android 3.0 (API level 11), the property animation system lets you animate properties of any object, including ones that are not rendered to the screen. The system is extensible and lets you animate properties of custom types as well.   

From: [https://developer.android.com/guide/topics/graphics/prop-animation.html](https://developer.android.com/guide/topics/graphics/prop-animation.html)     

The property animation system is a robust framework that allows you to animate almost anything. You can define an animation to change any object property over time, regardless of whether it draws to the screen or not. A property animation changes a property's (a field in an object) value over a specified length of time. To animate something, you specify the object property that you want to animate, such as an object's position on the screen, how long you want to animate it for, and what values you want to animate between.  

The property animation system lets you define the following characteristics of an animation:    

- Duration: You can specify the duration of an animation. The default length is 300 ms.
- Time interpolation: You can specify how the values for the property are calculated as a function of the animation's current elapsed time.
- Repeat count and behavior: You can specify whether or not to have an animation repeat when it reaches the end of a duration and how many times to repeat the animation. You can also specify whether you want the animation to play back in reverse. Setting it to reverse plays the animation forwards then backwards repeatedly, until the number of repeats is reached.
- Animator sets: You can group animations into logical sets that play together or sequentially or after specified delays.
- Frame refresh delay: You can specify how often to refresh frames of your animation. The default is set to refresh every 10 ms, but the speed in which your application can refresh frames is ultimately dependent on how busy the system is overall and how fast the system can service the underlying timer.  

**How Property Animation Differs from View Animation**   
The view animation system provides the capability to only animate View objects, so if you wanted to animate non-View objects, you have to implement your own code to do so. The view animation system is also constrained in the fact that it only exposes a few aspects of a View object to animate, such as the scaling and rotation of a View but not the background color, for instance.

Another disadvantage of the view animation system is that it only modified where the View was drawn, and not the actual View itself. For instance, if you animated a button to move across the screen, the button draws correctly, but the actual location where you can click the button does not change, so you have to implement your own logic to handle this.

With the property animation system, these constraints are completely removed, and you can animate any property of any object (Views and non-Views) and the object itself is actually modified. The property animation system is also more robust in the way it carries out animation. At a high level, you assign animators to the properties that you want to animate, such as color, position, or size and can define aspects of the animation such as interpolation and synchronization of multiple animators.

The view animation system, however, takes less time to setup and requires less code to write. If view animation accomplishes everything that you need to do, or if your existing code already works the way you want, there is no need to use the property animation system. It also might make sense to use both animation systems for different situations if the use case arises.  

**Animators**    
The Animator class provides the basic structure for creating animations. You normally do not use this class directly as it only provides minimal functionality that must be extended to fully support animating values. The following subclasses extend Animator:  
- *ValueAnimator*: The main timing engine for property animation that also computes the values for the property to be animated. It has all of the core functionality that calculates animation values and contains the timing details of each animation, information about whether an animation repeats, listeners that receive update events, and the ability to set custom types to evaluate. There are two pieces to animating properties: calculating the animated values and setting those values on the object and property that is being animated. ValueAnimator does not carry out the second piece, so you must listen for updates to values calculated by the ValueAnimator and modify the objects that you want to animate with your own logic. See the section about Animating with ValueAnimator for more information.  

- *ObjectAnimator*: A subclass of ValueAnimator that allows you to set a target object and object property to animate. This class updates the property accordingly when it computes a new value for the animation. You want to use ObjectAnimator most of the time, because it makes the process of animating values on target objects much easier. However, you sometimes want to use ValueAnimator directly because ObjectAnimator has a few more restrictions, such as requiring specific acessor methods to be present on the target object.  

- *AnimatorSet*: Provides a mechanism to group animations together so that they run in relation to one another. You can set animations to play together, sequentially, or after a specified delay. See the section about Choreographing multiple animations with Animator Sets for more information.  

**Evaluators**  
Evaluators tell the property animation system how to calculate values for a given property. They take the timing data that is provided by an Animator class, the animation's start and end value, and calculate the animated values of the property based on this data. The property animation system provides the following evaluators:   

- *IntEvaluator*: The default evaluator to calculate values for int properties.

- *FloatEvaluator*: The default evaluator to calculate values for float properties.

- *ArgbEvaluator*: The default evaluator to calculate values for color properties that are represented as hexidecimal values.  

- *TypeEvaluator*: 	An interface that allows you to create your own evaluator. If you are animating an object property that is not an int, float, or color, you must implement the TypeEvaluator interface to specify how to compute the object property's animated values. You can also specify a custom TypeEvaluator for int, float, and color values as well, if you want to process those types differently than the default behavior.   


**Interpolators**    
A time interpolator defines how specific values in an animation are calculated as a function of time. For example, you can specify animations to happen linearly across the whole animation, meaning the animation moves evenly the entire time, or you can specify animations to use non-linear time, for example, accelerating at the beginning and decelerating at the end of the animation.   

- *AccelerateDecelerateInterpolator*: An interpolator whose rate of change starts and ends slowly but accelerates through the middle.  
- *AccelerateInterpolator*: An interpolator whose rate of change starts out slowly and then accelerates.  
- *AnticipateInterpolator*: An interpolator whose change starts backward then flings forward.  
- *AnticipateOvershootInterpolator*: An interpolator whose change starts backward, flings forward and overshoots the target value, then finally goes back to the final value.  
- *BounceInterpolator*: An interpolator whose change bounces at the end.
- *CycleInterpolator*: An interpolator whose animation repeats for a specified number of cycles. 
- *DecelerateInterpolator*: An interpolator whose rate of change starts out quickly and and then decelerates.
- *LinearInterpolator*: An interpolator whose rate of change is constant.
- *OvershootInterpolator*: An interpolator whose change flings forward and overshoots the last value then comes back.
- *TimeInterpolator*:	An interface that allows you to implement your own interpolator.


#View Animation
View Animation is the older system and can only be used for Views. It is relatively easy to setup and offers enough capabilities to meet many application's needs.   

You can use the view animation system to perform tweened animation on Views. Tween animation calculates the animation with information such as the start point, end point, size, rotation, and other common aspects of an animation.

A tween animation can perform a series of simple transformations (position, size, rotation, and transparency) on the contents of a View object. So, if you have a TextView object, you can move, rotate, grow, or shrink the text. If it has a background image, the background image will be transformed along with the text. The animation package provides all the classes used in a tween animation.

A sequence of animation instructions defines the tween animation, defined by either XML or Android code. As with defining a layout, an XML file is recommended because it's more readable, reusable, and swappable than hard-coding the animation. In the example below, we use XML. (To learn more about defining an animation in your application code, instead of XML, refer to the AnimationSet class and other Animation subclasses.)

The animation instructions define the transformations that you want to occur, when they will occur, and how long they should take to apply. Transformations can be sequential or simultaneous - for example, you can have the contents of a TextView move from left to right, and then rotate 180 degrees, or you can have the text move and rotate simultaneously. Each transformation takes a set of parameters specific for that transformation (starting size and ending size for size change, starting angle and ending angle for rotation, and so on), and also a set of common parameters (for instance, start time and duration). To make several transformations happen simultaneously, give them the same start time; to make them sequential, calculate the start time plus the duration of the preceding transformation.

The animation XML file belongs in the res/anim/ directory of your Android project. The file must have a single root element: this will be either a single <alpha>, <scale>, <translate>, <rotate>, interpolator element, or <set> element that holds groups of these elements (which may include another <set>). By default, all animation instructions are applied simultaneously. To make them occur sequentially, you must specify the startOffset attribute, as shown in the example below.

The following XML from one of the ApiDemos is used to stretch, then simultaneously spin and rotate a View object.

```
<set android:shareInterpolator="false">
    <scale
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:fromXScale="1.0"
        android:toXScale="1.4"
        android:fromYScale="1.0"
        android:toYScale="0.6"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fillAfter="false"
        android:duration="700" />
    <set android:interpolator="@android:anim/decelerate_interpolator">
        <scale
           android:fromXScale="1.4"
           android:toXScale="0.0"
           android:fromYScale="0.6"
           android:toYScale="0.0"
           android:pivotX="50%"
           android:pivotY="50%"
           android:startOffset="700"
           android:duration="400"
           android:fillBefore="false" />
        <rotate
           android:fromDegrees="0"
           android:toDegrees="-45"
           android:toYScale="0.0"
           android:pivotX="50%"
           android:pivotY="50%"
           android:startOffset="700"
           android:duration="400" />
    </set>
</set>
```

Screen coordinates (not used in this example) are (0,0) at the upper left hand corner, and increase as you go down and to the right.

Some values, such as pivotX, can be specified relative to the object itself or relative to the parent. Be sure to use the proper format for what you want ("50" for 50% relative to the parent, or "50%" for 50% relative to itself).

You can determine how a transformation is applied over time by assigning an Interpolator. Android includes several Interpolator subclasses that specify various speed curves: for instance, AccelerateInterpolator tells a transformation to start slow and speed up. Each one has an attribute value that can be applied in the XML.

With this XML saved as hyperspace_jump.xml in the res/anim/ directory of the project, the following code will reference it and apply it to an ImageView object from the layout.

```
ImageView spaceshipImage = (ImageView) findViewById(R.id.spaceshipImage);
Animation hyperspaceJumpAnimation = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
spaceshipImage.startAnimation(hyperspaceJumpAnimation);
```
#Drawable Animation
Drawable animation involves displaying Drawable resources one after another, like a roll of film. This method of animation is useful if you want to animate things that are easier to represent with Drawable resources, such as a progression of bitmaps.  

Drawable animation lets you load a series of Drawable resources one after another to create an animation. This is a traditional animation in the sense that it is created with a sequence of different images, played in order, like a roll of film. The AnimationDrawable class is the basis for Drawable animations.

While you can define the frames of an animation in your code, using the AnimationDrawable class API, it's more simply accomplished with a single XML file that lists the frames that compose the animation. The XML file for this kind of animation belongs in the res/drawable/ directory of your Android project. In this case, the instructions are the order and duration for each frame of the animation.

The XML file consists of an <animation-list> element as the root node and a series of child <item> nodes that each define a frame: a drawable resource for the frame and the frame duration. Here's an example XML file for a Drawable animation:

```
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true">
    <item android:drawable="@drawable/rocket_thrust1" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust2" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust3" android:duration="200" />
</animation-list>
```

This animation runs for just three frames. By setting the android:oneshot attribute of the list to true, it will cycle just once then stop and hold on the last frame. If it is set false then the animation will loop. With this XML saved as rocket_thrust.xml in the res/drawable/ directory of the project, it can be added as the background image to a View and then called to play. Here's an example Activity, in which the animation is added to an ImageView and then animated when the screen is touched:  
```
AnimationDrawable rocketAnimation;

public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.main);

  ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
  rocketImage.setBackgroundResource(R.drawable.rocket_thrust);
  rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
}

public boolean onTouchEvent(MotionEvent event) {
  if (event.getAction() == MotionEvent.ACTION_DOWN) {
    rocketAnimation.start();
    return true;
  }
  return super.onTouchEvent(event);
}
```
It's important to note that the start() method called on the AnimationDrawable cannot be called during the onCreate() method of your Activity, because the AnimationDrawable is not yet fully attached to the window. If you want to play the animation immediately, without requiring interaction, then you might want to call it from the onWindowFocusChanged() method in your Activity, which will get called when Android brings your window into focus.

