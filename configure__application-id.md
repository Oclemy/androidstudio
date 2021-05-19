# Set the application ID

- [Set the application ID](#set-the-application-id)
  - [Change the application ID for build variants](#change-the-application-id-for-build-variants)
  - [Change the application ID for testing](#change-the-application-id-for-testing)
  - [Change the package name](#change-the-package-name)

Every Android app has a unique application ID that looks like a Java package name, such as *com.example.myapp*. This ID uniquely identifies your app on the device and in Google Play Store. If you want to upload a new version of your app, the application ID (and the [certificate you sign it with](https://developer.android.com/studio/publish/app-signing)) must be the same as the original APK—if you change the application ID, Google Play Store treats the APK as a completely different app. So once you publish your app, **you should never change the application ID**.

Your application ID is defined with the `applicationId` property in your module's `build.gradle` file, as shown here:

```
android {    defaultConfig {        applicationId "com.example.myapp"        minSdkVersion 15        targetSdkVersion 24        versionCode 1        versionName "1.0"    }    ...}
```

When you create a new project in Android Studio, the `applicationId` exactly matches the Java\-style package name you chose during setup. However, the application ID and package name are independent of each other beyond this point. You can change your code's package name (your code namespace) and it will not affect the application ID, and vice versa (though, again, you should not change your application ID once you publish your app). However, changing the package name has other consequences you should be aware of, so see the section about how to [change the package name](#change_the_package_name).

And although the application ID looks like a traditional Java package name, the naming rules for the application ID are a bit more restrictive:

*   It must have at least two segments (one or more dots).
*   Each segment must start with a letter.
*   All characters must be alphanumeric or an underscore \[a\-zA\-Z0\-9\_\].

**Note:** The application ID used to be directly tied to your code's package name; so some Android APIs use the term "package name" in their method names and parameter names, but this is actually your application ID. For example, the [`Context.getPackageName()`](https://developer.android.com/reference/android/content/Context#getPackageName()) method returns your application ID. There's no need to ever share your code's true package name outside your app code.

**Caution:** If you are using [`WebView`](https://developer.android.com/reference/android/webkit/WebView), consider using your package name as a prefix in your application ID; otherwise you might encounter problems as described in [issue 211768](https://code.google.com/p/android/issues/detail?id=211768).

## Change the application ID for build variants

When you build an APK for your app, the build tools tag the APK with the application ID defined in the `defaultConfig` block from the `build.gradle` file (as shown below). However, if you want to create different versions of your app to appear as separate listings on Google Play Store, such as a "free" and "pro" version, you need to create separate [build variants](https://developer.android.com/studio/build/build-variants) that each have a different application ID.

In this case, each build variant should be defined as a separate [product flavor](https://developer.android.com/studio/build/build-variants#product-flavors). For each flavor inside the `productFlavors` block, you can redefine the `applicationId` property, or you can instead append a segment to the default application ID using `applicationIdSuffix`, as shown here:

```
android {    defaultConfig {        applicationId "com.example.myapp"    }    productFlavors {        free {            applicationIdSuffix ".free"        }        pro {            applicationIdSuffix ".pro"        }    }}
```

This way, the application ID for the "free" product flavor is "com.example.myapp.free".

You can also use `applicationIdSuffix` to append a segment based on your [build type](https://developer.android.com/studio/build/build-variants#build-types), as shown here:

```
android {    ...    buildTypes {        debug {            applicationIdSuffix ".debug"        }    }}
```

Because Gradle applies the build type configuration after the product flavor, the application ID for the "free debug" build variant is now "com.example.myapp.free.debug". This is useful when you want to have both the debug and the release build on the same device, because no two APKs can have the same application ID.

Remember that APKs with different application IDs are treated as different apps in Google Play Store. So if you instead want to use the same app listing to distribute multiple APKs that each target a different device configuration (such as the API level), then you must use the same application ID for each build variant but give each APK a different `versionCode`. For more information, read about [Multiple APK support](https://developer.android.com/google/play/publishing/multiple-apks).

**Caution:** For compatibility with previous SDK tools, if you do not define the `applicationId` property in your `build.gradle` file, the build tools use the package name from the `AndroidManifest.xml` file as the application ID. In that case, refactoring your package name also changes your application ID.

**Tip:** If you need to reference the application ID in your manifest file, you can use the `${applicationId}` placeholder in any manifest attribute. During a build, Gradle replaces this tag with the actual application ID. For more information, see [Inject Build Variables into the Manifest](https://developer.android.com/studio/build/manifest-build-variables).

## Change the application ID for testing

By default, the build tools apply an application ID to your [instrumentation test](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests) APK using the application ID for the given build variant, appended with `.test`. For example, a test APK for the `com.example.myapp.free` build variant has the application ID `com.example.myapp.free.test`.

Although it shouldn't be necessary, you can change the application ID by defining the `testApplicationId` property in your `defaultConfig` or `productFlavor` block.

**Note:** To avoid name collisions with the app under test, the build tools generate the `R` class for your test APK with a namespace based on the test application ID, instead of the package name defined in the manifest file.

## Change the package name

Although your project's package name matches the application ID by default, you can change it. However, if you want to change your package name, be aware that the package name (as defined by your project directory structure) should always match the `package` attribute in the `AndroidManifest.xml` file, as shown here:

```
<?xml version="1.0" encoding="utf-8"?><manifest xmlns:android="http://schemas.android.com/apk/res/android"    package="com.example.myapp"    android:versionCode="1"    android:versionName="1.0" >
```

The Android build tools use the `package` attribute for two things:

*   It applies this name as the namespace for your app's generated `R.java` class.

    Example: With the above manifest, the `R` class will be `com.example.myapp.R`.

*   It uses it to resolve any relative class names that are declared in the manifest file.

    Example: With the above manifest, an activity declared as `<activity android:name=".MainActivity">` is resolved to be `com.example.myapp.MainActivity`.

As such, the name in the `package` attribute should always match your project's base package name where you keep your activities and other app code. Of course, you can have sub\-packages in your project, but then those files must import the `R.java` class using the namespace from the `package` attribute, and any app components declared in the manifest must add the missing sub\-package names (or use fully\-qualified package names).

If you want to refactor your package name completely, be sure you update the `package` attribute as well. As long as you use Android Studio's tools to rename and refactor your packages, then these automatically stay in sync. (If they don't stay in sync, your app code can't resolve the `R` class because it's no longer in the same package, and the manifest won't identify your activities or other components.)

You must always specify the `package` attribute in your project's main `AndroidManifest.xml` file. If you have additional manifest files (such as for a product flavor or build type), be aware that the package name supplied by the highest\-priority manifest file is always used in the final merged manifest. For more information, see [Merge multiple manifest files](https://developer.android.com/studio/build/manifest-merge).

**One more thing to know:** Although you may have a different name for the manifest `package` and the Gradle `applicationId`, the build tools copy the application ID into your APK's final manifest file at the end of the build. So if you inspect your `AndroidManifest.xml` file after a build, don't be surprised that the `package` attribute has changed. The `package` attribute is where Google Play Store and the Android platform actually look to identify your app; so once the build has made use of the original value (to namespace the `R` class and resolve manifest class names), it discards that value and replaces it with the application ID.
