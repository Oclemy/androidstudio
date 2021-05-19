# Build multiple APKs

- [Build multiple APKs](#build-multiple-apks)
  - [Configure your build for multiple APKs](#configure-your-build-for-multiple-apks)
    - [Configure multiple APKs for screen densities](#configure-multiple-apks-for-screen-densities)
    - [Configure multiple APKs for ABIs](#configure-multiple-apks-for-abis)
      - [mips, mips64, and armeabi](#mips-mips64-and-armeabi)
      - [Projects without native/C++ code](#projects-without-nativec-code)
      - [Projects with native/C++ code](#projects-with-nativec-code)
    - [Configure versioning](#configure-versioning)
  - [Build multiple APKs](#build-multiple-apks-1)
    - [APK file name format](#apk-file-name-format)

If you publish your app to Google Play, you should build and upload an [Android App Bundle](https://developer.android.com/guide/app-bundle). When you do so, Google Play automatically generates and serves optimized APKs for each user’s device configuration, so they download only the code and resources they need to run your app. Publishing multiple APKs is useful if you are not publishing to Google Play, but you must build, sign, and manage each APK yourself.

Although you should build a single APK to support all your target devices whenever possible, that might result in a very large APK due to files needed to support multiple [screen densities](https://developer.android.com/training/multiscreen/screendensities) or [Application Binary Interfaces](https://developer.android.com/ndk/guides/abis) (ABIs). One way to reduce the size of your APK is to create [multiple APKs](https://developer.android.com/google/play/publishing/multiple-apks) that contain files for specific screen densities or ABIs.

Gradle can create separate APKs that contain only code and resources specific to each density or ABI. This page describes how to configure your build to generate multiple APKs. If you need to create different versions of your app that are not based on screen density or ABI, you can instead use [build variants](https://developer.android.com/studio/build/build-variants).

## Configure your build for multiple APKs

To configure your build for multiple APKs, add a [`splits`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.Splits.html) block to your module\-level `build.gradle` file. Within the [`splits`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.Splits.html) block, provide a `density` block that specifies how Gradle should generate per\-density APKs, or an `abi` block that specifies how Gradle should generate per\-ABI APKs. You can provide both density and ABI blocks, and the build system creates an APK for each density and ABI combination.

**Note:** Generating per\-language APKs is supported only for Android Instant App projects. To learn more, read [Enable configuration APKs](https://developer.android.com/topic/instant-apps/guides/config-splits).

### Configure multiple APKs for screen densities

To create separate APKs for different screen densities, add a `density` block inside your [`splits`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.Splits.html) block. In your `density` block, provide a list of desired screen densities and compatible screen sizes. The list of compatible screen sizes should only be used if you need specific [`<compatible-screens>`](https://developer.android.com/guide/topics/manifest/compatible-screens-element) elements in each APK's manifest.

The following Gradle DSL options are used to configure multiple APKs for screen densities:

`enable`

If you set this element to `true`, Gradle generates multiple APKs based on the screen densities you define. The default value is `false`.

`exclude`

Specifies a comma\-separated list of densities that Gradle should *not* generate separate APKs for. Use `exclude` if you want to generate APKs for most densities, but need to exclude a few densities that your app doesn't support.

`reset()`

Clears the default list of screen densities. Only use when combined with the `include` element to specify the densities you would like to add. The following snippet sets the list of densities to just `ldpi` and `xxhdpi` by calling `reset()` to clear the list, and then using `include`.reset()  // Clears the default list from all densities to no densities.
include "ldpi", "xxhdpi" // Specifies the two densities we want to generate APKs for.

`include`

Specifies a comma\-separated list of densities that Gradle should generate APKs for. Only use in combination with `reset()` to specify an exact list of densities.

`compatibleScreens`

Specifies a comma\-separated list of compatible screen sizes. This will inject a matching [`<compatible-screens>`](https://developer.android.com/guide/topics/manifest/compatible-screens-element) node in the manifest for each APK. This setting provides a convenient way to manage both screen densities and screen sizes in the same `build.gradle` section. However, using [`<compatible-screens>`](https://developer.android.com/guide/topics/manifest/compatible-screens-element) can limit the types of devices your app will work with. For alternative ways to support different screen sizes, see [Support Multiple Screens](https://developer.android.com/guide/practices/screens_support).

Because each APK that's based on screen density includes a [`<compatible-screens>`](https://developer.android.com/guide/topics/manifest/compatible-screens-element) tag with specific restrictions about which screen types the APK supports, even if you publish several APKs, some new devices will not match your multiple APK filters. As such, Gradle always generates an additional universal APK that contains assets for all screen densities and does not include a [`<compatible-screens>`](https://developer.android.com/guide/topics/manifest/compatible-screens-element) tag. You should publish this universal APK along with your per\-density APKs to provide a fallback for devices that do not match the APKs with a [`<compatible-screens>`](https://developer.android.com/guide/topics/manifest/compatible-screens-element) tag.

The following example generates a separate APK for each screen density listed in [Range of screens supported](https://developer.android.com/guide/practices/screens_support#range), except `ldpi`, `xxhdpi`, and `xxxhdpi`. This is done by using `exclude` to remove three densities from the default list of all densities.

android { ...splits { // Configures multiple APKs based on screen density.density { // Configures multiple APKs based on screen density.enable true // Specifies a list of screen densities Gradle should not create multiple APKs for.exclude "ldpi",  "xxhdpi",  "xxxhdpi" // Specifies a list of compatible screen size settings for the manifest.compatibleScreens 'small',  'normal',  'large',  'xlarge' } }
}

For a list of density names and screen size names, see [How to Support Multiple Screens](https://developer.android.com/guide/practices/screens_support.html#support). For more details on distributing your app to specific screen types and devices, see [Distributing to Specific Screens](https://developer.android.com/guide/practices/screens-distribution).

### Configure multiple APKs for ABIs

To create separate APKs for different ABIs, add an `abi` block inside your `[splits](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.Splits.html)` block. In your `abi` block, provide a list of desired ABIs.

The following Gradle DSL options are used to configure multiple APKs per ABI:

`enable`

If you set this element to `true`, Gradle generates multiple APKs based on the ABIs you define. The default value is `false`

`exclude`

Specifies a comma\-separated list of ABIs that Gradle should *not* generate separate APKs for. Use `exclude` if you want to generate APKs for most ABIs, but need to exclude a few ABIs that your app doesn't support.

`reset()`

Clears the default list of ABIs. Only use when combined with the `include` element to specify the ABIs you would like to add. The following snippet sets the list of ABIs to just `x86` and `x86_64` by calling `reset()` to clear the list, and then using `include`:reset()  // Clears the default list from all ABIs to no ABIs.
include "x86", "x86\_64" // Specifies the two ABIs we want to generate APKs for.

`include`

Specifies a comma\-separated list of ABIs that Gradle should generate APKs for. Only use in combination with `reset()` to specify an exact list of ABIs.

`universalApk`

If `true`, Gradle generates a universal APK in addition to per\-ABI APKs. A universal APK contains code and resources for all ABIs in a single APK. The default value is `false`. Note that this option is only available in the `splits.abi` block. When building multiple APKs based on screen density, Gradle always generates a universal APK that contains code and resources for all screen densities.

The following example generates a separate APK for each ABI: `x86` and `x86_64`. This is done by using `reset()` to start with an empty list of ABIs, followed by `include` with a list of ABIs that will each get an APK.

android { ...splits { // Configures multiple APKs based on ABI.abi { // Enables building multiple APKs per ABI.enable true // By default all ABIs are included, so use reset() and include to specify that we only // want APKs for x86 and x86\_64. // Resets the list of ABIs that Gradle should create APKs for to none.
      reset() // Specifies a list of ABIs that Gradle should create APKs for.include "x86",  "x86\_64" // Specifies that we do not want to also generate a universal APK that includes all ABIs.universalApk false } }
}

For a list of supported ABIs, see [Supported ABIs](https://developer.android.com/ndk/guides/abis.html#sa).

#### mips, mips64, and armeabi

[Android Plugin for Gradle 3.1.0](https://developer.android.com/studio/releases/gradle-plugin#3-1-0) and higher no longer generate APKs for the following ABIs by default: `mips`, `mips64`, and `armeabi`. That's because NDK r17 and higher no longer include these ABIs as supported targets.

Consider first checking the Google Play Console to verify that you have users downloading APKs of your app that target these ABIs. If not, you may want to omit them from your build. If you want to continue building APKs that target these ABIs, you must use [NDK r16b or lower](https://developer.android.com/ndk/downloads/revision_history), [set the active build variants and ABIs](https://developer.android.com/studio/run#changing-variant), and specify the ABIs in your `build.gradle` file, as shown below:

splits {abi {include **'armeabi',  'mips',  'mips64'** ... }
}

**Known Issue:** If you are using Android Plugin for Gradle 3.0.1 or lower with NDK r17 or higher, you may get the following error: `Error:ABIs [mips64, armeabi, mips] are not supported for platform.` That's because older versions of the plugin still include the unsupported ABIs by default when you build per\-ABI APKs. To resolve this issue, either [update to the latest version of the plugin](https://developer.android.com/studio/releases/gradle-plugin#updating-plugin), or, in your app's `build.gradle` file, reset the plugin's default list of ABIs and include only the supported ABIs you want, as shown below:

...splits {abi { ... **reset()** **include**  "x86",  "armeabi\-v7a",  "arm64\-v8a",  "x86\_64" }
}

#### Projects without native/C++ code

The **Build Variants** panel has two columns: **Module** and **Active Build Variant**. The **Active Build Variant** value for the module determines the build variant that will be deployed and is visible in the editor.

![](https://developer.android.com/studio/images/build/build-variants.png) **Figure 1:** The **Build Variants** panel has two columns for projects that do not have native/C++ code

To switch between variants, click the **Active Build Variant** cell for a module and choose the desired variant from the list field.

#### Projects with native/C++ code

The **Build Variants** panel has three columns: **Module**, **Active Build Variant**, and **Active ABI**. The **Active Build Variant** value for the module determines the build variant that will be deployed and is visible in the editor. For native modules, the **Active ABI** value determines the ABI that the editor will use, but does not impact what is deployed.

![](https://developer.android.com/studio/images/build/build-variants-ndk.png) **Figure 2:** The **Build Variants** panel adds the **Active ABI** column for projects with native/C++ code

To change the build type or ABI, click the cell for the **Active Build Variant** or **Active ABI** column and choose the desired variant or ABI from the list field. A new sync automatically runs. Changing either column for an app or library module will apply the change to all dependent rows.

### Configure versioning

By default, when Gradle generates multiple APKs, each APK will have the same version information, as specified in the module\-level `build.gradle` file. Because the Google Play Store does not allow multiple APKs for the same app that all have the same version information, you need to ensure each APK has its own unique [`versionCode`](https://developer.android.com/studio/publish/versioning#appversioning) before you upload to the Play Store.

You can configure your module\-level `build.gradle` file to override the `versionCode` for each APK. By creating a mapping that assigns a unique numeric value for each ABI and density you configure multiple APKs for, you can override the output version code with a value that combines the version code defined within the `defaultConfig` or `productFlavors` block with the numeric value assigned to the density or ABI.

In the following example, the APK for the `x86` ABI would get a `versionCode` of 2004 and the `x86_64` ABI would get 3004. Assigning version codes in large increments, such as 1000, allows you to later assign unique version codes if you need to update your app. For example, if `defaultConfig.versionCode` iterates to 5 in a subsequent update, Gradle would assign a `versionCode` of 2005 to the `x86` APK and 3005 to the `x86_64` APK.

**Tip:** If your build includes a universal APK, you should assign it a `versionCode` that's lower than that of any of your other APKs. Because Google Play Store installs the version of your app that is both compatible with the target device and has the highest `versionCode`, assigning a lower `versionCode` to the universal APK ensures that Google Play Store tries to install one of your APKs before falling back to the universal APK. The sample code below handles this by not overriding a universal APK's default `versionCode`.

android { ...defaultConfig { ...versionCode 4 }splits { ... }
}

// Map for the version code that gives each ABI a value.
ext.abiCodes \=  \['armeabi\-v7a':1, x86:2, x86\_64:3\]

// For per\-density APKs, create a similar map like this:
// ext.densityCodes = \['mdpi': 1, 'hdpi': 2, 'xhdpi': 3\]

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

For more examples of alternate version code schemes, see [Assigning version codes](https://developer.android.com/google/play/publishing/multiple-apks#VersionCodes).

## Build multiple APKs

Once you configure your module\-level `build.gradle` file to build multiple APKs, click **Build > Build APK** to build all APKs for the currently selected module in the **Project** pane. Gradle creates the APKs for each density or ABI into the project's `build/outputs/apk/` directory.

Gradle builds an APK for each density or ABI you configure multiple APKs for. If you enable multiple APKs for both densities and ABIs, Gradle creates an APK for each density and ABI combination. For example, the following `build.gradle` snippet enables building multiple APKs for mdpi and hdpi densities, and also x86 and x86\_64 ABIs.

...splits {density {enable true
      reset()include "mdpi",  "hdpi" }abi {enable true
      reset()include "x86",  "x86\_64" } }

The output from the example configuration includes the following 4 APKs:

*   `app-hdpiX86-release.apk`: Contains code and resources for hdpi density and x86 ABI only.
*   `app-hdpiX86_64-release.apk`: Contains code and resources for hdpi density and x86\_64 ABI only.
*   `app-mdpiX86-release.apk`: Contains code and resources for mdpi density and x86 ABI only.
*   `app-mdpiX86_64-release.apk`: Contains code and resources for mdpi density and x86\_64 ABI only.

When building multiple APKs based on screen density, Gradle always generates a universal APK that includes code and resources for all densities, in addition to the per\-density APKs. When building multiple APKs based on ABI, Gradle only generates an APK that includes code and resources for all ABIs if you specify `universalApk true` in the `splits.abi` block in your `build.gradle` file.

### APK file name format

When building multiple APKs, Gradle uses APK filenames using the following scheme:

`modulename-screendensityABI-buildvariant.apk`

The scheme components are:

`modulename`

Specifies the module name being built.

`screendensity`

If multiple APKs for screen density are enabled, specifies the screen density for the APK, such as "mdpi".

`ABI`

If multiple APKs for ABI are enabled, specifies the ABI for the APK, such as "x86". If multiple APKs for both screen density and ABI are enabled, Gradle concatenates the density name with the ABI name, for example "mdpiX86". If `universalApk` is enabled for per\-ABI APKs, Gradle uses "universal" as the ABI portion of the universal APK filename.

`buildvariant`

Specifies the build variant being built, such as "debug".

For example, when building mdpi screen density APK for the debug version of "myApp", the APK filename is `myApp-mdpi-debug.apk`. The release version of "myApp" that is configured to build multiple APKs for both the mdpi screen density and the x86 ABI has an APK filename of `myApp-mdpiX86-release.apk`.
