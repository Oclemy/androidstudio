# Analyze a stack trace

- [Analyze a stack trace](#analyze-a-stack-trace)
  - [Open Stack traces from external sources](#open-stack-traces-from-external-sources)
  - [Monitor the clipboard for new stack traces](#monitor-the-clipboard-for-new-stack-traces)

Debugging an app often requires working with stack traces. A stack trace is generated whenever your app crashes because of an error or an exception. You can also print a stack trace at any point in your app code using methods such as [`Thread.dumpStack()`](https://developer.android.com/reference/java/lang/Thread#dumpStack()).

While your app is running in debug mode on a connected device, Android Studio prints and highlights stack traces in the **logcat** view, as shown in figure 1.

![](https://developer.android.com/studio/images/debug/logcat-stacktrace_2x.png)

**Figure 1.** A stacktrace in logcat.

A stack trace shows a list of method calls that lead to the exception being thrown, together with the filenames and line numbers where the calls happened. You can click on the highlighted filenames to open the files and examine the source of the method invocation. Click **Up the stack trace** ![](https://developer.android.com/studio/images/buttons/logcat-arrow-up.png) and **Down the stack trace** ![](https://developer.android.com/studio/images/buttons/logcat-arrow-down.png) to quickly move between stack trace lines displayed in the logcat window.

## Open Stack traces from external sources

Sometimes you want to analyze stack traces that were shared with you in a bug report as opposed to those you found while debugging. For example, you might be collecting stack traces generated on your users’ devices from the Google Play Console or from some other tool such as [Firebase Crash Reporting](https://firebase.google.com/docs/crash/).

To get the same highlighted and clickable view of an external stack trace from a bug report, follow these steps:

1.  Open your project in Android Studio.

    **Note:** Make sure the source code you're viewing is from the exact same version of your app which generated the stack trace. If the code is different, you will get mismatches between the filenames and line numbers, or the order of calls in the stack trace and your project.

2.  From the **Analyze** menu, click **Analyze Stack Trace**.

    ![Analyze Stack Trace window](https://developer.android.com/studio/images/debug/analyze-stacktrace_2-2_2x.png)

3.  Paste the stack trace text into the **Analyze Stack Trace** window and click **OK**.
4.  Android Studio opens a new **<Stacktrace>** tab with the stack trace you pasted under the **Run** window.

    ![Stacktrace window](https://developer.android.com/studio/images/debug/stacktrace-window_2x.png)

## Monitor the clipboard for new stack traces

If you work with external stack traces a lot, you can improve your productivity by allowing Android Studio to continuously monitor the system clipboard for new stacktraces:

1.  Open the **Analyze Stacktrace** tool.
2.  Check the **Automatically detect and analyze thread dumps copied to the clipboard outside of IntelliJ IDEA** checkbox.
3.  Copy a stack trace in another application (such as from a web browser) to the system clipboard.
4.  When you come back to the Android Studio window, the stack trace opens automatically under the **Run** window, without you having to paste it in the Analyze Stacktrace window.
