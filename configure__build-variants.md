# Configure build variants

- [Configure build variants](#configure-build-variants)
  - [Configure build types](#configure-build-types)
  - [Configure product flavors](#configure-product-flavors)
    - [Combine multiple product flavors with flavor dimensions](#combine-multiple-product-flavors-with-flavor-dimensions)
    - [Filter variants](#filter-variants)
  - [Create source sets](#create-source-sets)
    - [Change default source set configurations](#change-default-source-set-configurations)
    - [Build with source sets](#build-with-source-sets)
  - [Declare dependencies](#declare-dependencies)
  - [Configure signing settings](#configure-signing-settings)
    - [Signing Wear OS apps](#signing-wear-os-apps)

This page builds on the [Configure your build overview](https://developer.android.com/studio/build) to show you how you can configure build variants to create different versions of your app from a single project, and how to properly manage your dependencies and signing configurations.

Each *build variant* represents a different version of your app that you can build. For example, you might want to build one version of your app that's free, with a limited set of content, and another paid version that includes more. You can also build different versions of your app that target different devices, based on API level or other device variations. However, if you want to build different versions based on the device ABI or screen density, instead [build multiple APKs](https://developer.android.com/studio/build/configure-apk-splits).

Build variants are the result of Gradle using a [specific set of rules](#sourceset-build) to combine settings, code, and resources configured in your build types and product flavors. Although you do not configure build variants directly, you do configure the build types and product flavors that form them.

For example, a "demo" *product flavor* can specify different features and device requirements, such as custom source code, resources, and minimum API levels, while the "debug" *build type* applies different build and packaging settings, such as debug options and signing keys. The resulting build variant is the "demoDebug" version of your app, and it includes a combination of the configurations and resources included in the "demo" product flavor, "debug" build type, and `main/` source set.

## Configure build types

You can create and configure build types in the module\-level `build.gradle` file inside the `android` block. When you create a new module, Android Studio automatically creates the debug and release build types for you. Although the debug build type doesn't appear in the build configuration file, Android Studio configures it with [`debuggable true`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html#com.android.build.gradle.internal.dsl.BuildType:debuggable). This allows you to debug the app on secure Android devices and configures APK signing with a generic debug keystore.

You can add the debug build type to your configuration if you want to add or change certain settings. The following sample specifies an [`applicationIdSuffix`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html#com.android.build.gradle.internal.dsl.BuildType:applicationIdSuffix) for the debug build type, and configures a "staging" build type that is initialized using settings from the debug build type.

android {defaultConfig {manifestPlaceholders \=  \[hostName:"www.example.com"\] ... }buildTypes {release {minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard\-android.txt'),  'proguard\-rules.pro' }debug {applicationIdSuffix ".debug"debuggable true } /\*\*
         \* The \`initWith\` property allows you to copy configurations from other build types,
         \* then configure only the settings you want to change. This one copies the debug build
         \* type, and then changes the [manifest placeholder](https://developer.android.com/studio/build/manifest-build-variables) and [application ID](https://developer.android.com/studio/build/application-id).
         \*/staging {initWith debug
            manifestPlaceholders \=  \[hostName:"internal.example.com"\]applicationIdSuffix ".debugStaging" } }
}

**Note:** When you make changes to a build configuration file, Android Studio requires that you sync your project with the new configuration. To sync your project, you can click **Sync Now** in the notification bar that appears as soon as you make a change or **Sync Project** ![](https://developer.android.com/studio/images/buttons/toolbar-sync-gradle.png) from the toolbar. If Android Studio notices any errors with your configuration, the **Messages** window appears to describe the issue.

To learn more about all the properties you can configure with build types, read the [build type DSL reference](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html).

## Configure product flavors

Creating product flavors is similar to creating build types: add them to the `productFlavors` block in your build configuration and include the settings you want. The product flavors support the same properties as `defaultConfig`—this is because `defaultConfig` actually belongs to the [`ProductFlavor`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html) class. This means you can provide the base configuration for all flavors in the `defaultConfig` block, and each flavor can change any of these default values, such as the [`applicationId`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:applicationId). To learn more about the application ID, read [Set the Application ID](https://developer.android.com/studio/build/application-id).

**Note:** You still need to specify a package name using the [`package`](https://developer.android.com/guide/topics/manifest/manifest-element#package) attribute in the `main/` manifest file. You must also use that package name in your source code to refer to the R class, or resolve any relative activity or service registration. This allows you to use `applicationId` to give each product flavor a unique ID for packaging and distribution, without having to change your source code.

All flavors must belong to a named flavor dimension, which is a group of product flavors. You must assign all flavors to a flavor dimension; otherwise, you will get the build error shown below. If a given module specifies only one flavor dimension, the Android Gradle plugin automatically assigns all of the module's flavors to that dimension.

 Error:All flavors must now belong to a named flavor dimension.
  The flavor 'flavor\_name' is not assigned to a flavor dimension.

**Tip:** The plugin tries to match variants of your app with those of local library dependencies. Because [variant\-aware dependency matching](https://developer.android.com/studio/build/dependencies#variant_aware) relies on how you name flavor dimension, **name your flavor dimensions carefully**. Doing so gives you more control over which code and resources from your local dependencies are matched with each version of the app.

The following code sample creates a flavor dimension named "version" and adds "demo" and "full" product flavors. These flavors provide their own [`applicationIdSuffix`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:applicationIdSuffix) and [`versionNameSuffix`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:versionNameSuffix):

android { ...defaultConfig {...}buildTypes {
        debug{...}
        release{...} } // Specifies one flavor dimension. [flavorDimensions](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.AppExtension.html#com.android.build.gradle.AppExtension:flavorDimensions(java.lang.String[]))  "version"productFlavors {demo { // Assigns this product flavor to the "version" flavor dimension. // If you are using only one dimension, this property is optional, // and the plugin automatically assigns all the module's flavors to // that dimension.dimension "version"applicationIdSuffix ".demo"versionNameSuffix "\-demo" }full {dimension "version"applicationIdSuffix ".full"versionNameSuffix "\-full" } }
}

**Note:** To distribute your app using [Multiple APK Support](https://developer.android.com/google/play/publishing/multiple-apks) in Google Play, assign the same `applicationId` value to all variants and give each variant a different [`versionCode`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:versionCode). To distribute different variants of your app as separate apps in Google Play, you need to assign a different `applicationId` to each variant.

After you create and configure your product flavors, click **Sync Now** in the notification bar. After the sync completes, Gradle automatically creates build variants based on your build types and product flavors, and names them according to `<product-flavor><Build-Type>`. For example, if you created "demo" and "full" product flavors, and kept the default "debug" and "release" build types, Gradle creates the following build variants:

*   *demoDebug*
*   *demoRelease*
*   *fullDebug*
*   *fullRelease*

You can change the build variant to whichever one you want to build and run—just go to **Build** > **Select Build Variant** and select one from the drop\-down menu. To start customizing each build variant with its own features and resources, however, you'll need to know how to [create and manage source sets](#sourcesets).

### Combine multiple product flavors with flavor dimensions

In some cases, you may want to combine configurations from multiple product flavors. For example, you may want to create different configurations for the "full" and "demo" product flavors that are based on API level. To do this, the Android plugin for Gradle allows you to create multiple groups of product flavors as flavor dimensions. When building your app, Gradle combines a product flavor configuration from each flavor dimension you define, along with a build type configuration, to create the final build variant. Gradle does not combine product flavors that belong to the same flavor dimension.

**Tip:** To create different versions of your app based on ABI and screen density, you should [build multiple APKs](https://developer.android.com/studio/build/configure-apk-splits) instead of using product flavors.

The following code sample uses the [`flavorDimensions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.AppExtension.html#com.android.build.gradle.AppExtension:flavorDimensions(java.lang.String[])) property to create a "mode" flavor dimension to group the "full" and "demo" product flavors, and an "api" flavor dimension to group product flavor configurations based on API level:

android { ...buildTypes {debug {...}release {...} } // Specifies the flavor dimensions you want to use. The order in which you // list each dimension determines its priority, from highest to lowest, // when Gradle merges variant sources and configurations. You must assign // each product flavor you configure to one of the flavor dimensions.flavorDimensions "api",  "mode"productFlavors {demo { // Assigns this product flavor to the "mode" flavor dimension.dimension "mode" ... }full {dimension "mode" ... } // Configurations in the "api" product flavors override those in "mode" // flavors and the defaultConfig block. Gradle determines the priority // between flavor dimensions based on the order in which they appear next // to the flavorDimensions property above\-\-the first dimension has a higher // priority than the second, and so on.minApi24 {dimension "api"minSdkVersion 24 // To ensure the target device receives the version of the app with // the highest compatible API level, assign version codes in increasing // value with API level. To learn more about assigning version codes to // support app updates and uploading to Google Play, read [Multiple APK Support](https://developer.android.com/google/play/publishing/multiple-apks#HowItWorks)versionCode 30000  + android.defaultConfig.versionCode
      versionNameSuffix "\-minApi24" ... }minApi23 {dimension "api"minSdkVersion 23versionCode 20000 + android.defaultConfig.versionCode
      versionNameSuffix "\-minApi23" ... }minApi21 {dimension "api"minSdkVersion 21versionCode 10000 + android.defaultConfig.versionCode
      versionNameSuffix "\-minApi21" ... } }
}
...

The number of build variants Gradle creates is equal to the product of the number of flavors in each flavor dimension and the number of build types you configure. When Gradle names each build variant or corresponding APK, product flavors belonging to higher\-priority flavor dimension appear first, followed by those from lower\-priority dimensions, followed by the build type. Using the build configuration above as an example, Gradle creates a total of 12 build variants with the following naming scheme:

Build variant: `[minApi24, minApi23, minApi21][Demo, Full][Debug, Release]`

Corresponding APK: `app-[minApi24, minApi23, minApi21]-[demo, full]-[debug, release].apk`

For example,

Build variant: `minApi24DemoDebug`

Corresponding APK: `app-minApi24-demo-debug.apk`

In addition to the source set directories you can create for each individual product flavor and build variant, you can also create source set directories for each *combination* of product flavors. For example, you can create and add Java sources to the `src/demoMinApi24/java/` directory, and Gradle uses those sources only when building a variant that combines those two product flavors. Source sets you create for product flavor combinations have a higher priority than source sets that belong to each individual product flavor. To learn more about source sets and how Gradle merges resources, read the section about how to [Create Source Sets](#sourcesets).

### Filter variants

Gradle creates a build variant for every possible combination of the product flavors and build types that you configure. However, there may be certain build variants that either you do not need or do not make sense in the context of your project. You can remove certain build variant configurations by creating a variant filter in your module\-level `build.gradle` file.

Using the build configuration from the previous section as an example, suppose you plan to support only API levels 23 and higher for the demo version of the app. You can use the [`variantFilter`](https://google.github.io/android-gradle-dsl/current/com.android.build.api.variant.VariantFilter.html) block to filter out all build variant configurations that combine the "minApi21" and "demo" product flavors:

android { ...buildTypes {...}flavorDimensions "api",  "mode"productFlavors {demo {...}full {...}minApi24 {...}minApi23 {...}minApi21 {...} }variantFilter { variant \-> def names \= variant.flavors\*.name // To check for a certain build type, use variant.buildType.name == "<buildType>" if  (names.contains("minApi21")  && names.contains("demo"))  { // Gradle ignores any variants that satisfy the conditions above.
          setIgnore(true) } }
}
...

After you add a variant filter to your build configuration and click **Sync Now** in the notification bar, Gradle ignores any build variants that meet the conditions you specify, and they no longer appear in the drop down menu when you click **Build > Select Build Variant** from the menu bar (or **Build Variants** ![](https://developer.android.com/studio/images/buttons/window-monitor.png) in the tool window bar).

## Create source sets

By default, Android Studio creates the `main/` [source set](https://developer.android.com/studio/build#sourcesets) and directories for everything you want to share between all your build variants. However, you can create new source sets to control exactly what files Gradle compiles and packages for specific build types, product flavors (and combinations of product flavors when [using flavor dimensions](https://developer.android.com/studio/build/build-variants#flavor-dimensions)), and build variants. For example, you can define basic functionality in the `main/` source set and use product flavor source sets to change the branding of your app for different clients, or include special permissions and logging functionality only for build variants that use the debug build type.

Gradle expects you to organize source set files and directories a certain way, similar to the `main/` source set. For example, Gradle expects Java class files that are specific to your "debug" build type to be located in the `src/debug/java/` directory.

The Android plugin for Gradle provides a useful Gradle task that shows you how to organize your files for each of your build types, product flavors, and build variants. For example, the following sample from the task output describes where Gradle expects to find certain files for the "debug" build type:

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-
Project :app
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

...

debug
\-\-\-\-
Compile configuration: compile
build.gradle name: android.sourceSets.debug
Java sources: \[app/src/debug/java\]
Manifest file: app/src/debug/AndroidManifest.xml
Android resources: \[app/src/debug/res\]
Assets: \[app/src/debug/assets\]
AIDL sources: \[app/src/debug/aidl\]
RenderScript sources: \[app/src/debug/rs\]
JNI sources: \[app/src/debug/jni\]
JNI libraries: \[app/src/debug/jniLibs\]
Java\-style resources: \[app/src/debug/resources\]

To view this output, proceed as follows:

1.  Click **Gradle** ![](https://developer.android.com/images/tools/as-gradle.png) on the right side of the IDE window.
2.  Navigate to **MyApplication > Tasks > android** and double\-click **sourceSets**. After Gradle executes the task, the **Run** window should then open to display the output.
3.  If the display is not in text mode as shown above, click **Toggle view** ![](https://developer.android.com/studio/images/buttons/build-toggle_view-2x.png) on the left side of the **Run** window.

**Note:** The task output also shows you how to organize source sets for files you want to use to run tests for your app, such as the `test/` and `androidTest/` [testing source sets](https://developer.android.com/studio/test#sourcesets).

When you create a new build variant, Android Studio doesn't create the source set directories for you, but it does give you a few options to help you. For example, to create just the `java/` directory for your "debug" build type:

1.  Open the **Project** pane and select the **Project** view from the drop\-down menu at the top of the pane.
2.  Navigate to `MyProject/app/src/`.
3.  Right\-click the `src` directory and select **New** > **Folder** > **Java Folder**.
4.  From the drop\-down menu next to **Target Source Set**, select **debug**.
5.  Click **Finish**.

Android Studio creates a source set directory for your debug build type, and then creates the `java/` directory inside it. Alternatively, you can make Android Studio create the directories for you when you add a new file to your project for a specific build variant. For example, to create a values XML file for your "debug" build type:

1.  In the same **Project** pane, right\-click the `src` directory and select **New** > **XML** > **Values XML File**.
2.  Enter the name for the XML file or keep the default name.
3.  From the drop\-down menu next to **Target Source Set**, select **debug**.
4.  Click **Finish**.

Because the "debug" build type was specified as the target source set, Android Studio automatically creates the necessary directories when it creates the XML file. The resulting directory structure should look like figure 2.

![](https://developer.android.com/images/tools/debug-directories_2-1_2x.png)

**Figure 2.** New source set directories for the debug build type.

Using the same procedure, you can also create source set directories for product flavors, such as `src/demo/`, and build variants, such as `src/demoDebug/`. Additionally, you can create testing source sets that target specific build variants, such as `src/androidTestDemoDebug/`. To learn more, go to [Testing source sets](https://developer.android.com/studio/test#sourcesets).

### Change default source set configurations

If you have sources that are not organized into the default source set file structure that Gradle expects, as described above in the section about [creating source sets](#sourcesets), you can use the [`sourceSets`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.api.AndroidSourceSet.html) block to change where Gradle looks to gather files for each component of a source set. You don't need to relocate the files; you only need to provide Gradle with the path(s), relative to the module\-level `build.gradle` file, where Gradle should expect to find files for each source set component. To learn which components you can configure, and whether you can map them to multiple files or directories, read the [Android plugin for Gradle DSL reference](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.api.AndroidSourceSet.html).

The following code sample maps sources from the `app/other/` directory to certain components of the `main` source set and changes the root directory of the `androidTest` source set.

android { ...sourceSets { // Encapsulates configurations for the main source set.main { // Changes the directory for Java sources. The default directory is // 'src/main/java'.
      java.srcDirs \=  \['other/java'\] // If you list multiple directories, Gradle uses all of them to collect // sources. Because Gradle gives these directories equal priority, if // you define the same resource in more than one directory, you get an // error when merging resources. The default directory is 'src/main/res'.
      res.srcDirs \=  \['other/res1',  'other/res2'\] // **Note:** You should avoid specifying a directory which is a parent to one // or more other directories you specify. For example, avoid the following: // res.srcDirs = \['other/res1', 'other/res1/layouts', 'other/res1/strings'\] // You should specify either only the root 'other/res1' directory, or only the // nested 'other/res1/layouts' and 'other/res1/strings' directories. // For each source set, you can specify only one Android manifest. // By default, Android Studio creates a manifest for your main source // set in the src/main/ directory.
      manifest.srcFile 'other/AndroidManifest.xml' ... } // Create additional blocks to configure other source sets.androidTest { // If all the files for a source set are located under a single root // directory, you can specify that directory using the setRoot property. // When gathering sources for the source set, Gradle looks only in locations // relative to the root directory you specify. For example, after applying the // configuration below for the androidTest source set, Gradle looks for Java // sources only in the src/tests/java/ directory.setRoot 'src/tests' ... } }
}
...

### Build with source sets

You can use source set directories to contain the code and resources you want packaged only with certain configurations. For example, if you are building the "demoDebug" build variant, which is the crossproduct of a "demo" product flavor and "debug" build type, Gradle looks at these directories, and gives them the following priority:

1.  `src/demoDebug/` *(build variant source set)*
2.  `src/debug/` *(build type source set)*
3.  `src/demo/` *(product flavor source set)*
4.  `src/main/` *(main source set)*

**Note:** If you [combine multiple product flavors](#flavor-dimensions), priority between the product flavors is determined by the flavor dimension they belong to. When listing flavor dimensions with the [`android.flavorDimensions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.AppExtension.html#com.android.build.gradle.AppExtension:flavorDimensions(java.lang.String[])) property, product flavors that belong to the first flavor dimension you list have a higher priority than those belonging to the second flavor dimension, and so on. Additionally, source sets you create for combinations of product flavors have a higher priority than source sets that belong to each individual product flavor.

The order listed above determines which source set has a higher priority when Gradle combines code and resources. Because the `demoDebug/` source set directory likely contains files that are specific to that build variant, if `demoDebug/` includes a file that is also defined in `debug/`, Gradle uses the file in the `demoDebug/` source set. Similarly, Gradle gives files in the build type and product flavor source sets a higher priority than the same files in `main/`. Gradle considers this priority order when applying the following build rules:

*   All source code in the `java/` directories are compiled together to generate a single output.

    **Note:** For a given build variant, Gradle throws a build error if it encounters two or more source set directories that have defined the same Java class. For example, when building a debug APK, you cannot define both `src/debug/Utility.java` and `src/main/Utility.java`. This is because Gradle looks at both these directories during the build process and throws a "duplicate class" error. If you want different versions of `Utility.java` for different build types, you can have each build type define its own version of the file and not include it in the `main/` source set.

*   Manifests are merged together into a single manifest. Priority is given in the same order as the list above. That is, manifest settings for a build type override the manifest settings for a product flavor, and so on. To learn more, read about [manifest merging](https://developer.android.com/studio/build/manifest-merge).
*   Similarly, files in the `values/` directories are merged together. If two files share the same name, such as two `strings.xml` files, priority is given in the same order as the list above. That is, values defined in a file in the build type source set override the values defined in the same file in a product flavor, and so on.
*   Resources in the `res/` and `asset/` directories are packaged together. If there are resources with the same name defined in two or more source sets, priority is given in the same order as the list above.
*   Finally, Gradle gives resources and manifests included with library module dependencies the lowest priority when building the APK.

## Declare dependencies

You can configure a dependency for a specific build variant or [testing source set](https://developer.android.com/studio/test#sourcesets) by prefixing the name of the build variant or testing source set before the `Implementation` keyword, as shown in the following example.

dependencies { // Adds the local "mylibrary" module as a dependency to the "free" flavor.
    freeImplementation project(":mylibrary") // Adds a remote binary dependency only for local tests.testImplementation 'junit:junit:4.12' // Adds a remote binary dependency only for the instrumented test APK.androidTestImplementation 'com.android.support.test.espresso:espresso\-core:3.0.2'
}

For more information, see [Add build dependencies](https://developer.android.com/studio/build/dependencies).

## Configure signing settings

Gradle does not sign your release build's APK unless you explicitly define a signing configuration for this build. You can easily create a release key and [sign your release build type using Android Studio](https://developer.android.com/studio/publish/app-signing#release-mode).

To manually configure the signing configurations for your release build type using Gradle build configurations:

1.  *Create a keystore.* A **keystore** is a binary file that contains a set of private keys. You must keep your keystore in a safe and secure place.
2.  *Create a private key.* A **private key** represents the entity to be identified with the app, such as a person or a company.
3.  Add the signing configuration to the module\-level `build.gradle` file:

    ...android { ...defaultConfig {...}signingConfigs {release {
                storeFile file("myreleasekey.keystore")storePassword "password"keyAlias "MyReleaseKey"keyPassword "password" } }buildTypes {release { ...
                signingConfig signingConfigs.release } }
    }

To generate a signed APK, select **Build > Generate Signed APK** from the menu bar. The package in `app/build/apk/app-release.apk` is now signed with your release key.

**Note:** Including the passwords for your release key and keystore inside the build file is not a good security practice. Alternatively, you can configure the build file to obtain these passwords from environment variables or have the build process prompt you for these passwords.

To obtain these passwords from environment variables:

storePassword System.getenv("KSTOREPWD")
keyPassword System.getenv("KEYPWD")

To have the build process prompt you for these passwords if you are invoking the build from the command line:

storePassword System.console().readLine("\\nKeystore password: ")
keyPassword System.console().readLine("\\nKey password: ")

After you complete this process, you can distribute your app and publish it on Google Play.

**Warning:** Keep your keystore and private key in a safe and secure place, and ensure that you have secure backups of them. If you publish an app to Google Play and then lose the key with which you signed your app, you will not be able to publish any updates to your app, since you must always sign all versions of your app with the same key.

### Signing Wear OS apps

When publishing Wear OS apps, both the watch APK and optional phone APK need to be signed. For more information on packaging and signing Wear OS apps, see [Packaging Wearable Apps](https://developer.android.com/training/wearables/apps/packaging).
