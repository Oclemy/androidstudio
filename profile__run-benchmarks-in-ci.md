# Run benchmarks in Continuous Integration

- [Run benchmarks in Continuous Integration](#run-benchmarks-in-continuous-integration)
  - [Results](#results)
    - [Storage and Android 10](#storage-and-android-10)
  - [Lock clocks](#lock-clocks)
  - [Instrumentation arguments](#instrumentation-arguments)
    - [androidx.benchmark.startupMode.enable](#androidxbenchmarkstartupmodeenable)
    - [androidx.benchmark.output.enable](#androidxbenchmarkoutputenable)
    - [androidx.benchmark.suppressErrors](#androidxbenchmarksuppresserrors)
    - [androidx.benchmark.profiling.mode](#androidxbenchmarkprofilingmode)
    - [additionalTestOutputDir](#additionaltestoutputdir)

It's common to run tests in CI without Gradle, or locally if you're using a different build system. This topic explains how to configure the Benchmark library at runtime without Gradle.

To learn about why Benchmarks are useful in CI and how to use benchmarking data to detect regressions, see [Fighting Regressions with Benchmarks in CI](https://medium.com/androiddevelopers/fighting-regressions-with-benchmarks-in-ci-6ea9a14b5c71) on the Android Developers blog.

## Results

Benchmark outputs a JSON file with results and metadata from a test run. This JSON file is written to external storage on the device. You must to pull the file from the device with each run.

Output is disabled by default, but you can enable it with the following [instrumentation argument](https://developer.android.com/studio/test/command-line#AMOptionsSyntax), passed to your `am instrument` command:

\-e androidx.benchmark.output.enable true

By default, the reports are saved on\-device in the download directory of the tested app's external storage, which you can find with the following query:

adb shell content query \-\-uri content://media/external/file \-\-projection \_data \-\-where "\\"\_data LIKE '%Android'\\""

You can configure the path Benchmark writes to using the following instrumentation argument:

\-e additionalTestOutputDir "device\_path\_you\_can\_write\_to"

### Storage and Android 10

If, like in Android Gradle, it is difficult to pull output data from the device before a test is uninstalled, you can pass the following instrumentation argument. On devices running Android 10 and higher, this argument lets the files persist after uninstall:

\-e no\-isolated\-storage 1

If you are also targeting API 29 and higher, you also must explicitly allow legacy storage options in your benchmark's manifest:

<application  android:requestLegacyExternalStorage\="true" ... \>

For more information, see [Temporarily opt\-out of scoped storage](https://developer.android.com/training/data-storage/use-cases#opt-out-scoped-storage).

## Lock clocks

The Benchmark Gradle plugin provides the command `./gradlew lockClocks` to lock the CPU clocks of a rooted device. This is useful for ensuring stability when you have access to rooted devices, such as "userdebug" builds. You can replicate this with the `lockClocks.sh` shell script, available in the [library's source](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/androidx-main/benchmark/gradle-plugin/src/main/resources/scripts/lockClocks.sh).

You can either run the script directly from a Linux or Mac host, or you can push to the device with a few adb commands:

adb push path/lockClocks.sh /data/local/tmp/lockClocks.sh
adb shell /data/local/tmp/lockClocks.sh
adb shell rm /data/local/tmp/lockClocks.sh

If you run the shell script directly on a host, it dispatches these commands to a connected device.

## Instrumentation arguments

You can configure the behavior of the library with the following instrumentation arguments:

### androidx.benchmark.startupMode.enable

*   Reconfigures looping behavior to support benchmarking code during startup
*   Disables warmup looping
*   Captures 10 measurements
*   Disables loop averaging, which minimize overhead in microbenchmarks
*   Defaults to `false`

### androidx.benchmark.output.enable

*   Enables writing the result JSON file to external storage
*   Defaults to `false`

### androidx.benchmark.suppressErrors

*   Comma\-separated list of errors, such as `DEBUGGABLE,LOW-BATTERY`, to turn into warnings
*   Defaults to an empty list

### androidx.benchmark.profiling.mode

*   One of `none`, `method`, or `sampled`
*   Defaults to `none`
*   For more information, see [Profiling](https://developer.android.com/studio/profile/benchmark#profiling).

### additionalTestOutputDir

*   Configures where JSON benchmark reports and profiling results are saved on device
*   Defaults to test APK's external download directory
