# Benchmark app code

- [Benchmark app code](#benchmark-app-code)
  - [Quickstart](#quickstart)
  - [When to benchmark](#when-to-benchmark)
  - [What to benchmark](#what-to-benchmark)
    - [Caching](#caching)
    - [Infrequently\-run code](#infrequently-run-code)
  - [Full project setup](#full-project-setup)
    - [Set Android Studio properties](#set-android-studio-properties)
    - [Create a new module](#create-a-new-module)
  - [Write a benchmark](#write-a-benchmark)
  - [Run the benchmark](#run-the-benchmark)
    - [Collect the data](#collect-the-data)
      - [Android Gradle Plugin 3.6 and higher](#android-gradle-plugin-36-and-higher)
      - [Android Gradle Plugin 3.5 and lower](#android-gradle-plugin-35-and-lower)
    - [Clock stability](#clock-stability)
      - [Lock clocks (requires root)](#lock-clocks-requires-root)
      - [Sustained performance mode](#sustained-performance-mode)
      - [Automatic execution pausing](#automatic-execution-pausing)
    - [Configuration Errors](#configuration-errors)
  - [Profiling](#profiling)
    - [Method tracing](#method-tracing)
    - [Method profiling](#method-profiling)
  - [Benchmarking samples](#benchmarking-samples)
  - [Additional resources](#additional-resources)
    - [Blogs](#blogs)
  - [Provide feedback](#provide-feedback)

The Jetpack Benchmark library allows you to quickly benchmark your Kotlin\-based or Java\-based code from within Android Studio. The library handles warmup, measures your code performance and allocation counts, and outputs benchmarking results to both the [Android Studio console](#run-benchmark) and a [JSON file](#collect-data) with more detail.

Use cases include scrolling a `RecyclerView`, performing database queries, and measuring any slow part of your code you want to make faster.

You can use the library in a continuous integration (CI) environment, as described in [Run benchmarks in Continuous Integration](https://developer.android.com/studio/profile/run-benchmarks-in-ci).

If you haven't yet adopted AndroidX in a project you want to benchmark, see [Migrate an existing project using Android Studio](https://developer.android.com/jetpack/androidx/migrate#migrate).

## Quickstart

This section provides a quick set of steps to try out benchmarking without requiring you to move code into modules. Because the steps involve disabling debugging for accurate performance results, you won't commit the changes to your source control system, but it can still be helpful when you want to run one\-off measurements.

To quickly perform one\-off benchmarking, do the following:

1.  Add the library to your module’s `build.gradle` file:

    project\_root/module\_dir/build.gradle

    dependencies {androidTestImplementation "androidx.benchmark:benchmark\-junit4:1.0.0"
    }

2.  To disable debugging in the test manifest, update your `<application>` element to force\-disable debugging temporarily as follows:

    project\_root/module\_dir/src/androidTest/AndroidManifest.xml

    ```
    <!-- Important: disable debuggable for accurate performance results --><application    android:debuggable="false"    tools:ignore="HardcodedDebugMode"    tools:replace="android:debuggable"/>
    ```

3.  To add your benchmark, add an instance of [`BenchmarkRule`](https://developer.android.com/reference/kotlin/androidx/benchmark/junit4/BenchmarkRule) in a test file in the `androidTest` directory. For more information on writing benchmarks, see [Write a benchmark](#write-benchmark).

    The following code snippet shows how to add a benchmark to a JUnit test:

    [Kotlin](#kotlin) [Java](#java) [More](#)

    @RunWith(AndroidJUnit4::class)
    class  MyBenchmark  { @get:Rule val benchmarkRule \=  BenchmarkRule() @Test fun benchmarkSomeWork()  \= benchmarkRule.measureRepeated {
            doSomeWork() }
    }

    @RunWith(AndroidJUnit4.class)
    class  MyBenchmark  { @Rule public  BenchmarkRule benchmarkRule \=  new  BenchmarkRule(); @Test public  void benchmarkSomeWork()  { final  BenchmarkState state \= benchmarkRule.getState(); while  (state.keepRunning())  {
                doSomeWork(); } }
    }

## When to benchmark

It's recommended to profile your code before writing a benchmark. This helps you find expensive operations that are worth optimizing. It can also expose why the operations are slow by showing what is happening while they run. Examples could include running on a low\-priority thread, sleeping due to disk access, or unexpectedly calling into a expensive function, like bitmap decoding.

Adding custom trace points via the [TraceCompat](https://developer.android.com/reference/kotlin/androidx/core/os/TraceCompat) API (or the [`-ktx` wrapper](https://developer.android.com/reference/kotlin/androidx/core/os/package-summary#trace(kotlin.String,%20kotlin.Function0))) allows you to view them in the [Android Studio CPU Profiler](https://developer.android.com/studio/profile/android-profiler) or [Systrace](https://developer.android.com/studio/command-line/systrace):

[Kotlin](#kotlin) [Java](#java) [More](#)

fun proccessBitmap(bitmap:  Bitmap):  Bitmap  {
    trace("processBitmap")  { // perform work on bitmap... }
}

public  Bitmap processBitmap(Bitmap bitmap)  { TraceCompat.beginSection("processBitmap"); try  { // perform work on bitmap... }  finally  { TraceCompat.endSection(); }
}

## What to benchmark

Benchmarks are most useful for CPU work that is run many times in your app. Good examples are `RecyclerView` scrolling, data conversions/processing, and pieces of code that get used repeatedly.

Other types of code are more difficult to measure with benchmarking. Because benchmarks run in a loop, any code that isn't run frequently, or performs differently when called multiple times, may not be a good fit for benchmarking.

### Caching

Try to avoid measuring just the cache. For example, a custom view's layout benchmark might measure only the performance of the layout cache. To avoid this, you can pass different layout parameters in each loop. In other cases, such as when measuring file system performance, this may be difficult because the OS caches the file system while in a loop.

### Infrequently\-run code

Code that's run once during application startup is not very likely to get JIT compiled by Android Runtime (ART). Because of that, benchmarking this code as it runs in a loop isn't a realistic way to measure its performance.

For this sort of code, we recommend [tracing](https://developer.android.com/studio/command-line/systrace) or [profiling](https://developer.android.com/studio/profile/android-profiler) the code in your app, instead. Note that this doesn't mean you *can't* benchmark code in your startup path, but rather that you should pick code that's run in a loop, and that is likely to get JIT compiled.

## Full project setup

To set up benchmarking for regular benchmarking rather than one\-off benchmarking, you isolate benchmarks into their own module. This ensures that their configuration, such as setting [`debuggable`](https://developer.android.com/guide/topics/manifest/application-element#debug) to `false`, is separate from regular tests.

Before adding your Benchmark module, place code and resources you want to benchmark into a [library module](https://developer.android.com/studio/projects/android-library#Convert) if they aren't already in one.

Our [samples](#samples) give examples of how to set up a project in this way.

### Set Android Studio properties

If you are using Android Studio 3.5, you must manually set Android Studio properties to enable benchmark module wizard support. This is not required for Android Studio 3.6 or higher.

To enable the Android Studio template for benchmarking, do the following:

1.  In Android Studio 3.5, click **Help** > **Edit Custom Properties**.

2.  Add the following line to the file that opens:

    `npw.benchmark.template.module=true`

3.  Save and close the file.

4.  Restart Android Studio.

### Create a new module

The benchmarking module template automatically configures settings for benchmarking.

To use the module template to create a new module, do the following:

1.  Right\-click your project or module and select **New** > **Module**.

2.  Select **Benchmark Module** and click **Next**.

    ![](https://developer.android.com/studio/images/profile/benchmark-icon.png)

    **Figure 1.** Benchmark module

3.  Enter a module name, choose the language, and click **Finish**.

    A module is created that is pre\-configured for benchmarking, with a benchmark directory added and `debuggable` set to `false`.

**Note:** `debuggable=false` prevents using the debugger and profiling tools with your tests. Using a separate library module eliminates this drawback.

## Write a benchmark

Benchmarks are standard instrumentation tests. To create a benchmark, use the [`BenchmarkRule`](https://developer.android.com/reference/kotlin/androidx/benchmark/junit4/BenchmarkRule) class provided by the library. To benchmark activities, use [`ActivityTestRule`](https://developer.android.com/reference/androidx/test/rule/ActivityTestRule) or [`ActivityScenarioRule`](https://developer.android.com/reference/androidx/test/ext/junit/rules/ActivityScenarioRule). To benchmark UI code, use [`@UiThreadTest`](https://developer.android.com/reference/androidx/test/annotation/UiThreadTest).

The following code shows a sample benchmark:

[Kotlin](#kotlin) [Java](#java) [More](#)

@RunWith(AndroidJUnit4::class)
class  ViewBenchmark  { @get:Rule val benchmarkRule \=  BenchmarkRule() @Test fun simpleViewInflate()  { val context \=  ApplicationProvider.getApplicationContext<Context\>() val inflater \=  LayoutInflater.from(context) val root \=  FrameLayout(context)

        benchmarkRule.keepRunning {
            inflater.inflate(R.layout.test\_simple\_view, root,  false) } }
}

@RunWith(AndroidJUnit4::class)
public  class  ViewBenchmark  { @Rule public  BenchmarkRule benchmarkRule \=  new  BenchmarkRule(); @Test public  void simpleViewInflate()  { Context context \=  ApplicationProvider.getApplicationContext<Context\>(); final  BenchmarkState state \= benchmarkRule.getState(); LayoutInflater inflater \=  LayoutInflater.from(context); FrameLayout root \=  new  FrameLayout(context); while  (state.keepRunning())  {
            inflater.inflate(R.layout.test\_simple\_view, root,  false); } }
}

You can disable timing for sections of code you don't want to measure, as shown in the following code sample:

[Kotlin](#kotlin) [Java](#java) [More](#)

@Test
fun bitmapProcessing()  \= benchmarkRule.measureRepeated { val input:  Bitmap  \= runWithTimingDisabled { constructTestBitmap()  }
    processBitmap(input)
}

@Test
public  void bitmapProcessing()  { final  BenchmarkState state \= benchmarkRule.getState(); while  (state.keepRunning())  {
        state.pauseTiming(); Bitmap input \= constructTestBitmap();
        state.resumeTiming();

        processBitmap(input); }
}

For information on running the benchmark, see [Run the benchmark](#run-benchmark).

## Run the benchmark

In Android Studio, run your benchmark as you would any `@Test`. On Android Studio 3.4 and higher, you can see output sent to the console.

To run the benchmark, in the module, navigate to `benchmark/src/androidTest` and press `Control`+`Shift`+`F10` (`Command`+`Shift`+`R` on Mac). Results of the benchmark appear in the console, as shown in Figure 2:

![Benchmarking output in Android Studio](https://developer.android.com/studio/images/profile/benchmark-output.png)

**Figure 2.** Benchmarking output in Android Studio

From the command line, run the regular `connectedCheck`:

```
./gradlew benchmark:connectedCheck

```

### Collect the data

A full benchmark report with additional metrics and device information is available in JSON format.

The `androidx.benchmark` Gradle plugin enables JSON output by default. To manually enable JSON output in non\-Gradle build environments, you pass an instrumentation argument, `androidx.benchmark.output.enable`, set to `true`.

The following is an example using the `adb shell am instrument` command:

adb shell am instrument \-w \-e "androidx.benchmark.output.enable" "true" com.android.foo/androidx.benchmark.junit4.AndroidBenchmarkRunner

By default, the JSON report is written to disk on device in the test APK's external shared downloads folder, which is typically located at:

/storage/emulated/0/Download/app\_id\-benchmarkData.json

You can configure the location where benchmark reports are saved on the device using the instrumentation argument `additionalTestOutputDir`.

adb shell am instrument \-w \-e "androidx.benchmark.output.enable" "true" \-e "additionalTestOutputDir" "/path\_to\_a\_directory\_on\_device\_test\_has\_write\_permissions\_to/" com.android.foo/androidx.benchmark.junit4.AndroidBenchmarkRunner

JSON reports are copied from device to host. These are written on the host machine at:

project\_root/module/build/outputs/connected\_android\_test\_additional\_output/debugAndroidTest/connected/device\_id/app\_id\-benchmarkData.json

#### Android Gradle Plugin 3.6 and higher

When running benchmarks from the command line with Gradle, projects using Android Gradle Plugin 3.6 and higher can add the following flag to the project's `gradle.properties` file:

android.enableAdditionalTestOutput=true

This enables an experimental Android Gradle Plugin feature to pull benchmark reports from devices running API 16 and higher.

#### Android Gradle Plugin 3.5 and lower

For users of older versions of Android Gradle Plugin, the `androidx.benchmark` Gradle plugin handles copying JSON benchmark reports from device to host.

When using AGP 3.5 or lower and targeting API level 29 or higher, you need to add a flag to your Android manifest in the `androidTest` directory of your benchmarks to enable legacy external storage behavior.

<manifest ... \> <!\-\- This attribute is "false" by default on apps targeting Android 10. \-\-> <application  android:requestLegacyExternalStorage\="true" ... \>... </application>
</manifest>

For more information, see [Temporarily opt\-out of scoped storage](https://developer.android.com/training/data-storage/use-cases#opt-out-scoped-storage).

### Clock stability

Clocks on mobile devices dynamically change from high state (for performance) to low state (to save power, or when the device gets hot). These varying clocks can make your benchmark numbers vary widely, so the library provides ways to deal with this issue.

#### Lock clocks (requires root)

Locking clocks is the best way to get stable performance. It ensures that clocks never get high enough to heat up the device, or low if a benchmark isn't fully utilizing the CPU. While this is the best way to ensure stable performance, it isn't supported on most devices, due to requiring `adb` root.

To lock your clocks, add the supplied helper plugin to the top level project’s classpath in the main `build.gradle` file:

```
buildscript {    ...    dependencies {        ...        classpath "androidx.benchmark:benchmark-gradle-plugin:1.0.0"    }}
```

Apply the plugin in the `build.gradle` of your benchmark module:

```
apply plugin: com.android.appapply plugin: androidx.benchmark...
```

This adds benchmarking Gradle tasks to your project, including `./gradlew lockClocks` and `./gradlew unlockClocks`. Use these tasks to lock and unlock a device’s CPU using adb.

If you have multiple devices visible to adb, use the environment variable `ANDROID_SERIAL` to specify which device the Gradle task should operate on:

ANDROID\_SERIAL=device\-id\-from\-adb\-devices ./gradlew lockClocks

**Note:** Rebooting resets your device clocks.

#### Sustained performance mode

[`Window.setSustainedPerformanceMode()`](https://developer.android.com/reference/android/view/Window#setSustainedPerformanceMode(boolean)) is a feature supported by some devices that enables an app to opt for a lower max CPU frequency. When running on supported devices, the Benchmark library uses a combination of this API and launching its own activity to both prevent thermal throttling and stabilize results.

This functionality is enabled by default by the `testInstrumentationRunner` set by the Gradle plugin. If you want to use a custom runner, you can subclass the `AndroidBenchmarkRunner` and use it as your `testInstrumentationRunner`.

The runner launches an opaque, fullscreen activity to ensure that the benchmark runs in the foreground and without any other app drawing.

#### Automatic execution pausing

If neither clock\-locking nor sustained performance are used, the library performs automatic thermal throttling detection. When enabled, the internal benchmark periodically runs to determine when the device temperature has gotten high enough to lower CPU performance. When lowered CPU performance is detected, the library pauses execution to let the device cool down, and retries the current benchmark.

### Configuration Errors

The library detects the following conditions to ensure your project and environment are set up for release\-accurate performance:

*   `Debuggable` is set to `false`.
*   A physical device, not an emulator, is being used.
*   Clocks are locked if the device is rooted.
*   Sufficient battery level on device.

If any of the above checks fail, the benchmark will throw an error to discourage inaccurate measurements.

To suppress these errors as warnings, and prevent them from throwing an error and halting the benchmark, pass the type of errors you want to suppress in a comma\-separated list to the instrumentation argument `androidx.benchmark.suppressErrors`:

adb shell am instrument \-w \-e "androidx.benchmark.suppressErrors" "DEBUGGABLE" com.android.foo/androidx.benchmark.junit4.AndroidBenchmarkRunner

Which you may set in Gradle as follows:

android {defaultConfig {testInstrumentationRunnerArgument 'androidx.benchmark.suppressErrors',  'DEBUGGABLE' }
}

Note that suppressing errors allows the benchmark to run in an incorrectly configured state, but the output of the benchmark will be intentionally corrupted by prepending test names with the error. That is, running a debuggable benchmark with the above suppression will prepend test names with `DEBUGGABLE_`.

## Profiling

You can profile a benchmark to investigate why the measured code is running slowly.

Add the following to your benchmark module's `build.gradle` file:

android {defaultConfig { // must be one of: none, sampled, or methodtestInstrumentationRunnerArgument 'androidx.benchmark.profiling.mode',  'sampled' }
}

When you run a benchmark, an output `.trace` file will be copied to host, in the directory [alongside JSON results](#collect-data). Open this file with Android Studio using **File > Open** to inspect profiling results in the CPU Profiler.

### Method tracing

With method tracing, the benchmark will warm up before capturing a method trace, recording every method called by your benchmark. Note that performance results will be significantly affected by the overhead of capturing each method entry/exit.

### Method profiling

With method sampling, the benchmark will sample stack traces at a 100 microsecond interval, after warmup has completed. The benchmark will loop significantly longer than usual to ensure enough samples are captured for meaningful results, and measurement results will be somewhat affected.

To read more about how to use method tracing and sampling, see the [CPU profiling configuration](https://developer.android.com/studio/profile/cpu-profiler#configurations).

## Benchmarking samples

Sample benchmark code is available in the following projects:

*   [Performance samples](https://github.com/android/performance-samples)
*   [Architecture component samples](https://github.com/android/architecture-components-samples)

The sample projects include:

*   **BenchmarkSample**: this is a standalone sample, which shows how to use a benchmark module to measure code and UI.

*   **PagingWithNetworkSample**: Android Architecture Components sample, which shows how to benchmark [`RecyclerView`](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView) performance.

*   **WorkManagerSample**: Android Architecture Components sample, which shows how to benchmark [`WorkManager`](https://developer.android.com/topic/libraries/architecture/workmanager) workers.

## Additional resources

To learn more about Benchmarking, consult the following additional resources.

### Blogs

*   [Fighting Regressions with Benchmarks in CI](https://medium.com/androiddevelopers/fighting-regressions-with-benchmarks-in-ci-6ea9a14b5c71)

## Provide feedback

To report issues or submit feature requests when using benchmarking, see the [public issue tracker](https://issuetracker.google.com/issues/new?component=585351).
