# Optimize your build speed

- [Optimize your build speed](#optimize-your-build-speed)
  - [Optimize your build configuration](#optimize-your-build-configuration)
    - [Keep your tools up\-to\-date](#keep-your-tools-up-to-date)
    - [Create a build variant for development](#create-a-build-variant-for-development)
    - [Enable single\-variant project sync](#enable-single-variant-project-sync)
    - [Avoid compiling unnecessary resources](#avoid-compiling-unnecessary-resources)
    - [Disable Crashlytics for your debug builds](#disable-crashlytics-for-your-debug-builds)
      - [Disable automatic build ID generation](#disable-automatic-build-id-generation)
    - [Use static build config values with your debug build](#use-static-build-config-values-with-your-debug-build)
    - [Use static dependency versions](#use-static-dependency-versions)
    - [Create library modules](#create-library-modules)
    - [Create tasks for custom build logic](#create-tasks-for-custom-build-logic)
    - [Convert images to WebP](#convert-images-to-webp)
    - [Disable PNG crunching](#disable-png-crunching)
    - [Use incremental annotation processors](#use-incremental-annotation-processors)
      - [*Incremental annotation processor support*](#incremental-annotation-processor-support)
    - [Configure the JVM garbage collector](#configure-the-jvm-garbage-collector)

Long build times slow down your development process. This page offers some techniques to help you resolve build speed bottlenecks.

The general process of improving your build speed is as follows:

1.  [Optimize your build configuration](#optimize) by taking a few steps that immediately benefit most Android Studio projects.
2.  [Profile your build](https://developer.android.com/studio/build/profile-your-build) to identify and diagnose some of the trickier bottlenecks that may be specific to your project or workstation.

When developing your app, you should **deploy to a device running Android 7.0 (API level 24) or higher** whenever possible. Newer versions of the Android platform implement better mechanics for pushing updates to your app, such as the [Android Runtime (ART)](https://source.android.com/devices/tech/dalvik/) and native support for [multiple DEX files](https://developer.android.com/studio/build/multidex).

**Note:** After your first clean build, you may notice that subsequent builds—clean and incremental—perform much faster (even without using any of the optimizations described on this page). This is because the Gradle daemon has a "warm\-up" period of increasing performance—similar to other JVM processes.

## Optimize your build configuration

Follow these tips to improve the build speed of your Android Studio project.

### Keep your tools up\-to\-date

The Android tools receive build optimizations and new features with almost every update, and some tips on this page assume you're using the latest version. To take advantage of the latest optimizations, keep the following up to date:

*   [Android Studio and SDK tools](https://developer.android.com/studio/intro/update)
*   [The Android plugin for Gradle](https://developer.android.com/studio/releases/gradle-plugin)

### Create a build variant for development

Many of the configurations you need when [preparing your app for release](https://developer.android.com/studio/publish/preparing) are not required while developing your app. Enabling unnecessary build processes slows down your incremental and clean builds, so [configure a build variant](https://developer.android.com/studio/build/build-variants) that keeps only the build configurations you need while developing your app. The following sample creates a "dev" flavor and a "prod" flavor (for your release version configurations):

android { ...defaultConfig {...}buildTypes {...}productFlavors { // When building a variant that uses this flavor, the following configurations // override those in the defaultConfig block.dev { // To avoid using legacy multidex when building from the command line, // set minSdkVersion to 21 or higher. When using Android Studio 2.3 or higher, // the build automatically avoids legacy multidex when deploying to a device running // API level 21 or higher—regardless of what you set as your minSdkVersion.minSdkVersion 21versionNameSuffix "\-dev"applicationIdSuffix '.dev' }prod { // If you've configured the defaultConfig block for the release version of // your app, you can leave this block empty and Gradle uses configurations in // the defaultConfig block instead. You still need to create this flavor. // Otherwise, all variants use the "dev" flavor configurations. } }
}

If your build configuration already uses product flavors to create different versions of your app, you can combine the "dev" and "prod" configurations with those flavors by [using flavor dimensions](https://developer.android.com/studio/build/build-variants#flavor-dimensions). For example, if you already configure a "demo" and "full" flavor, you can use the following sample configuration to create combined flavors, such as "devDemo" and "prodFull":

android { ...defaultConfig {...}buildTypes {...} // Specifies the flavor dimensions you want to use. The order in which you // list each dimension determines its priority, from highest to lowest, // when Gradle merges variant sources and configurations. You must assign // each product flavor you configure to one of the flavor dimensions.flavorDimensions "stage",  "mode"productFlavors {dev {dimension "stage"minSdkVersion 21versionNameSuffix "\-dev"applicationIdSuffix '.dev' ... }prod {dimension "stage" ... }demo {dimension "mode" ... }full {dimension "mode" ... } }
}

### Enable single\-variant project sync

[Syncing your project](https://developer.android.com/studio/build#sync-files) with your build configuration is an important step in letting Android Studio understand how your project is structured. However, this process can be time\-consuming for large projects. If your project uses multiple build variants, you can now optimize project syncs by limiting them to only the variant you have currently selected.

You need to use Android Studio 3.3 or higher with Android Gradle Plugin 3.3.0 or higher to enable this optimization. The optimization is enabled by default on all projects.

To enable this optimization manually, click **File > Settings > Experimental > Gradle** (**Android Studio > Preferences > Experimental > Gradle** on a Mac) and select the **Only sync the active variant** checkbox.

**Note:** This optimization fully supports projects that include Java and C++ languages, and has some support for Kotlin. When enabling the optimization for projects with Kotlin content, Gradle sync falls back to using full variants internally.

### Avoid compiling unnecessary resources

Avoid compiling and packaging resources that you aren't testing (such as additional language localizations and screen\-density resources). You can do that by only specifying one language resource and screen density for your "dev" flavor, as shown in the following sample:

android { ...productFlavors {dev { ... // The following configuration limits the "dev" flavor to using // English stringresources and xxhdpi screen\-density resources.resConfigs "en",  "xxhdpi" } ... }
}

### Disable Crashlytics for your debug builds

If you don't need to run a [Crashlytics report](https://docs.fabric.io/android/crashlytics/overview.html), speed up your debug builds by disabling the plugin as follows:

android { ...buildTypes {debug {
      ext.enableCrashlytics \=  false }
}

You also need to disable the Crashlytics kit at runtime for debug builds by changing the way you initialize support for Fabric in your app, as shown below:

[Kotlin](#kotlin) [Java](#java) [More](#)

// Initializes Fabric for builds that don't use the debug build type.
Crashlytics.Builder() .core(CrashlyticsCore.Builder().disabled(BuildConfig.DEBUG).build()) .build() .also { crashlyticsKit \-> Fabric.with(this, crashlyticsKit) }

// Initializes Fabric for builds that don't use the debug build type.
Crashlytics crashlyticsKit \=  new  Crashlytics.Builder() .core(new  CrashlyticsCore.Builder().disabled(BuildConfig.DEBUG).build()) .build();

Fabric.with(this, crashlyticsKit);

#### Disable automatic build ID generation

If you want to use Crashlytics with your debug builds, you can still speed up incremental builds by preventing Crashlytics from updating app resources with its own unique build ID during every build. Because this build ID is stored in a resource file that is referenced by the manifest, disabling automatic build ID generation also allows you to [use Apply Changes alongside Crashlytics](https://developer.android.com/studio/run#apply-changes-libraries-plugins) for your debug builds.

To prevent Crashlytics from automatically updating its build ID, add the following to your `build.gradle` file:

android { ...buildTypes {debug {
      ext.alwaysUpdateBuildId \=  false }
}

For more information about optimizing your builds while using Crashlytics, read the [official documentation](https://docs.fabric.io/android/crashlytics/build-tools.html).

### Use static build config values with your debug build

Always use static/hard\-coded values for properties that go in the manifest file or resource files for your debug build type.

For example, using dynamic version codes, version names, resources, or any other build logic that changes the manifest file requires a full APK build every time you want to run a change—even though the actual change might otherwise require only a hot swap. If your build configuration requires such dynamic properties, then isolate them to your release build variants and keep the values static for your debug builds, as shown in the `build.gradle` file below.

int MILLIS\_IN\_MINUTE \=  1000  \*  60
int minutesSinceEpoch \=  System.currentTimeMillis()  / MILLIS\_IN\_MINUTE

android { ...defaultConfig { // Making either of these two values dynamic in the defaultConfig will // require a full APK build and reinstallation because the AndroidManifest.xml // must be updated.versionCode 1versionName "1.0" ... } // The defaultConfig values above are fixed, so your incremental builds don't // need to rebuild the manifest (and therefore the whole APK, slowing build times). // But for release builds, it's okay. So the following script iterates through // all the known variants, finds those that are "release" build types, and // changes those properties to something dynamic.
    applicationVariants.all { variant \-> if  (variant.buildType.name \==  "release")  {
            variant.mergedFlavor.versionCode \= minutesSinceEpoch;
            variant.mergedFlavor.versionName \= minutesSinceEpoch +  "\-"  + variant.flavorName; } }
}

### Use static dependency versions

When you declare dependencies in your `build.gradle` files, you should avoid using version numbers with a plus sign at the end, such as `'com.android.tools.build:gradle:2.+'`. Using dynamic version numbers can cause unexpected version updates, difficulty resolving version differences, and slower builds caused by Gradle checking for updates. You should use static/hard\-coded version numbers instead.

### Create library modules

Look for code in your app that you can convert into an [Android library module](https://developer.android.com/studio/projects/android-library). Modularizing your code this way allows the build system to compile only the modules you modify and cache those outputs for future builds. It also makes [parallel project execution](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:parallel_execution) more effective (when you enable that optimization).

### Create tasks for custom build logic

After you [create a build profile](#profile), if it shows that a relatively long portion of the build time is spent in the "Configuring Projects" phase, review your `build.gradle` scripts and look for code that you can include in a custom Gradle task. By moving some build logic into a task, it is run only when required, results can be cached for subsequent builds, and that build logic becomes eligible to run in parallel (if you enable [parallel project execution](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:parallel_execution)). To learn more, read the [official Gradle documentation](https://docs.gradle.org/current/userguide/more_about_tasks.html).

**Tip:** If your build includes a large number of custom tasks, you may want to declutter your `build.gradle` files by [creating custom task classes](https://docs.gradle.org/current/userguide/custom_tasks.html). Add you classes to the `project-root/buildSrc/src/main/groovy/` directory and Gradle automatically includes them in the classpath for all `build.gradle` files in your project.

### Convert images to WebP

[WebP](https://developers.google.com/speed/webp/) is an image file format that provides lossy compression (like JPEG) as well as transparency (like PNG) but can provide better compression than either JPEG or PNG. Reducing image file sizes, without having to perform build\-time compression, can speed up your builds, especially if your app uses a lot of image resources. However, you may notice a small increase in device CPU usage while decompressing WebP images. Using Android Studio, you can easily [convert your images to WebP](https://developer.android.com/studio/write/convert-webp#convert_images_to_webp).

### Disable PNG crunching

If you can't (or don't want to) [convert your PNG images to WebP](https://developer.android.com/studio/write/convert-webp#convert_images_to_webp), you can still speed up your build by disabling automatic image compression every time you build your app. If you're using [Android plugin 3.0.0](https://developer.android.com/studio/releases/gradle-plugin#3-0-0) or higher, PNG crunching is disabled by default for only the "debug" build type. To disable this optimization for other build types, add the following to your `build.gradle` file:

android {buildTypes {release { // Disables PNG crunching for the release build type.crunchPngs false } }

// If you're using an older version of the plugin, use the
// following:
//  aaptOptions {
//      cruncherEnabled false
//  }
}

Because build types or product flavors don't define this property, you need to manually set this property to `true` when building the release version of your app.

### Use incremental annotation processors

Android Gradle plugin 3.3.0 and higher improve support for [incremental annotation processing](https://docs.gradle.org/4.10.1/userguide/java_plugin.html#sec:incremental_annotation_processing). So, to improve incremental build speeds, you should update your Android Gradle plugin and use only incremental annotation processors whenever possible.

**Note:** This feature is compatible with Gradle versions 4.10.1 and higher, except for Gradle 5.1 (see [Gradle issue #8194](https://github.com/gradle/gradle/issues/8194)).

To get started, see the following table of popular annotation processors that support incremental annotation processing. For a more complete list, see [State of support in popular annotation processors](https://docs.gradle.org/current/userguide/java_plugin.html#state_of_support_in_popular_annotation_processors). Some of the annotation processors may require additional steps to enable the optimization, so make sure to read documentation for each annotation processor.

Additionally, if you use Kotlin in your app, you need to use [kapt 1.3.30 and higher](https://kotlinlang.org/docs/reference/kapt.html#incremental-annotation-processing-since-1330) to support incremental annotation processors for your Kotlin code. Make sure to read the official documentation on whether you need to manually enable this behavior.

Keep in mind, if you have to use one or more annotation processors that don't support incremental builds, annotation processing won't be incremental. However, if your project is using kapt, Java compilation is still incremental.

#### *Incremental annotation processor support*

| Project name | Annotation processor class name | Supported since... |
| --- | --- | --- |
| **DataBinding** | android.databinding.annotationprocessor.ProcessDataBinding | AGP 3.5 |
| **Room** | androidx.room.RoomProcessor | 2.3.0\-alpha02

2.20: Use [`room.incremental` option](https://developer.android.com/jetpack/androidx/releases/room#compiler-options). |
| **ButterKnife** | butterknife.compiler.ButterKnifeProcessor | 10.2.0 |
| **Glide** | com.bumptech.glide.annotation.compiler.GlideAnnotationProcessor | 4.9.0 |
| **Dagger** | dagger.internal.codegen.ComponentProcessor | 2.18 |
| **Lifecycle** | androidx.lifecycle.LifecycleProcessor | 2.2.0\-alpha02 |
| **AutoService** | com.google.auto.service.processor.AutoServiceProcessor | 1.0\-rc7 |
| **Dagger** | dagger.android.processor.AndroidProcessor | 2.18 |
| **Realm** | io.realm.processor.RealmProcessor | 5.11.0 |
| **Lombok** | lombok.launch.AnnotationProcessorHider$AnnotationProcessor | 1.16.22 |
| **Lombok** | lombok.launch.AnnotationProcessorHider$ClaimingProcessor | 1.16.22 |

### Configure the JVM garbage collector

Build performance can be improved by configuring the optimal JVM garbage collector used by Gradle. While JDK 8 is configured to use the parallel garbage collector by default, JDK 9 and higher are configured to use [the G1 garbage collector](https://docs.oracle.com/javase/9/gctuning/garbage-first-garbage-collector.htm#JSGCT-GUID-ED3AB6D3-FD9B-4447-9EDF-983ED2F7A573).

To potentially improve build performance, we recommend [testing your Gradle builds](https://developer.android.com/studio/build/profile-your-build) with the parallel garbage collector. In `gradle.properties` set the following:

org.gradle.jvmargs\=\-XX:+UseParallelGC

If there are other options already set in this field, add a new option:

org.gradle.jvmargs\=\-Xmx1536m  \-XX:+UseParallelGC

To measure build speed with different configurations, see [Profile your build](https://developer.android.com/studio/build/profile-your-build#profiling_different_memorycpu_settings).
