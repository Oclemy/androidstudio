# Inspect energy use with Energy Profiler

- [Inspect energy use with Energy Profiler](#inspect-energy-use-with-energy-profiler)
  - [Energy Profiler overview](#energy-profiler-overview)
  - [Inspect system events: wake locks, jobs, and alarms](#inspect-system-events-wake-locks-jobs-and-alarms)

The Energy Profiler helps you to find where your app uses more energy than necessary.

The Energy Profiler monitors the use of the CPU, network radio, and GPS sensor, and it displays a visualization of how much energy each of these components uses. The Energy Profiler also shows you occurrences of system events (wake locks, alarms, jobs, and location requests) that can affect energy consumption.

The Energy Profiler does not directly measure energy consumption. Rather, it uses a model that estimates the energy consumption for each resource on the device.

## Energy Profiler overview

The Energy Profiler appears as a row in of the **Profiler** window when you run your app on a connected device or Android Emulator running Android 8.0 (API 26) or higher.

To open the Energy Profiler, follow these steps:

1.  Select **View > Tool Windows > Profiler** or click **Profile** ![](https://developer.android.com/studio/images/buttons/toolbar-android-profiler.png) in the toolbar.

    If prompted by the **Select Deployment Target** dialog, choose the device to which to deploy your app for profiling. If you've connected a device over USB but don't see it listed, ensure that you have [enabled USB debugging](https://developer.android.com/studio/debug/dev-options#enable).

2.  Click anywhere in the **Energy** timeline to open the Energy Profiler.

When you open the Energy Profiler, it immediately starts displaying your app’s estimated energy usage. You should see something similar to figure 1.

![](https://developer.android.com/studio/images/profile/energy-profiler-L1_2x.png)

**Figure 1.** Timelines in the Energy Profiler.

As indicated in figure 1, the default view for the Energy Profiler includes the following timelines:

1.  **Event timeline:** Shows the activities in your app as they transition through different states in their lifecycle. This timeline also indicates user interactions with the device, including screen rotation events.
2.  **Energy timeline:** Shows estimated energy consumption of your app.
3.  **System timeline:** Indicates system events that may affect energy consumption.

To see a breakdown of energy use by CPU, network, and location (GPS) resources, as well as relevant system events, place your mouse pointer over a bar in the **Energy** timeline.

## Inspect system events: wake locks, jobs, and alarms

You can use the Energy Profiler to find system events that can affect energy consumption, including wake locks, jobs, and alarms:

*   A [wake lock](https://developer.android.com/training/scheduling/wakelock) is a mechanism for keeping the CPU or screen on when the device would otherwise go to sleep. For example, an app that plays video may use a wake lock to keep the screen on when there is no user interaction with the device. Requesting a wake lock is not an energy\-intensive action, but failing to release a wake lock can cause the screen or CPU to stay on for longer than necessary, which can drain the battery quickly. For more information, see the guidance for [working with wake locks](https://developer.android.com/topic/performance/vitals/wakelock).

*   You can use [alarms](https://developer.android.com/training/scheduling/alarms) to run background tasks outside of the context of your app at regular intervals. When an alarm goes off, it may wake up the device and run energy\-intensive code. For more information, see the guidance for [working with alarms](https://developer.android.com/topic/performance/vitals/wakeup).

*   You can use [jobs](https://developer.android.com/reference/android/app/job/JobScheduler) to perform actions under specified conditions, such as when the network becomes available. You create jobs with `JobBuilder` and schedule them with `JobScheduler`. In many cases, the recommended approach is to use jobs with `JobScheduler` rather than alarms or wake locks.

*   Location requests use the GPS sensor, which can consume significant amounts of energy. For information on how to make energy\-efficient location requests, see [Optimize location for battery](https://developer.android.com/guide/topics/location/battery).

The Energy Profiler makes it easy to find where your app uses each of these features so that you can make informed decisions about how each is used.

The Energy Profiler shows a color\-coded bar in the **System** timeline below the **Energy** timeline for the time range when a system event is active. Wake locks are represented with a red bar, jobs and alarms are indicated with a yellow bar, and location events are indicated with a light purple bar.

Figure 2 shows the Energy Profiler and the code editor open to the source code responsible for an unreleased wake lock.

![](https://developer.android.com/studio/images/profile/energy-profiler-L2_2x.png)

**Figure 2.** Using the Energy Profiler to find a wake lock.

1.  To open the **System Event** pane and show details for events such as wake locks, select a time range in the **Energy** timeline.
2.  To open the **Wake Lock Details** pane and show details for a specific wake lock, select the wake lock in the **System Event** pane.
3.  To open the code editor and jump to the source code for a wake lock, double\-click the entry for the calling method at the top of the call stack in the **Wake Lock Details** pane.
4.  The call to acquire the wake lock is highlighted in the source editor.

The instructions for showing details for other system events are essentially the same as for wake locks, with information specific to each kind of event in its details pane. For example, the **Job Details** pane shows call stacks for the portions of the code where the job is scheduled and where the job is finished.
