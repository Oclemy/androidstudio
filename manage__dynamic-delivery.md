# Overview of Play Feature Delivery

- [Overview of Play Feature Delivery](#overview-of-play-feature-delivery)
  - [Use feature modules for custom delivery](#use-feature-modules-for-custom-delivery)
  - [Modularize your app](#modularize-your-app)
  - [Feature module manifest](#feature-module-manifest)
  - [Test Play Feature Delivery](#test-play-feature-delivery)
  - [Building a URI for a resource](#building-a-uri-for-a-resource)
  - [Considerations for feature modules](#considerations-for-feature-modules)
  - [Additional resources](#additional-resources)
    - [Samples](#samples)
    - [Codelabs](#codelabs)
    - [Blog posts](#blog-posts)
    - [Videos](#videos)

Google Play’s app serving model uses [Android App Bundles](https://developer.android.com/guide/app-bundle) to generate and serve optimized APKs for each user’s device configuration, so users download only the code and resources they need to run your app.

Play Feature Delivery uses advanced capabilities of app bundles, allowing certain features of your app to be delivered conditionally or downloaded on demand.

## Use feature modules for custom delivery

A unique benefit of feature modules is the ability to customize how and when different features of your app are downloaded onto devices running Android 5.0 (API level 21) or higher. For example, to reduce the initial download size of your app, you can configure certain features to be either downloaded as needed on demand or only by devices that support certain capabilities, such as the ability to take pictures or support augmented reality features.

Although you get highly optimized downloads by default when you upload your app as an app bundle, the more advanced and customizable feature delivery options require additional configuration and modularization of your app’s features using *feature modules*. That is, feature modules provide the building blocks for creating modular features that you can configure to each be downloaded as needed.

Consider an app that allows your users to buy and sell goods in an online marketplace. You can reasonably modularize each of the following functionalities of the app into separate feature modules:

*   Account login and creation
*   Browsing the marketplace
*   Placing an item for sale
*   Processing payments

The table below describes the different delivery options that feature modules support, and how they might be used to optimize the initial download size of the sample marketplace app.

| Delivery option | Behavior | Sample use\-case | Getting started |
| --- | --- | --- | --- |
| Install\-time delivery | Feature modules that don’t configure any of the delivery options described above are downloaded at app install, by default. This is an important behavior because it means that you can adopt advanced delivery options gradually. For example, you can benefit from modularizing your app’s features and enable on demand delivery only after you’ve fully implemented on demand downloads using the Play Core library.
In addition, your app can request to uninstall features at a later time. So, if you require certain features at app install, but not after that, you can reduce install size by requesting to remove the feature from the device.

 | If the app has certain training activities, such as an interactive guide on how to buy and sell items in the marketplace, you can include that feature at app install, by default.

However, to reduce the installed size of the app, the app can request to delete the feature after the user has completed the training.

 | [Modularize your app](#modularize) using feature modules that configure no advanced delivery options.

To learn how to reduce the installed size of your app by removing certain feature modules that the user may no longer need, read [Manage installed modules](https://developer.android.com/guide/playcore/play-feature-delivery#manage_installed_modules).

 |
| On demand delivery | Allows your app to request and download feature modules as needed. | If only 20% of those who use the marketplace app post items for sale, a good strategy to reduce the initial download size for the majority of users is to make the functionality for taking pictures, including an item description, and placing an item for sale available as an on demand download. That is, you can configure the feature module for the selling functionality of the app to be downloaded only when a user shows interest in placing items for sale onto the marketplace.

Additionally, if the user no longer sells items after a certain period of time, the app can reduce its installed sized by requesting to uninstall the feature.

 | Create a feature module and [configure on demand delivery](https://developer.android.com/guide/app-bundle/on-demand-delivery). Your app can then use the [Play Core library](https://developer.android.com/guide/app-bundle/playcore) to request to download the module on demand. |
| Conditional delivery | Allows you to specify certain user device requirements, such as hardware features, locale, and minimum API level to determine whether a modularized feature is downloaded at app install. | If the marketplace app has global reach, you might need to support payment methods that are popular in only certain regions or locals. In order to reduce the initial app download size, you can create separate feature modules for processing certain types of payment methods and have them installed conditionally on a user’s device based on their registered locale. | Create a feature module and [configure conditional delivery](https://developer.android.com/guide/app-bundle/conditional-delivery). |
| Instant delivery | [Google Play Instant](https://developer.android.com/topic/google-play-instant/overview) allows users to interact with your app without needing to install APK(s) on their device. Instead, they can experience your app through the "Try Now" button on the Google Play Store or a URL that you create. This form of delivering content makes it easier for you to increase engagement with your app.

With instant delivery, you can utilize Google Play Instant to allow your users to instantly experience certain features of your app without installation.

 | Consider a game that include the first few levels of the game in a lightweight feature module. You can instant\-enable that module so that users can instantly experience the game through a URL link or “Try Now" button, without app installation. | Create a feature module and [configure instant delivery](https://developer.android.com/guide/app-bundle/instant-delivery). Your app can then use the [Play Core library](https://developer.android.com/guide/app-bundle/playcore) to request to download the module on demand.

Keep in mind, modularizing your app features using feature modules is only the first step. To support Google Play Instant, the download size of the base module of your app and a given instant\-enabled feature must satisfy strict size restrictions. To learn more, read [Enable instant experiences by reducing app or game size](https://developer.android.com/topic/google-play-instant/overview#reduce-size).

 |

## Modularize your app

Modularizing your app is the process of separating logical components of your app project into discrete modules.

Reorganizing your app’s functionality into these discrete components takes careful consideration and time. However, modularization provides your project with the following benefits:

*   **Develop in parallel:** By separating logical components of your app into modules, different teams or individuals in your organization can take ownership of each module and work on them with fewer merge conflicts or disruptions to other teams. Additionally, if you have logic that’s used in various parts of your app, you can [use library modules](https://developer.android.com/studio/projects/android-library) to promote code reuse and encapsulation.
*   **Improve build times:** Build systems, such as the Android Studio build system using Gradle, are optimized for projects that are organized into modules. For example, if you enable Gradle’s [parallel project execution](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:parallel_execution) optimization on a workstation includes a multi\-core processor, the build system is able to build multiple modules in parallel and significantly reduce build times. The more modular your project is, the more significant the build performance improvement becomes.
*   **Customize feature delivery:** Modularizing your app’s features as feature modules is a requirement to take advantage of Play Feature Delivery’s [custom delivery options](#customize_delivery), such as on demand, conditional, and instant delivery. Creating on demand features requires more effort and possible refactoring of your app. So, consider carefully which of your app’s features would benefit the most from being modularized into feature modules and benefiting from custom delivery options.

Modularizing your project by app features takes time and consideration to do properly. When you do decide to begin modularizing your app, you should first [configure your base module](https://developer.android.com/guide/app-bundle/configure-base) with the properties necessary to support modular features. Then, you can gradually modularize your app’s features without changing the current behavior of your app by [configuring feature modules for install\-time delivery](https://developer.android.com/guide/app-bundle/at-install-delivery).

## Feature module manifest

When creating a new feature module using Android Studio, the IDE includes most of the manifest attributes that the module requires to behave like a feature module. Additionally, some attributes are injected by the build system at compile time, so you needn’t specify or modify them yourself. The following table describes the manifest attributes that are important to feature modules.

| Attribute | Description |
| `<manifest
...` | This is your typical [`<manifest>`](https://developer.android.com/guide/topics/manifest/manifest-element) block. |
| `xmlns:dist="http://schemas.android.com/apk/distribution"` | Specifies a new `dist:` XML namespace that’s described further below. |
| `split="split_name"` | When Android Studio builds your app bundle, it includes this attribute for you. So, **you should not include or modify this attribute yourself**.

Defines the name of the module, which your app specifies when requesting an on demand module using the Play Core Library.

**How Gradle determines the value for this attribute:**

By default, when you create a feature module using Android Studio, The IDE uses what you specify as its **Module name** to identify the module as a Gradle subproject in your [Gradle settings file](https://developer.android.com/studio/build#settings-file).

When you build your app bundle, Gradle uses the last element of the subproject path to inject this manifest attribute in the module’s manifest. For example, if you create a new feature module in the `MyAppProject/features/` directory and specified "dynamic\_feature1" as its **Module name**, the IDE adds `':features:dynamic_feature1'` as a subproject in your `settings.gradle` file. When building your app bundle, Gradle then injects `<manifest split="dynamic_feature1">` in the module’s manifest.

 |
| `android:isFeatureSplit="true | false">` | When Android Studio builds your app bundle, it includes this attribute for you. So, **you should not include or modify this attribute manually**.

Specifies that this module is a feature module. Manifests in the base module and configuration APKs either omit this attribute or set it to `false`.

 |
| `<dist:module` | This new XML element defines attributes that determine how the module is packaged and distributed as APKs. |
| `dist:instant="true | false"` | Specifies whether the module should be available through [Google Play Instant](https://developer.android.com/topic/google-play-instant/overview) as an instant experience.

If your app includes one or more instant\-enabled feature modules, you must also instant\-enable the base module. When using Android Studio 3.5 or higher, the IDE does this for you when you [create an instant\-enabled feature module](https://developer.android.com/guide/app-bundle/instant-delivery).

You can’t set this XML element to `true` while also setting `<dist:on-demand/>`. However, you can still request on demand downloads of your instant\-enabled feature modules *as instant experiences* [using the Play Core Library](https://developer.android.com/guide/app-bundle/playcore). When a user downloads and *installs* your app, the device downloads and installs your app's instant\-enabled feature modules, along with the base APK, by default.

 |
| `dist:title="@string/feature_name"` | Specifies a user\-facing title for the module. For example, the device may display this title when it requests download confirmation.

You need to include the string resource for this title in the base module’s `module_root/src/source_set/res/values/strings.xml` file.

 |
| `<dist:fusing dist:include="true | false" />
</dist:module>` | Specifies whether to include the module in multi\-APKs that target devices running Android 4.4 (API level 20) and lower.

Additionally, when you [use `bundletool` to generate APKs from an app bundle](https://developer.android.com/studio/command-line/bundletool#generate_apks), only feature modules that set this property to `true` are included in the universal APK—which is a monolithic APK that includes code and resources for all device configurations your app supports.

 |
| `<dist:delivery>` | Encapsulates options that customizes module delivery, as shown below. Keep in mind, each feature module must configure only one type of these custom delivery options. |
| `<dist:install-time>` | Specifies that the module should be available at install time. This is the default behavior for feature modules that do not specify another type of custom delivery option.

To learn more about install\-time downloads, read [Configure install\-time delivery](https://developer.android.com/guide/app-bundle/at-install-delivery).

This node is can also specify conditions that limit the module to devices that meet certain requirements, such as device features, user country, or minimum API level. To learn more, read [Configure conditional delivery](https://developer.android.com/guide/app-bundle/conditional-delivery).

 |
| `<dist:removable value="true | false" />` |

When unset or set to `false`, bundletool will fuse install\-time modules into the base module when generating split APKs from the bundle. Because there will be fewer split APKs as a result of fusing, this setting may improve your app's performance.

When `removable` is set to `true`: install\-time modules will not be fused into the base module. Set to `true` if you want to uninstall modules in the future. However, configuring too many modules to be removable might increase the install time for your app.

Defaults to `false`. It's only necessary to set this value in the manifest if you want to disable fusing for a feature module.

**Note:** This feature is only available when using Android Gradle plugin 4.2 or when using bundletool v1.0 from the command line.

 |
| `</dist:install-time>` |   |
| `<dist:on-demand/>` | Specifies that the module should be available as an on demand download. That is, the module is not available at install time, but your app may request to download it later.

To learn more about on demand downloads, read [Configure on demand delivery](https://developer.android.com/guide/app-bundle/on-demand-delivery).

 |
| `</dist:delivery>` |  |
| `<application
[android:hasCode](https://developer.android.com/guide/topics/manifest/application-element#code)="true | false">
...
</application>` | If the feature module generates no DEX files—that is, it contains no code that is later compiled into the DEX file format—you must do the following (otherwise, you may get runtime errors):

1.  Set `android:hasCode` to `"false"` in the feature module's manifest.
2.  Add the following to your **base** module's manifest:

    <application android:hasCode\="true" tools:replace\="android:hasCode"\>
      ...
    </application>

 |

**Note:** Feature modules should not specify activities in their manifest with [`android:exported`](https://developer.android.com/guide/topics/manifest/activity-element#exported) set to `true`. That's because there's no guarantee that the device has downloaded the feature module when another app tries to launch the activity. Additionally, your app should confirm that a feature is downloaded before trying to access its code and resources. To learn more, read [Manage installed modules](https://developer.android.com/guide/playcore/play-feature-delivery#manage_installed_modules).

## Test Play Feature Delivery

The best way to test Play Feature Delivery is through the Google Play Store. That's because a lot of the benefits of Play Feature Delivery rely on deferring optimized APK generation, signing, and serving to the Play Store. So, whether you simply upload an app bundle or configure more advanced delivery options, you should use the following methods to test your app.

*   [Share your app with a URL](https://support.google.com/googleplay/android-developer/answer/9303479). This is the fastest way to upload your app bundle and share your app as a Google Play Store link with trusted testers. Additionally, this is the fastest way to test custom delivery options, such as downloading features on demand.
*   [Set up an open, closed, or internal test](https://support.google.com/googleplay/android-developer/answer/3131213). This method provides structured test channels and is a good way to test the final release version of your app before you roll it out to external users.

## Building a URI for a resource

If you want to access a resource stored in a feature module using a URI, here’s how to generate a feature module resource URI using [`Uri.Builder()`](https://developer.android.com/reference/kotlin/android/net/Uri.Builder):

[Kotlin](#kotlin) [Java](#java) [More](#)

val uri \=  Uri.Builder() .scheme(ContentResolver.SCHEME\_ANDROID\_RESOURCE) .authority(context.getPackageName())  // Look up the resources in the application with its splits loaded .appendPath(resources.getResourceTypeName(resId)) .appendPath(String.format("%s:%s",
                  resources.getResourcePackageName(resId),  // Look up the dynamic resource in the split namespace.
                  resources.getResourceEntryName(resId) )) .build()

String uri \=  Uri.Builder() .scheme(ContentResolver.SCHEME\_ANDROID\_RESOURCE) .authority(context.getPackageName())  // Look up the resources in the application with its splits loaded .appendPath(resources.getResourceTypeName(resId)) .appendPath(String.format("%s:%s",
                  resources.getResourcePackageName(resId),  // Look up the dynamic resource in the split namespace.
                  resources.getResourceEntryName(resId) )) .build().toString();

Each part of the path to the resource is constructed at run time, ensuring that the correct namespace is generated after the split APKs have been loaded.

As an example of how the URI is generated, suppose you have an app and feature modules with these names:

*   App package name: `com.example.my_app_package`
*   Feature's resources package name: `com.example.my_app_package.my_dynamic_feature`

If the `resId` in the code snippet above refers to a raw file resource named “my\_video” in your feature module, then the `Uri.Builder()` code above would output the following:

```
android.resource://com.example.my_app_package/raw/com.example.my_app_package.my_dynamic_feature:my_video

```

This URI can then be used by your app to access the feature module’s resource.

To validate the paths in your URI, you can use the [APK Analyzer](https://developer.android.com/studio/build/apk-analyzer) to inspect your feature module APK and determine the package name:

![A screenshot of the APK Analyzer inspecting the contents of a compiled resource file.](https://developer.android.com/images/app-bundle/apk-analyzer-package-name.png)

**Figure 2.** Use the APK Analyzer to inspect the package name in a compiled resource file.

## Considerations for feature modules

With feature modules, you can improve build speed and engineering velocity and extensively customize delivery of your app's features to reduce your app's size. However, there are some constraints and edge cases to keep in mind when using feature modules:

*   Installing 50 or more feature modules on a single device, via conditional or on\-demand delivery, might lead to performance issues. Install\-time modules, which are not configured as removable, are automatically included in the base module and only count as one feature module on each device.
*   Limit the number of modules you configure as removable for [install\-time delivery](https://developer.android.com/guide/app-bundle/at-install-delivery) to 10 or fewer. Otherwise, the download and install time of your app might increase.
*   Only devices running Android 5.0 (API level 21) and higher support downloading and installing features on demand. To make your feature available to earlier versions of Android, enable **Fusing** when you create a feature module.
*   [Enable SplitCompat](https://developer.android.com/guide/playcore/play-feature-delivery#access_downloaded_modules), so that your app has access to downloaded feature modules that are delivered on demand.
*   Feature modules should not specify activities in their manifest with [`android:exported`](https://developer.android.com/guide/topics/manifest/activity-element#exported) set to `true`. That's because there's no guarantee that the device has downloaded the feature module when another app tries to launch the activity. Additionally, your app should confirm that a feature is downloaded before trying to access its code and resources. To learn more, read [Manage installed modules](https://developer.android.com/guide/playcore/play-feature-delivery#manage_installed_modules).
*   Because Play Feature Delivery requires you to publish your app using an app bundle, make sure that you're aware of app bundle [known issues](https://developer.android.com/guide/app-bundle#known_issues).

## Additional resources

To learn more about using supporting Play Feature Delivery, try the following resource.

### Samples

*   [PlayCore API sample](https://github.com/android/app-bundle-samples/tree/main/DynamicFeatures), which demonstrates usage of the PlayCore API to request and download feature modules.
*   [Dynamic code loading sample](https://github.com/googlesamples/android-dynamic-code-loading), which demonstrates three different approaches to safely access code from an installed feature module.

### Codelabs

*   [Your First Android App Bundle](https://codelabs.developers.google.com/codelabs/your-first-dynamic-app/index.html), a codelab that explores the basic principles of Android App Bundles and shows you how to quickly get started with building your own using Android Studio. This codelab also explores how to test your app bundles [using `bundletool`](https://developer.android.com/studio/command-line/bundletool).
*   [On demand modules](https://codelabs.developers.google.com/codelabs/on-demand-dynamic-delivery/index.html), which helps you create an app that downloads and installs feature modules on demand.

### Blog posts

*   [What a new publishing format means for the future of Android](https://medium.com/googleplaydev/what-a-new-publishing-format-means-for-the-future-of-android-2e34981793a)
*   [New features to help you develop, release, and grow your business on Google Play](https://android-developers.googleblog.com/2019/05/whats-new-in-play.html)
*   [The latest Android App Bundle updates including the additional languages API](https://android-developers.googleblog.com/2019/03/the-latest-android-app-bundle-updates.html)
*   [What a new publishing format means for the future of Android](https://medium.com/googleplaydev/what-a-new-publishing-format-means-for-the-future-of-android-2e34981793a)
*   [Patchwork Plaid — A modularization story](https://medium.com/androiddevelopers/a-patchwork-plaid-monolith-to-modularized-app-60235d9f212e)
*   [Google Santa Tracker — Moving to an Android App Bundle](https://medium.com/androiddevelopers/google-santa-tracker-moving-to-an-android-app-bundle-dde180716096)

### Videos

*   [Customizable Delivery with the App Bundle and Easy Sharing of Test Builds](https://www.youtube.com/watch?v=flhib2krW7U)
*   [New Tools to Optimize Your App's Size and Boost Installs on Google Play](https://www.youtube.com/watch?v=rEuwVWpYBOY)
*   [Bundling an App in an Instant](https://www.youtube.com/watch?v=L9J2e5PYXNg)