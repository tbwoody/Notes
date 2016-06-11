From:[http://simplyadvanced.net/blog/android-how-to-decode-proguards-obfuscated-code-from-stack-trace/](http://simplyadvanced.net/blog/android-how-to-decode-proguards-obfuscated-code-from-stack-trace/)

#1. Before
Here’s the stack trace as obfuscated by ProGuard:  
```
Caused by: java.lang.NullPointerException
at net.simplyadvanced.ltediscovery.be.u(Unknown Source)
at net.simplyadvanced.ltediscovery.at.v(Unknown Source)
at net.simplyadvanced.ltediscovery.at.d(Unknown Source)
at net.simplyadvanced.ltediscovery.av.onReceive(Unknown Source)
```


#2. After
All you need to do is write one line in the command prompt, and the obfuscation will be removed, as you can see here:  

```
Caused by: java.lang.NullPointerException
at net.simplyadvanced.ltediscovery.UtilTelephony.boolean is800MhzNetwork()(Unknown Source)
at net.simplyadvanced.ltediscovery.ServiceDetectLte.void checkAndAlertUserIf800MhzConnected()(Unknown Source)
at net.simplyadvanced.ltediscovery.ServiceDetectLte.void startLocalBroadcastReceiver()(Unknown Source)
at net.simplyadvanced.ltediscovery.ServiceDetectLte$2.void onReceive(android.content.Context,android.content.Intent)(Unknown Source)
```
#3. HOW TO DECODE STACK TRACE
You can choose either the command line method or GUI (Graphical User Interface) method.  

##3.1 DECODE STACK TRACE VIA GUI
- Open /tools/proguard/bin/proguardgui.bat 
- Select the “ReTrace” option on the left column. 
- Add your mapping file and obfuscated stack trace. 
- Click “ReTrace!”

##3.2 DECODE STACK TRACE VIA COMMAND LINE
- You will need your ProGuard’s mapping.txt file and the stack trace (Ex: stacktrace.txt) that you want to de-obfuscate.
- The easiest way to do the next step is copy both these files into your /tools/proguard/bin</span>. 
- If you are on Windows, **run the following command** in the same directory as the files (make sure you change to your own file names):

```
retrace.bat -verbose mapping.txt stacktrace.txt > out.txt
```
- out.txt will have the stack trace de-obfuscated. Now you can debug much easier and faster than before.

