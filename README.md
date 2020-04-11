# Privacy Issues in Android

## Introduction

The primary motivation behind enforcing *permissions* in Android is to protect the user's privacy. In this article, we will be looking at some *issues* in this *permission model* and how that can compromise the user's privacy.

## Severity of Issues

We will be categorising the issues as follows:

 1. Low Severity Issue
 2. Medium Severity Issue
 3. High Severity Issue

Here, the 'severity' is defined as "how severely does this issue affect user's privacy". 

'Low severity' (indicated by  ![severity - low](https://img.shields.io/badge/severity-low-blue)) means that the issue has some negative impact on user's privacy but it is OK as it has some legitimate use-case.

'Medium severity' (indicated by ![severity-medium](https://img.shields.io/badge/severity-medium-orange)) means that the issue breaches user's privacy but doesn't amount to spying and can merely be used for analytics by a company.

'High severity' (indicated by ![severity-high](https://img.shields.io/badge/severity-high-red)) means that the issue breaches user's privacy and can be used for spying (like monitoring your body movements and studying your behaviour).

### Note

The issues being listed here are applicable for the current version of Android (Android 11, Android R, API 30) which was released on February 19, 2020.


## List of Issues

 1. **An app can monitor the screen state of your phone without any permissions.** 

![severity-high](https://img.shields.io/badge/severity-high-red)

Android sends a [broadcast](https://developer.android.com/guide/components/broadcasts) whenever the phone's screen turns on/off. So, if an app is running a service (whether it's foreground or a background service), it can receive a broadcast and record when you turn on/off your screen and the app is not required to ask any permissions. In this way, the app can roughly figure out from what time to what time you use your phone and for how many hours you are on the phone everyday. 

This issue's severity is marked as 'high' because this can be used to directly spy on a user and study his/her phone usage pattern.

**Our suggestion to the Android team**:

The screen state broadcasts need to protected with *permissions*.

Related links:
[ACTION_SCREEN_OFF](https://developer.android.com/reference/android/content/Intent#ACTION_SCREEN_OFF) and [ACTION_SCREEN_ON](https://developer.android.com/reference/android/content/Intent#ACTION_SCREEN_ON) broadcasts.

<br>

 2. **An app can monitor when you plug-in and remove your phone charger and headphones without any permissions.**

![severity-low](https://img.shields.io/badge/severity-low-blue)

Android sends a broadcast whenever you plug-in your phone charger or remove it. This is also true for your wired headphones. The apps know at what time your plugged in your charger, at what time your unplugged it and thus how many hours you charged it. And the apps don't need any approval from you to do this.

But, we are marking the severity as 'low' because, this has some legitimate use-cases like, if an app wants to run an important long running task, then it can do so when the phone is charging to make sure the battery is not drained.

But there is a catch here. The apps can get some metadata about your charging. They can know how you are charging your phone, i.e., if you are charging it with your power bank/AC Charger or are you charging it with your phone connected to a laptop/PC through USB cable.

Related links:
[ACTION_POWER_CONNECTED](https://developer.android.com/reference/android/content/Intent#ACTION_POWER_CONNECTED), [ACTION_POWER_DISCONNECTED](https://developer.android.com/reference/android/content/Intent#ACTION_POWER_DISCONNECTED) and [ACTION_HEADSET_PLUG](https://developer.android.com/reference/android/content/Intent#ACTION_HEADSET_PLUG) broadcasts.

<br>

 3. **An app can figure out at what time you switched on/off your phone without any permissions.**

![severity-low](https://img.shields.io/badge/severity-low-blue)

Android sends a broadcast whenever the phone is shutting down. Hence, an app which might be running a service can record at what time you switched off your phone. To start a service on boot, the app requires RECEIVE_BOOT_COMPLETED permission. But, the app can get the device uptime without any permissions and thus know at what time you booted your phone.

We are marking the severity as 'low' because, this feature has some legitimate use cases like a service can properly do the cleanup task and shutdown smoothly when the device is shutting down. But, device uptime is a system information and providing it to all apps without any permissions can lead to some privacy concerns for the user.

Related links:
[ACTION_SHUTDOWN](https://developer.android.com/reference/android/content/Intent#ACTION_SHUTDOWN), [uptimeMillis()](https://developer.android.com/reference/android/os/SystemClock.html#uptimeMillis%28%29).

<br>

 4. **An app can get most of your device related information without any permissions.**

![severity-low](https://img.shields.io/badge/severity-low-blue)

All apps on your phone have access to the device information like the device model, brand, manufacturer, Android version, device uptime etc. without any permissions.

We are marking the severity as 'low' because, this has some legitimate use case. Information about your device can be used by app developers for debugging their apps.

Code example:
```java 
product = Build.PRODUCT;
model = Build.MODEL;
manufacturer = Build.MANUFACTURER;
buildTime = new Date(Build.TIME).toString();
brand = Build.BRAND;
device = Build.DEVICE;
```

Related links:
[Build](https://developer.android.com/reference/android/os/Build) 

<br>

 5. **An app can keep a track of your WiFi/Mobile data usage without any permissions.**

![severity-medium](https://img.shields.io/badge/severity-medium-orange)

All apps on your phone can access your WiFi/Mobile data usage without any permissions. But, the WiFi data usage value might not be accurate sometimes.

We are marking the severity of this as 'medium' because, the apps can use this data for analytics. For example, if your data usage is high, then you probably stream a lot of video content on your phone and ads can be served to you based on that. 

Code example:
```java 
long mobileDataUsed = TrafficStats.getMobileRxBytes() + TrafficStats.getTotalTxBytes();
long totalDataUsed = TrafficStats.getTotalRxBytes() + TrafficStats.getTotalTxBytes();
long wifiDataUsed = totalDataUsed - mobileDataUsed;
```

**Our suggestion to the Android team**:

TrafficStats class should be removed. [NetworkStatsManager](https://developer.android.com/reference/android/app/usage/NetworkStatsManager) is already present in Android. So, it's time that TrafficStats is removed in the future versions of Android.

Related links:
[TrafficStats](https://developer.android.com/reference/android/net/TrafficStats)

<br>

 6. **An app can get a list of all the other apps installed on your phone without any permissions.**

![severity-medium](https://img.shields.io/badge/severity-medium-orange)

An app can get a list of all the other apps installed on your phone without any permissions.

We are marking the severity of this issue as 'medium' because, this data can be used for analytics. For example, Netflix can know if any of it's competitors like Hulu or Amazon Prime Video are installed on your phone. Also, the apps you install on your phone reflect your interests and ads can be shown to you based on that. 

Code example:
```java 
PackageManager pm = activity.getPackageManager();
List<ApplicationInfo> packages = pm.getInstalledApplications(PackageManager.GET_META_DATA);
for (ApplicationInfo packageInfo : packages) {
        String appName = pm.getApplicationLabel(packageInfo).toString();
        Drawable appIcon = pm.getApplicationIcon(packageInfo);
}
```
**Our suggestion to the Android team**:

getInstalledApplications() needs to be protected with *permissions* so that the user can decide whether to allow or disallow an app to use this method.

Related links:
[getInstalledApplications()](https://developer.android.com/reference/android/content/pm/PackageManager#getInstalledApplications%28int%29)

<br>

 7. **Apps installed on your phone can get to know at what angle you are holding your phone, in which direction your phone is facing and can determine if you are moving your phone or not, all without any permissions.**

![severity-high](https://img.shields.io/badge/severity-high-red)

Sensors like Gyroscope (used to detect rotation), Accelerometer (used to detect linear motion) and Magnetometer (used to detect direction) can be accessed by any app without any permissions.

The severity of this issue is marked as 'high' because, any app running a foreground or a background service can continuously monitor and detect even the slightest rotation or movement of your phone and create a 3D visualization of your body movements. This issue can turn any app into a spyware.

**Our suggestion to the Android team**:

In the future versions of Android, the aforementioned sensors need to be protected with permissions just like WiFi, Bluetooth, Camera etc.

Related links:
[Motion sensors](https://developer.android.com/guide/topics/sensors/sensors_motion)


## Proof of concept

You can practically test all these issues on your phone. We have built an open source Android app as a proof-of-concept for this article.

Repository: [PrivacyBreacher](https://github.com/databurn-in/PrivacyBreacher)

## Contribute

If you find any discrepancy, grammatical mistakes, arguments or any rants related to this article, then feel free to open a Github issue.

If you have found new privacy issues or any of the issues listed in this article are already fixed, then please send us a pull request to add that. 

Format to contribute new privacy issues:

> < title or short description of the issue >
> 
> < issue severity badge >
> 
> < description of the issue >
> 
> < reason for why you assigned that particular severity >
>
>< code examples (if any) and related links >


## Author

[Nandan Desai](https://github.com/NandanDesai)

## License
![CC BY-NC-SA license](https://mirrors.creativecommons.org/presskit/buttons/88x31/png/by-nc-sa.png)

This article is published under [Creative Commons BY-NC-SA 4.0 license](https://creativecommons.org/licenses/by-nc-sa/4.0/). "This license lets others remix, tweak, and build upon your work non-commercially, as long as they credit you and license their new creations under the identical terms."
