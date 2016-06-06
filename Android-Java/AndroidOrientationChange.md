From:[http://code.hootsuite.com/orientation-changes-on-android/](http://code.hootsuite.com/orientation-changes-on-android/)

#1. Introduction
Handling orientation changes on Android is one of the most frustrating things to deal with as an Android engineer. I hope to make that challenge just a little bit easier and to help you better understand exactly what’s happening when you rotate your Android device. I’m going to cover what not to do when handling orientation changes, and what you should do in some common scenarios including dealing with Fragments, AsyncTasks and ListViews.   

#2. Background
When you rotate your device and the screen changes orientation, Android usually destroys your application’s existing Activities and Fragments and recreates them. Android does this so that your application can reload resources based on the new configuration. When it destroys your Activities and Fragments it will end up creating new instances of them which will wipe out all of your member variables. To work around this, Android gives you the opportunity to save your app’s state before destroying your Activities and Fragments, and the opportunity to restore your state when recreating them. Proper handling of orientation changes centers around saving this state and also avoiding memory leaks.
While it may seem a bit tedious to implement, handling orientation changes properly provides you with several benefits: you will be able to easily use alternate layouts in portrait and landscape orientations, and you will be able to handle many exceptional states such as low memory situations and interruptions from incoming phone calls without any extra code.       

#3. What Not To Do

**android:configChanges**                  
One of the most common “solutions” to dealing with orientation changes is to not deal with them. You can do this by setting the android:configChanges flag on your Activity in AndroidManifest.xml as shown below:   
```
<activity
    android:name=".MyActivity"
    android:label="@string/title_my_activity"
    android:configChanges="orientation|screenSize|keyboardHidden" />
```
This flag signals to the Android platform that you are going to manually handle orientation, screenSize and keyboard appearance/disappearance changes for this Activity. So instead of destroying and recreating your Activity, Android will just rotate the screen and call one of the lifecycle methods: onConfigurationChanged. If you have a Fragment attached to this Activity, it will also receive a call to its onConfigurationChanged method. This means that the same instances of your Activities and Fragments will be in use and your member variables will remain untouched. If you do want something to be different when the orientation changes, such as using a new layout, you would have to implement onConfigurationChanged and manually discard the old layout/View, inflate the new layout and display it – which is a lot more work and can make your code difficult to work with in the future.   

**Fragment.setRetainInstance(true)**         
Calling setRetainInstance(true) on a Fragment is similar to setting the android:configChanges flag on an Activity. It signals to Android that you want to continue using the same instance of the current Fragment, so all of your member variables will remain untouched. If you rotate your device when you have an Activity that is NOT using the configChanges flag and a Fragment that IS being retained, the following lifecycle methods will be called on the Fragment:   

```
onPause
onSaveInstanceState
onStop
onDestroyView
onDetach
onAttach
onCreateView
onActivityCreated
onStart
onResume
```

Notice that Android does not call onCreate and onDestroy because we retained the Fragment; nor does it call the constructor, because the same Fragment instance will be used after the orientation change. Android will call all of the other callbacks because the Fragment’s parent Activity IS being destroyed and recreated, so the Fragment does have to go through the process of being detached then reattached. Alternatively, if your Activity HAS the configChanges flag set and your Fragment IS retained, all that will happen is the screen will rotate and both your Activity and Fragment will receive calls to their respective onConfigurationChanged methods. Calling setRetainInstance(true) on a Fragment is generally a bad idea for the same reasons as using the configChanges flag on an Activity is a bad idea: you won’t be able to reload resources that may need to be refreshed. There is one situation in which retaining a Fragment is a good idea and we’ll explore that in more detail in the Handling AsyncTasks section.   
