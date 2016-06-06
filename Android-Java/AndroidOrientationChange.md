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

**android:screenOrientation**      
Finally, another “solution” to the orientation change problem is to set the android:screenOrientation flag on your activity:   
```

<activity
    android:name=".MyActivity"
    android:label="@string/title_my_activity"
    android:screenOrientation="portrait" />
```
This altogether prevents orientation changes from happening while the user is in the Activity with the flag set. So if you rotate your device the screen won’t rotate with it. While there are certainly situations in which this is the desired behavior, you should, if possible, allow your app to be used in both landscape and portrait orientations because it greatly increases your app’s usability.    

#4. Saving State      
The most important aspect of handling orientation changes is saving state. In most cases this involves implementing the onSaveInstanceState method (this could be in your Activity, Fragment or both) and placing the values you need to save in the Bundle argument that gets passed to the method.

Most of the time you don’t have to worry about saving the state of your Views because Android automatically calls the View.onSaveInstanceState method on each of the views in your view hierarchy as long as you call through to the super method in onSaveInstanceState. This also means that if you use any custom Views, they should contain an implementation of onSaveInstanceState. Do note, that in order for a View’s state to be saved it MUST have an android:id attribute because this is essentially used as the key for that particular View’s state.       

```
private static final String STATE_COUNTER = "counter";

private int mCounter;

...

@Override
protected void onSaveInstanceState(Bundle outState) {
    // Make sure to call the super method so that the states of our views are saved
    super.onSaveInstanceState(outState);
    // Save our own state now
    outState.putInt(STATE_COUNTER, mCounter);
}
```

One thing to watch out for is a ListView. The super method of onSaveInstanceState will take care of saving certain things such as scroll position, but it is up to you to save the contents of the adapter in your onSaveInstanceState method. If you are using an ArrayList of a model object to populate your ListView, then one option is to ensure your model object implements Serializable and in onSaveInstanceState, place it in the Bundle through putSerializable. One thing to keep in mind: even though it is good practice to “code to interface” and declare your ArrayList as a List (List items = new ArrayList<>()), in this particular case, you will have to declare it as an ArrayList (ArrayList items = new ArrayList<>()) because the compiler needs to know that your List implements Serializable (which List itself does not, but ArrayList does) in order to pass it to Bundle.putSerializable(String, Serializable).

```

private static final String STATE_ITEMS = "items";

// Make sure to declare as ArrayList so it's Serializable
private ArrayList<Item> mItems;

...

@Override
protected void onSaveInstanceState(Bundle outState) {
    // Make sure to call the super method so that the states of our views are saved
    super.onSaveInstanceState(outState);
    // Save our own state now
    outState.putSerializable(STATE_ITEMS, mItems);
}
```

onSaveInstanceState gets called before onStop but it is not guaranteed to be called before or after onPause. Android will also only call it when your application needs to save temporary state which includes when orientation changes occur and when your Activity is killed for its memory resources. It will not be called in certain situations such as finishing an Activity normally or putting an Activity into the background.

#5. Restoring State
The way you restore state can vary between different Activities and between Activities and Fragments. In a typical Activity, you would check the savedInstanceState argument that gets passed to your onCreate method. If savedInstanceState != null, you would retrieve your state from that Bundle.  

```
private static final String STATE_COUNTER = "counter";

private TextView mCounterTextView;
private int mCounter;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    
    ...
    
    // If we have a saved state then we can restore it now
    if (savedInstanceState != null) {
        mCounter = savedInstanceState.getInt(STATE_COUNTER, 0);
    }
    
    // Display the value of the counter
    mCounterTextView = (TextView) findViewById(R.id.counter_text);
    mCounterTextView.setText(Integer.toString(mCounter));
    
    ...
}

@Override
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    outState.putInt(STATE_COUNTER, mCounter);
}
```

Another option in an Activity is to implement onRestoreInstanceState which also gets passed a savedInstanceState Bundle. The only reason to take this approach is if you want to wait for all of your onCreate initialization to be done before restoring state or if you want to allow subclasses to specifically handle restoring state. If you choose to implement onRestoreInstanceState, you must call through to the super method if you want your View states restored. Again, if you use any custom Views, they should implement their own onRestoreInstanceState. An Activity’s onRestoreInstanceState is called after onStart and before onResume.    

