# Use Java 8 language features and APIs

- [Use Java 8 language features and APIs](#use-java-8-language-features-and-apis)
  - [Java 8 language feature support (Android Gradle Plugin 3.0.0+)](#java-8-language-feature-support-android-gradle-plugin-300)
  - [Java 8+ API desugaring support (Android Gradle Plugin 4.0.0+)](#java-8-api-desugaring-support-android-gradle-plugin-400)

Android Gradle plugin 3.0.0 and later support all Java 7 language features and a subset of Java 8 language features that vary by platform version. When building your app using Android Gradle plugin 4.0.0 and higher, you can use a number of Java 8 language APIs without requiring a minimum API level for your app.

This page describes the Java 8 language features you can use, how to properly configure your project to use them, and any known issues you may encounter. Also see the following video for an overview.

**Note:** When developing apps for Android, using Java 8 language features is optional. You can keep your project's source and target compatibility values set to Java 7, but you still need to compile using JDK 8.

The Android Gradle plugin provides built\-in support for using certain Java 8 language features and third\-party libraries that use them. As shown in figure 1, the default toolchain implements the new language features by performing bytecode transformations, called `desugar`, as part of the D8/R8 compilation of class files into dex code.

![](https://developer.android.com/studio/images/write/desugar_diagram.png)

**Figure 1.** Java 8 language feature support using `desugar` bytecode transformations.

## Java 8 language feature support (Android Gradle Plugin 3.0.0+)

To start using supported Java 8 language features, [update the Android plugin](https://developer.android.com/studio/releases/gradle-plugin#updating-plugin) to `3.0.0` (or higher). After that, for each module that uses Java 8 language features (either in its source code or through dependencies), update the module's `build.gradle` file, as shown below:

```
android {  ...  // Configure only for each module that uses Java 8  // language features (either in its source code or  // through dependencies).  compileOptions {    sourceCompatibility JavaVersion.VERSION_1_8    targetCompatibility JavaVersion.VERSION_1_8  }  // For Kotlin projects  kotlinOptions {    jvmTarget = "1.8"  }}
```

When building your app using Android Gradle plugin 3.0.0 and higher, the plugin does not support all Java 8 language features. The following language features are now available on any API level:

| Java 8 language feature | Notes |
| --- | --- |
| [Lambda expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) | Note that Android does not support the serialization of lambda expressions. |
| [Method references](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html) |   |
| [Type annotations](https://docs.oracle.com/javase/tutorial/java/annotations/type_annotations.html) | Type annotation information is only available at compile time, and not at runtime. Also, the platform supports [`TYPE`](https://developer.android.com/reference/java/lang/annotation/ElementType#TYPE) in API level 24 and below, but not `ElementType.TYPE_USE` or `ElementType.TYPE_PARAMETER`. |
| [Default and static interface methods](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) |   |
| [Repeating annotations](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html) |   |

In addition to the Java 8 language features above, plugin versions 3.0.0 and later extend support for [`try`\-with\-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) to all Android API levels.

Desugar currently doesn't support [`MethodHandle.invoke`](https://docs.oracle.com/javase/8/docs/api/java/lang/invoke/MethodHandle.html#invoke-java.lang.Object...-) or [`MethodHandle.invokeExact`](https://docs.oracle.com/javase/8/docs/api/java/lang/invoke/MethodHandle.html#invokeExact-java.lang.Object...-). If your source code or one of your module dependencies use one of these methods you need to specify `minSdkVersion 26` or higher. Otherwise, you get the following error:

```
Dex: Error converting bytecode to dex:
Cause: signature-polymorphic method called without --min-sdk-version >= 26

```

In some cases, your module may not be using the `invoke` or `invokeExact` methods even when they're included in a library dependency. So, to keep using that library with `minSdkVersion 25` or lower, [enable code shrinking](https://developer.android.com/studio/build/shrink-code#shrink-code) to remove unused methods. If that doesn't work, consider using an alternative library that doesn't use the unsupported methods.

Java 8+ language features desugaring that is available from Android Gradle Plugin 3.0.0 does not make any additional classes and APIs (such as `java.util.stream.*`) available for use on older Android releases. Support for partial Java API desugaring is available from Android Gradle Plugin 4.0.0 or higher and is described in the following section.

## Java 8+ API desugaring support (Android Gradle Plugin 4.0.0+)

If you're building your app using Android Gradle plugin 4.0.0 or higher, the plugin extends support for using a number of Java 8 language APIs without requiring a minimum API level for your app.

This additional support for older platform versions is possible because plugin 4.0.0 and higher extend the desugaring engine to also desugar Java language APIs. So, you can include standard language APIs that were available only in recent Android releases (such as `java.util.streams`) in apps that support older versions of Android.

The following set of APIs are supported when building your app using Android Gradle plugin 4.0.0 or higher:

*   Sequential streams (`java.util.stream`)
*   A subset of `java.time`
*   `java.util.function`
*   Recent additions to `java.util.{Map,Collection,Comparator}`
*   Optionals (`java.util.Optional`, `java.util.OptionalInt` and `java.util.OptionalDouble`) and some other new classes useful with the above APIs
*   Some additions to `java.util.concurrent.atomic` (new methods on `AtomicInteger`, `AtomicLong` and `AtomicReference`)
*   `ConcurrentHashMap` (with bug fixes for Android 5.0)

For a complete list of supported APIs, visit [Java 8+ APIs available through desugaring](https://developer.android.com/studio/write/java8-support-table).

To support these language APIs, the plugin compiles a separate DEX file that contains an implementation of the missing APIs and includes it in your app. The desugaring process rewrites your app’s code to instead use this library at runtime.

To enable support for these language APIs on any version of the Android platform, [update the Android plugin](https://developer.android.com/studio/releases/gradle-plugin#updating-plugin) to `4.0.0` (or higher) and include the following in your module’s `build.gradle` file:

```
android {  defaultConfig {    // Required when setting minSdkVersion to 20 or lower    multiDexEnabled true  }  compileOptions {    // Flag to enable support for the new language APIs    coreLibraryDesugaringEnabled true    // Sets Java compatibility to Java 8    sourceCompatibility JavaVersion.VERSION_1_8    targetCompatibility JavaVersion.VERSION_1_8  }}dependencies {  coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:1.0.9'}
```