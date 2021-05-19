# Inspect CPU activity with CPU Profiler

- [Inspect CPU activity with CPU Profiler](#inspect-cpu-activity-with-cpu-profiler)
  - [CPU Profiler overview](#cpu-profiler-overview)
  - [Record traces](#record-traces)
    - [Choose a recording configuration](#choose-a-recording-configuration)
    - [Create, edit, or view a recording configuration](#create-edit-or-view-a-recording-configuration)
    - [Record CPU activity with the Debug API](#record-cpu-activity-with-the-debug-api)
    - [Record CPU activity during app startup](#record-cpu-activity-during-app-startup)
  - [Export traces](#export-traces)
  - [Import traces](#import-traces)
  - [Inspect traces](#inspect-traces)
    - [Inspect traces using the Call Chart](#inspect-traces-using-the-call-chart)
    - [Inspect traces using the Flame Chart tab](#inspect-traces-using-the-flame-chart-tab)
    - [Inspect traces using Top Down and Bottom Up](#inspect-traces-using-top-down-and-bottom-up)
    - [Inspect system traces](#inspect-system-traces)
    - [Inspect frame rendering data](#inspect-frame-rendering-data)

Optimizing your app’s CPU usage has many advantages, such as providing a faster and smoother user experience and preserving device battery life.

You can use the CPU Profiler to inspect your app’s CPU usage and thread activity in real time while interacting with your app, or you can inspect the details in recorded method traces, function traces, and system traces.

The specific kinds of information that the CPU Profiler records and shows are determined by which recording configuration you choose:

*   **System Trace:** Captures fine\-grained details that allow you to inspect how your app interacts with system resources.
*   **Method and function traces:** For each thread in your app process, you can find out which methods (Java) or functions (C/C++) are executed over a period of time and the CPU resources each method or function consumes during its execution. You can also use method and function traces to identify *callers* and *callees*. A caller is a method or function that invokes another method or function, and a callee is one that is invoked by another method or function. You can use this information to determine which methods or functions are responsible for invoking particular resource\-heavy tasks too often and optimize your app’s code to avoid unnecessary work.

    When recording method traces, you can choose *sampled* or *instrumented* recording. When recording function traces, you can only use sampled recording.

For details of using and choosing each of these trace options, see [Choose a recording configuration](#configurations).

## CPU Profiler overview

To open the CPU Profiler, follow these steps:

1.  Select **View > Tool Windows > Profiler** or click **Profile** ![](https://developer.android.com/studio/images/buttons/toolbar-android-profiler_dark.png) in the toolbar.

    If prompted by the **Select Deployment Target** dialog, choose the device to which to deploy your app for profiling. If you've connected a device over USB but don't see it listed, ensure that you have [enabled USB debugging](https://developer.android.com/studio/debug/dev-options#enable).

2.  Click anywhere in the **CPU** timeline to open the CPU Profiler.

When you open the CPU Profiler, it immediately starts displaying your app’s CPU usage and thread activity. You should see something similar to figure 1.

![](https://developer.android.com/studio/images/profile/cpu_profiler_L2-2X.png)

**Figure 1.** Timelines in the CPU Profiler.

As indicated in figure 1, the default view for the CPU Profiler includes the following timelines:

1.  **Event timeline:** Shows the activities in your app as they transition through different states in their lifecycle, and indicates user interactions with the device, including screen rotation events. For information on enabling the event timeline on devices running Android 7.1 (API level 25) and lower, see [Enable advanced profiling](https://developer.android.com/studio/profile/android-profiler#advanced-profiling).
2.  **CPU timeline:** Shows real\-time CPU usage of your app—as a percentage of total available CPU time—and the total number of threads your app is using. The timeline also shows the CPU usage of other processes (such as system processes or other apps), so you can compare it to your app’s usage. You can inspect historical CPU usage data by moving your mouse along the horizontal axis of the timeline.
3.  **Thread activity timeline:** Lists each thread that belongs to your app process and indicates their activity along a timeline using the colors listed below. After you record a trace, you can select a thread from this timeline to inspect its data in the trace pane.

    *   **Green:** The thread is active or is ready to use the CPU. That is, it's in a running or runnable state.
    *   **Yellow:** The thread is active, but it’s waiting on an I/O operation, such as disk or network I/O, before it can complete its work.
    *   **Gray:** The thread is sleeping and is not consuming any CPU time. This sometimes occurs when the thread requires access to a resource that is not yet available. Either the thread goes into voluntary sleep, or the kernel puts the thread to sleep until the required resource becomes available.

    The CPU Profiler also reports CPU usage of threads that Android Studio and the Android platform add to your app process—such as `JDWP`, `Profile Saver`, `Studio:VMStats`, `Studio:Perfa`, and `Studio:Heartbeat` (although, the exact names displayed in the thread activity timeline may vary). Android Studio reports this data so that you can identify when thread activity and CPU usage are actually caused by your app’s code.

## Record traces

To start recording a trace, [choose a recording configuration](#configurations) from either of the dropdown menus above or below the CPU Profiler and click **Record**.

![](https://developer.android.com/studio/images/profile/cpu-profiler.png)

**Figure 2.** The CPU Profiler displays the status, duration, and type of recording being made.

Interact with your app, and then click **Stop** when you're done. The profiler automatically displays its tracing information in the trace pane, as shown in figure 3:

![](https://developer.android.com/studio/images/profile/sample-java-methods.png)

**Figure 3.** The CPU Profiler after recording a method trace.

1.  **Selected range:** Determines the portion of the recorded time to inspect in the trace pane. When you first record a trace, the CPU Profiler automatically selects the entire length of your recording in the CPU timeline. To inspect trace data for only a portion of the recorded time range, drag the edges of the highlighted region.
2.  **Interaction section:** Displays user interaction and app lifecycle events along a timeline.
3.  **Threads section:** Displays thread state activity (such as running, sleeping, etc.) and **Call Chart** (or trace event chart in System Trace) for every thread along a timeline.
    *   Use [mouse and keyboard shortcuts](#ui-shortcuts) to navigate the timeline.
    *   Double\-click the thread name or press Enter while a thread is selected to expand or collapse a thread.
    *   Select a thread to see additional information in the Analysis pane. Hold Shift or Ctrl (Command on Mac) to select multiple threads.
    *   Select a method call (or trace event in System Trace) to see additional information in the Analysis pane.
4.  **Analysis pane:** Displays trace data for the time range and thread or method call you have selected. In this pane, you can select how to view each stack trace (using the analysis tabs) and how to measure execution time (using the time reference dropdown menu).
5.  **Analysis pane tabs:** Choose how to display trace details. For details about each option, see [Inspect traces](#inspect-traces).
6.  **Time reference menu:** Select one of the following to determine how timing information for each call is measured (only supported in **Sample/Trace Java Methods**):
    *   **Wall clock time:** Timing information represents actual elapsed time.
    *   **Thread time:** Timing information represents actual elapsed time minus any portion of that time when the thread is not consuming CPU resources. For any given call, its thread time is always less than or equal to its wall clock time. Using thread time gives you a better understanding of how much of a thread’s actual CPU usage is consumed by a given method or function.
7.  **Filter:** Filters trace data by function, method, class, or package name. For example, if you want to quickly identify trace data related to a specific call, type the name in the search field. In the **Flame chart** tab, call stacks that include a call, package, or class that matches the search query are emphasized. In the **Top down** and **Bottom up** tabs, those call stacks are prioritized over other trace results. You can also enable the following options by checking the appropriate box next to the search field:
    *   **Regex:** To include regular expressions in your search, use this option.
    *   **Match case:** If your search is case\-sensitive, use this option.

**Tip:** When inspecting the **Threads** timeline, the following shortcuts are available:

*   **Zoom in:** Press W or scroll the mouse wheel while holding Ctrl (Command on Mac).
*   **Zoom out:** Press S or scroll the mouse wheel backward while holding Ctrl (Command on Mac).
*   **Pan left:** Press A or drag mouse right while holding Space.
*   **Pan right:** Press D or drag mouse left while holding Space.
*   **Expand or collapse a thread:** Double\-click the thread name or press Enter while a thread is selected.

### Choose a recording configuration

Before you begin recording trace information, choose the appropriate recording configuration for the profiling information that you want to capture:

*   **Sample Java Methods:** Captures your app’s call stack at frequent intervals during your app’s Java\-based code execution. The profiler compares sets of captured data to derive timing and resource usage information about your app’s Java\-based code execution.

    An inherent issue of sampled\-based tracing is that if your app enters a method after a capture of the call stack and exits the method before the next capture, that method call is not logged by the profiler. If you are interested in tracing methods with such short lifecycles, you should use instrumented tracing.

*   **Trace Java Methods:** Instruments your app at runtime to record a timestamp at the beginning and end of each method call. Timestamps are collected and compared to generate method tracing data, including timing information and CPU usage.

    Note that the overhead associated with instrumenting each method impacts runtime performance and may influence profiling data; this is even more noticeable for methods with relatively short lifecycles. Additionally, if your app executes a large number of methods in a short time, the profiler may quickly exceed its file size limit and may not be able to record any further tracing data.

*   **Sample C/C++ Functions:** Captures sampled traces of your app’s native threads. To use this configuration, you must deploy your app to a device running Android 8.0 (API level 26) or higher.

    Internally, this configuration uses `[simpleperf](https://developer.android.com/ndk/guides/simpleperf)` to trace your app's native code. If you want to specify additional options for `simpleperf`, such as sampling specific device CPUs or specifying sampling durations at a high accuracy, you can [use `simpleperf` from the command line](https://developer.android.com/ndk/guides/simpleperf-commands).

*   **Trace System Calls:** Captures fine\-grained details that allow you to inspect how your app interacts with system resources. You can inspect exact timings and durations of your thread states, visualize where your CPU bottlenecks are across all cores, and add custom trace events to analyze. Such information can be crucial when you are troubleshooting performance issues. To use this configuration, you must deploy your app to a device running Android 7.0 (API level 24) or higher.

    While using this trace configuration, you can visually mark important code routines in the profiler timeline by instrumenting your code. To instrument C/C++ code, use the [native tracing API](https://developer.android.com/ndk/guides/tracing) provided by `trace.h`. To instrument Java code, use the [`Trace`](https://developer.android.com/reference/android/os/Trace) class. For more information, see [Instrument your app code](https://developer.android.com/studio/command-line/systrace#app-trace).

    This trace configuration is built on `systrace`. You can [use the `systrace` command\-line utility](https://developer.android.com/studio/command-line/systrace) to specify options beyond those provided in the CPU Profiler. The additional system\-level data provided by `systrace` can help you to inspect native system processes and troubleshoot dropped or delayed frames.

    On devices running Android 9 (API level 28) or higher, you can use a system app called *System Tracing* to [record system traces on a device](https://developer.android.com/topic/performance/tracing/on-device).

### Create, edit, or view a recording configuration

You create, edit, and view recording configurations in the **CPU Recording Configurations** dialog, which you open by selecting **Edit configurations** from the recording configurations dropdown menu at the top of the CPU Profiler.

To view the settings of an existing recording configuration, select it in the left pane of the **CPU Recording Configurations** dialog.

To create a new recording configuration, do the following:

1.  Click **Add** ![](https://developer.android.com/studio/images/buttons/ic_plus.png) at the top\-left of the dialog. This creates a new configuration with some default settings.
2.  Name your configuration.
3.  Select a **Trace Technology**.
4.  For sampled recording configurations, specify the **Sampling interval** in microseconds (μs). This value represents the time between each sample of your app’s call stack. The shorter the interval you specify, the faster you reach the file size limit for the recorded data.
5.  Specify the **File size limit**, in megabytes (MB), for the recorded data that’s written to the connected device. When you stop recording, Android Studio parses this data and displays it in the profiler window. So, if you increase the limit and record a large amount of data, Android Studio takes much longer to parse the file and may become unresponsive.

    **Note:** If you use a connected device running Android 8.0 (API level 26) or higher, there is no limit on the file size of the trace data, and this value is ignored. However, you still need to be careful about how much data the device collects after each recording—Android Studio may have difficulty parsing large trace files. For example, if you’re recording either a sampled trace with a short sampling interval or an instrumented trace while your app calls many methods in a short time, you’ll generate large trace files quickly.

6.  To accept the changes and continue making changes to other configurations, click **Apply**. To accept all applied changes and close the dialog, click **OK**.

### Record CPU activity with the Debug API

You can use the [`Debug` API](https://developer.android.com/reference/android/os/Debug) to give your app the ability to start and stop the recording of CPU activity in the CPU Profiler.

The CPU Profiler begins recording when your app calls [`startMethodTracing(String tracePath)`](https://developer.android.com/reference/android/os/Debug#startMethodTracing(java.lang.String)), and the CPU Profiler stops recording when your app calls [`stopMethodTracing()`](https://developer.android.com/reference/android/os/Debug#stopMethodTracing()). While recording CPU activity that’s triggered using this API, the CPU profiler shows **Debug API** as the active CPU recording configuration.

To control the recording of CPU activity with the `Debug` API, deploy your instrumented app to a device running Android 8.0 (API level 26) or higher.

**Important:** The `Debug` API is intended to be used separately from other means for starting and stopping the recording of CPU activity, such as the buttons in the CPU Profiler graphical user interface and the setting in the recording configuration for automatic recording on app startup.

For more information, see [Generate trace logs by instrumenting your app](https://developer.android.com/studio/profile/generate-trace-logs).

### Record CPU activity during app startup

To begin recording CPU activity automatically during app startup, do the following:

1.  Select **Run > Edit Configurations**.
2.  In the **Profiling** tab, check the box next to **Start recording a method trace on startup**.
3.  Select a CPU recording configuration from the menu.
4.  Click **Apply**.
5.  Deploy your app to a device running Android 8.0 (API level 26) or higher by selecting **Run > Profile**.

## Export traces

After you record CPU activity with the CPU profiler, you can export the data as a `.trace` file to share with others or inspect later.

To export a trace file from the CPU timeline, do the following:

1.  In the CPU timeline, right\-click the recorded method trace or system trace that you want to export.
2.  Select **Export trace** from the menu.
3.  Browse to where you want to save the file, specify the file name, and click **OK**.

To export a trace file from the **Sessions** pane, do the following:

1.  In the **Sessions** pane, right\-click the recorded trace that you want to export.
2.  Click the **Export method trace** or **Export system trace** button at the right of the session entry.
3.  Browse to where you want to save the file, specify the file name, and click **OK**.

## Import traces

You can import `.trace` files created with the [`Debug` API](https://developer.android.com/reference/android/os/Debug) or CPU Profiler.

Import your trace file by clicking **Start new profiler session** ![](https://developer.android.com/studio/images/buttons/ic_plus.png) in the profiler’s **Sessions** pane and then selecting **Load from file**.

You can inspect an imported trace in the CPU Profiler much as you do with traces captured directly in the CPU Profiler, with the following exceptions:

*   CPU activity is not represented along the CPU timeline (except in System Trace).
*   The timeline in the **Threads** section does not show thread states such as running, waiting, or sleeping (except in System Trace).

## Inspect traces

The trace view in the CPU Profiler provides several ways to view information from recorded traces.

For method traces and function traces, you can view the **Call Chart** directly in the **Threads** timeline and the **Flame Chart**, **Top Down**, and **Bottom Up** tabs from the **Analysis** pane. For system traces, you can view the **Trace Events** directly in the **Threads** timeline, and the **Flame Chart**, **Top Down**, and **Bottom Up** tabs from the **Analysis** pane.

[Mouse and keyboard shortcuts](#ui-shortcuts) are available for easier navigation of **Call Charts** or **Trace Events**.

### Inspect traces using the Call Chart

The **Call Chart** provides a graphical representation of a method trace or function trace, where the period and timing of a call is represented on the horizontal axis, and its callees are shown along the vertical axis. Calls to system APIs are shown in orange, calls to your app’s own methods are shown in green, and calls to third\-party APIs (including Java language APIs) are shown in blue. Figure 4 shows an example call chart and illustrates the concept of self time, children time, and total time for a given method or function. You can learn more about these concepts in the section on how to [inspect traces using Top Down and Bottom Up](#top_down_bottom_up).

![](https://developer.android.com/studio/images/profile/call_chart_1-2X.png)

**Figure 4.** An example call chart that illustrates self, children, and total time for method D.

**Tip:** To jump the source code of a method or function, right\-click it and select **Jump to Source**. This works from any of the Analysis pane tabs.

### Inspect traces using the Flame Chart tab

The **Flame Chart** tab provides an inverted call chart that aggregates identical call stacks. That is, identical methods or functions that share the same sequence of callers are collected and represented as one longer bar in a flame chart (rather than displaying them as multiple shorter bars, as shown in a call chart). This makes it easier to see which methods or functions consume the most time. However, this also means that the horizontal axis doesn't represent a timeline; instead, it indicates the relative amount of time each method or function takes to execute.

To help illustrate this concept, consider the call chart in figure 5. Note that method D makes multiple calls to B (B1, B2, and B 3), and some of those calls to B make a call to C (C1 and C 3).

![](https://developer.android.com/studio/images/profile/call_chart_2-2X.png)

**Figure 5.** A call chart with multiple method calls that share a common sequence of callers.

Because B1, B2, and B3 share the same sequence of callers (A → D → B) they are aggregated, as shown in figure 6. Similarly, C 1 and C3 are aggregated because they share the same sequence of callers (A → D → B → C); note that C2 is not included because it has a different sequence of callers (A → D → C).

![](https://developer.android.com/studio/images/profile/flame_chart_aggregation-2X.png)

**Figure 6.** Aggregating identical methods that share the same call stack.

The aggregated calls are used to create the flame chart, as shown in figure 7. Note that, for any given call in a flame chart, the callees that consume the most CPU time appear first.

![](https://developer.android.com/studio/images/profile/flame_chart-2X.png)

**Figure 7.** A flame chart representation of the call chart shown in figure 5.

### Inspect traces using Top Down and Bottom Up

The **Top Down** tab displays a list of calls in which expanding a method or function node displays its callees. Figure 8 shows a top down graph for the call chart in figure 4. Each arrow in the graph points from a caller to a callee.

As shown in figure 8, expanding the node for method A in the **Top Down** tab displays its callees, methods B and D. After that, expanding the node for method D exposes its callees, methods B and C, and so on. Similar to the **Flame chart** tab, the top down tree aggregates trace information for identical methods that share the same call stack. That is, the **Flame chart** tab provides a graphical representation of the **Top down** tab.

The **Top Down** tab provides the following information to help describe CPU time spent on each call (times are also represented as a percentage of the thread’s total time over the selected range):

*   **Self:** the time the method or function call spent executing its own code and not that of its callees, as illustrated in figure 4 for method D.
*   **Children:** the time the method or function call spent executing its callees and not its own code, as illustrated in figure 4 for method D.
*   **Total:** the sum of the method’s **Self** and **Children** time. This represents the total time the app spent executing a call, as illustrated in figure 4 for method D.

![](https://developer.android.com/studio/images/profile/top_down_tree-2X.png)

**Figure 8.** A Top Down tree.

![](https://developer.android.com/studio/images/profile/bottom_up_tree-2X.png)

**Figure 9.** A Bottom Up tree for method C from figure 8.

The **Bottom Up** tab displays a list of calls in which expanding a function or method’s node displays its callers. Using the example trace shown in figure 8, figure 9 provides a bottom up tree for method C. Opening the node for method C in the bottom up tree displays each of its unique callers, methods B and D. Note that, although B calls C twice, B appears only once when expanding the node for method C in the bottom up tree. After that, expanding the node for B displays its caller, methods A and D.

The **Bottom Up** tab is useful for sorting methods or functions by those that consume the most (or least) CPU time. You can inspect each node to determine which callers spend the most CPU time invoking those methods or functions. Compared to the top down tree, timing info for each method or function in a bottom up tree is in reference to the method at the top of each tree (top node). CPU time is also represented as a percentage of the thread’s total time during that recording. The following table helps explain how to interpret timing information for the top node and its callers (sub\-nodes).

|  | Self | Children | Total |
| --- | --- | --- | --- |
| Method or function at the top of the bottom up tree (top node) | Represents the total time the method or function spent executing its own code and not that of its callees. Compared to the top down tree, this timing information represents a sum of all calls to this method or function over the duration of the recording. | Represents the total time the method or function spent executing its callees and not its own code. Compared to the top down tree, this timing information represents the sum of all calls to this method or function's callees over the duration of the recording. | The sum of the self time and children time. |
| Callers (sub\-nodes) | Represents the total self time of the callee when being called by the caller. Using the bottom up tree in figure 9 as an example, the self time for method B would equal the sum of the self times for each execution of method C when called by B. | Represents the total children time of the callee when being invoked by the caller. Using the bottom up tree in figure 9 as an example, the children time for method B would equal the sum of the children times for each execution of method C when called by B. | The sum of the self time and children time. |

**Note:** For a given recording, Android Studio stops collecting new data when the profiler reaches the file size limit (however, this does not stop the recording). This typically happens much more quickly when performing instrumented traces because this type of tracing collects more data in a shorter time, compared to a sampled trace. If you extend the inspection time into a period of the recording that occurred after reaching the limit, timing data in the trace pane does not change (because no new data is available). Additionally, the trace pane displays **NaN** for timing information when you select only the portion of a recording that has no data available.

### Inspect system traces

When inspecting a system trace, you can examine **Trace Events** in the **Threads** timeline to view the details of the events occurring on each thread. Hover your mouse pointer over an event to see the name of the event and the time spent in each state. Click an event to see more information in the **Analysis** pane.

The **CPU Cores** pane (as shown in figure 10) shows thread activity scheduled on every core. Hover your mouse pointer over a thread activity to see which thread this core is running on at that particular time.

![](https://developer.android.com/studio/images/profile/system-trace-cpu-cores.png)

**Figure 10.** Viewing CPU activity and trace events for the render thread.

For additional information on inspecting system trace information, see the [Investigate UI performance problems](https://developer.android.com/topic/performance/tracing/navigate-report#analysis) section of the `systrace` documentation.

### Inspect frame rendering data

You can inspect how long it takes your app to render each frame on the main thread and `RenderThread` to investigate bottlenecks that cause UI jank and low framerates.

To see frame rendering data, [record a trace](https://developer.android.com/studio/profile/cpu-profiler#method_traces) using a configuration that allows you to **Trace System Calls**. After recording the trace, look for info about each frame under the **Frames** timeline in the **Display** section, as shown in figure 11.

![](https://developer.android.com/studio/images/profile/render_thread.png)

**Figure 11.** Each frame that takes longer than 16ms is displayed in red.
