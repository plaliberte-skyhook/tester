# Skyhook Location SDK (Android)

## Prerequisites

Supported on all Android versions starting with 2.3.x (Gingerbread).

## Installation

### Add SDK to your project

Add Skyhook's Maven repository URL to the `repositories` section in your `build.gradle`:
```gradle
repositories {
    maven { url 'https://skyhookwireless.github.io/skyhook-location-android' }
}
```

Add SDK to the `dependencies` section:
```gradle
dependencies {
    implementation 'com.skyhook.location:location-sdk-android:5.2+'
}
```

Note that you can exclude transitive dependencies to resolve version conflicts, and include those dependencies separately:
```gradle
implementation 'com.android.support:appcompat-v7:28.0.0'
implementation('com.skyhook.location:location-sdk-android:5.2+') {
    exclude module: 'support-v4'
}
```

### Permissions

Location SDK automatically adds the following permissions to your app's manifest:

| Android Permission                         | Used For
|--------------------------------------------|-----------------------------------------------------------
| android.permission.INTERNET                | Communication with Skyhook's servers
| android.permission.CHANGE_WIFI_STATE       | Initiation of Wi-Fi scans
| android.permission.ACCESS_WIFI_STATE       | Obtaining information about the Wi-Fi environment
| android.permission.ACCESS_COARSE_LOCATION  | Obtaining Wi-Fi or cellular based locations
| android.permission.ACCESS_FINE_LOCATION    | Accessing GPS location for hybrid location functionality
| android.permission.WAKE_LOCK               | Keeping processor awake when receiving background updates
| android.permission.ACCESS_NETWORK_STATE    | Checking network connection type to optimize performance

### Background Mode

Starting with **Android Oreo**, the system enforces significant limitations in location determination capabilities when the app is running in background, for power consumption and user privacy considerations.

In order to use the Skyhook Location SDK in background mode, a regular app has to provide general means of background execution - e.g. by running a foreground service, or setting up a periodic alarm. A foreground service is required to achieve location update rates faster than once every 30 minutes.

Add the following service declaration in your manifest if you are planning to use the Location SDK in background without running a foreground service:
```xml
<service
    android:name="com.skyhookwireless.spi.network.NetworkJobService"
    android:permission="android.permission.BIND_JOB_SERVICE"
    android:exported="false"/>
```

The following receiver declaration is recommended (but not required) if your app's minimum supported Android version is below **API 24 (Nougat)**:
```xml
<receiver
    android:name="com.skyhookwireless.spi.power.AlarmReceiver"
    android:exported="false"/>
```

If your app is targeting **API level 29 (Android 10)** or higher and you are willing to determine location in background, add the following permission to your manifest file:

| Android Permission                            | Used For
|-----------------------------------------------|----------------------------------
| android.permission.ACCESS_BACKGROUND_LOCATION | Determine location in background

### Using the Android Emulator

The Precision Location SDK will not be able to determine location using Wi-Fi or cellular beacons from the emulator because it is unable to scan for those signals. Because of that, its functionality will be limited on the emulator. In order to verify your integration of the SDK using the emulator, you may want to use the `getIPLocation()` method call. The full functionality will work only on an Android device.

### Android System Settings

The Skyhook SDK will respect the system setting with regard to location. This includes the granular location settings for GPS and network based location. The SDK will return `WPS_ERROR_LOCATION_SETTING_DISABLED` if location cannot be determined because of the current location setting. XPS will continue to determine location when only the GPS location setting is enabled whereas WPS requires that network based location is enabled.

## Initializing

### Import the SDK

Import the Skyhook WPS package:
```java
import com.skyhookwireless.wps;
```

### Initialize API

Create an instance of [XPS](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/XPS.html) (or [WPS](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html)) API object in the `onCreate` method of your activity or service:
```java
private IWPS xps;
...
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    xps = new XPS(this);
    ...
}
```

