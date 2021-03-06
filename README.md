# Bugsnag Air Native Extension

This is an Adobe Air Native Extension for the [Bugsnag](https://bugsnag.com/) bug reporting platform for iOS and Android applications. This extension allows you to track:

* Native iOS and Android crashes
* Unhandled Actionscript errors
* Manual Actionscript errors/messages

## Version

This extension uses the Bugsnag SDK version 5.15.2 for iOS and SDK version 4.3.0 for Android.


## Requirements

This extension requires iOS 6.0 or higher and Android 2.2 (API level 8) or higher.

## Binary Files

You can find the final compiled ANE binary along with the swc file in the bin folder or on the [releases](https://github.com/DigitalStrawberry/ANE-Bugsnag/releases) page.


## Setup the Extension

### Manifest Setup

When using the extension on the Android platform you'll need to add the following permissions in the app manifest file:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

There is no additional code required for the iOS platform.

### Project Setup

Before you can begin tracking bugs you will need to set up a two new projects on the Bugsnag website. Create a project for iOS and another one for Android tracking. You'll need the API keys for each project.

### Initialize

Start off by initializing the Bugsnag extension with your API key:

```as3
Bugsnag.init("IOS_KEY", "ANDROID_KEY");
```

### Catch Unhandled Errors (Optional)

Bugsnag can catch and report unhandled errors in Actionscript. You'll need access to your app's loaderInfo object do to this. Use the following code:

```as3
loaderInfo.uncaughtErrorEvents.addEventListener(UncaughtErrorEvent.UNCAUGHT_ERROR, Bugsnag.handleUncaughtError);
```

Alternatively you can catch unhandled errors yourself and pass them directly into Bugsnag for reporting:

```as3
Bugsnag.notifyError(event.error);
```

Unhandled errors and crashes in the native code are already set up and do not require any additional code.

### Stack Traces (Optional)

If you'd like Bugsnag to record stack traces for errors (highly recommended), add the following code:

```as3
Bugsnag.getStackTrace = function(error:*):String {
	if(error is Error)
    {
        return Error(error).getStackTrace();
    }
    return null;
}
```

## Using the Extension

### Track Unhandled Actionscript Errors

If you followed the setup instructions to catch unhandled errors, then these errors will be automatically reported to Bugsnag as they occur.

Native exceptions and crashes are reported automatically.

### Track Handled Error

You can easily track handled errors using the following code:

```as3
try
{

}
catch(e:Error)
{
    Bugsnag.notifyError(e);
}
```

You can also pass in a severity value to the method:

```as3
Bugsnag.notifyError(new Error(), Severity.WARN);
```

### Track Manual Errors

You aren't limited to tracking only ```Error``` objects. You can send a generic error:

```as3
Bugsnag.notify("Big Error", "A really big error has occurred");
```

You can also add a severity to it:

```as3
Bugsnag.notify("Big Error", "A really big error has occurred", Severity.INFO);
```

It may be helpful to provide a stacktrace of where the error occurred:

```as3
Bugsnag.notify("Big Error", "A really big error has occurred", Severity.INFO, new Error().getStackTrace());
```

You can also provide additional metadata for errors that will show up in a custom tab on the Bugsnag dashboard. Each metadata object will display as a new tab.

```as3
var metadata:Metadata = new Metadata("Error Details");
metadata.addAttribute("Description", "Something went really wrong");

Bugsnag.notify("Big Error", "A really big error has occurred", Serverity.INFO, new Error().getStackTrace(), new <Metadata>[metadata]);
```

## Additional Settings

### User Data

You can set custom data for users to track their errors:

```as3
Bugsnag.setUser("123456", "user_123456", "user123456@example.com");
```

By default the iOS extension will use the IDFV for the user id. Android will generate and save a random UUID value for the user id.

### Release Stage

You can tell Bugsnag which release stage your app is in to more easily group errors:

```as3
Bugsnag.releaseStage = ReleaseStage.DEVELOPMENT;
```

By default the release stage is set to ```PRODUCTION```.


### Auto Notify

You can enable or disable auto notification of unhandled exceptions:

```as3
Bugsnag.autoNotify = false;
```

### Notify Release Stages

You can also tell Bugsnag to only notify errors in certain production stages:

```as3
Bugsnag.notifyReleaseStages = [ReleaseStage.PRODUCTION];
```

The code above restricts automatic notification to production stages only. Errors in all other production stages will be ignored.

### Custom Data and Tabs

You can add custom data and custom tabs to your error reports. The following code adds custom data to the 'user' tab:

```as3
Bugsnag.addAttribute("City", "New York", "User");
```

You can also remove attributes:

```as3
Bugsnag.removeAttribute("Attribute", "Tab Name");
```

Or even remove entire custom tabs:

```as3
Bugsnag.removeTab("My Tab");
```

Note that custom tabs will show up for every error that you log, including native errors. You should use the ```metadata``` parameter of the ```notify``` method if you only want to include custom data for a single event.

### Context

The context represents the state the application is in before the error. You can set the context:

```as3
Bugsnag.context = "Store";
```

### Offline Mode

If an error occurs while the user is offline, the error will be queued until network connectivity is re-established. 


## Further Documentation

You can learn more about the inner workings of the Bugsnag platform using the following resources:

* [Bugsnag Documentation](https://bugsnag.com/docs/notifiers)
* [Bugsnag iOS SDK](https://bugsnag.com/docs/notifiers/cocoa)
* [Bugsnag Android SDK](https://bugsnag.com/docs/notifiers/android)
* [Bugsnag API](https://bugsnag.com/docs/notifier-api)


## Compiling from Source

You can compile the Air Native Extension from source by renaming the build/sample-build.conf file to build/build.conf, then changing the path settings to match your development enviroment. Then run the ant command in the build folder to build the entire project.

There are also IntelliJ IDEA project files included to make editing and testing the Java and Actionscript easier.


## Special Thanks

Special thanks to the [StickSports](https://github.com/StickSports/) ANEs for the initial build script and testing code and the [ANEZipFile](https://github.com/xperiments/ANEZipFile) project for the FRETypeUtils code for iOS.
