
From:[https://developer.android.com/reference/android/app/Activity.html#Fragments](https://developer.android.com/reference/android/app/Activity.html#Fragments)

#1. Activity Lifecycle
Activities in the system are managed as an activity stack. When a new activity is started, it is placed on the top of the stack and becomes the running activity -- the previous activity always remains below it in the stack, and will not come to the foreground again until the new activity exits.

An activity has essentially four states:   

- If an activity is in the foreground of the screen (at the top of the stack), it is active or running.
- If an activity has lost focus but is still visible (that is, a new non-full-sized or transparent activity has focus on top of your activity), it is paused. A paused activity is completely alive (it maintains all state and member information and remains attached to the window manager), but can be killed by the system in extreme low memory situations.
- If an activity is completely obscured by another activity, it is stopped. It still retains all state and member information, however, it is no longer visible to the user so its window is hidden and it will often be killed by the system when memory is needed elsewhere.
- If an activity is paused or stopped, the system can drop the activity from memory by either asking it to finish, or simply killing its process. When it is displayed again to the user, it must be completely restarted and restored to its previous state.

The following diagram shows the important state paths of an Activity. The square rectangles represent callback methods you can implement to perform operations when the Activity moves between states. The colored ovals are major states the Activity can be in.

![Activity Lifecycle](/images/lifecycle.png)

There are three key loops you may be interested in monitoring within your activity:

- The **entire lifetime** of an activity happens between the first call to onCreate(Bundle) through to a single final call to onDestroy(). An activity will do all setup of "global" state in onCreate(), and release all remaining resources in onDestroy(). For example, if it has a thread running in the background to download data from the network, it may create that thread in onCreate() and then stop the thread in onDestroy().

- The **visible lifetime** of an activity happens between a call to onStart() until a corresponding call to onStop(). During this time the user can see the activity on-screen, though it may not be in the foreground and interacting with the user. Between these two methods you can maintain resources that are needed to show the activity to the user. For example, you can register a BroadcastReceiver in onStart() to monitor for changes that impact your UI, and unregister it in onStop() when the user no longer sees what you are displaying. The onStart() and onStop() methods can be called multiple times, as the activity becomes visible and hidden to the user.

- The **foreground lifetime** of an activity happens between a call to onResume() until a corresponding call to onPause(). During this time the activity is in front of all other activities and interacting with the user. An activity can frequently go between the resumed and paused states -- for example when the device goes to sleep, when an activity result is delivered, when a new intent is delivered -- so the code in these methods should be fairly lightweight.

The entire lifecycle of an activity is defined by the following Activity methods. All of these are hooks that you can override to do appropriate work when the activity changes state. All activities will implement onCreate(Bundle) to do their initial setup; many will also implement onPause() to commit changes to data and otherwise prepare to stop interacting with the user. You should always call up to your superclass when implementing these methods.

```
 public class Activity extends ApplicationContext {
     protected void onCreate(Bundle savedInstanceState);
     
     protected void onRestart();
     
     protected void onStart();

     protected void onResume();

     protected void onPause();

     protected void onStop();

     protected void onDestroy();
 }
```

**onCreate()**     
Called when the activity is first created. This is where you should do all of your normal static set up: create views, bind data to lists, etc. This method also provides you with a Bundle containing the activity's previously frozen state, if there was one.
Always followed by onStart(). Killable:NO.        

**onRestart()**   
Called after your activity has been stopped, prior to it being started again.
Always followed by onStart(). Killable:NO.

**onStart()**    
Called when the activity is becoming visible to the user.     
Followed by onResume() if the activity comes to the foreground, or onStop() if it becomes hidden. Killable:NO. 

**onResume()**      
Called when the activity will start interacting with the user. At this point your activity is at the top of the activity stack, with user input going to it.     
Always followed by onPause().   Killable:NO.

**onPause()**   
alled when the system is about to start resuming a previous activity. This is typically used to commit unsaved changes to persistent data, stop animations and other things that may be consuming CPU, etc. Implementations of this method must be very quick because the next activity will not be resumed until this method returns.     
Followed by either onResume() if the activity returns back to the front, or onStop() if it becomes invisible to the user.  Killable: Pre-HONEYCOMB

**onStop()**      
Called when the activity is no longer visible to the user, because another activity has been resumed and is covering this one. This may happen either because a new activity is being started, an existing one is being brought in front of this one, or this one is being destroyed.               
Followed by either onRestart() if this activity is coming back to interact with the user, or onDestroy() if this activity is going away.  Killable:YES.

**onDestroy()**     
The final call you receive before your activity is destroyed. This can happen either because the activity is finishing (someone called finish() on it, or because the system is temporarily destroying this instance of the activity to save space. You can distinguish between these two scenarios with the isFinishing() method.  Killable:YES.

Note the "Killable" column in the above table -- for those methods that are marked as being killable, after that method returns the process hosting the activity may be killed by the system at any time without another line of its code being executed. Because of this, you should use the onPause() method to write any persistent data (such as user edits) to storage. In addition, the method onSaveInstanceState(Bundle) is called before placing the activity in such a background state, allowing you to save away any dynamic instance state in your activity into the given Bundle, to be later received in onCreate(Bundle) if the activity needs to be re-created.


- When the Activity first time loads the events are called as below:
```
onCreate()
onStart()
onResume()
```
- When you click on Phone button the Activity goes to the background and the below events are called:
```
onPause()
onStop()
```
- Exit the phone dialer and the below events will be called:
```
onRestart()
onStart()
onResume()
```
- When you click the back button OR try to finish() the activity the events are called as below:
```
onPause()
onStop()
onDestroy()
```
- When phone screen off:
```
onPaused() 
onStop()
```

- Life cycle of re-orientation
```
onPause();
onSaveInstanceState();
onStop();
onDestroy();

onCreate();
onStart();
onResume();
```
