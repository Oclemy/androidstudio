# Profile your app performance

*   [Table of contents](#top_of_page)
*   [Android Studio tools](#android-studio-tools)
*   [Command line tools](#command-line-tools)

An app is considered to have poor performance if it responds slowly, shows choppy animations, freezes, crashes, or consumes a lot of power. To avoid these performance problems, use the profiling and benchmarking tools listed on this page to identify where your app is making inefficient use of resources, such as the CPU, memory, graphics, network, and device battery.

**Note:** While profiling an app, you should [disable Instant Run](https://developer.android.com/studio/run#disable-ir). There is a small performance impact when using Instant Run and a slightly larger impact when updating methods. This performance impact could interfere with information provided by performance profiling tools. Additionally, the stub methods generated while using the feature can complicate stack traces.

## Android Studio tools

[](https://developer.android.com/studio/profile/hierarchy-viewer?hl=en)

[

### Profile your layout with Hierarchy Viewer

](https://developer.android.com/studio/profile/hierarchy-viewer?hl=en)

Hierarchy Viewer is a tool built into Android Device Monitor that allows you to measure the layout speed for each view in your layout hierarchy.

[](https://developer.android.com/games/optimize/cpu-profiler?hl=en)

[

### Identify CPU hot spots

](https://developer.android.com/games/optimize/cpu-profiler?hl=en)

Learn how to optimize your app’s CPU usage in Android Studio.

[](https://developer.android.com/studio/profile/cpu-profiler?hl=en)

[

### Inspect CPU activity with CPU Profiler

](https://developer.android.com/studio/profile/cpu-profiler?hl=en)

Learn how to optimize your app’s CPU usage in Android Studio.

[](https://developer.android.com/games/optimize/game-size?hl=en)

[

### Reduce game size

](https://developer.android.com/games/optimize/game-size?hl=en)

Learn how to optimize your game's size.

[](https://developer.android.com/studio/profile/android-profiler?hl=en)

[

### Measure app performance with Android Profiler

](https://developer.android.com/studio/profile/android-profiler?hl=en)

The Android Profiler in Android Studio 3.0 and higher replaces the Android Monitor tools. The Android Profiler tools provide real\-time data to help you to understand how your app uses CPU, memory, network, and battery resources. For details about

[](https://developer.android.com/studio/profile/memory-profiler?hl=en)

[

### Inspect your app's memory usage with Memory Profiler

](https://developer.android.com/studio/profile/memory-profiler?hl=en)

Find out about the Memory Profiler component in the Android Profiler that helps you identify memory leaks and memory churn that can lead to stutter, freezes, and even app crashes.

[](https://developer.android.com/studio/profile/network-profiler?hl=en)

[

### Inspect network traffic with Network Profiler

](https://developer.android.com/studio/profile/network-profiler?hl=en)

Find out how to examine how and when your app transfers data, and optimize the underlying code appropriately with the Network Profiler.

LessMore

## Command line tools

[](https://developer.android.com/studio/command-line/dmtracedump?hl=en)

[

### dmtracedump

](https://developer.android.com/studio/command-line/dmtracedump?hl=en)

dmtracedump is a tool that generates graphical call\-stack diagrams from trace log files.

[](https://developer.android.com/topic/performance/tracing/command-line?hl=en)

[

### Capture a system trace on the command line

](https://developer.android.com/topic/performance/tracing/command-line?hl=en)

Learn about the Systrace tool, which allows you to collect and inspect timing information across all processes running on your device at the system level.

[](https://developer.android.com/studio/command-line/dumpsys?hl=en)

[

### dumpsys

](https://developer.android.com/studio/command-line/dumpsys?hl=en)

dumpsys is a tool that runs on Android devices and provides information about system services.