In a Fragment you can restore state in several different callbacks (these are listed in the order they get called): onCreate, onCreateView, onActivityCreated, or onViewStateRestored. Where you choose to check your savedInstanceState Bundle depends entirely on what you need to have happened before restoring your state. If you need to make sure your parent Activity’s view hierarchy has been created, you should restore state in onActivityCreated. If you need to make sure your own Fragment’s view hierarchy has been created and had its state restored, you should restore state in onViewStateRestored.

ListViews again pose an extra requirement for restoring state. If you want your scroll position to be saved and restored properly, you must retrieve your saved ArrayList of model objects, create an adapter and assign it to the ListView before it gets its state restored (i.e. you have to do it in onCreateView or onActivityCreated).

```

private static final String STATE_ID = "id";
private static final String STATE_ITEMS = "items";

private long mId;
private ArrayList<Item> mItems;

private ListView mListView;
private ArrayAdapter<Item> mAdapter;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    if (savedInstanceState != null) {
        // Restore some state before we've even inflated our own layout
        // This could be generic things like an ID that our Fragment represents
        mId = savedInstanceState.getLong(STATE_ID, 0);
    }
}

@Override
public void onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View root = inflater.inflate(R.layout.my_fragment, container, false);
    
    // Get references to some views
    mListView = (ListView) root.findViewById(R.id.list_view);
    
    if (savedInstanceState != null) {
        // Restore some state right after inflating our layout
        // Note: Our views haven't had their states restored yet
        ...
    }
}

@SuppressWarnings("unchecked")
@Override
public void onActivityCreated(Bundle savedInstanceState) {
    super.onActivityCreated(savedInstanceState);
    if (savedInstanceState != null) {
        // Restore some state that needs to happen after the Activity was created
        //
        // Note #1: Our views haven't had their states restored yet
        // This could be a good place to restore a ListView's contents (and it's your last
        // opportunity if you want your scroll position to be restored properly)
        //
        // Note #2:
        // The following line will cause an unchecked type cast compiler warning
        // It's impossible to actually check the type because of Java's type erasure:
        //      At runtime all generic types become Object
        // So the best you can do is add the @SuppressWarnings("unchecked") annotation
        // and understand that you must make sure to not use a different type anywhere
        mItems = (ArrayList<Item>) savedInstanceState.getSerializable(STATE_ITEMS);
    } else {
        mItems = new ArrayList<>();
    }
    
    mAdapter = new ArrayAdapter<>(getActivity(), android.R.layout.simple_list_item_1, mItems);
    mListView.setAdapter(mAdapter);
}

@Override
public void onViewStateRestored(Bundle savedInstanceState) {
    super.onViewStateRestored(savedInstanceState);
    if (savedInstanceState != null) {
        // Restore some state that needs to happen after our own views have had
        // their state restored
        // DON'T try to restore ListViews here because their scroll position will
        // not be restored properly
        ...
    }
}

@Override
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    outState.putInt(STATE_COUNTER, mCounter);
    outState.putSerializable(STATE_ITEMS, mItems);
}
```

In the example above, we restored state in two different places. That was just for the purposes of the example. In reality, it is much better to only restore state in one place.

#6. Adding Fragments
One of the common pitfalls of handling orientation changes with Fragments is accidentally re-instantiating and re-adding them every time the Activity is recreated. If you allow Android to handle orientation changes, it will take care of re-instantiating your fragments, re-adding them to the activity, and recreating the fragment backstack, when it recreates the parent Activity. Therefore, you should only instantiate or add a fragment to an Activity if the savedInstanceState bundle passed to your Activity callbacks is null.

If you need to obtain a reference to the Fragment you can do so through the FragmentManager using a tag. This means that when you add the Fragment to the Activity you have to provide a tag argument. When you need a reference to your Fragment you can call FragmentManager.findFragmentByTag(String) and pass in the tag you used when adding the Fragment. The FragmentManager will take care of everything related to re-instantiating and re-adding the Fragment and it will make sure to return the right instance of the Fragment from findFragmentByTag. See below for an example of how to implement this:

