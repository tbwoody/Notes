Android gives you the freedom to implement your own device specifications and drivers. The hardware abstraction layer (HAL) provides a standard method for creating software hooks between the Android platform stack and your hardware. The Android operating system is also open source, so you can contribute your own interfaces and enhancements.

To ensure devices maintain a high level of quality and offer a consistent user experience, each device must pass tests in the compatibility test suite (CTS). The CTS verifies devices meet a quality standard that ensures apps run reliably and users have a good experience. For details on the CTS, see Compatibility.

Before porting Android to your hardware, take a moment to understand the Android system architecture at a high level. Because your drivers and the HAL interact with Android, knowing how Android works can help you navigate 

![Android System Architecture] (/images/AndroidArchitecture.png)     
Figure 1. Android System Architecture
