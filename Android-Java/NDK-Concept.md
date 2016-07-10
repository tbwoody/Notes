The Native Development Kit (NDK) is a set of tools that allow you to leverage C and C++ code in your Android app. You can use it either to build from your own source code, or to take advantage of existing prebuilt libraries.

The NDK is not appropriate for most novice Android programmers, and has little value for many types of Android apps. It is often not worth the additional complexity it inevitably brings to the development process. However, it can be useful in cases in which you need to:

- Squeeze extra performance out of a device for computationally intensive applications like games or physics simulations.
- Reuse your own or other developers' C or C++ libraries.

#1. Main Components
You should have an understanding of the following components as you build your app:   
- ndk-build: The ndk-build script launches the build scripts at the heart of the NDK. These scripts:
  - Automatically probe your development system and app project file to determine what to build.
  - Generate binaries.
  - Copy the binaries to your app's project path.
- Java: From your Java source, the Android build process generates .dex (Dalvik EXecutable) files, which are what the Android OS runs in the Dalvik Virtual Machine (“DVM”). Even if your app contains no Java source code at all, the build process still generates a .dex executable file within which the native component runs.
When developing Java components, use the native keyword to indicate methods implemented as native code. For example, the following function declaration tells the compiler that the implementation is in a native library:
```
public native int add(int  x, int  y);
```

- Native shared libraries: The NDK builds these libraries, or .so files, from your native source code.   
If two libraries implement respective methods with the same signature, a link error occurs. In C, "signature" means method name only. In C++, "signature" means not only method name, but also its argument names and types.

- Native static libraries: The NDK can also build static libraries, or .a files, which you can link against other libraries.

- Java Native Interface (JNI): The JNI is the interface via which the Java and C++ components talk to one another. 

- Application Binary Interface (ABI): The ABI defines exactly how your app's machine code is expected to interact with the system at runtime. The NDK builds .so files against these definitions. Different ABIs correspond to different architectures: The NDK includes ABI support for ARMEABI (default), MIPS, and x86.

- Manifest: If you are writing an app with no Java component to it, you must declare the NativeActivity class in the manifest.

The following two items are only required for building using the ndk-build script, and for debugging using the ndk-gdb script.  

- Android.mk: You must create an Android.mk configuration file inside your jni folder. The ndk-build script looks at this file, which defines the module and its name, the source files to be compiled, build flags and libraries to link.

- Application.mk: This file enumerates and describes the modules that your app requires. This information includes: 
 - ABIs used to compile for specific platforms.
 - Toolchains.
 - Standard libraries to include (static and dynamic STLport or default system).
 
#Native Activities and Applications   
The Android SDK provides a helper class, NativeActivity, that allows you to write a completely native activity. NativeActivity handles the communication between the Android framework and your native code, so you do not have to subclass it or call its methods. All you need to do is declare your application to be native in your AndroidManifest.xml file, and begin creating your native application.

An Android application using NativeActivity still runs in its own virtual machine, sandboxed from other applications. You can therefore still access Android framework APIs through the JNI. In certain cases, however–such as for sensors, input events, and assets–the NDK provides native interfaces that you can use instead of having to call across the JNI. For more information about such support, see Android NDK Native APIs.

Regardless of whether or not you are developing a native activity, we recommend that you create your projects with the traditional Android build tools. Doing so helps ensure building and packaging of Android applications with the correct structure.

The Android NDK provides you with two choices to implement your native activity:

- The native_activity.h header defines the native version of the NativeActivity class. It contains the callback interface and data structures that you need to create your native activity. Because the main thread of your application handles the callbacks, your callback implementations must not be blocking. If they block, you might receive ANR (Application Not Responding) errors because your main thread is unresponsive until the callback returns.
- The android_native_app_glue.h file defines a static helper library built on top of the native_activity.h interface. It spawns another thread, which handles things such as callbacks or input events in an event loop. Moving these events to a separate thread prevents any callbacks from blocking your main thread.

The <ndk_root>/sources/android/native_app_glue/android_native_app_glue.c source is also available, allowing you to modify the implementation.

For more information on how to use this static library, examine the native-activity sample application and its documentation. Further reading is also available in the comments in the <ndk_root>/sources/android/native_app_glue/android_native_app_glue.h file.

#Using the native_activity.h interface
To implement a native activity with the native_activity.h interface:  
- 1.Create a jni/ directory in your project's root directory. This directory stores all of your native code.

- 2.Declare your native activity in the AndroidManifest.xml file.
Because your application has no Java code, set android:hasCode to false.
```
<application android:label="@string/app_name" android:hasCode="false">
```

You must set the android:name attribute of the activity tag to NativeActivity.
```
<activity android:name="android.app.NativeActivity"
android:label="@string/app_name">
```
The android:value attribute of the meta-data tag specifies the name of the shared library containing the entry point to the application (such as C/C++ main), omitting the lib prefix and .so suffix from the library name.
```
<meta-data android:name="android.app.lib_name"
            android:value="native-activity" />
            <intent-filter>
              <action android:name="android.intent.action.MAIN" />
              <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
        </application>
      </manifest>
```
- 3.Create a file for your native activity, and implement the function named in the ANativeActivity_onCreate variable. The app calls this function when the native activity starts. This function, analogous to main in C/C++, receives a pointer to an ANativeActivity structure, which contains function pointers to the various callback implementations that you need to write. Set the applicable callback function pointers in ANativeActivity->callbacks to the implementations of your callbacks.

- 4.Set the ANativeActivity->instance field to the address of any instance of specific data that you want to use.

- 5.Implement anything else that you want your activity to do upon starting.

- 6.Implement the rest of the callbacks that you set in ANativeActivity->callbacks. For more information on when the callbacks are called, see Managing the Activity Lifecycle.

- 7.Develop the rest of your application.

- 8.Create an Android.mk file in the jni/ directory of your project to describe your native module to the build system. For more information, see Android.mk.

- 9.Once you have an Android.mk file, compile your native code using the ndk-build command.  

```
$ cd <path>/<to>/<project>
$ <ndk>/ndk-build
```

- 10.Build and install your Android project as usual. If your native code is in the jni/ directory, the build script automatically packages the .so file(s) built from it into the APK.

