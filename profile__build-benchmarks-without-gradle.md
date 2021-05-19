# Build benchmarks without Gradle

- [Build benchmarks without Gradle](#build-benchmarks-without-gradle)
  - [AndroidBenchmarkRunner](#androidbenchmarkrunner)
  - [Debuggable](#debuggable)
    - [Self\-instrumenting APKs](#self-instrumenting-apks)
    - [App APK instrumented by test APK](#app-apk-instrumented-by-test-apk)
  - [Minification and optimization](#minification-and-optimization)
  - [Coverage](#coverage)
  - [Running](#running)

While the Benchmark library ships a Gradle plugin to integrate directly with the Android Gradle Plugin, you can also use Benchmark in other build systems, such as [Bazel](https://bazel.build) or [Buck](https://buck.build).

This topic describes configuring a non\-Gradle build system when using the Benchmark library.

## AndroidBenchmarkRunner

Use [`AndroidBenchmarkRunner`](https://developer.android.com/reference/kotlin/androidx/benchmark/junit4/AndroidBenchmarkRunner) or a subclass as your instrumentation runner by specifying it in the instrumentation block of the test manifest:

<instrumentation android:name\="androidx.benchmark.junit4.AndroidBenchmarkRunner"... />

## Debuggable

To get accurate measurements, benchmarks must not be [debuggable](https://developer.android.com/guide/topics/manifest/application-element#debug). If the debuggable flag is not set correctly, the library throws an error, rather than reporting invalid results. Note that this setting may need to be toggled during local runs for use with Android Studio profilers, which require `debuggable=true`.

Benchmarks can be built to run in two ways: within a self\-instrumenting APK, or with one test APK instrumenting another APK.

### Self\-instrumenting APKs

With a simple self\-instrumenting APK (as output by Gradle for a `com.android.library`'s `androidTest` directory), the single APK's Android manifest must have debuggable disabled:

<manifest package\="com.example.library.test" ...\> <instrumentation android:name\="androidx.benchmark.junit4.AndroidBenchmarkRunner" android:targetPackage\="com.example.library.test"/> **<application  android:debuggable\="false"/>**
</manifest>

### App APK instrumented by test APK

If your build outputs two APKs, an app APK and test APK (as output by Gradle for a `com.android.app`'s `androidTest` directory), the app APK must be set to `debuggable=false`. The test APK's debuggable flag is ignored by the Android OS.

<!\-\- test manifest \-\->
<manifest package\="com.example.android.app.test" ...\> <instrumentation android:name\="androidx.benchmark.junit4.AndroidBenchmarkRunner" android:targetPackage\="com.example.android.app"/> <!\-\- debuggable here ignored by OS! \-\->
</manifest>

<!\-\- app being tested \-\->
<manifest package\="com.example.android.app" ...\> <application  android:debuggable\="false"/>
</manifest>

Note that benchmarking an app module APK isn't supported by Android Studio or Gradle. This is due to the complexity of supporting an additional testing directory that depends on a non\-debuggable, optimized or minified variant of the APK, but without minification breaking calls from benchmarks into app code.

## Minification and optimization

We recommend using minification and optimization for your benchmarks, to get closer\-to\-release performance. For example code, see the [Benchmark sample project](https://github.com/android/performance-samples/blob/master/BenchmarkSample/benchmark/build.gradle).

## Coverage

Benchmarks should run with coverage disabled, without any library or DEX mangling done by tools such as Jacoco.

For this reason, we recommend benchmarks be a completely isolated source set from other instrumentation tests, and built separately with release dependencies. We also find this avoids having to build tests more than once: both with and without coverage.

Note also that debug variants of libraries that your benchmark depends on, especially those built locally, may be built with coverage enabled.

## Running

To configure the Benchmark library at runtime without Gradle, see [Run benchmarks in Continuous Integration](https://developer.android.com/studio/profile/run-benchmarks-in-ci).