Call [setKey()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#setKey-java.lang.String-) to initialize the API with your key:
```java
xps.setKey("YOUR KEY");
```

You can obtain the API key from [my.skyhook.com](https://my.skyhook.com), creating an account and a new Precision Location project.

### Enable tiling mode

If you are planning to request location determination frequently, it is recommended to enable the tiling mode in the SDK. It downloads, from the server, a small portion of the database so the device can automonously determine its location, without further need to contact the server.

This mode is activated by calling [setTiling()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#setTiling-java.lang.String-long-long-com.skyhookwireless.wps.TilingListener-):
```java
File tileDir = new File(getFilesDir(), "tiles");

xps.setTiling(
    tileDir.getAbsolutePath(),  // directory where to store tiles
    9 * 50 * 1024,              // 3x3 tiles (~50KB each) for each session
    9 * 50 * 1024 * 10,         // total size is 10x times the session size
    null);
```

### Request location permission

With the runtime permissions model introduced in **Android M**, the Precision Location SDK requires location permission to be granted before calling most of its methods. Depending on how the SDK is used in the application, developer can decide when to request the permission and if an explanation needs to be displayed for the user:
```java
ActivityCompat.requestPermissions(
    this, new String[] { Manifest.permission.ACCESS_FINE_LOCATION }, 0);
```

In order to determine location in background on **Android 10** or higher, make sure to check that the user has granted the "Allow all the time" (`ACCESS_BACKGROUND_LOCATION`) location permission.

## Location API

### One-time location request

The one-time location request provides a method to make a single request for location: [getLocation()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#getLocation-com.skyhookwireless.wps.WPSAuthentication-com.skyhookwireless.wps.WPSStreetAddressLookup-boolean-com.skyhookwireless.wps.WPSLocationCallback-).

The request will call the callback method. The [handleWPSLocation()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPSLocationCallback.html#handleWPSLocation-com.skyhookwireless.wps.WPSLocation-) method is passed the [WPSLocation](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPSLocation.html) object which will contain the location information.

A one-time location request call from your application would look like this:
```java
xps.getLocation(null, WPSStreetAddressLookup.WPS_NO_STREET_ADDRESS_LOOKUP, false, new WPSLocationCallback() {
    @Override
    public void handleWPSLocation(WPSLocation location) {
        // Do something with location
    }

    @Override
    public WPSContinuation handleError(WPSReturnCode error) {
        // ...

        // To retry the location call on error use WPS_CONTINUE,
        // otherwise return WPS_STOP
        return WPSContinuation.WPS_CONTINUE;
    }

    @Override
    public void done() {
        // after done() returns, you can make more WPS calls
    }
});
```

You can also request a street address or time zone lookup with this method.

### Periodic location

A request for periodic location updates can be made using the following method: [getPeriodicLocation()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#getPeriodicLocation-com.skyhookwireless.wps.WPSAuthentication-com.skyhookwireless.wps.WPSStreetAddressLookup-boolean-long-int-com.skyhookwireless.wps.WPSPeriodicLocationCallback-).

This method will continue running for the specified number of iterations or until the user stops the request by returning [WPS_STOP](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPSContinuation.html#WPS_STOP) from the callback.

It is highly recommended to enable [tiling mode](#enable-tiling-mode) with this location method to eliminate frequent network requests to the server. Note that if street address or time zone lookup is requested, this call will **not use the tiling cache** to determine locations.

Starting with **Android Pie** the recommended minimum period for location updates is **30 seconds or longer**.

### Offline location

The API supports offline location that allows the application to determine the location of the device even offline and outside of tile coverage by collecting a token that can be replayed when the device is once again online. Offline tokens are only valid for 90 days after they are generated. Attempting to redeem a token more than 90 days old will result in an error.

* [getOfflineToken()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#getOfflineToken-com.skyhookwireless.wps.WPSAuthentication-byte:A-)
* [getOfflineLocation()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#getPeriodicLocation-com.skyhookwireless.wps.WPSAuthentication-com.skyhookwireless.wps.WPSStreetAddressLookup-boolean-long-int-com.skyhookwireless.wps.WPSPeriodicLocationCallback-)

### Abort

When your app is terminating, it is recommended to cancel any ongoing operations by invoking the [abort()](https://skyhookwireless.github.io/skyhook-location-android/javadoc/com/skyhookwireless/wps/WPS.html#abort--) method:
```java
public void onDestroy() {
    super.onDestroy();
    xps.abort();
    ...
}
```

## API reference

Check the [full API reference](https://skyhookwireless.github.io/skyhook-location-android/javadoc) for more information on APIs that are exposed by the SDK.

## Logging

To turn logging on, add the following code in the `onCreate()` method of your activity, service or application:
```java
SharedPreferences.Editor skyhookPrefs = getSharedPreferences("skyhook", MODE_PRIVATE).edit();
skyhookPrefs.putBoolean("com.skyhook.wps.LogEnabled", true);
skyhookPrefs.apply();
```

By default, the SDK will output log messages to Android logcat.

If you want the SDK to write log to a file, set the following preferences:
```java
skyhookPrefs.putBoolean("com.skyhook.wps.LogEnabled", true);
skyhookPrefs.putString("com.skyhook.wps.LogType", "BUILT_IN,FILE");
skyhookPrefs.putString("com.skyhook.wps.LogFilePath", "/sdcard/wpslog.txt");
skyhookPrefs.apply();
```

If you want to write the log file to external storage, make sure to obtain the `WRITE_EXTERNAL_STORAGE` permission in your app.
