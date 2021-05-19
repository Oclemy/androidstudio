# Capture and read bug reports

- [Capture and read bug reports](#capture-and-read-bug-reports)
  - [Capture a bug report from a device](#capture-a-bug-report-from-a-device)
  - [Capture a bug report from the Android Emulator](#capture-a-bug-report-from-the-android-emulator)
  - [Capture a bug report using adb](#capture-a-bug-report-using-adb)
  - [Inspect the bug report ZIP file](#inspect-the-bug-report-zip-file)
  - [Get reports from your users](#get-reports-from-your-users)
    - [Google Play Console](#google-play-console)
    - [Firebase crash reporting](#firebase-crash-reporting)

A bug report contains device logs, stack traces, and other diagnostic information to help you find and fix bugs in your app. You can capture a bug report from your device by using either the **Take bug report** developer option on the device, the Android Emulator menu, or the `adb bugreport` command on your development machine.

![](https://developer.android.com/studio/images/run/dev-options-pixel_2x_cropped.png)

**Figure 1.** Developer options

To take a bug report, you must have [Developer options](https://developer.android.com/studio/debug/dev-options) enabled on your device so you can access the **Take bug report** option.

## Capture a bug report from a device

![](https://developer.android.com/studio/images/run/dev-options-take-bug-report_2x.png)

**Figure 2.** The bug report is ready

To get a bug report directly from your device, do the following:

1.  Be sure you have [Developer Options](https://developer.android.com/studio/debug/dev-options) enabled.
2.  In **Developer options**, tap **Take bug report**.
3.  Select the type of bug report you want and tap **Report**.

    After a moment you get a notification that the bug report is ready (see figure 2).

4.  To share the bug report, tap the notification.

## Capture a bug report from the Android Emulator

From the Android Emulator, you can use the **File a bug** feature in the extended controls:

1.  Click **More** ![](https://developer.android.com/studio/images/buttons/emulator-extended-controls.png) in the emulator panel.
2.  In the **Extended controls** window, select **Bug report** on the left.

    This opens a screen where you can see the bug report details such as the screenshot, the AVD configuration info, and the bug report log. You can also type a message with reproduction steps to save with the report.

3.  Wait for the bug report to finish collecting, and then click **Save Report**.

## Capture a bug report using adb

If you have just one device connected, you can get a bugreport using [`adb`](https://developer.android.com/studio/command-line/adb) as follows:

$ adb bugreport E:\\Reports\\MyBugReports

If you do not specify a path for the bugreport, it is saved to the local directory.

If you have multiple devices connected, you must specify the device with the `-s` option. Run the following `adb` commands to get the device serial number and generate the bug report.

$ adb devices
List of devices attached
emulator\-5554      device
8XV7N15C31003476 device

$ adb \-s 8XV7N15C31003476 bugreport

## Inspect the bug report ZIP file

By default the ZIP file is called `bugreport-BUILD_ID-DATE.zip` and it may contain multiple files, but the most important file is `bugreport-BUILD_ID-DATE.txt`. This is the bug report and it contains diagnostic output for system services (`dumpsys`), error logs (`dumpstate`), and system message logs (`logcat`). The system messages include stack traces when the device throws an error, and messages written from all apps with the [`Log`](https://developer.android.com/reference/android/util/Log) class.

The ZIP file contains a `version.txt` metadata file that contains the Android release letter, and when systrace is enabled, the ZIP file also contains a `systrace.txt` file. The [Systrace tool](https://developer.android.com/topic/performance/tracing/command-line) helps analyze the performance of your application by capturing and displaying execution times of your application processes and other Android system processes.

The `dumpstate` tool copies files from the device’s filesystem into the ZIP file under the `FS` folder so you can reference them. For example, a `/dirA/dirB/fileC` file in the device would generate an `FS/dirA/dirB/fileC` entry in the ZIP file.

![](https://developer.android.com/studio/images/debug/capture-and-read-bug-report_2x.png)

**Figure 3.** Bug report file structure

For more information, see [Reading bug reports](https://source.android.com/source/read-bug-reports.html).

## Get reports from your users

Capturing bug reports as described above is helpful as you're using the app yourself, but your end\-users can't easily share these types of bug reports with you. To get crash reports with stack traces from real\-world users, you should take advantage of Google Play's and Firebase's crash reporting features.

### Google Play Console

You can get reports from the [Google Play Console](https://play.google.com/console/) to view data for crashes and application not responding (ANR) errors from users who installed your app from Google Play. Data is available for the previous six months.

For more information, see [View crashes & application not responding (ANR) errors](https://support.google.com/googleplay/android-developer/answer/6083203?hl=en&ref_topic=7071935) in Play Console help.

### Firebase crash reporting

Firebase Crashlytics reporting creates detailed reports of the errors in your app. Errors are grouped into issues based on having similar stack traces, and triaged by the severity of impact on your users. In addition to automatic reports, you can log custom events to help capture the steps leading to a crash.

You'll start receiving crash reports from any user by simply [adding the Firebase dependencies](https://firebase.google.com/docs/crashlytics/get-started?platform=android) to your `build.gradle` file. For more information, see [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics/).
