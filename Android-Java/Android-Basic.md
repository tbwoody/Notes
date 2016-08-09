**1. What are the tools are placed in An Android SDK?**    
Android SDK collaborated with Android Emulator,DDMS(Dalvik Debug Monitoring Services),AAPT(Android Asset Packaging tool) and ADB(Android debug bridge)


**2. Android App Components**
- Intents and Intent Filters
- Activities
- Services
- Content Providers
- App Widgets
- Processes and Threads

**3. How many dialog boxes do support in android?**     
- AlertDialog, 
- ProgressDialog,
- DatePickerDialog, 
- and TimePickerDialog   

**4. Different Intents in Android**
- Intent - is a message passing mechanism between components of android, except for Content Provider. You can use intent to start any component.

- Sticky Intent - Sticks with android, for future broad cast listeners. For example if BATTERY_LOW event occurs then that intent will be stick with android so that if any future user requested for BATTER_LOW, it will be fired;

- Pending Intent - If you want some one to perform any Intent operation at future point of time on behalf of you, then we will use Pending Intent.

**5. Singleton**     
The Singleton's purpose is to control object creation, limiting the number of obejcts to one only. Since there is only one Singleton instance, any instance fields of a Singleton will occur only once per class, just like static fields. Singletons often control access to resources such as database connections or sockets.

For example, if you have a license for only one connection for your database or your JDBC driver has trouble with multithreading, the Singleton makes sure that only one connection is made or that only one thread can access the connection at a time.