```
private static final String TAG_MY_FRAGMENT = "myFragment";

private MyFragment mFragment;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    
    setContentView(R.layout.activity_adding_fragments);
    
    if (savedInstanceState == null) {
        // The Activity is NOT being re-created so we can instantiate a new Fragment
        // and add it to the Activity
        mFragment = new MyFragment();
        
        getSupportFragmentManager()
            .beginTransaction()
            // It's almost always a good idea to use .replace instead of .add so that
            // you never accidentally layer multiple Fragments on top of each other
            // unless of course that's your intention
            .replace(R.id.content_frame, mFragment, TAG_MY_FRAGMENT)
            .commit();
    } else {
        // The Activity IS being re-created so we don't need to instantiate the Fragment or add it,
        // but if we need a reference to it, we can use the tag we passed to .replace
        mFragment = getSupportFragmentManager().findFragmentByTag(TAG_MY_FRAGMENT);
    }
}
...
public void myMethod() {
    // Later on in the code we can do something with the Fragment
    mFragment.doSomething();
}
```

#7. Handling AsyncTasks
Starting an AsyncTask can pose problems if your Activities and Fragments are randomly getting recreated, so you will have to pay special attention to how you handle them. There are a few dangerous things that could happen with improperly handled AsyncTasks: memory leaks and crashes.   

**Memory Leaks**      
Memory leaks can occur if your AsyncTask holds on to a reference to an Activity or a Fragment. When Android destroys your Activity or Fragment because of an orientation change (or any other configuration change), it will not destroy any AsyncTasks that you started. So if an AsyncTask has a reference to a now-destroyed Activity or Fragment, the garbage collector won’t be able to collect that Activity or Fragment even though it should never be used again. You are particularly susceptible to memory leaks if you have an AsyncTask declared as a non-static inner class of an Activity or Fragment because that AsyncTask will implicitly hold a reference to its parent class (in this case the Activity or Fragment) even though it appears as though it doesn’t have this reference.

**IllegalArgumentExceptions**    
The other problem you can run into is an actual crash. If, for example, you are displaying a ProgressDialog in the onPreExecute method of your AsyncTask and dismissing it (calling ProgressDialog.dismiss()) in the onPostExecute or onCancelled method a few things will happen.

If you rotate the device before the task is finished you’ll immediately see a WindowLeaked exception printed out to the logs. This indicates that Android couldn’t release the resources for that window (your ProgressDialog) because you are still referencing it in your AsyncTask. This exception won’t actually cause a crash. However, when the task eventually does complete, and you call ProgressDialog.dismiss(), you will get an IllegalArgumentException: View…not attached to window manager. This is indicating that you are trying to dismiss a dialog that’s not actually attached to anything and it will cause a crash.

**Cancelling AsyncTasks**     
One way to avoid this is to cancel AsyncTasks in your Activity or Fragment’s onDestroy method. You can also save the state of your task (if it’s running or not) in the onSaveInstanceState method. When your Activity or Fragment is recreated, you can use that to determine if you need to restart the task.

```

private static final String STATE_TASK_RUNNING = "taskRunning";

private MyTask mTask;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    
    setContentView(...);
    
    // Setup views
    ...
    
    // Check if the task was running so we can restart it
    if (savedInstanceState != null) {
        if (savedInstanceState.getBoolean(STATE_TASK_RUNNING, false)) {
            mTask = new MyTask();
            mTask.execute(...);
        }
    }
}

/**
 * Suppose this was a click handler for something
 */
public void startTaskClicked(View view) {
    // We can start the task now
    mTask = new MyTask();
    mTask.execute(...);
}

private boolean isTaskRunning() {
    return (mTask != null) && (mTask.getStatus() == AsyncTask.Status.RUNNING);
}

@Override
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    
    // If the task is running, save it in our state
    if (isTaskRunning()) {
        outState.putBoolean(STATE_TASK_RUNNING, true);
    }
}

@Override
protected void onDestroy() {
    super.onDestroy();
    
    // Cancel the task if it's running
    if (isTaskRunning()) {
        mTask.cancel(true);
    }
}

private class MyTask extends AsyncTask<...> {
    ...
}
```


