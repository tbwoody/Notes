#What's Intent          
An Intent is a messaging object you can use to request an action from another app component. Although intents facilitate communication between components in several ways, there are three fundamental use-cases:   

**To start an activity:**   
An Activity represents a single screen in an app. You can start a new instance of an Activity by passing an Intent to startActivity(). The Intent describes the activity to start and carries any necessary data.
If you want to receive a result from the activity when it finishes, call startActivityForResult(). Your activity receives the result as a separate Intent object in your activity's onActivityResult() callback. For more information, see the Activities guide.    

**To start a service:**      
A Service is a component that performs operations in the background without a user interface. You can start a service to perform a one-time operation (such as download a file) by passing an Intent to startService(). The Intent describes the service to start and carries any necessary data.
If the service is designed with a client-server interface, you can bind to the service from another component by passing an Intent to bindService(). For more information, see the Services guide.    

**To deliver a broadcast:**     
A broadcast is a message that any app can receive. The system delivers various broadcasts for system events, such as when the system boots up or the device starts charging. You can deliver a broadcast to other apps by passing an Intent to sendBroadcast(), sendOrderedBroadcast(), or sendStickyBroadcast().      


#Intent Types
There are two types of intents:       

**Explicit intents** specify the component to start by name (the fully-qualified class name). You'll typically use an explicit intent to start a component in your own app, because you know the class name of the activity or service you want to start. For example, start a new activity in response to a user action or start a service to download a file in the background.    

**Implicit intents** do not name a specific component, but instead declare a general action to perform, which allows a component from another app to handle it. For example, if you want to show the user a location on a map, you can use an implicit intent to request that another capable app show a specified location on a map.           

#Intent Structure
The primary pieces of information in an intent are:    

- *action*，The general action to be performed, such as ACTION_VIEW, ACTION_EDIT, ACTION_MAIN, etc.   
- *data*，The data to operate on, such as a person record in the contacts database, expressed as a Uri.
Some examples of action/data pairs are:

  - ACTION_VIEW content://contacts/people/1 -- Display information about the person whose identifier is "1".
  - ACTION_DIAL content://contacts/people/1 -- Display the phone dialer with the person filled in.
  - ACTION_VIEW tel:123 -- Display the phone dialer with the given number filled in. Note how the VIEW action does what is considered the most reasonable thing for a particular URI.
  - ACTION_DIAL tel:123 -- Display the phone dialer with the given number filled in.
  - ACTION_EDIT content://contacts/people/1 -- Edit information about the person whose identifier is "1".
  - ACTION_VIEW content://contacts/people/ -- Display a list of people, which the user can browse through. This example is a typical top-level entry into the Contacts application, showing you the list of people. Selecting a particular person to view would result in a new intent { ACTION_VIEW content://contacts/N } being used to start an activity to display that person.

In addition to these primary attributes, there are a number of secondary attributes that you can also include with an intent:

- *category*，Gives additional information about the action to execute. For example, CATEGORY_LAUNCHER means it should appear in the Launcher as a top-level application, while CATEGORY_ALTERNATIVE means it should be included in a list of alternative actions the user can perform on a piece of data.
- *type*，Specifies an explicit type (a MIME type) of the intent data. Normally the type is inferred from the data itself. By setting this attribute, you disable that evaluation and force an explicit type.
- *component*，Specifies an explicit name of a component class to use for the intent. Normally this is determined by looking at the other information in the intent (the action, data/type, and categories) and matching that with a component that can handle it. If this attribute is set then none of the evaluation is performed, and this component is used exactly as is. By specifying this attribute, all of the other Intent attributes become optional.
- *extras*，This is a Bundle of any additional information. This can be used to provide extended information to the component. For example, if we have a action to send an e-mail message, we could also include extra pieces of data here to supply a subject, body, etc.


#Intent, Sticky Intent and Pending Intent
An Android Intent is an object carrying an intent ie. message from one component to another component with-in the application or outside the application. The intents can communicate messages among any of the three core components of an application - activities, services, and broadcast receivers.

The intent itself, an Intent object, is a passive data structure holding an abstract description of an operation to be performed.

For example, let's assume that you have an Activity that needs to launch an email client and sends an email using your Android device. For this purpose, your Activity would send an ACTION_SEND along with appropriate chooser, to the Android Intent Resolver. The specified chooser gives the proper interface for the user to pick how to send your email data.

A PendingIntent is a token that you give to a foreign application (e.g. NotificationManager, AlarmManager, Home Screen AppWidgetManager, or other 3rd party applications), which allows the foreign application to use your application's permissions to execute a predefined piece of code.

By giving a PendingIntent to another application, you are granting it the right to perform the operation you have specified as if the other application was yourself (with the same permissions and identity). As such, you should be careful about how you build the PendingIntent: almost always, for example, the base Intent you supply should have the component name explicitly set to one of your own components, to ensure it is ultimately sent there and nowhere else.

An intent that is used with sticky broadcast, is called as sticky intent. This intent will stick with android system for future broadcast receiver requests.

OR

sendStickyBroadcast() performs a sendBroadcast(Intent) known as sticky, i.e. the Intent you are sending stays around after the broadcast is complete, so that others can quickly retrieve that data through the return value of registerReceiver(BroadcastReceiver, IntentFilter). In all other ways, this behaves the same as sendBroadcast(Intent). One example of a sticky broadcast sent via the operating system is ACTION_BATTERY_CHANGED. When you call registerReceiver() for that action -- even with a null BroadcastReceiver -- you get the Intent that was last broadcast for that action. Hence, you can use this to find the state of the battery without necessarily registering for all future state changes in the battery.
