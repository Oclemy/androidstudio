# Measure app performance with Android Profiler

- [Measure app performance with Android Profiler](#measure-app-performance-with-android-profiler)
  - [Sessions](#sessions)
  - [Enable advanced profiling](#enable-advanced-profiling)
  - [Run standalone profilers](#run-standalone-profilers)

The Android Profiler in Android Studio 3.0 and higher replaces the Android Monitor tools. The Android Profiler tools provide real\-time data to help you to understand how your app uses CPU, memory, network, and battery resources.

For details about each of the profilers, see the following:

*   [Inspect CPU activity and traces with CPU Profiler](https://developer.android.com/studio/profile/cpu-profiler)
*   [Inspect the Java heap and memory allocations with Memory Profiler](https://developer.android.com/studio/profile/memory-profiler)
*   [Inspect network traffic with Network Profiler](https://developer.android.com/studio/profile/network-profiler)
*   [Inspect energy usage with Energy Profiler](https://developer.android.com/studio/profile/energy-profiler)

The Android Profiler is compatible with Android 5.0 (API level 21) and higher.

See the following video for an introduction to the Android Profiler.

To open the **Profiler** window, choose **View > Tool Windows > Profiler** or click **Profile** ![](https://developer.android.com/studio/images/buttons/toolbar-android-profiler.png) in the toolbar. If prompted by the **Select Deployment Target** dialog, select the device on which to profile your app. If you've connected a device over USB but don't see it listed, ensure that you have [enabled USB debugging](https://developer.android.com/studio/debug/dev-options#enable). If you're using the Android Emulator or a rooted device, the Android Profiler lists all running processes, even though they might not be debuggable. When you launch a debuggable app, that process is selected by default.

Android Profiler continues to collect profiling data until you disconnect the device or click **End Session**.

![](https://developer.android.com/studio/images/profile/android-profiler-callouts_2x.png)

**Figure 1.** Android Profiler shared timeline view

1 Android Profiler shows the process and device currently being profiled.

2 In the **Sessions** pane, choose which session to view, or start a new profiling session.

3 Use the zoom buttons to control how much of the timeline to view, or use the **Attach to live** button to jump to the real\-time updates.

4 The event timeline shows events related to user input, including keyboard activity, volume control changes, and screen rotations.

5 The shared timeline view, which includes graphs for CPU, memory, network, and energy usage.

This shared timeline view only shows the timeline graphs. To access the detailed profiling tools, click the graph that corresponds to the performance data that you want to inspect. For example, to access tools to inspect the heap and track memory allocations, click the **Memory** graph.

Not all profiling data is visible by default. If you see a message that says "Advanced profiling is unavailable for the selected process," you can [enable advanced profiling](#advanced-profiling) in your run configuration to view the additional data.

## Sessions

You can save Profiler data as *sessions*, which are retained until you quit Android Studio. By recording profiling information in multiple sessions and switching between them, you can compare resource usage in various scenarios.

*   To start a new session, click the **Start a new profiling session** ![](https://developer.android.com/studio/images/buttons/ic_plus.png) button and select an app process from the dropdown menu that appears.

    When you [record a trace](https://developer.android.com/studio/profile/cpu-profiler#method_traces) or [capture a heap dump](https://developer.android.com/studio/profile/memory-profiler#capture-heap-dump), Android Studio adds that data (along with your app's network activity) as a separate entry to the current session.

*   To stop adding data to the current session, click **Stop the current profiling session** ![](https://developer.android.com/studio/images/profile/ic_red_stop.png).

*   To import a trace exported from a previous run of Android Studio, click **Start new profiler session** ![](https://developer.android.com/studio/images/buttons/ic_plus.png) and choose **Load from file**.

## Enable advanced profiling

To show you advanced profiling data when running a device with Android 7.1 or lower, Android Studio must inject monitoring logic into your compiled app. Features provided by advanced profiling include the following:

*   The event timeline on all profiler windows
*   The number of allocated objects in Memory Profiler
*   Garbage collection events in Memory Profiler
*   Details about all transmitted files in Network Profiler

**Note:** These features are available by default if your device is running Android 8.0 or higher.

To enable advanced profiling, follow these steps:

1.  Select **Run > Edit Configurations**.
2.  Select your app module in the left pane.
3.  Click the **Profiling** tab, and then check **Enable advanced profiling for older devices**.
4.  Build and run your app again.

The advanced profiling configuration makes the build process slower, so you should enable it only when you want to start profiling your app.

**Note:** Advanced profiling is not available for native code on devices using Android 9 or lower. If your app is a pure native app targeting Android 9 or lower (i.e., it does not have a Java `Activity` class), advanced profiling features are not available. If your app uses JNI, some advanced profiling features are available—such as the event timeline, garbage collection events, Java\-allocated objects, and Java\-based network activity—but it cannot detect native\-based allocations and network activity.

## Run standalone profilers

The standalone Android Studio Profilers allow you to profile your app without running the full Android Studio IDE.

To run the standalone profilers, do the following:

1.  Make sure the profiler is not currently running inside of Android Studio.
2.  Go to the installation directory and navigate to the `bin` directory:

    **Windows/Linux**: `<studio-installation-folder>/bin`

    **macOS**: `<studio-installation-folder>/Contents/bin`

3.  Depending on your OS, run `profiler.exe` or `profiler.sh`. The Android Studio splash screen appears.

    After the splash screen disappears, a profiler window opens: ![](https://developer.android.com/studio/images/profile/standalone-profilers.png)

4.  Start the Android emulator or connect an Android device and wait for the home screen to load. To run the emulator from the command line, see [Start the emulator from the command line](https://developer.android.com/studio/run/emulator-commandline). If you start the emulator from Android Studio, be sure to close Android Studio after the emulator has started.

    In the menu of the standalone profiler, click the ![](https://developer.android.com/studio/images/profile/standalone-profilers-plus.png) button and all of your connected devices and emulators should now be displayed:

    ![](https://developer.android.com/studio/images/profile/standalone-profilers-emulators.png)

    As an example, open Google Maps in the emulator. Create a new profiling session by selecting an emulator from the dropdown, and then select **com.google.android.apps.maps (...)**. This launches a profiling session.

    ![](https://developer.android.com/studio/images/profile/standalone-profilers-session.png)

When you interact with the map, touch events and CPU usage appear in the profiler. Click on the CPU, Memory, Network, or Energy graphs to show more details.

Click the ![](https://developer.android.com/studio/images/profile/standalone-profilers-stop.png) button to end the profiling session.
