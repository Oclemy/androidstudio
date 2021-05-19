# Add build dependencies

- [Add build dependencies](#add-build-dependencies)
  - [Dependency types](#dependency-types)
    - [Native dependencies](#native-dependencies)
  - [Dependency configurations](#dependency-configurations)
    - [Deprecated configurations (available in AGP 1.0–4.2)](#deprecated-configurations-available-in-agp-10%E2%80%9342)
    - [Add annotation processors](#add-annotation-processors)
      - [Pass arguments to annotation processors](#pass-arguments-to-annotation-processors)
      - [Disable the annotation processor error check](#disable-the-annotation-processor-error-check)
    - [Exclude transitive dependencies](#exclude-transitive-dependencies)
      - [Exclude transitive dependencies from test configurations](#exclude-transitive-dependencies-from-test-configurations)
  - [Use variant\-aware dependency management](#use-variant-aware-dependency-management)
    - [Resolve build errors related to variant matching](#resolve-build-errors-related-to-variant-matching)
  - [Configure Wear OS app dependencies](#configure-wear-os-app-dependencies)
  - [Remote repositories](#remote-repositories)
  - [Google's Maven repository](#googles-maven-repository)
    - [Programmatic access](#programmatic-access)
    - [Offline repository from SDK Manager](#offline-repository-from-sdk-manager)
  - [Dependency order](#dependency-order)
  - [View module dependencies](#view-module-dependencies)
  - [Fix dependency resolution errors](#fix-dependency-resolution-errors)
    - [Fix duplicate class errors](#fix-duplicate-class-errors)
    - [Fix conflicts between classpaths](#fix-conflicts-between-classpaths)
  - [Apply custom build logic](#apply-custom-build-logic)
    - [Publish variant dependencies to custom logic](#publish-variant-dependencies-to-custom-logic)
    - [Custom dependency resolution strategies](#custom-dependency-resolution-strategies)

The Gradle build system in Android Studio makes it easy to include external binaries or other library modules to your build as dependencies. The dependencies can be located on your machine or in a remote repository, and any transitive dependencies they declare are automatically included as well. This page describes how to use dependencies with your Android project, including details about behaviors and configurations that are specific to the Android plugin for Gradle. For a deeper conceptual guide to Gradle dependencies, you should also see the [Gradle guide for dependency management](https://docs.gradle.org/current/userguide/dependency_management.html)—but remember that your Android project must use only the [dependency configurations](#dependency_configurations) defined on this page.

**Caution:** When specifying dependencies, you should not use dynamic version numbers, such as `'com.android.tools.build:gradle:3.+'`. Using this feature can cause unexpected version updates and difficulty resolving version differences.

## Dependency types

To add a dependency to your project, specify a dependency configuration such as `implementation` in the `dependencies` block of your `build.gradle` file.

For example, the following `build.gradle` file for an app module includes three different types of dependencies:

```
apply plugin: 'com.android.application'android { ... }dependencies {    // Dependency on a local library module    implementation project(":mylibrary")    // Dependency on local binaries    implementation fileTree(dir: 'libs', include: ['*.jar'])    // Dependency on a remote binary    implementation 'com.example.android:app-magic:12.3'}
```

Each of these requests a different kind of library dependency as follows:

**Local library module dependency**

implementation project(':mylibrary')

This declares a dependency on an [Android library module](https://developer.android.com/studio/projects/android-library) named "mylibrary" (this name must match the library name defined with an `include:` in your [`settings.gradle`](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html) file). When you build your app, the build system compiles the library module and packages the resulting compiled contents in the APK.

**Local binary dependency**

implementation fileTree(dir:  'libs', include:  \['\*.jar'\])

Gradle declares dependencies on JAR files inside your project's `module_name/libs/` directory (because Gradle reads paths relative to the `build.gradle` file).

Alternatively, you can specify individual files as follows:

implementation files('libs/foo.jar',  'libs/bar.jar')

**Remote binary dependency**

implementation 'com.example.android:app\-magic:12.3'

This is actually shorthand for the following:

implementation group:  'com.example.android', name:  'app\-magic', version:  '12.3'

This declares a dependency on version 12.3 of the "app\-magic" library, inside the "com.example.android" namespace group.

**Note:** Remote dependencies like this require that you declare the appropriate [remote repositories](#remote-repositories) where Gradle should look for the library. If the library does not already exist locally, Gradle pulls it from the remote site when the build requires it (such as when you click **Sync Project with Gradle Files** ![](https://developer.android.com/studio/images/buttons/toolbar-sync-gradle.png) or when you run a build).

### Native dependencies

As of Android Gradle Plugin 4.0, native dependencies can also be imported in the manner described in this document.

Depending on an AAR that exposes native libraries will automatically make them available to the build system used by `externalNativeBuild`. To access the libraries from your code, you must link to them in your native build scripts. For more information, see [Using native dependencies](https://developer.android.com/studio/build/native-dependencies).

## Dependency configurations

Inside the `dependencies` block, you can declare a library dependency using one of several different *dependency configurations* (such as `implementation` shown above). Each dependency configuration provides Gradle with different instructions about how to use the dependency. The following table describes each of the configurations you can use for a dependency in your Android project. The table also compares these configurations to those that were deprecated as of Android Gradle Plugin 3.0.0.

| Configuration | Behavior |
| --- | --- |
| `implementation` | Gradle adds the dependency to the compile classpath and packages the dependency to the build output. However, when your module configures an `implementation` dependency, it's letting Gradle know that you do not want the module to leak the dependency to other modules at compile time. That is, the dependency is available to other modules only at runtime.
Using this dependency configuration instead of `api` or `compile` (deprecated) can result in **significant build time improvements** because it reduces the number of modules that the build system needs to recompile. For example, if an `implementation` dependency changes its API, Gradle recompiles only that dependency and the modules that directly depend on it. Most app and test modules should use this configuration.

 |
| `api` | Gradle adds the dependency to the compile classpath and build output. When a module includes an `api` dependency, it's letting Gradle know that the module wants to transitively export that dependency to other modules, so that it's available to them at both runtime and compile time.

This configuration behaves just like `compile` (which is now deprecated), but you should use it with caution and only with dependencies that you need to transitively export to other upstream consumers. That's because, if an `api` dependency changes its external API, Gradle recompiles all modules that have access to that dependency at compile time. So, having a large number of `api` dependencies can significantly increase build time. Unless you want to expose a dependency's API to a separate module, library modules should instead use `implementation` dependencies.

 |
| `compileOnly` | Gradle adds the dependency to the compile classpath only (that is, it is not added to the build output). This is useful when you're creating an Android module and you need the dependency during compilation, but it's optional to have it present at runtime.

If you use this configuration, then your library module must include a runtime condition to check whether the dependency is available, and then gracefully change its behavior so it can still function if it's not provided. This helps reduce the size of the final APK by not adding transient dependencies that aren't critical. This configuration behaves just like `provided` (which is now deprecated).

**Note:** You can't use the `compileOnly` configuration with AAR dependencies.

 |
| `runtimeOnly` | Gradle adds the dependency to the build output only, for use during runtime. That is, it is not added to the compile classpath. This configuration behaves just like `apk` (which is now deprecated). |
| `annotationProcessor` |

To add a dependency on a library that is an annotation processor, you must add it to the annotation processor classpath using the `annotationProcessor` configuration. That's because using this configuration improves build performance by separating the compile classpath from the annotation processor classpath. If Gradle finds annotation processors on the compile classpath, it deactivates [compile avoidance](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_compile_avoidance), which negatively impacts build time (Gradle 5.0 and higher ignore annotation processors found on the compile classpath).

The Android Gradle Plugin assumes a dependency is an annotation processor if its JAR file contains the following file:
`META-INF/services/javax.annotation.processing.Processor`. If the plugin detects an annotation processor that's on the compile classpath, it produces a build error.

**Note:** Kotlin projects should [use kapt](https://kotlinlang.org/docs/reference/kapt.html) to declare annotation processor dependencies.

 |
| `lintChecks` | Use this configuration to include lint checks you want Gradle to execute when building your project.

**Note:** When using Android Gradle plugin 3.4.0 and higher, this dependency configuration no longer packages the lint checks in your Android Library projects. To include lint check dependencies in your AAR libraries, use the `lintPublish` configuration described below.

 |
| `lintPublish` | Use this configuration in Android library projects to include lint checks you want Gradle to compile into a `lint.jar` file and package in your AAR. This causes projects that consume your AAR to also apply those lint checks. If you were previously using the `lintChecks` dependency configuration to include lint checks in the published AAR, you need to migrate those dependencies to instead use the `lintPublish` configuration.

dependencies {
  // Executes lint checks from the ':checks' project
  // at build time.
  lintChecks project(':checks')
  // Compiles lint checks from the ':checks\-to\-publish'
  // into a lint.jar file and publishes it to your
  // Android library.
  lintPublish project(':checks\-to\-publish')
}

 |

### Deprecated configurations (available in AGP 1.0–4.2)

| Configuration | Behavior |
| --- | --- |
| `apk` | Gradle adds the dependency to the build output only, for use during runtime. That is, it is not added to the compile classpath. |
| `compile` | Gradle adds the dependency to the compile classpath and build output. Exports the dependency to other modules. |
| `provided` | Gradle adds the dependency to the compile classpath only (that is, it is not added to the build output). |

All of the above configurations apply dependencies to all build variants. If you instead want to declare a dependency for only a specific [build variant](https://developer.android.com/studio/build/build-variants) source set or for a [testing source set](https://developer.android.com/studio/test#sourcesets), you must capitalize the configuration name and prefix it with the name of the build variant or testing source set.

For example, to add an `implementation` dependency only to your "free" product flavor (using a remote binary dependency), it looks like this:

```
dependencies {    freeImplementation 'com.google.firebase:firebase-ads:9.8.0'}
```

However, if you want to add a dependency for a variant that combines a product flavor *and* a build type, then you must initialize the configuration name in the `configurations` block. The following sample adds a `runtimeOnly` dependency to your "freeDebug" build variant (using a local binary dependency):

```
configurations {    // Initializes a placeholder for the freeDebugRuntimeOnly dependency    // configuration.    freeDebugRuntimeOnly {}}dependencies {    freeDebugRuntimeOnly fileTree(dir: 'libs', include: ['*.jar'])}
```

To add `implementation` dependencies for your local tests and instrumented tests , it looks like this:

```
dependencies {    // Adds a remote binary dependency only for local tests.    testImplementation 'junit:junit:4.12'    // Adds a remote binary dependency only for the instrumented test APK.    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'}
```

However, certain configurations don't make sense in this situation. For example, because other modules can't depend on `androidTest`, you get the following warning if you use the `androidTestApi` configuration:

WARNING: Configuration 'androidTestApi' is obsolete and has been replaced with
'androidTestImplementation'.

### Add annotation processors

If you add annotation processors to your compile classpath, you'll see an error message similar to the following:

Error: Annotation processors must be explicitly declared now.

To resolve this error, add annotation processors to your project by configuring your dependency using `annotationProcessor` as shown below:

dependencies { // Adds libraries defining annotations to only the compile classpath.compileOnly 'com.google.dagger:dagger:version\-number' // Adds the annotation processor dependency to the annotation processor classpath.annotationProcessor 'com.google.dagger:dagger\-compiler:version\-number'
}

**Note:** Android Plugin for Gradle 3.0.0+ no longer supports [`android-apt` plugin.](https://bitbucket.org/hvisser/android-apt)

#### Pass arguments to annotation processors

If you need to pass arguments to an annotation processor, you can do so using the [`AnnotationProcessorOptions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.AnnotationProcessorOptions.html) block in your module's build configuration. For example, if you want to pass primitive data types as key\-value pairs, you can use the `argument` property, as shown below:

```
android {    ...    defaultConfig {        ...        javaCompileOptions {            annotationProcessorOptions {                argument "key1", "value1"                argument "key2", "value2"            }        }    }}
```

However, when using Android Gradle plugin 3.2.0 and higher, you need to pass processor arguments that represent files or directories using Gradle's [`CommandLineArgumentProvider`](https://docs.gradle.org/current/javadoc/org/gradle/process/CommandLineArgumentProvider.html) interface.

Using `CommandLineArgumentProvider` allows you or the annotation processor author to improve the correctness and performance of incremental and cached clean builds by applying [incremental build property type annotations](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks) to each argument.

For example, the class below implements `CommandLineArgumentProvider` and annotates each argument for the processor. The sample also uses the Groovy language syntax and is included directly in the module's `build.gradle` file.

**Note:** Typically, annotation processor authors provide either this class or instructions on how to write such a class. That's because each argument needs to specify the correct build property type annotation in order to work as intended.

```
class MyArgsProvider implements CommandLineArgumentProvider {    // Annotates each directory as either an input or output for the    // annotation processor.    @InputFiles    // Using this annotation helps Gradle determine which part of the file path    // should be considered during up-to-date checks.    @PathSensitive(PathSensitivity.RELATIVE)    FileCollection inputDir    @OutputDirectory    File outputDir    // The class constructor sets the paths for the input and output directories.    MyArgsProvider(FileCollection input, File output) {        inputDir = input        outputDir = output    }    // Specifies each directory as a command line argument for the processor.    // The Android plugin uses this method to pass the arguments to the    // annotation processor.    @Override    Iterable<String> asArguments() {        // Use the form '-Akey[=value]' to pass your options to the Java compiler.        ["-AinputDir=${inputDir.singleFile.absolutePath}",         "-AoutputDir=${outputDir.absolutePath}"]    }}android {...}
```

After you create a class that implements `CommandLineArgumentProvider`, you need to initialize and pass it to the Android plugin using the [`annotationProcessorOptions.compilerArgumentProvider`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.AnnotationProcessorOptions.html#com.android.build.gradle.internal.dsl.AnnotationProcessorOptions:compilerArgumentProviders) property, as shown below.

```
// This is in your module's build.gradle file.android {    defaultConfig {        javaCompileOptions {            annotationProcessorOptions {                // Creates a new MyArgsProvider object, specifies the input and                // output paths for the constructor, and passes the object                // to the Android plugin.                compilerArgumentProvider new MyArgsProvider(files("input/path"),                                         new File("output/path"))            }        }    }}
```

To learn more about how implementing `CommandLineArgumentProvider` helps improve build performance, read [Caching Java projects](https://guides.gradle.org/using-build-cache/#caching_java_projects).

#### Disable the annotation processor error check

If you have dependencies on the compile classpath that include annotation processors you don't need, you can disable the error check by adding the following to your `build.gradle` file. Keep in mind, the annotation processors you add to the compile classpath are still not added to the processor classpath.

```
android {    ...    defaultConfig {        ...        javaCompileOptions {            annotationProcessorOptions {                includeCompileClasspath false            }        }    }}
```

If you experience issues after migrating your project's annotation processors to the processor classpath, you can allow annotation processors on the compile classpath by setting `includeCompileClasspath` to `true`. However, setting this property to `true` is not recommended, and the option to do so will be removed in a future update of the Android plugin.

### Exclude transitive dependencies

As an app grows in scope, it can contain a number of dependencies including direct dependencies and transitive dependencies (libraries which your app's imported libraries depend on). To exclude transitive dependencies that you no longer need, you can use the `exclude` keyword as given below:

```
dependencies {    implementation('some-library') {        exclude group: 'com.example.imgtools', module: 'native'    }}
```

#### Exclude transitive dependencies from test configurations

If you need to exclude certain transitive dependencies from your tests, the code sample shown above might not work as expected. That's because a test configuration (e.g., `androidTestImplementation`) extends the module's `implementation` configuration. That is, it always contains `implementation` dependencies when Gradle resolves the configuration.

So, to exclude transitive dependencies from your tests, you must do so at execution time, as shown below:

```
android.testVariants.all { variant ->    variant.getCompileConfiguration().exclude group: 'com.jakewharton.threetenabp', module: 'threetenabp'    variant.getRuntimeConfiguration().exclude group: 'com.jakewharton.threetenabp', module: 'threetenabp'}
```

**Note:** You can still use the `exclude` keyword in the dependencies block as shown in the original code sample from the [**Exclude dependencies**](#exclude_dependencies) section to omit transitive dependencies that are specific to the test configuration and are not included in other configurations.

## Use variant\-aware dependency management

Android plugin 3.0.0 and higher include a new dependency mechanism that automatically matches variants when consuming a library. This means an app's `debug` variant automatically consumes a library's `debug` variant, and so on. It also works when using flavors—an app's `freeDebug` variant will consume a library's `freeDebug` variant.

In order for the plugin to accurately match variants, you need to [provide matching fallbacks](#resolve_matching_errors) for instances where a direct match is not possible. Consider if your app configures a build type called "staging", but one of its library dependencies does not. When the plugin tries to build the "staging" version of your app, it won't know which version of the library to use, and you'll see an error message similar to the following:

Error:Failed to resolve: Could not resolve project :mylibrary.
Required by:
    project :app

### Resolve build errors related to variant matching

The plugin includes DSL elements to help you control how Gradle should resolve situations in which a direct variant match between an app and a dependency is not possible. Consult the table below to determine which DSL property you should use to resolve certain build errors related to variant\-aware dependency matching.

| Cause of build error | Resolution |
|

**Your app includes a build type that a library dependency does not.**

For example, your app includes a "staging" build type, but a dependency includes only a "debug" and "release" build type.

Note that there is no issue when a library dependency includes a build type that your app does not. That's because the plugin simply never requests that build type from the dependency.

 |

Use `matchingFallbacks` to specify alternative matches for a given build type, as shown below:

// In the app's build.gradle file.android {buildTypes {debug {}release {}staging { // Specifies a sorted list of fallback build types that the // plugin should try to use when a dependency does not include a // "staging" build type. You may specify as many fallbacks as you // like, and the plugin selects the first build type that's // available in the dependency.matchingFallbacks \=  \['debug',  'qa',  'release'\] } }
}

 |
|

For a given flavor dimension that exists in both the app and its library dependency, **your app includes flavors that the library does not.**

For example, both your app and its library dependencies include a "tier" flavor dimension. However, the "tier" dimension in the app includes "free" and "paid" flavors, but a dependency includes only "demo" and "paid" flavors for the same dimension.

Note that, for a given flavor dimension that exists in both the app and its library dependencies, there is no issue when a library includes a product flavor that your app does not. That's because the plugin simply never requests that flavor from the dependency.

 |

Use `matchingFallbacks` to specify alternative matches for the app's "free" product flavor, as shown below:

// In the app's build.gradle file.android {
    defaultConfig{ // Do not configure matchingFallbacks in the defaultConfig block. // Instead, you must specify fallbacks for a given product flavor in the // productFlavors block, as shown below. }flavorDimensions 'tier'productFlavors {paid {dimension 'tier' // Because the dependency already includes a "paid" flavor in its // "tier" dimension, you don't need to provide a list of fallbacks // for the "paid" flavor. }free {dimension 'tier' // Specifies a sorted list of fallback flavors that the plugin // should try to use when a dependency's matching dimension does // not include a "free" flavor. You may specify as many // fallbacks as you like, and the plugin selects the first flavor // that's available in the dependency's "tier" dimension.matchingFallbacks \=  \['demo',  'trial'\] } }
}

 |
|

**A library dependency includes a flavor dimension that your app does not.**

For example, a library dependency includes flavors for a "minApi" dimension, but your app includes flavors for only the "tier" dimension. So, when you want to build the "freeDebug" version of your app, the plugin doesn't know whether to use the "minApi23Debug" or "minApi18Debug" version of the dependency.

Note that there is no issue when your app includes a flavor dimension that a library dependency does not. That's because the plugin matches flavors of only the dimensions that exist in the dependency. For example, if a dependency did not include a dimension for ABIs, the "freeX86Debug" version of your app would simply use the "freeDebug" version of the dependency.

 |

Use `missingDimensionStrategy` in the `defaultConfig` block to specify the default flavor the plugin should select from each missing dimension, as shown in the sample below. You can also override your selections in the `productFlavors` block, so each flavor can specify a different matching strategy for a missing dimension.

// In the app's build.gradle file.android {
    defaultConfig{ // Specifies a sorted list of flavors that the plugin should try to use from // a given dimension. The following tells the plugin that, when encountering // a dependency that includes a "minApi" dimension, it should select the // "minApi18" flavor. You can include additional flavor names to provide a // sorted list of fallbacks for the dimension.missingDimensionStrategy 'minApi',  'minApi18',  'minApi23' // You should specify a missingDimensionStrategy property for each // dimension that exists in a local dependency but not in your app.missingDimensionStrategy 'abi',  'x86',  'arm64' }flavorDimensions 'tier'productFlavors {free {dimension 'tier' // You can override the default selection at the product flavor // level by configuring another missingDimensionStrategy property // for the "minApi" dimension.missingDimensionStrategy 'minApi',  'minApi23',  'minApi18' }paid {} }
}

 |

## Configure Wear OS app dependencies

Configuring dependencies for a [Wear OS](https://developer.android.com/training/wearables) module is similar to that of any other module. That is, they use the same dependency configurations, such as `implementation` and `compileOnly`.

Wear modules also support [variant\-aware dependency management](#variant_aware). As a result, if your base app module has a dependency on a Wear module, each variant of the base module consumes the matching variant from the Wear module. If you are building a simple app with a dependency on only one Wear module, where the module configures the same variants as your base module, you need to specify the `wearApp` configuration in your base module's `build.gradle` file as shown below:

```
dependencies {    // If the main and Wear app modules have the same variants,    // variant-aware dependency management automatically matches    // variants of the main app module with that of the wear module.    wearApp project(':wearable')}
```

If you have multiple Wear modules and you want to specify a different Wear module per app flavor, you can do so using the `flavorWearApp` configuration, as follows (however, you can't include other dependencies that use the `wearApp` configuration):

```
dependencies {    paidWearApp project(':wear1')    demoWearApp project(':wear1')    freeWearApp project(':wear2')}
```

## Remote repositories

When your dependency is something other than a local library or file tree, Gradle looks for the files in whichever online repositories are specified in the `repositories` block of your `build.gradle` file. The order in which you list each repository determines the order in which Gradle searches the repositories for each project dependency. For example, if a dependency is available from both repository A and B, and you list A first, Gradle downloads the dependency from repository A.

By default, new Android Studio projects specifies [Google's Maven repository](#google-maven) and JCenter as repository locations in the project's top\-level `build.gradle` file, as shown below:

```
allprojects {    repositories {        google()        jcenter()    }}
```

If you want something from the Maven central repository, then add `mavenCentral()`, or for a local repository use `mavenLocal()`:

```
allprojects {    repositories {        google()        jcenter()        mavenCentral()        mavenLocal()    }}
```

Or you can declare specific Maven or Ivy repositories as follows:

```
allprojects {    repositories {        maven {            url "https://repo.example.com/maven2"        }        maven {            url "file://local/repo/"        }        ivy {            url "https://repo.example.com/ivy"        }    }}
```

For more information, see the [Gradle Repositories guide](https://docs.gradle.org/current/userguide/dependency_management.html#sec:repositories).

## Google's Maven repository

The most recent versions of the following Android libraries are available from Google's Maven repository:

*   [Android Support Library](https://developer.android.com/topic/libraries/support-library)
*   [Architecture Components Library](https://developer.android.com/topic/libraries/architecture)
*   [Constraint Layout Library](https://developer.android.com/training/constraint-layout)
*   [AndroidX Test](https://developer.android.com/training/testing)
*   [Databinding Library](https://developer.android.com/topic/libraries/data-binding)
*   [Android Instant App Library](https://developer.android.com/topic/instant-apps)
*   [Wear OS](https://developer.android.com/training/building-wearables)
*   [Google Play services](https://developers.google.com/android/guides/setup)
*   [Google Play Billing Library](https://developer.android.com/google/play/billing)
*   [Firebase](https://firebase.google.com/docs/android/setup)

You can see all available artifacts at [Google's Maven repository index](https://maven.google.com) (see below for [programmatic access](#gmaven-access)).

To add one of these libraries to your build, include Google's Maven repository in your top\-level `build.gradle` file:

```
allprojects {    repositories {        google()        // If you're using a version of Gradle lower than 4.1, you must instead use:        // maven {        //     url 'https://maven.google.com'        // }        // An alternative URL is 'https://dl.google.com/dl/android/maven2/'    }}
```

Then add the desired library to your module's `dependencies` block. For example,the [appcompat library](https://developer.android.com/topic/libraries/support-library/packages#v7-appcompat) looks like this:

```
dependencies {    implementation 'com.android.support:appcompat-v7:28.0.0'}
```

However, if you're trying to use an older version of the above libraries and your dependency fails, then it's not available in the Maven repository and you must instead get the library from the offline repository.

### Programmatic access

For programmatic access to Google's Maven artifacts, you can get an XML list of artifact groups from [maven.google.com/master\-index.xml](https://maven.google.com/master-index.xml). Then, for any group, you can view its library names and versions at:

*maven.google.com/group\_path/group\-index.xml*

For example, libraries in the android.arch.lifecycle group are listed at [maven.google.com/android/arch/lifecycle/group\-index.xml](https://maven.google.com/android/arch/lifecycle/group-index.xml).

You can also download the POM and JAR files at:

*maven.google.com/group\_path/library/version /library\-version.ext*

For example: [maven.google.com/android/arch/lifecycle/compiler/1.0.0/compiler\-1. 0.0.pom](https://maven.google.com/android/arch/lifecycle/compiler/1.0.0/compiler-1.0.0.pom).

### Offline repository from SDK Manager

For libraries not available from the Google Maven repository (usually older library versions), you must download the offline **Google Repository** package from the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager).

Then you can add these libraries to your `dependencies` block as usual.

The offline libraries are saved in `android_sdk/extras/`.

## Dependency order

The order in which you list your dependencies indicates the priority for each: the first library is higher priority than the second, the second is higher priority than the third, and so on. This order is important in the event that [resources are merged](https://developer.android.com/studio/write/add-resources#resource_merging) or [manifest elements are merged](https://developer.android.com/studio/build/manifest-merge) into your app from the libraries.

For example, if your project declares the following:

*   Dependency on `LIB_A` and `LIB_B` (in that order)
*   And `LIB_A` depends on `LIB_C` and `LIB_D` (in that order)
*   And `LIB_B` also depends on `LIB_C`

Then, the flat dependency order will be as follows:

1.  `LIB_A`
2.  `LIB_D`
3.  `LIB_B`
4.  `LIB_C`

This ensures that both `LIB_A` and `LIB_B` can override `LIB_C`; and `LIB_D` is still higher priority than `LIB_B` because `LIB_A` (which depends on it) has higher priority than `LIB_B`.

For more information about how manifests from different project sources/dependencies are merged, see [Merge multiple manifest files](https://developer.android.com/studio/build/manifest-merge).

## View module dependencies

Some direct dependencies may have dependencies of their own. These are called *transitive dependencies*. Rather than requiring you to manually declare each transitive dependency, Gradle automatically gathers and adds them for you. The Android plugin for Gradle provides a task that displays a list of the dependencies Gradle resolves for a given module.

For each module, the report also groups the dependencies based on build variant, testing source set, and classpath. The following is sample report for an app module's runtime classpath of its debug build variant and compile classpath of its instrumented test source set.

```
debugRuntimeClasspath - Dependencies for runtime/packaging
+--- :mylibrary (variant: debug)
+--- com.google.android.material:material:1.0.0@aar
+--- androidx.appcompat:appcompat:1.0.2@aar
+--- androidx.constraintlayout:constraintlayout:1.1.3@aar
+--- androidx.fragment:fragment:1.0.0@aar
+--- androidx.vectordrawable:vectordrawable-animated:1.0.0@aar
+--- androidx.recyclerview:recyclerview:1.0.0@aar
+--- androidx.legacy:legacy-support-core-ui:1.0.0@aar
...

debugAndroidTest
debugAndroidTestCompileClasspath - Dependencies for compilation
+--- androidx.test.ext:junit:1.1.0@aar
+--- androidx.test.espresso:espresso-core:3.1.1@aar
+--- androidx.test:runner:1.1.1@aar
+--- junit:junit:4.12@jar
...

```

To run the task, proceed as follows:

1.  Select **View > Tool Windows > Gradle** (or click **Gradle** ![](https://developer.android.com/studio/images/buttons/window-gradle.png) in the tool windows bar).
2.  Expand **AppName > Tasks > android** and double\-click **androidDependencies**. After Gradle executes the task, the **Run** window should open to display the output.

For more information about managing dependencies in Gradle, see [Dependency management basics](http://www.gradle.org/docs/current/userguide/artifact_dependencies_tutorial.html) in the Gradle User Guide.

## Fix dependency resolution errors

When you add multiple dependencies to your app project, those direct and transitive dependencies might conflict with one another. The Android Gradle Plugin tries to resolve these conflicts gracefully, but some conflicts may lead to compile time or runtime errors.

To help you investigate which dependencies are contributing to errors, [inspect your app's dependency tree](#view-dependency-tree) and look for dependencies that appear more than once or with conflicting versions.

If you can't easily identify the duplicate dependency, try using Android Studio's UI to search for dependencies that include the duplicate class as follows:

1.  Select **Navigate > Class** from the menu bar.
2.  In the pop\-up search dialog, make sure that the box next to **Include non\-project items** is checked.
3.  Type the name of the class that appears in the build error.
4.  Inspect the results for the dependencies that include the class.

The following sections describe the different types of dependency resolution errors you may encounter and how to fix them.

### Fix duplicate class errors

If a class appears more than once on the runtime classpath, you get an error similar to the following:

Program type already present com.example.MyClass

This error typically occurs due to one of the following circumstances:

*   A binary dependency includes a library that your app also includes as a direct dependency. For example, your app declares a direct dependency on Library A and Library B, but Library A already includes Library B in its binary.
    *   **To resolve this issue**, remove Library B as a direct dependency.
*   Your app has a local binary dependency and a remote binary dependency on the same library.
    *   **To resolve this issue**, remove one of the binary dependencies.

### Fix conflicts between classpaths

When Gradle resolves the compile classpath, it first resolves the *runtime* classpath and uses the result to determine what versions of dependencies should be added to the compile classpath. In other words, the runtime classpath determines the required version numbers for identical dependencies on downstream classpaths.

Your app's runtime classpath also determines the version numbers that Gradle requires for matching dependencies in the runtime classpath for the app's test APK. The hierarchy of classpaths is described in figure 1.

![](https://developer.android.com/studio/images/build/classpath_sync-2x.png)

**Figure 1.** Version numbers of dependencies that appear across multiple classpaths must match according to this hierarchy.

A conflict where different versions of the same dependency appears across multiple classpaths migh occur when, for example, your app includes a version of a dependency using the `implementation` [dependency configuration](https://developer.android.com/studio/build/dependencies#dependency_configurations) and a library module includes a different version of the dependency using the `runtimeOnly` configuration.

When resolving dependencies on your runtime and compile time classpaths, Android Gradle plugin 3.3.0 and higher attempt to automatically fix certain downstream version conflicts. For example, if the runtime classpath includes Library A version 2.0 and the compile classpath includes Library A version 1.0, the plugin automatically updates the dependency on the compile classpath to Library A version 2.0 to avoid errors.

However, if the runtime classpath includes Library A version 1.0 and the compile classpath includes Library A version 2.0, the plugin does not downgrade the dependency on the compile classpath to Library A version 1.0, and you still get an error similar to the following:

Conflict with dependency 'com.example.library:some\-lib:2.0' in project 'my\-library'.
Resolved versions for runtime classpath (1.0) and compile classpath (2.0) differ.

To resolve this issue, do one of the following:

*   Include the desired version of the dependency as an `api` dependency to your library module. That is, only your library module declares the dependency, but the app module will also have access to its API, transitively.
*   Alternatively, you can declare the dependency in both modules, but you should make sure that each module uses the same version of the dependency. Consider [configuring project\-wide properties](https://developer.android.com/studio/build/gradle-tips#configure-project-wide-properties) to ensure versions of each dependency remain consistent throughout your project.

## Apply custom build logic

This section describes advanced topics that are useful when you want to extend the Android Gradle plugin or write your own plugin.

### Publish variant dependencies to custom logic

A library can have functionalities that other projects or sub\-projects might want to use. Publishing a library is the process by which the library is made available to its consumers. Libraries can control which dependencies its consumers have access to at compile time and runtime.

There are two separate configurations that hold the transitive dependencies of each classpath which must be used by consumers to consume the library as described below:

*   `variant_nameApiElements`: This configuration holds the transitive dependencies that are available to consumers at compile time.
*   `variant_nameRuntimeElements`: This configuration holds the transitive dependencies that are available to consumers at runtime.

To learn more about the relationships between the different configurations, go to [The Java Library plugin configurations](https://docs.gradle.org/current/userguide/java_library_plugin.html#sec:java_library_configurations_graph).

### Custom dependency resolution strategies

A project may include a dependency on two different versions of the same library which can lead to dependency conflicts. For example, if your project depends on version 1 of module A and version 2 of module B, and module A transitively depends on version 3 of module B, there arises a dependency version conflict.

To resolve this conflict, the Android Gradle Plugin uses the following dependency resolution strategy: when the plugin detects that different versions of the same module are in the dependency graph, by default, it chooses the one with the highest version number.

However, this strategy might not always work as you intend. To customize the dependency resolution strategy, use the following configurations to resolve specific dependencies of a variant that are needed for your task:

*   `variant_nameCompileClasspath`: This configuration contains the resolution strategy for a given variant’s compile classpath.
*   `variant_nameRuntimeClasspath`: This configuration contains the resolution strategy for a given variant’s runtime classpath.

The Android Gradle plugin includes getters that you can use to access the configuration objects of each variant. Thus, you can use the variant API to query the dependency resolution as shown in the example below:

```groovy
android {
    applicationVariants.all { variant \-> // Return compile configuration objects of a variant.
        variant.getCompileConfiguration().resolutionStrategy { // Use Gradle's [ResolutionStrategy API](https://docs.gradle.org/current/dsl/org.gradle.api.artifacts.ResolutionStrategy.html) // to customize how this variant resolves dependencies. ... } // Return runtime configuration objects of a variant.
        variant.getRuntimeConfiguration().resolutionStrategy { ... } // Return annotation processor configuration of a variant.
        variant.getAnnotationProcessorConfiguration().resolutionStrategy { ... } }
}
```