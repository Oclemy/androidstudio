# Publish your app

- [Publish your app](#publish-your-app)
  - [Preparing your app for release](#preparing-your-app-for-release)
  - [Releasing your app to users](#releasing-your-app-to-users)
    - [Releasing through an app marketplace](#releasing-through-an-app-marketplace)
      - [Releasing your apps on Google Play](#releasing-your-apps-on-google-play)
  - [Releasing through a website](#releasing-through-a-website)
  - [User opt\-in for unknown apps and sources](#user-opt-in-for-unknown-apps-and-sources)
    - [Install unknown apps](#install-unknown-apps)
    - [Unknown sources](#unknown-sources)

**Important:** In the second half of 2021, new apps will be required to publish with the [Android App Bundle](https://developer.android.com/guide/app-bundle) on Google Play. New apps larger than 150 MB must use either [Play Feature Delivery](https://developer.android.com/guide/app-bundle/dynamic-delivery) or [Play Asset Delivery](https://developer.android.com/guide/app-bundle/asset-delivery).

Publishing is the general process that makes your Android applications available to users. When you publish an Android application you perform two main tasks:

*   You prepare the application for release.

    During the preparation step you build a release version of your application, which users can download and install on their Android\-powered devices.

*   You release the application to users.

    During the release step you publicize, sell, and distribute the release version of your application to users.

This page provides an overview of the process you should follow as you prepare to publish your app. If you plan to publish on Google Play, you should also read the [Google Play launch checklist](https://developer.android.com/distribute/best-practices/launch/launch-checklist).

## Preparing your app for release

Preparing your application for release is a multi\-step process that involves the following tasks:

*   Configuring your application for release.

    At a minimum you need to remove `[Log](https://developer.android.com/reference/android/util/Log)` calls and remove the [android:debuggable](https://developer.android.com/guide/topics/manifest/application-element#debug) attribute from your manifest file. You should also provide values for the `android:versionCode` and `android:versionName` attributes, which are located in the [<manifest>](https://developer.android.com/guide/topics/manifest/manifest-element) element. You may also have to configure several other settings to meet Google Play requirements or accommodate whatever method you're using to release your application.

    If you are using Gradle build files, you can use the *release* build type to set your build settings for the published version of your app.

*   Building and signing a release version of your application.

    You can use the Gradle build files with the *release* build type to build and sign a release version of your application. See [Building and Running from Android Studio](https://developer.android.com/tools/building/building-studio).

*   Testing the release version of your application.

    Before you distribute your application, you should thoroughly test the release version on at least one target handset device and one target tablet device.

*   Updating application resources for release.

    You need to be sure that all application resources such as multimedia files and graphics are updated and included with your application or staged on the proper production servers.

*   Preparing remote servers and services that your application depends on.

    If your application depends on external servers or services, you need to be sure they are secure and production ready.

You may have to perform several other tasks as part of the preparation process. For example, you will need to get a private key for signing your application. You will also need to create an icon for your application, and you may want to prepare an End User License Agreement (EULA) to protect your person, organization, and intellectual property.

When you are finished preparing your application for release you will have a signed `.apk` file that you can distribute to users.

To learn how to prepare your application for release, see [Preparing for Release](https://developer.android.com/tools/publishing/preparing) in the Dev Guide. This topic provides step\-by\-step instructions for configuring and building a release version of your application.

## Releasing your app to users

You can release your Android applications several ways. Usually, you release applications through an application marketplace such as Google Play, but you can also release applications on your own website or by sending an application directly to a user.

### Releasing through an app marketplace

If you want to distribute your apps to the broadest possible audience, releasing through an app marketplace such as Google Play is ideal.

Google Play is the premier marketplace for Android apps and is particularly useful if you want to distribute your applications to a large global audience. However, you can distribute your apps through any app marketplace you want or you can use multiple marketplaces.

#### Releasing your apps on Google Play

Google Play is a robust publishing platform that helps you publicize, sell, and distribute your Android applications to users around the world. When you release your applications through Google Play you have access to a suite of developer tools that let you analyze your sales, identify market trends, and control who your applications are being distributed to. You also have access to several revenue\-enhancing features such as [in\-app billing](https://developer.android.com/google/play/billing) and [application licensing](https://developer.android.com/google/play/licensing). The rich array of tools and features, coupled with numerous end\-user community features, makes Google Play the premier marketplace for selling and buying Android applications.

Releasing your application on Google Play is a simple process that involves three basic steps:

*   Preparing promotional materials.

    To fully leverage the marketing and publicity capabilities of Google Play, you need to create promotional materials for your application, such as screenshots, videos, graphics, and promotional text.

*   Configuring options and uploading assets.

    Google Play lets you target your application to a worldwide pool of users and devices. By configuring various Google Play settings, you can choose the countries you want to reach, the listing languages you want to use, and the price you want to charge in each country. You can also configure listing details such as the application type, category, and content rating. When you are done configuring options you can upload your promotional materials and your application as a draft (unpublished) application.

*   Publishing the release version of your application.

    If you are satisfied that your publishing settings are correctly configured and your uploaded application is ready to be released to the public, you can simply click **Publish** in the Play Console and within minutes your application will be live and available for download around the world.

For more information, see [Google Play](https://developer.android.com/distribute/googleplay).

## Releasing through a website

If you do not want to release your app on a marketplace like Google Play, you can make the app available for download on your own website or server, including on a private or enterprise server. To do this, you must first prepare your application for release in the normal way. Then all you need to do is host the release\-ready APK file on your website and provide a download link to users.

When users browse to the download link from their Android\-powered devices, the file is downloaded and Android system automatically starts installing it on the device. However, the installation process will start automatically only if the user has configured their Settings to allow the installation of apps from [unknown sources](#unknown-sources).

Although it is relatively easy to release your application on your own website, it can be inefficient. For example, if you want to monetize your application you will have to process and track all financial transactions yourself and you will not be able to use Google Play's [In\-app Billing service](https://developer.android.com/google/play/billing) to sell in\-app products. In addition, you will not be able to use the [Licensing service](https://developer.android.com/google/play/licensing) to help prevent unauthorized installation and use of your application.

## User opt\-in for unknown apps and sources

Android protects users from inadvertent download and install of apps from locations other than a first\-party app store, such as Google Play, which is trusted. Android blocks such installs until the user opts into allowing the installation of apps from other sources. The opt\-in process depends on the version of Android running on the user's device:

![Screenshot showing the settings screen for accepting install of
       unknown apps from different sources.](https://developer.android.com/images/publishing/publishing_unknown_apps_sm.png)

**Figure 1.** The *Install unknown apps* system settings screen, where users grant permission for a particular source to install unknown apps.

*   On devices running Android 8.0 (API level 26) and higher, users must navigate to the *Install unknown apps* system settings screen to enable app installations from a particular source.
*   On devices running Android 7.1.1 (API level 25) and lower, users must either enable the **Unknown sources** system setting or allow a single installation of an unknown app.

### Install unknown apps

On devices running Android 8.0 (API level 26) and higher, users must grant permission to install apps from a source that isn't a first\-party app store. To do so, they must enable the **Allow app installs** setting for that source within the *Install unknown apps* system settings screen. Figure 1 illustrates this process.

**Note:** Users can change this setting for a particular source at any time. Therefore, a source that installs unknown apps should always call `[canRequestPackageInstalls()](https://developer.android.com/reference/android/content/pm/PackageManager#canRequestPackageInstalls())` to check whether the user has granted that source permission to install unknown apps. If this method returns `false`, the source should prompt the user to re\-enable the **Allow app installs** setting for that source.

### Unknown sources

![Screenshot showing the setting for accepting download and install of
       apps from unknown sources.](https://developer.android.com/images/publishing/publishing_unknown_sources_sm.png)

**Figure 2.** The **Unknown sources** setting, which determines whether users can install apps that aren't downloaded from Google Play.

In order for users to allow the installation of apps from non\-first\-party sources on devices running Android 7.1.1 (API level 25) and lower, they should enable the **Unknown sources** setting in **Settings > Security**, as shown in Figure 2.

**Note:** When users attempt to install an unknown app on a device running Android 7.1.1 (API level 25) or lower, the system sometimes shows a dialog that asks the user whether they want to allow only one particular unknown app to be installed. In almost all cases, users should allow only one unknown app installation at a time if the option is available to them.

In either case, users need to make this configuration change *before* they can download and install unknown apps onto their devices.

**Note:** Some network providers do not allow users to install apps from unknown sources.