#8. Using a Retained Fragment
Now, it is not always an option (or at least a good option) to cancel and restart your AsyncTasks whenever an orientation change occurs. For example if you have a task that is downloading a file and it’s almost done when suddenly the user rotates the device, it would be an extremely unpleasant user experience for the task to be cancelled then restarted after that. This brings us back to something I mentioned earlier: calling setRetainInstance(true) on a Fragment. As I had described, setRetainInstance(true) tells Android to not destroy a Fragment when a configuration change happens. If you use a retained Fragment to host your AsyncTask, you can avoid ever having to restart your tasks. There are still a few things you should keep in mind though. Your retained Fragment should have no UI. Instead, you can declare an interface that your Activity will implement and that your Fragment will use to tell the Activity to update the UI (or anything else). In onAttach, you should cast your Activity to that interface and save it in a listener member variable. In onDetach you must set that listener variable to null, so you don’t leak an Activity reference. In the AsyncTask callbacks (onPreExecute, onProgressUpdate, onPostExecute and onCancelled), you should make sure the listener isn’t null then trigger the appropriate callback on the listener and let it handle the rest. Here’s an example of how to implement this:

```
public class NetworkRequestFragment extends Fragment {
    
    // Declare some sort of interface that your AsyncTask will use to communicate with the Activity
    public interface NetworkRequestListener {
        void onRequestStarted();
        void onRequestProgressUpdate(int progress);
        void onRequestFinished(SomeObject result);
    }
    
    private NetworkTask mTask;
    private NetworkRequestListener mListener;
    
    private SomeObject mResult;
    
    @Override
    public void onAttach(Activity activity) {
        super.onAttach(activity);
        
        // Try to use the Activity as a listener
        if (activity instanceof NetworkRequestListener) {
            mListener = (NetworkRequestListener) activity;
        } else {
            // You can decide if you want to mandate that the Activity implements your callback interface
            // in which case you should throw an exception if it doesn't:
            throw new IllegalStateException("Parent activity must implement NetworkRequestListener");
            // or you could just swallow it and allow a state where nobody is listening
        }
    }
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // Retain this Fragment so that it will not be destroyed when an orientation
        // change happens and we can keep our AsyncTask running
        setRetainInstance(true);
    }
    
    /**
     * The Activity can call this when it wants to start the task
     */
    public void startTask(String url) {
        mTask = new NetworkTask(url);
        mTask.execute();
    }
    
    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        // If the AsyncTask finished when we didn't have a listener we can
        // deliver the result here
        if ((mResult != null) && (mListener != null)) {
            mListener.onRequestFinished(mResult);
            mResult = null;
        }
    }
    
    @Override
    public void onDestroy() {
        super.onDestroy();
        
        // We still have to cancel the task in onDestroy because if the user exits the app or
        // finishes the Activity, we don't want the task to keep running
        // Since we are retaining the Fragment, onDestroy won't be called for an orientation change
        // so this won't affect our ability to keep the task running when the user rotates the device
        if ((mTask != null) && (mTask.getStatus == AsyncTask.Status.RUNNING)) {
            mTask.cancel(true);
        }
    }
    
    @Override
    public void onDetach() {
        super.onDetach();
        
        // This is VERY important to avoid a memory leak (because mListener is really a reference to an Activity)
        // When the orientation change occurs, onDetach will be called and since the Activity is being destroyed
        // we don't want to keep any references to it
        // When the Activity is being re-created, onAttach will be called and we will get our listener back
        mListener = null;
    }
    
    private class NetworkTask extends AsyncTask<String, Integer, SomeObject> {
        
        @Override
        protected void onPreExecute() {
            if (mListener != null) {
                mListener.onRequestStarted();
            }
        }
        
        @Override
        protected SomeObject doInBackground(String... urls) {
           // Make the network request
           ...
           // Whenever we want to update our progress:
           publishProgress(progress);
           ...
           return result;
        }
        
        @Override
        protected void onProgressUpdate(Integer... progress) {
            if (mListener != null) {
                mListener.onRequestProgressUpdate(progress[0]);
            }
        }
        
        @Override
        protected void onPostExecute(SomeObject result) {
            if (mListener != null) {
                mListener.onRequestFinished(result);
            } else {
                // If the task finishes while the orientation change is happening and while
                // the Fragment is not attached to an Activity, our mListener might be null
                // If you need to make sure that the result eventually gets to the Activity
                // you could save the result here, then in onActivityCreated you can pass it back
                // to the Activity
                mResult = result;
            }
        }
        
    }
}
```
