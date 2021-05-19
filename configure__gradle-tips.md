gradle-tips# Gradle tips and recipes

- [Manage projects and sources](#manage-projects-and-sources)
  - [Change default source set configurations](#change-default-source-set-configurations)
  - [Configure project\-wide properties](#configure-project-wide-properties)
- [Manage libraries and dependencies](#manage-libraries-and-dependencies)
  - [Target specific builds with dependency configurations](#target-specific-builds-with-dependency-configurations)
- [Create different versions of your app](#create-different-versions-of-your-app)
  - [Configure multiple APK support](#configure-multiple-apk-support)
    - [Configure separate APKs per screen density](#configure-separate-apks-per-screen-density)
    - [Configure separate APKs per ABI](#configure-separate-apks-per-abi)
  - [Configure dynamic version codes](#configure-dynamic-version-codes)
  - [Combine multiple product flavors](#combine-multiple-product-flavors)
  - [Filter variants](#filter-variants)
- [Test your app](#test-your-app)
  - [Configure lint options](#configure-lint-options)
  - [Configure instrumentation manifest settings](#configure-instrumentation-manifest-settings)
  - [Change the test build type](#change-the-test-build-type)
  - [Configure Gradle test options](#configure-gradle-test-options)
- [Optimize your build](#optimize-your-build)
  - [Shrink your code](#shrink-your-code)
- [Publish your app](#publish-your-app)
  - [Sign your app](#sign-your-app)
  - [Remove private signing information from your project](#remove-private-signing-information-from-your-project)
- [Simplify app development](#simplify-app-development)
  - [Share custom fields and resource values with your app's code](#share-custom-fields-and-resource-values-with-your-apps-code)
  - [Share properties with the manifest](#share-properties-with-the-manifest)

Gradle and the Android plugin for Gradle provide a flexible way to compile, build, and package your Android app or library. This page collects some useful tips and configurations to help you get the most out of each build. If you want to learn about ways to make your builds faster, read [Optimize Your Build Speed](https://developer.android.com/studio/build/optimize-your-build).

If you are new to Gradle, learn the basics by reading [Configure Your Build](https://developer.android.com/studio/build). You can also inspect the Android plugin's [DSL reference documentation](https://developer.android.com/reference/tools/gradle-api) to learn more about the properties used in this page.

## Manage projects and sources

Here are some configurations to manage your project's modules and their sources. To learn more about creating and managing projects and modules, read the [Projects Overview](https://developer.android.com/studio/projects).

### Change default source set configurations

You can use the `[sourceSets](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.api.AndroidSourceSet.html)` block in the module\-level `build.gradle` file to change where Gradle looks to gather files for each component of a [source set](https://developer.android.com/studio/build/build-variants#sourcesets).

android { ...sourceSets { // Encapsulates configurations for the main source set.main { // Changes the directory for Java sources. The default directory is // 'src/main/java'.
      java.srcDirs \=  \['other/java'\] // When you list multiple directories, Gradle uses all of them to collect // sources. You should avoid specifying a directory which is a parent to one // or more other directories you specify.
      res.srcDirs \=  \['other/res1',  'other/res2'\] // For each source set, you can specify only one Android manifest. // The following points Gradle to a different manifest for this source set.
      manifest.srcFile 'other/AndroidManifest.xml' ... } // Create additional blocks to configure other source sets.androidTest { // If all the files for a source set are located under a single root // directory, you can specify that directory using the setRoot property. // When gathering sources for the source set, Gradle looks only in locations // relative to the root directory you specify. For example, after applying // the configuration below for the androidTest source set, Gradle looks for // Java sources only in the src/tests/java/ directory.setRoot 'src/tests' ... } }
}
...

### Configure project\-wide properties

For projects that include multiple modules, it might be useful to define properties at the project level and share them across all modules. You can do this by adding [extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties) to the `ext` block in [the top\-level `build.gradle`](https://developer.android.com/studio/build#top-level) file.

buildscript {...}allprojects {...}

// This block encapsulates custom properties and makes them available to all
// modules in the project.ext { // The following are only a few examples of the types of properties you can define.compileSdkVersion \=  28 // You can also use this to specify versions for dependencies. Having consistent // versions between modules can avoid behavior conflicts.supportLibVersion \=  "28.0.0" ...
}
...

To access these properties from a module in the same project, use the following syntax in the [module\-level `build.gradle`](https://developer.android.com/studio/build#module-level) file.

android { // Use the following syntax to access properties you define at the project level: // rootProject.ext.property\_name
  compileSdkVersion rootProject.ext.compileSdkVersion ...
}
...dependencies {implementation "com.android.support:appcompat\-v7:${rootProject.ext.supportLibVersion}" ...
}

## Manage libraries and dependencies

Gradle provides a robust mechanism to [manage dependencies](https://developer.android.com/studio/build/dependencies), whether they're remote libraries or local [library modules](https://developer.android.com/studio/projects/android-library).

### Target specific builds with dependency configurations

If you want a dependency for only a specific [build variant](https://developer.android.com/studio/build/build-variants) source set or [testing source set](https://developer.android.com/studio/test#sourcesets), capitalize the [dependency configuration](https://developer.android.com/studio/build/dependencies#dependency-configurations) name and prefix it with the name of the build variant or testing source set.

android {...}

// Creates Gradle dependency configurations to use in the dependencies block.configurations { // For variants that combine a product flavor and build type, you need to // intitialize a placeholder for its dependency configuration.
  freeDebugRuntimeOnly{} ...
}dependencies { // Adds an implementation dependency only to the "free" product flavor.freeImplementation 'com.google.firebase:firebase\-ads:9.8.0' // Adds a runtimeOnly dependency only to the "freeDebug" build variant.
    freeDebugRuntimeOnly fileTree(dir:  'libs', include:  \['\*.jar'\]) // Adds a remote binary dependency only for local tests.testImplementation 'junit:junit:4.12' // Adds a remote binary dependency only for the instrumented test APK.androidTestImplementation 'com.android.support.test.espresso:espresso\-core:3.0.2'
}

## Create different versions of your app

Gradle and the Android plugin allow you to create different versions of your app from a single module by [configuring build variants](https://developer.android.com/studio/build/build-variants).

### Configure multiple APK support

With the Android plugin, you can [build multiple APKs](https://developer.android.com/studio/build/configure-apk-splits) that each target ABI or screen density, and take advantage of Google Play's [multiple APK support](https://developer.android.com/google/play/publishing/multiple-apks).

#### Configure separate APKs per screen density

To create separate APKs for different screen densities, add the [`android.splits.density`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.DensitySplitOptions.html) block to your module's `build.gradle` file.

android { ...splits { // Configures multiple APKs based on screen density.density { // Enables building multiple APKs.enable true // Specifies a list of screen densities Gradle should not create APKs for.exclude "ldpi",  "mdpi" // Alternatively, you can use the following to clear the default list of // screen densities and specify only the screen densities you want to build // APKs for: // reset() // include "hdpi", "xhdpi", "xxhdpi", "xxxhdpi" // Specifies a list of compatible screen size settings. This property // configures the [<compatible\-screens>](https://developer.android.com/guide/topics/manifest/compatible-screens-element) element in the manifest. You // typically don't need to configure this manifest property, but it's // important when building multiple APKs based on screen density.compatibleScreens 'normal',  'large',  'xlarge' } }
}

#### Configure separate APKs per ABI

To create separate APKs for each ABI, add the [`android.splits.abi`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.AbiSplitOptions.html) block to your module's `build.gradle` file.

android { ...splits { // Configures multiple APKs based on ABI.abi { // Enables building multiple APKs.enable true // By default all ABIs are included, so use reset() and include to specify that we only // want APKs for x86, armeabi\-v7a, and mips.
      reset() // Specifies a list of ABIs that Gradle should create APKs for.include "x86",  "armeabi\-v7a",  "mips" // Specify that we want to also generate a universal APK that includes all ABIs.universalApk true } }
}

### Configure dynamic version codes

By default, when Gradle generates APKs for your project, each APK has the same version information, as specified in the module\-level `build.gradle` file. Because the Google Play Store does not allow multiple APKs for the same app that all have the same version information, you need to ensure each APK has its own unique [versionCode](https://developer.android.com/studio/publish/versioning#appversioning) before you upload to the Play Store.

You can do this with custom build logic that assigns a different version code to each APK at build time. For example, when creating separate APKs for each ABI, automatic APK versioning looks something like this:

android { ...defaultConfig { ...versionCode 4 }splits { ... }
}

// Map for the version code that gives each ABI a value.
ext.abiCodes \=  \['armeabi\-v7a':1, mips:2, x86:3\]

// For per\-density APKs, create a similar map like this:
// ext.densityCodes = \['hdpi': 1, 'xhdpi': 2, 'xxhdpi': 3, 'xxxhdpi': 4\]

import com.android.build.OutputFile

// For each APK output variant, override versionCode with a combination of
// ext.abiCodes \* 1000 + variant.versionCode. In this example, variant.versionCode
// is equal to defaultConfig.versionCode. If you configure product flavors that
// define their own versionCode, variant.versionCode uses that value instead.
android.applicationVariants.all { variant \-> // Assigns a different version code for each output APK // other than the universal APK.
  variant.outputs.each { output \-> // Stores the value of ext.abiCodes that is associated with the ABI for this variant. def baseAbiVersionCode \= // Determines the ABI for this variant and returns the mapped value.
            project.ext.abiCodes.get(output.getFilter(OutputFile.ABI)) // Because abiCodes.get() returns null for ABIs that are not mapped by ext.abiCodes, // the following code does not override the version code for universal APKs. // However, because we want universal APKs to have the lowest version code, // this outcome is desirable. if  (baseAbiVersionCode !=  null)  { // Assigns the new version code to versionCodeOverride, which changes the version code // for only the output APK, not for the variant itself. Skipping this step simply // causes Gradle to use the value of variant.versionCode for the APK.
      output.versionCodeOverride \=baseAbiVersionCode \*  1000  + variant.versionCode } }
}

### Combine multiple product flavors

In some cases, you may want to [combine configurations from multiple product flavors](https://developer.android.com/studio/build/build-variants#flavor-dimensions). To do this, the Android plugin for Gradle allows you to create groups of product flavors, called *flavor dimensions*.

The following code sample uses the [`flavorDimensions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.AppExtension.html#com.android.build.gradle.AppExtension:flavorDimensions(java.lang.String[])) property to create a "mode" flavor dimension to group the "full" and "demo" product flavors, and an "api" flavor dimension to group product flavor configurations based on API level. Gradle then combines product flavors from the "mode" dimension with those of the "api" dimension.

android { ...buildTypes {debug {...}release {...} } // Specifies the flavor dimensions you want to use. The order in which you // list each dimension determines its priority, from highest to lowest, // when Gradle merges variant sources and configurations. You must assign // each product flavor you configure to one of the flavor dimensions.flavorDimensions "api",  "mode"productFlavors {demo { // Assigns this product flavor to the "mode" flavor dimension.dimension "mode" ... }full {dimension "mode" ... } // Configurations in the "api" product flavors override those in "mode" // flavors and the defaultConfig block. Gradle determines the priority // between flavor dimensions based on the order in which they appear next // to the flavorDimensions property above\-\-the first dimension has a higher // priority than the second, and so on.minApi24 {dimension "api"minSdkVersion '24' // To ensure the target device receives the version of the app with // the highest compatible API level, assign version codes in increasing // value with API level. To learn more about assigning version codes to // support app updates and uploading to Google Play, read [Multiple APK Support](https://developer.android.com/google/play/publishing/multiple-apks#HowItWorks)versionCode 30000  + android.defaultConfig.versionCode
      versionNameSuffix "\-minApi24" ... }minApi23 {dimension "api"minSdkVersion '23'versionCode 20000 + android.defaultConfig.versionCode
      versionNameSuffix "\-minApi23" ... }minApi21 {dimension "api"minSdkVersion '21'versionCode 10000 + android.defaultConfig.versionCode
      versionNameSuffix "\-minApi21" ... } }
}
...

### Filter variants

You can [filter build variants](https://developer.android.com/studio/build/build-variants#filter-variants) that you do not want using the [`variantFilter`](https://google.github.io/android-gradle-dsl/current/com.android.build.api.variant.VariantFilter.html) block in the module's `build.gradle` file. The following sample code tells Gradle to not build any variants that combine the "minApi21" and "demo" product flavors:

android {
 ...buildTypes {...}flavorDimensions "api",  "mode"productFlavors {demo {...}full {...}minApi24 {...}minApi23 {...}minApi21 {...} }variantFilter { variant \-> def names \= variant.flavors\*.name // To check for a build type instead, use variant.buildType.name == "buildType" if  (names.contains("minApi21")  && names.contains("demo"))  { // Gradle ignores any variants that satisfy the conditions above.
      setIgnore(true) } }
}
...

## Test your app

To learn more about running local and integrated unit tests, read [Test Your App](https://developer.android.com/studio/test).

### Configure lint options

You can configure certain lint options using the [`lintOptions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.LintOptions.html#com.android.build.gradle.internal.dsl.LintOptions) block in your module\-level `build.gradle` file. To learn more about using lint for your Android project, read [Improve Your Code with Lint](https://developer.android.com/studio/write/lint).

android { ...lintOptions { // Turns off checks for the issue IDs you specify.disable 'TypographyFractions','TypographyQuotes' // Turns on checks for the issue IDs you specify. These checks are in // addition to the default lint checks.enable 'RtlHardcoded',  'RtlCompat',  'RtlEnabled' // To enable checks for only a subset of issue IDs and ignore all others, // list the issue IDs with the 'check' property instead. This property overrides // any issue IDs you enable or disable using the properties above.check 'NewApi',  'InlinedApi' // If set to true, turns off analysis progress reporting by lint.quiet true // if set to true (default), stops the build if errors are found.abortOnError false // if true, only report errors.ignoreWarnings true }
}
...

### Configure instrumentation manifest settings

When Gradle builds your test APK, it automatically generates the [`AndroidManifest.xml`](https://developer.android.com/guide/topics/manifest/manifest-intro) file and configures it with the [`<instrumentation>`](https://developer.android.com/guide/topics/manifest/instrumentation-element) node. You can change some of the settings for this node by either creating another manifest file in the [test source set](https://developer.android.com/studio/test#test_types_and_location) or configuring your module\-level `build.gradle` file, as shown in the following code sample.

android { ... // Each product flavor you configure can override properties in the // defaultConfig block. To learn more, go to [Configure Product Flavors](https://developer.android.com/studio/build/build-variants#product-flavors).defaultConfig { ... // Specifies the [application ID](https://developer.android.com/studio/build/application-id) for the test APK. [testApplicationId](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:testApplicationId)  "com.test.foo" // Specifies the fully\-qualified class name of the test instrumentation runner. [testInstrumentationRunner](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:testInstrumentationRunner)  "android.test.InstrumentationTestRunner" // If set to 'true', enables the instrumentation class to start and stop profiling. // If set to false (default), profiling occurs the entire time the instrumentation // class is running. [testHandleProfiling](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:testHandleProfiling)  true // If set to 'true', indicates that the Android system should run the instrumentation // class as a functional test. The default value is 'false' [testFunctionalTest](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:testFunctionalTest)  true }
}
...

### Change the test build type

By default, all tests run against the debug build type. You can change this to another build type by using the `testBuildType` property in your module\-level `build.gradle` file. For example, if you want to run your tests against your "staging" build type, edit the file as shown in the following snippet.

android { ...testBuildType "staging"
}

### Configure Gradle test options

To specify options that change how Gradle runs all your tests, configure the [`testOptions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.TestOptions.html) block in the module\-level `build.gradle`.

android { ... // Encapsulates options for running tests.testOptions { // Changes the directory where Gradle saves test reports. By default, Gradle saves test reports // in the path\_to\_your\_project/module\_name/build/outputs/reports/ directory. // '$rootDir' sets the path relative to the root directory of the current project.reportDir "$rootDir/test\-reports" // Changes the directory where Gradle saves test results. By default, Gradle saves test results // in the path\_to\_your\_project/module\_name/build/outputs/test\-results/ directory. // '$rootDir' sets the path relative to the root directory of the current project.resultsDir "$rootDir/test\-results" }
}

To specify options for only local unit tests, configure the [`testOptions.unitTests`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.TestOptions.UnitTestOptions.html) block.

android { ...testOptions { ... // Encapsulates options for local unit tests.unitTests { // By default, local unit tests throw an exception any time the code you are testing tries to access // Android platform APIs (unless you [mock Android dependencies](https://developer.android.com/training/testing/unit-testing/local-unit-tests#mocking-dependencies) yourself or with a testing // framework like Mockito). However, you can enable the following property so that the test // returns either null or zero when accessing platform APIs, rather than throwing an exception.returnDefaultValues true // Encapsulates options for controlling how Gradle executes local unit tests. For a list // of all the options you can specify, read [Gradle's reference documentation](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/testing/Test.html).all { // Sets JVM argument(s) for the test JVM(s).jvmArgs '\-XX:MaxPermSize=256m' // You can also check the task name to apply options to only the tests you specify. if  (it.name \==  'testDebugUnitTest')  {systemProperty 'debug',  'true' } } } }
}

## Optimize your build

This section provides some configurations to help speed up your full and incremental builds. To learn more, read [Optimize Your Build Speed](https://developer.android.com/studio/build/optimize-your-build).

### Shrink your code

Android Studio uses R8, which consumes ProGuard rules files, to [shrink your code](https://developer.android.com/studio/build/shrink-code#shrink-code). For new projects, Android Studio uses a default settings file (`proguard-android.txt`) from the Android SDK's `tools/proguard/folder`. For even more code shrinking, try the `proguard-android-optimize.txt` file that's in the same location.

android {buildTypes {release {minifyEnabled true
      proguardFiles getDefaultProguardFile('**proguard\-android\-optimize.txt**'),
                                           'proguard\-rules.pro' } } ...
}
...

To add rules that are specific to each build variant, configure additional [`proguardFiles`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:proguardFiles) property for each flavor. For example, the following sample adds `flavor2-rules.pro` to "flavor2". Now the release version of "flavor2" uses all three rules files because those from the release block are also applied.

android { ...buildTypes {release {minifyEnabled true
      proguardFiles getDefaultProguardFile('proguard\-android.txt'),
             'proguard\-rules.pro' } }productFlavors {flavor1 { ... }flavor2 {proguardFile 'flavor2\-rules.pro' } }
}
...

## Publish your app

To learn more about publishing your app to Google Play, read [Publish Your App](https://developer.android.com/studio/publish).

### Sign your app

Although Android Studio provides an easy way to [configure signing for release builds](https://developer.android.com/studio/publish/app-signing#release-mode) from the UI, you can manually configure the `[signingConfigs](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.SigningConfig.html)` block in your module's `build.gradle` file:

android { ...defaultConfig {  ...  } // Encapsulates signing configurations.signingConfigs { // Creates a signing configuration called "release".release { // Specifies the path to your keystore file.
      storeFile file("my\-release\-key.jks") // Specifies the password for your keystore.storePassword "password" // Specifies the identifying name for your key.keyAlias "my\-alias" // Specifies the password for your key.keyPassword "password" } }buildTypes {release { // Adds the "release" signing configuration to the release build type.
      signingConfig signingConfigs.release ... } }
}
...

### Remove private signing information from your project

By default, signing configurations are recorded in plain text to the module's `build.gradle` file. If you are working with a team or an open\-source project, you can move this sensitive information out of the build files by proceeding as follows.

1.  Create a file named `keystore.properties` in the root directory of your project and include the following information:storePassword=myStorePassword
    keyPassword=myKeyPassword
    keyAlias=myKeyAlias
    storeFile=myStoreFileLocation
2.  In your `build.gradle` file, load the `keystore.properties` file as follows (this has to be before the android block):

    // Creates a variable called keystorePropertiesFile, and initializes it to the
    // keystore.properties file.
    def keystorePropertiesFile \= rootProject.file("keystore.properties")

    // Initializes a new Properties() object called keystoreProperties.
    def keystoreProperties \=  new  Properties()

    // Loads the keystore.properties file into the keystoreProperties object.
    keystoreProperties.load(new  FileInputStream(keystorePropertiesFile))android { ...
    }
    ...

3.  Input the signing information stored in the `keystoreProperties` object:

    android {signingConfigs {config {
          keyAlias keystoreProperties\['keyAlias'\]
          keyPassword keystoreProperties\['keyPassword'\]
          storeFile file(keystoreProperties\['storeFile'\])
          storePassword keystoreProperties\['storePassword'\] } } ...
    }
    ...

4.  Click **Sync Now** in the notification bar.

To learn more about app signing, read [Sign Your App](https://developer.android.com/studio/publish/app-signing).

## Simplify app development

The following tips help make developing your Android app easier.

### Share custom fields and resource values with your app's code

At build time, Gradle generates the `BuildConfig` class so your app code can inspect information about the current build. You can also add custom fields to the `BuildConfig` class from your Gradle build configuration file using the `buildConfigField()` method and access those values in your app's runtime code. Likewise, you can add app resource values with `resValue()`.

android { ...buildTypes {release { // These values are defined only for the release build, which // is typically used for full builds and continuous builds.
      buildConfigField("String",  "BUILD\_TIME",  "\\"${minutesSinceEpoch}\\"")
      resValue("string",  "build\_time",  "${minutesSinceEpoch}") ... }debug { // Use static values for incremental builds to ensure that // resource files and BuildConfig aren't rebuilt with each run. // If these rebuild dynamically, they can interfere with // Apply Changes as well as Gradle UP\-TO\-DATE checks.
      buildConfigField("String",  "BUILD\_TIME",  "\\"0\\"")
      resValue("string",  "build\_time",  "0") } }
}
...

In your app code, you can access the properties as follows:

[Kotlin](#kotlin) [Java](#java) [More](#)

...
Log.i(TAG,  BuildConfig.BUILD\_TIME)
Log.i(TAG, getString(R.string.build\_time))

...
Log.i(TAG,  BuildConfig.BUILD\_TIME);
Log.i(TAG, getString(R.string.build\_time));

### Share properties with the manifest

In some cases, you may need to declare the same property in both your manifest and your code (for example, when declaring authorities for a `[FileProvider](https://developer.android.com/reference/androidx/core/content/FileProvider)`). Rather than updating the same property in multiple locations to reflect a change, define a single property in your module's `build.gradle` file to have it available to both the manifest and your code, as shown in the following sample. To learn more, read [Inject Build Variables into the Manifest](https://developer.android.com/studio/build/manifest-build-variables).

android { // For settings specific to a product flavor, configure these properties // for each flavor in the productFlavors block.defaultConfig { // Creates a property for the [FileProvider](https://developer.android.com/reference/androidx/core/content/FileProvider) authority. def filesAuthorityValue \= applicationId +  ".files" // Creates a placeholder property to use in the manifest.manifestPlaceholders \= \[filesAuthority: filesAuthorityValue\] // Adds a new field for the authority to the BuildConfig class.
      buildConfigField("String",
                       "FILES\_AUTHORITY",
                       "\\"${filesAuthorityValue}\\"") } ...
}
...

In your manifest, access the placeholder as follows:

```
<manifest>... <application>... <provider android:name\="android.support.v4.content.FileProvider" android:authorities\="**${filesAuthority}**" android:exported\="false" android:grantUriPermissions\="true"\>... </provider> </application>
</manifest>
```

Accessing the `FILES_AUTHORITY` field in your app's code looks something like this:

[Kotlin](#kotlin) [Java](#java) [More](#)

```
val contentUri:  Uri  \=  FileProvider.getUriForFile(context,  BuildConfig.FILES\_AUTHORITY, myFile)
```

```
Uri contentUri \=  FileProvider.getUriForFile(getContext(), *BuildConfig.FILES\_AUTHORITY*,
  myFile);
```
