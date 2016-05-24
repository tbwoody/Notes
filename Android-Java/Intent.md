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
