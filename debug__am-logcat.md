# Write and View Logs with Logcat

- [Write and View Logs with Logcat](#write-and-view-logs-with-logcat)
  - [View your app logs](#view-your-app-logs)
  - [Write log messages](#write-log-messages)
  - [Logcat message format](#logcat-message-format)
  - [Set the log level](#set-the-log-level)
  - [Search logcat messages](#search-logcat-messages)
  - [Filter logcat messages](#filter-logcat-messages)
  - [Read garbage collection messages](#read-garbage-collection-messages)
    - [Dalvik log messages](#dalvik-log-messages)
    - [ART log messages](#art-log-messages)

The **Logcat** window in Android Studio displays system messages, such as when a garbage collection occurs, and messages that you added to your app with the [`Log`](https://developer.android.com/reference/android/util/Log) class. It displays messages in real time and keeps a history so you can view older messages.

To display just the information of interest, you can create filters, modify how much information is displayed in messages, set priority levels, display messages produced by app code only, and search the log. By default, logcat shows the log output related to the most recently run app only.

When an app throws an exception, logcat shows a message followed by the associated stack trace containing links to the line of code.

As of Android Studio 2.2, the **Run** window also displays log messages for the current running app. Note that you can configure the logcat output display, but not the **Run** window.

## View your app logs

To display the log messages for an app:

1.  [Build and run your app](https://developer.android.com/studio/run#RunningApp) on a device.
2.  Click **View > Tool Windows > Logcat** (or click **Logcat** ![](https://developer.android.com/studio/images/buttons/logcat-icon.png) in the tool window bar).

The Logcat window shows the log messages for the selected app, as selected from the dropdown lists at the top of the window, as shown in figure 1.

![](https://developer.android.com/studio/images/debug/logcat_2x.png)

**Figure 1.** Logcat window

By default, logcat displays just the log messages for your app running on the device. To change this default, see how to [filter logcat messages](#filtering).

The Logcat toolbar provides the following buttons:

1.  **Clear logcat** ![](https://developer.android.com/studio/images/buttons/logcat-clear.png): Click to clear the visible log.
2.  **Scroll to the end** ![](https://developer.android.com/studio/images/buttons/logcat-scroll-end.png): Click to jump to the bottom of the log and see the latest log messages. If you then click a line in the log, the view pauses scrolling at that point.
3.  **Up the stack trace** ![](https://developer.android.com/studio/images/buttons/logcat-trace-up.png) and **Down the stack trace** ![](https://developer.android.com/studio/images/buttons/logcat-trace-down.png): Click to navigate up and down the stack traces in the log, selecting the subsequent filenames (and viewing the correspnding line numbers in the editor) that appear in the printed exceptions. This is the same behavior as when you click on a filename in the log.
4.  **Use soft wraps** ![](https://developer.android.com/studio/images/buttons/logcat-wrap.png): Click to enable line wrapping and prevent horizontal scrolling (though any unbreakable strings will still require horizontal scrolling).
5.  **Print** ![](https://developer.android.com/studio/images/buttons/logcat-print.png): Click to print the logcat messages. After selecting your print preferences in the dialog that appears, you can also choose to save to a PDF.
6.  **Restart** ![](https://developer.android.com/studio/images/buttons/logcat-restart.png): Click to clear the log and restart logcat. Unlike the **Clear logcat** button, this recovers and displays previous log messages, so is most useful if Logcat becomes unresponsive and you don't want to lose your log messages.
7.  **Logcat header** ![](https://developer.android.com/studio/images/buttons/logcat-headers.png): Click to open the **Configure Logcat Header** dialog, where you can customize the appearance of each logcat message, such as whether to show the date and time.
8.  **Screen capture** ![](https://developer.android.com/studio/images/buttons/monitor-screenshot.png): Click to [capture a screenshot](https://developer.android.com/studio/debug/am-screenshot).
9.  **Screen record** ![](https://developer.android.com/studio/images/buttons/logcat-record.png): Click to [record a video](https://developer.android.com/studio/debug/am-video) of the device (for a maximum of 3 minutes).

## Write log messages

The `[Log](https://developer.android.com/reference/android/util/Log)` class allows you to create log messages that appear in logcat. Generally, you should use the following log methods, listed in order from the highest to lowest priority (or, least to most verbose):

*   `[Log.e(String, String)](https://developer.android.com/reference/android/util/Log#e(java.lang.String,%20java.lang.String))` (error)
*   `[Log.w(String, String)](https://developer.android.com/reference/android/util/Log#w(java.lang.String,%20java.lang.String))` (warning)
*   `[Log.i(String, String)](https://developer.android.com/reference/android/util/Log#i(java.lang.String,%20java.lang.String))` (information)
*   `[Log.d(String, String)](https://developer.android.com/reference/android/util/Log#d(java.lang.String,%20java.lang.String))` (debug)
*   `[Log.v(String, String)](https://developer.android.com/reference/android/util/Log#v(java.lang.String,%20java.lang.String))` (verbose)

See the `[Log](https://developer.android.com/reference/android/util/Log)` class description for a more complete list of options.

You should never compile verbose logs into your app, except during development. Debug logs are compiled in but stripped at runtime, while error, warning, and info logs are always kept.

For each log method, the first parameter should be a unique tag and the second parameter is the message. The tag of a system log message is a short string indicating the system component from which the message originates (for example, [`ActivityManager`](https://developer.android.com/reference/android/app/ActivityManager) ). Your tag can be any string that you find helpful, such as the name of the current class.

A good convention is to declare a `TAG` constant in your class to use in the first parameter. For example, you might create an information log message as follows:

[Kotlin](#kotlin) [Java](#java) [More](#)

private const val TAG \=  "MyActivity"
...
Log.i(TAG,  "MyClass.getView() — get item number $position")

private  static  final  String TAG \=  "MyActivity";
...
Log.i(TAG,  "MyClass.getView() — get item number "  + position);

**Note:** Tag names greater than 23 characters are truncated in the logcat output.

## Logcat message format

Every Android log message has a tag and a priority associated with it. The tag of a system log message is a short string indicating the system component from which the message originates (for example, `ActivityManager`). A user\-defined tag can be any string that you find helpful, such as the name of the current class (the recommended tag). You define it in a `Log` method call, for example:

[Kotlin](#kotlin) [Java](#java) [More](#)

Log.d(tag, message)

Log.d(tag, message);

The priority is one of the following values:

*   **V:** Verbose (lowest priority)
*   **D:** Debug
*   **I:** Info
*   **W:** Warning
*   **E:** Error
*   **A:** Assert

The log message format is:

*date time PID\-TID/package priority/tag: message*

For example, the following log message has a priority of `V` and a tag of `AuthZen`:

12\-10 13:02:50.071 1901\-4229/com.google.android.gms V/AuthZen: Handling delegate intent.

PID stands for process identifier and TID is thread identifier; they can be the same if there’s only one thread.

## Set the log level

You can control how many messages appear in logcat by setting the log level. You can display all messages, or just the messages indicating the most severe conditions.

Remember that logcat continues to collect all messages regardless of the log level setting. The setting just determines what logcat displays.

In the Log level menu, select one of the following values:

*   **Verbose:** Show all log messages (the default).

*   **Debug:** Show debug log messages that are useful during development only, as well as the message levels lower in this list.

*   **Info:** Show expected log messages for regular usage, as well as the message levels lower in this list.

*   **Warn:** Show possible issues that are not yet errors, as well as the message levels lower in this list.

*   **Error:** Show issues that have caused errors, as well as the message level lower in this list.

*   **Assert:** Show issues that the developer expects should never happen.

## Search logcat messages

To search the messages currently displayed in logcat:

1.  Optionally select **Regex** if you want to use a regular expression search pattern.
2.  Type a character sequence in the search field ![](https://developer.android.com/images/tools/am-isearch.png).

    The logcat output display changes accordingly.

3.  Press Enter to store the search string in the menu during this session.
4.  To repeat a search, choose it from the search menu. Select or deselect **Regex** as needed (the setting isn’t remembered).

## Filter logcat messages

One way to reduce the log output to a manageable level is to restrict it by using a filter.

**Note:** The filter applies to your full logcat history, not just those messages currently displayed in logcat. Make sure your other display options are set appropriately so you can see the filter output you want to examine.

To define and apply a filter:

1.  In the filter menu, select a filter option:

    *   **Show only selected application:** Display the messages produced by the app code only (the default). Logcat filters the log messages using the PID of the active app.
    *   **No Filters:** Apply no filters. Logcat displays all log messages from the device, regardless of which process you selected.
    *   **Edit Filter Configuration:** Create or modify a custom filter. For example, you could create a filter to view log messages from two apps at the same time.

    After you define filters, you can also select them in the menu. To remove them from the menu, delete them.

2.  If you selected **Edit Filter Configuration**, create or modify a filter:
    1.  Specify the filter parameters in the *Create New Logcat Filter* dialog:
        *   **Filter Name:** Type the name of a filter you want to define, or select it in the left pane to modify an existing filter. The name can contain lowercase characters, underscores, and digits only.
        *   **Log Tag:** Optionally specify a tag. For more information, see [logcat Message Format](#format).
        *   **Log Message:** Optionally specify log message text. For more information, see [logcat Message Format](#format).
        *   **Package Name:** Optionally specify a package name. For more information, see [logcat Message Format](#format).
        *   **PID:** Optionally specify a process ID. For more information, see [logcat Message Format](#format).
        *   **Log Level:** Optionally select a log level. For more information, see [Set the Log Level](#level).
        *   **Regex:** Select this option to use regular expression syntax for that parameter.
    2.  Click **+** to add the filter definition to the left pane.

        To remove a filter, select it in the left pane and click **\-**.

    3.  When you’re finished, click **OK**.

If you don't think you see the log messages you want, try selecting **No filters** and [searching](#searching) for particular log messages.

## Read garbage collection messages

Sometimes when a garbage collection event occurs, they're printed to logcat.

For more detail about your app's memory, use the [Memory Profiler](https://developer.android.com/studio/profile/memory-profiler).

### Dalvik log messages

In Dalvik (but not ART), every GC prints the following information to logcat:

D/dalvikvm(PID): GC\_Reason Amount\_freed, Heap\_stats, External\_memory\_stats, Pause\_time

Example:

D/dalvikvm( 9050): GC\_CONCURRENT freed 2049K, 65% free 3571K/9991K, external 4703K/5261K, paused 2ms+2ms

GC Reason

What triggered the GC and what kind of collection it is. Reasons that might appear include:

`GC_CONCURRENT`

A concurrent GC that frees up memory as your heap begins to fill up.

`GC_FOR_MALLOC`

A GC was caused because your app attempted to allocate memory when your heap was already full, so the system had to stop your app and reclaim memory.

`GC_HPROF_DUMP_HEAP`

A GC that occurs when you request to create an HPROF file to analyze your heap.

`GC_EXPLICIT`

An explicit GC, such as when you call `[gc()](https://developer.android.com/reference/java/lang/System#gc())` (which you should avoid calling and instead trust the GC to run when needed).

`GC_EXTERNAL_ALLOC`

This happens only on API level 10 and lower (newer versions allocate everything in the Dalvik heap). A GC for externally allocated memory (such as the pixel data stored in native memory or NIO byte buffers).

Amount freed

The amount of memory reclaimed from this GC.

Heap stats

Percentage free of the heap and (number of live objects)/(total heap size).

External memory stats

Externally allocated memory on API level 10 and lower (amount of allocated memory) / (limit at which collection will occur).

Pause time

Larger heaps will have larger pause times. Concurrent pause times show two pauses: one at the beginning of the collection and another near the end.

While these log messages accumulate, look out for increases in the heap stats (the `3571K/9991K` value in the above example). If this value continues to increase, you might have a memory leak.

### ART log messages

Unlike Dalvik, ART doesn't log messages for GCs that were not explicitly requested. GCs are only printed when they are deemed slow. More precisely, if the GC pause exceeds 5ms or the GC duration exceeds 100ms. If the app is not in a pause perceptible state (such as when the app is in the background, where the user cannot preceive a GC pause), then none of its GCs are deemed slow. Explicit GCs are always logged.

ART includes the following information in its garbage collection log messages:

I/art: GC\_Reason GC\_Name Objects\_freed(Size\_freed) AllocSpace Objects,
    Large\_objects\_freed(Large\_object\_size\_freed) Heap\_stats LOS objects, Pause\_time(s)

Example:

I/art : Explicit concurrent mark sweep GC freed 104710(7MB) AllocSpace objects,
    21(416KB) LOS objects, 33% free, 25MB/38MB, paused 1.230ms total 67.216ms

GC Reason

What triggered the GC and what kind of collection it is. Reasons that might appear include:

`Concurrent`

A concurrent GC that does not suspend app threads. This GC runs in a background thread and does not prevent allocations.

`Alloc`

The GC was initiated because your app attempted to allocate memory when your heap was already full. In this case, the garbage collection occurred in the allocating thread.

`Explicit`

The garbage collection was explicitly requested by an app, for example, by calling `[gc()](https://developer.android.com/reference/java/lang/System#gc())` or `[gc()](https://developer.android.com/reference/java/lang/Runtime#gc())`. Like Dalvik, in ART the best practice is that you trust the GC and avoid requesting explicit GCs, if possible. Explicit GCs are discouraged because they block the allocating thread and unnecessarily waste CPU cycles. Explicit GCs could also cause jank (stuttering, juddering, or halting in the app) if they cause other threads to get preempted.

`NativeAlloc`

The collection was caused by native memory pressure from native allocations such as Bitmaps or RenderScript allocation objects.

`CollectorTransition`

The collection was caused by a heap transition; this is caused by changing the GC strategy at run time (such as when the app changes between pause perceptible states). Collector transitions consist of copying all the objects from a free\-list backed space to a bump pointer space (or visa versa).

This occurs only on low RAM device prior to Android 8.0 when an app changes process states from a pause perceptible state (such as when the app is in the foreground, where the user can preceive a GC pause) to a non pause perceptible state (or visa versa).

`HomogeneousSpaceCompact`

Homogeneous space compaction is free\-list space to free\-list space compaction which usually occurs when an app is moved to a pause imperceptible process state. The main reasons for doing this are reducing RAM usage and defragmenting the heap.

`DisableMovingGc`

This is not a real GC reason, but a note that collection was blocked due to use of GetPrimitiveArrayCritical. while concurrent heap compaction is occurring. In general, the use of GetPrimitiveArrayCritical is strongly discouraged due to its restrictions on moving collectors.

`HeapTrim`

This is not a GC reason, but a note that collection was blocked until a heap trim finished.

GC Name

ART has various different GCs which can get run.

`Concurrent mark sweep (CMS)`

A whole heap collector which frees collects all spaces other than the image space.

`Concurrent partial mark sweep`

A mostly whole heap collector which collects all spaces other than the image and zygote spaces.

`Concurrent sticky mark sweep`

A generational collector which can only free objects allocated since the last GC. This garbage collection is run more often than a full or partial mark sweep since it is faster and has lower pauses.

`Marksweep + semispace`

A non concurrent, copying GC used for heap transitions as well as homogeneous space compaction (to defragement the heap).

Objects freed

The number of objects which were reclaimed from this GC from the non large object space.

Size freed

The number of bytes which were reclaimed from this GC from the non large object space.

Large objects freed

The number of object in the large object space which were reclaimed from this garbage collection.

Large object size freed

The number of bytes in the large object space which were reclaimed from this garbage collection.

Heap stats

Percentage free and (number of live objects)/(total heap size).

Pause times

In general pause times are proportional to the number of object references which were modified while the GC was running. Currently, the ART CMS GCs only has one pause, near the end of the GC. The moving GCs have a long pause which lasts for the majority of the GC duration.

If you are seeing a large amount of GCs in logcat, look for increases in the heap stats (the `25MB/38MB` value in the above example). If this value continues to increase and doesn't ever seem to get smaller, you could have a memory leak. Alternatively, if you are seeing GC which are for the reason "Alloc", then you are already operating near your heap capacity and can expect OOM exceptions in the near future.
