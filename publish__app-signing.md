# Sign your app

- [Sign your app](#sign-your-app)
  - [Play App Signing](#play-app-signing)
  - [Keys, certificates, and keystores](#keys-certificates-and-keystores)
    - [Certificates](#certificates)
  - [Sign your debug build](#sign-your-debug-build)
    - [Expiry of the debug certificate](#expiry-of-the-debug-certificate)
  - [Sign your app for release to Google Play](#sign-your-app-for-release-to-google-play)
    - [Generate an upload key and keystore](#generate-an-upload-key-and-keystore)
    - [Sign your app with your key](#sign-your-app-with-your-key)
  - [Opt in to Play App Signing](#opt-in-to-play-app-signing)
    - [Opt in a new app](#opt-in-a-new-app)
    - [Opt in an existing app](#opt-in-an-existing-app)
      - [Generate and register an upload certificate](#generate-and-register-an-upload-certificate)
      - [Upgrade your app signing key](#upgrade-your-app-signing-key)
    - [Reset a lost or compromised private upload key](#reset-a-lost-or-compromised-private-upload-key)
    - [Configure the build process to automatically sign your app](#configure-the-build-process-to-automatically-sign-your-app)
    - [Sign each product flavor differently](#sign-each-product-flavor-differently)
  - [Manage your own signing key](#manage-your-own-signing-key)
  - [Signing considerations](#signing-considerations)
    - [Keep your key secure](#keep-your-key-secure)
      - [Remove signing information from your build files](#remove-signing-information-from-your-build-files)

Android requires that all APKs be digitally signed with a certificate before they are installed on a device or updated. If you use [Android App Bundles](https://developer.android.com/guide/app-bundle), you need to sign only your app bundle before you upload it to the Play Console, and Play App Signing takes care of the rest. However, you can also manually sign your app for upload to Google Play and other app stores.

This page guides your through some important concepts related to app signing and security, how to sign your app for release to Google Play using Android Studio, and how to opt in to Play App Signing.

The following is a high\-level overview of the steps you might need to take to sign and publish a new app to Google Play:

1.  [Generate an upload key and keystore](#generate-key)
2.  [Sign your app with your upload key](#sign_release)
3.  [Opt in to Play App Signing](#enroll)
4.  [Upload your app to Google Play](https://developer.android.com/studio/publish/upload-bundle)
5.  [Prepare & roll out release of your app](https://support.google.com/googleplay/android-developer/answer/7159011)

If instead your app is already published to the Google Play Store with an existing app signing key, or you would like to choose the app signing key for a new app instead of having Google generate it, follow these steps:

1.  [Sign your app](#sign_release) with your app’s *signing key* and select the option to encrypt and export its signing key.
2.  [Upload your app’s signing key](#upload_signing_key) to opt in to Play App Signing.
3.  (Recommended) [Generate and register an upload certificate](#generate-key) for future updates to your app
4.  [Upload your app to Google Play](https://developer.android.com/studio/publish/upload-bundle)
5.  [Prepare & roll out release of your app](https://support.google.com/googleplay/android-developer/answer/7159011)

This page also explores how to manage your own keys for when uploading your app to other app stores. If you do not use Android Studio or would rather sign your app from the command line, learn about how to use [`apksigner`](https://developer.android.com/studio/command-line/apksigner).

**Note:** If you are building an Wear OS app, the process for signing the app can differ from the process described on this page. See the information about [packaging and publishing Wear OS apps](https://developer.android.com/training/wearables/apps/packaging).

## Play App Signing

With Play App Signing, Google manages and protects your app's signing key for you and uses it to sign your APKs for distribution. And, because app bundles defer building and signing APKs to the Google Play Store, you need to opt in to Play App Signing before you upload your app bundle. Doing so lets you benefit from the following:

*   Use the Android App Bundle and support Google Play’s advanced delivery modes. The Android App Bundle makes your app much smaller, your releases simpler, and makes it possible to use feature modules and offer instant experiences.
*   Increase the security of your signing key, and make it possible to use a separate upload key to sign the app bundle you upload to Google Play.

    **Note:** Opting in to Play App Signing applies for the lifetime of your app. In order to ensure security, after you opt in you cannot retrieve a copy of your app's signing key and you can't delete it from Google's servers without deleting your app.

Play App Signing uses two keys: the *app signing key* and the *upload key*, which are described in further detail in the section about [Keys and keystores](#keys_keystores). You keep the upload key and use it to sign your app for upload to the Google Play Store. By using a separate upload key you can [request an upload key reset](https://support.google.com/googleplay/android-developer/answer/7384423#reset) if your key is ever lost or compromised. By comparison, if you’re not opted in to app signing with by Google Play and you lose your app’s signing key, you lose the ability to update your app.

When you are ready to publish, you can sign your app using Android Studio upload it to Google Play. The key with which you sign your app becomes your app’s upload key. Google uses the upload certificate to verify your identity, and signs your APK(s) with your app signing key for distribution as shown in figure 1.

If you do not already have an app signing key, you can generate one during the sign\-up process.

**Note:** When you opt in to Play App Signing, **you aren’t able to download the signing key from Google**. If you want to use the same signing key across multiple stores, make sure to provide your own signing key when you [opt in to Play App Signing](#enroll), instead of having Google generate one for you.

![](https://developer.android.com/studio/images/publish/appsigning_googleplayappsigningdiagram_2x.png)

**Figure 1**. Signing an app with Play App Signing

Your keys are stored on the same infrastructure that Google uses to store its own keys, where they are protected by Google’s Key Management Service. You can learn more about Google’s technical infrastructure by reading the [Google Cloud Security Whitepapers](https://services.google.com/fh/files/misc/security_whitepapers_march2018.pdf).

When you use Play App Signing, if you lose your upload key, or if it is compromised, you can contact Google to revoke your old upload key and generate a new one. Because your app signing key is secured by Google, you can continue to upload new versions of your app as updates to the original app, even if you change upload keys. To learn more, read [Reset a lost or compromised private upload key](#reset_upload_key).

The next section describes some important terms and concepts related to app signing and security. If you’d rather skip ahead and learn how to prepare your app for upload to the Google Play Store, go to [Sign your app for release](#sign-apk).

## Keys, certificates, and keystores

When it comes to signing your app, it’s important to understand the concepts and definitions described below.

### Certificates

A **public key certificate** (`.der` or `.pem` files), also known as a digital certificate or an identity certificate, contains the public key of a public/private key pair, as well as some other metadata identifying the owner (for example, name and location) who holds the corresponding private key.

When signing your app, the signing tool attaches the certificate to your app. The certificate associates the APK or app bundle to you and your corresponding private key. This helps Android ensure that any future updates to your app are authentic and come from the original author. The key used to create this certificate is called the **app signing key**.

You can download the certificate for your app signing key and your upload key from the app signing page in the Play Console in order to register your key(s) with API providers. The certificate can be shared with anyone. It does not contain your private key.

Every app must use the same certificate throughout its lifespan in order for users to be able to install new versions as updates to the app. For more about the benefits of using the same certificate for all your apps throughout their lifespans, see [Signing considerations](#considerations) below.

A **certificate fingerprint** is a short and unique representation of a certificate that is often requested by API providers alongside the package name to register an app to use their service. The MD5, SHA\-1 and SHA\-256 fingerprints of the upload and app signing certificates can be found on the app signing page of the Play Console. Other fingerprints can also be computed by downloading the original certificate (`.der`) from the same page.

The following are the different types of keys and keystores you should understand:

*   **App signing key:** The key that is used to sign APKs that are installed on a user's device. As part of Android’s secure update model, the signing key never changes during the lifetime of your app. The app signing key is private and must be kept secret. You can, however, share the certificate that is generated using your app signing key.
*   **Upload key:** The key you use to sign the app bundle or APK before you upload it for [app signing with Google Play](#app-signing-google-play). You must keep the upload key secret. However, you can share the certificate that is generated using your upload key. You may generate an upload key in one of the following ways:

    *   If you choose for Google to generate the app signing key for you when you opt in, then the key you use to [sign your app for release](#sign-apk) is designated as your upload key.
    *   If you provide the app signing key to Google when opting in your new or existing app, then you have the option to generate a new upload key during or after opting in for increased security.
    *   If you do not generate a new upload key, you continue to use your app signing key as your upload key to sign each release.

    **Tip:** To keep your keys secure, it’s a good idea to make sure your app signing key and upload key are different.

*   **Java keystore (.jks or .keystore):** A binary file that serves as a repository of certificates and private keys.

*   **Play Encrypt Private Key (PEPK) tool:** Use this tool to export private keys from a Java Keystore and encrypt them for transfer to Google Play. When providing the app signing key for Google to use, select the option to **Export and upload a key from a Java keystore** and follow the instructions to download and use the tool. Alternatively, select the option to **Export and upload a key (not using a Java keystore)** to download, review, and use the PEPK tool’s open source code.

## Sign your debug build

When running or debugging your project from the IDE, Android Studio automatically signs your app with a debug certificate generated by the Android SDK tools. The first time you run or debug your project in Android Studio, the IDE automatically creates the debug keystore and certificate in `$HOME/.android/debug.keystore`, and sets the keystore and key passwords.

Because the debug certificate is created by the build tools and is insecure by design, most app stores (including the Google Play Store) do not accept apps signed with a debug certificate for publishing.

Android Studio automatically stores your debug signing information in a signing configuration so you do not have to enter it every time you debug. A signing configuration is an object consisting of all of the necessary information to sign your app, including the keystore location, keystore password, key name, and key password. You cannot directly edit the debug signing configuration, but you can configure how you [sign your release build](#sign-apk).

For more information about how to build and run apps for debugging, see [Build and Run Your App](https://developer.android.com/tools/building/building-studio).

### Expiry of the debug certificate

The self\-signed certificate used to sign your app for debugging has an expiration date of 30 years from its creation date. When the certificate expires, you get a build error.

To fix this problem, simply delete the `debug.keystore` file stored in one of the following locations:

*   `~/.android/` on OS X and Linux
*   `C:\Documents and Settings\user\.android\` on Windows XP
*   `C:\Users\user\.android\` on Windows Vista and Windows 7, 8, and 10

The next time you build *and run* a debug version of your app, Android Studio regenerates a new keystore and debug key.

## Sign your app for release to Google Play

When you are ready to publish your app, you need to sign your app and upload it to an app store, such as Google Play. When publishing your app to Google Play, you should also opt in to Play App Signing. This section shows you how to properly sign your app for release and opt in to Play App Signing.

### Generate an upload key and keystore

If you don't already have an upload key, which is useful when opting in to Play App Signing, you can generate one using Android Studio as follows:

1.  In the menu bar, click **Build > Generate Signed Bundle/APK**.
2.  In the **Generate Signed Bundle or APK** dialog, select **Android App Bundle** or **APK** and click **Next**.
3.  Below the field for **Key store path**, click **Create new**.
4.  On the **New Key Store** window, provide the following information for your keystore and key, as shown in figure 2.

    ![](https://developer.android.com/studio/images/publish/keystore-wizard_2x.png)

    **Figure 2.** Create a new upload key and keystore in Android Studio.

5.  **Keystore**

    *   **Key store path:** Select the location where your keystore should be created.
    *   **Password:** Create and confirm a secure password for your keystore.
6.  **Key**

    *   **Alias:** Enter an identifying name for your key.
    *   **Password:** Create and confirm a secure password for your key. This should be different from the password you chose for your keystore.
    *   **Validity (years):** Set the length of time in years that your key will be valid. Your key should be valid for at least 25 years, so you can sign app updates with the same key through the lifespan of your app.
    *   **Certificate:** Enter some information about yourself for your certificate. This information is not displayed in your app, but is included in your certificate as part of the APK.
7.  Once you complete the form, click **OK**.

8.  If you would like to build and sign your app with your upload key, continue to the section about how to [Sign your app with your upload key](#sign_release). If you only want to generate the key and keystore, click **Cancel**.

### Sign your app with your key

If you already have an upload key, use it to sign your app. If instead your app is already signed and published to the Google Play store with an existing app signing key, use it to sign your app and make sure to encrypt and export it to opt your app in to Play App Signing. You can later [generate a separate upload key](#generate-key) and [register your upload key’s public certificate](#register_certificate) with Google Play to sign and upload subsequent updates to your app.

To sign your app using Android Studio, and export an existing app signing key, follow these steps:

1.  If you don’t currently have the **Generate Signed Bundle or APK** dialog open, click **Build > Generate Signed Bundle/APK**.
2.  In the **Generate Signed Bundle or APK** dialog, select either **Android App Bundle** or **APK** and click **Next**.
3.  Select a module from the drop down.
4.  Specify the path to your keystore, the alias for your key, and enter the passwords for both. If you haven't yet prepared your upload keystore and key, first [Generate an upload key and keystore](#generate-key) and then return to complete this step.

    ![](https://developer.android.com/studio/images/publish/generate-signed-apk-wizard_2x.png)

    **Figure 3**. Sign your app with your upload key.

5.  If you're signing an app bundle with an existing app signing key, and you’d like to later opt your app in to Play App Signing, check the box next to **Export encrypted key** and specify a path to save your signing key as an encrypted `*.pepk` file. You can then use your encrypted app signing key to [opt in an existing app into Play App Signing](#enroll_existing).

6.  Click **Next**.

7.  In the next window (shown in figure 4), select a destination folder for your signed app, select the build type, choose the product flavor(s) if applicable.

8.  If you are building and signing an APK, you need to select which **Signature Versions** you want your app to support. To learn more, read about [app signing schemes](https://source.android.com/security/apksigning)

    **Note:** Google Play supports [APK Signature Scheme v3](https://source.android.com/security/apksigning/v3) for APKs that aren't already published with an existing [signing certificate lineage](https://developer.android.com/studio/command-line/apksigner#usage-rotate).

9.  Click **Finish**.

    ![](https://developer.android.com/studio/images/publish/signed-apk-destination_2x.png)

    **Figure 4**. Generate a signed version of your app for the selected product flavors.

    **Note:** If your project uses product flavors, you can select multiple product flavors while holding down the **Control** key on Windows/Linux, or the **Command** key on Mac OSX. Android Studio will generate a separate APK or app bundle for each product flavor you select.

![](https://developer.android.com/studio/images/publish/generate_signed_bundle_popup-2x.png)

**Figure 5.** Click the link in the popup to analyze or locate your app bundle, or locate your exported signing key.

After Android Studio finishes building your signed app, you can either **locate** or **analyze** your app by clicking on the appropriate option in the pop\-up notification. If you selected the option to export your signing key, you can quickly navigate to it by clicking the dropdown arrow in the bottom right corner of the popup to expand it and clicking **Show Exported Key File**, as shown in figure 5.

Now you’re ready to opt your app in to Play App Signing and upload your app for release. If you’re new to the app publishing process, you may want to read the [Launch overview](https://developer.android.com/distribute/best-practices/launch). Otherwise, continue to the page about how to [Upload your app to the Play Console](https://developer.android.com/studio/publish/upload-bundle).

## Opt in to Play App Signing

As described earlier in this page, [Play App Signing](#app-signing-google-play) is the recommended way to sign your app for distribution through Google Play. The steps you need to take in order to opt in your app depends on whether your app has not yet been published to Google Play, or your app is already signed and published using an existing app signing key.

### Opt in a new app

To opt in an app that has not yet been published to Google Play, proceed as follows:

1.  If you haven’t already done so, [generate an upload key](#generate-key) and [sign your app](#sign_release) with that upload key.
2.  Sign in to your [Play Console](https://play.google.com/console/).
3.  Follow the steps to [prepare & roll out your release](https://support.google.com/googleplay/android-developer/answer/7159011) to create a new release.
4.  After you choose a release track, configure app signing under the **Let Google create and manage my app signing key** section as follows:
    *   To have Google Play generate an app signing key for you and use it to sign your app, select **Continue**. The key you use to sign your first release becomes your upload key, and you should use it to sign future releases.
    *   To use the same key as another app on your developer account, select **Advanced options > Use the same key as another app in this account**, select an app, and then click **Continue**.
    *   To provide your own signing key for Google to use when signing your app, select **Advanced options** and select one of the options that lets you securely upload a private key and its public certificate.

**Note:** If you haven't already accepted the [Terms of Service](https://play.google.com/about/play-app-signing-terms.html), you are required to review the terms and select **Accept** to continue. If you choose not to opt in to app signing at this time, you can opt in an existing app at any time by following the instructions below.

In the section called **Android App Bundles and APKs to add**, click **Browse files** to locate and upload the app you signed using your upload key. For more information about releasing your app, refer to [prepare & roll out your release](https://support.google.com/googleplay/android-developer/answer/7159011). When you release your app after opting into Play App Signing, Google Play generates and manages your app’s signing key for you. Simply sign subsequent updates to your app using your app’s upload key before uploading it to Google Play.

If you need to create a new upload key for you app, go to the section about how to [Reset a lost or compromised private upload key](#reset_upload_key).

### Opt in an existing app

If you’re updating an app that’s already published to Google Play using an existing app signing key, you can opt in to Play App Signing as follows:

1.  If you haven’t already done so, [sign your app](#sign_release) with your existing app signing key and make sure to check the box next to **Export encrypted key** to save your signing key as an encrypted `*.pepk` file. You’ll need this file in a later step.
2.  Sign in to your [Play Console](https://play.google.com/console/) and navigate to your app.
3.  On the left menu, click **Release management > App signing**.
4.  If applicable, review the Terms of Service and select **Accept**.
5.  Select one of the options that best describes the signing key you want to upload to Google Play and follow the instructions that are shown. For example, if you used Android Studio to export your app’s signing key, as described on this page, select **Upload a key exported from Android Studio** and upload the `*.pepk` file for your key.
6.  Click **Enroll**.

You should now see a page with the details of your app’s signing and upload certificates. Google Play now signs your app with your existing key when deploying it to users. However, one of the most important benefits to Play App Signing is the ability to separate the key you use to sign the artifact you upload to Google Play from the key that Google Play uses to sign your app for distribution to users. So, consider following the steps in the next section to generate and register a separate upload key.

#### Generate and register an upload certificate

When you're publishing an app that is not signed by an upload key, the Google Play Console provides the option to register one for future updates to the app. Although this is an optional step, it’s recommended that you publish your app with a key that’s separate from the one Google Play uses to distribute your app to users. That way, Google keeps your signing key secure, and you have the option to [reset a lost or compromised private upload key](#reset_upload_key). This section describes how to create an upload key, generate an upload certificate from it, and register that certificate with Google Play for future updates of your app.

The following describes the situations in which you see the option to register an upload certificate in the Play Console:

*   When you publish a new app that’s signed with a signing key and opt it in to Play App Signing.
*   When you are about to publish an existing app that’s already opted in to Play App Signing, but it is signed using its signing key.

If you are not publishing an update to an existing app that’s already opted in to Play App Signing, and you’d like to register an upload certificate, complete the steps below and continue on to the section about how to [reset a lost or compromised private upload key](#reset_upload_key).

If you haven’t already done so, [generate an upload key and keystore](#generate-key).

After you create your upload key and keystore, you need to generate a public certificate from your upload key using [`keytool`](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html), with the following command:

$ keytool \-export \-rfc
  \-keystore your\-upload\-keystore.jks
  \-alias upload\-alias
  \-file output\_upload\_certificate.pem

Now that you have your upload certificate, register it with Google when prompted in the Play Console or read the section below to register it though the Google Play support team.

#### Upgrade your app signing key

In some circumstances, you might want to change your app's signing key. For example, because you want a cryptographically stronger key or your signing key has been compromised. However, because users can only update your app if the update is signed with the same signing key, it's difficult to change the signing key for an app that's already published.

If you publish your app to Google Play, you can upgrade the siging key for your published app through the Play Console—your new key is used to sign new installs and app updates, while your older app signing key is used to sign updates for users who installed your app before the key upgrade.

To learn more, read [Upgrade your app signing key for new installs](https://support.google.com/googleplay/android-developer/answer/7384423#Upgrade).

### Reset a lost or compromised private upload key

If you lost your private upload key or your private key has been compromised, you can create a new one and contact the Google Play support team to [reset the key](https://support.google.com/googleplay/android-developer/answer/7384423#reset).

**Note:** Resetting your upload key will not affect the app signing key that Google Play uses to re\-sign APKs before delivering to users.

### Configure the build process to automatically sign your app

In Android Studio, you can configure your project to sign the release version of your app automatically during the build process by creating a signing configuration and assigning it to your release build type. A signing configuration consists of a keystore location, keystore password, key alias, and key password. To create a signing configuration and assign it to your release build type using Android Studio, complete the following steps:

1.  In the **Project** window, right click on your app and click **Open Module Settings**.
2.  On the **Project Structure** window, under **Modules** in the left panel, click the module you would like to sign.
3.  Click the **Signing** tab, then click **Add** ![](https://developer.android.com/studio/images/publish/add-signing-config_2-1_2x.png).
4.  Select your keystore file, enter a name for this signing configuration (as you may create more than one), and enter the required information.

    ![](https://developer.android.com/studio/images/publish/project-structure-signing_2-1_2x.png)

    **Figure 7**. The window for creating a new signing configuration.

5.  Click the **Build Types** tab.
6.  Click the **release** build.
7.  Under **Signing Config**, select the signing configuration you just created.

    ![](https://developer.android.com/studio/images/publish/project-structure-build-types_2-1_2x.png)

    **Figure 8**. Select a signing configuration in Android Studio.

8.  Click **OK**.

Now every time you build your release build type by selecting an option under **Build > Build Bundle(s) / APK(s)** in Android Studio, the IDE will sign your app automatically, using the signing configuration you specified. You can find your signed APK or app bundle in the `build/outputs/` directory inside the project directory for the module you are building.

When you create a signing configuration, your signing information is included in plain text in your Gradle build files. If you are working in a team or sharing your code publicly, you should keep your signing information secure by removing it from the build files and storing it separately. You can read more about how to remove your signing information from your build files in [Remove Signing Information from Your Build Files](#secure-shared-keystore). For more about keeping your signing information secure, read [Secure your key](#secure-key).

### Sign each product flavor differently

If your app uses product flavors and you would like to sign each flavor differently, you can create additional signing configurations and assign them by flavor:

1.  In the **Project** window, right click on your app and click **Open Module Settings**.
2.  On the **Project Structure** window, under **Modules** in the left panel, click the module you would like to sign.
3.  Click the **Signing** tab, then click **Add** ![](https://developer.android.com/studio/images/publish/add-signing-config_2-1_2x.png).
4.  Select your keystore file, enter a name for this signing configuration (as you may create more than one), and enter the required information.

    ![](https://developer.android.com/studio/images/publish/project-structure-signing_2-1_2x.png)

    **Figure 10**. The window for creating a new signing configuration.

5.  Repeat steps 3 and 4 as necessary until you have created all your signing configurations.
6.  Click the **Flavors** tab.
7.  Click the flavor you would like to configure, then select the appropriate signing configuration from the **Signing Config** dropdown menu. ![](https://developer.android.com/studio/images/publish/project-structure-flavors_2-1_2x.png)

    **Figure 11**. Configure signing settings by product flavor.

    Repeat to configure any additional product flavors.

8.  Click **OK**.

You can also specify your signing settings in Gradle configuration files. For more information, see [Configuring Signing Settings](https://developer.android.com/studio/build/build-variants#signing).

## Manage your own signing key

If you choose not to opt in to Play App Signing, you can manage your own app signing key and keystore. Keep in mind, **you are responsible for securing the key and the keystore**. Additionally, your app will not be able to support Android App Bundles, Play Feature Delivery and Play Asset Delivery.

When you are ready to create your own key and keystore, make sure you first choose a strong password for your keystore and a separate strong password for each private key stored in the keystore. You must keep your keystore in a safe and secure place. If you lose access to your app signing key or your key is compromised, Google cannot retrieve the app signing key for you, and you will not be able to release new versions of your app to users as updates to the original app. For more information, see [Secure your key](https://developer.android.com/studio/publish/app-signing#secure-key), below.

If you manage your own app signing key and keystore, when you sign your APK, you will sign it locally using your app signing key and upload the signed APK directly to the Google Play Store for distribution as shown in figure 10.

![](https://developer.android.com/studio/images/publish/appsigning_selfmanagediagram_2x.png)

**Figure 12**. Signing an app when you manage your own app signing key

When you use [Play App Signing](https://developer.android.com/studio/publish/app-signing#google-%0Aplay-app-signing), Google keeps your signing key safe, and ensures your apps are correctly signed and able to receive updates throughout their lifespans. However, if you decide to manage your app signing key yourself, there are a few considerations you should keep in mind.

## Signing considerations

You should sign your app with the same certificate throughout its expected lifespan. There are several reasons why you should do so:

*   **App upgrade:** When the system is installing an update to an app, it compares the certificate(s) in the new version with those in the existing version. The system allows the update if the certificates match. If you sign the new version with a different certificate, you must assign a different package name to the app—in this case, the user installs the new version as a completely new app.
*   **App modularity:** Android allows APKs signed by the same certificate to run in the same process, if the apps so request, so that the system treats them as a single app. In this way you can deploy your app in modules, and users can update each of the modules independently.
*   **Code/data sharing through permissions:** Android provides signature\-based permissions enforcement, so that an app can expose functionality to another app that is signed with a specified certificate. By signing multiple APKs with the same certificate and using signature\-based permissions checks, your apps can share code and data in a secure manner.

If you plan to support upgrades for an app, ensure that your app signing key has a validity period that exceeds the expected lifespan of that app. A validity period of 25 years or more is recommended. When your key's validity period expires, users will no longer be able to seamlessly upgrade to new versions of your app.

If you plan to publish your apps on Google Play, the key you use to sign your app must have a validity period ending after 22 October 2033. Google Play enforces this requirement to ensure that users can seamlessly upgrade apps when new versions are available.

### Keep your key secure

If you choose to manage and secure your app signing key and keystore yourself (instead of opting in to [Play App Signing](#app-signing-google-play)), securing your app signing key is of critical importance, both to you and to the user. If you allow someone to use your key, or if you leave your keystore and passwords in an unsecured location such that a third\-party could find and use them, your authoring identity and the trust of the user are compromised.

**Note:** If you use Play App Signing, your app signing key is kept secure using Google's infrastructure. You should still keep your upload key secure as described below. If your upload key is compromised, you can contact Google to revoke it and receive a new upload key.

If a third party should manage to take your key without your knowledge or permission, that person could sign and distribute apps that maliciously replace your authentic apps or corrupt them. Such a person could also sign and distribute apps under your identity that attack other apps or the system itself, or corrupt or steal user data.

Your private key is required for signing all future versions of your app. If you lose or misplace your key, you will not be able to publish updates to your existing app. You cannot regenerate a previously generated key.

Your reputation as a developer entity depends on your securing your app signing key properly, at all times, until the key is expired. Here are some tips for keeping your key secure:

*   Select strong passwords for the keystore and key.
*   Do not give or lend anyone your private key, and do not let unauthorized persons know your keystore and key passwords.
*   Keep the keystore file containing your private key in a safe, secure place.

In general, if you follow common\-sense precautions when generating, using, and storing your key, it will remain secure.

#### Remove signing information from your build files

When you create a signing configuration, Android Studio adds your signing information in plain text to the module's `build.gradle` files. If you are working with a team or open\-sourcing your code, you should move this sensitive information out of the build files so it is not easily accessible to others. To do this, you should create a separate properties file to store secure information and refer to that file in your build files as follows:

1.  Create a signing configuration, and assign it to one or more build types. These instructions assume you have configured a single signing configuration for your release build type, as described in [Configure the build process to automatically sign your app](#sign-auto), above.
2.  Create a file named `keystore.properties` in the root directory of your project. This file should contain your signing information, as follows:storePassword=myStorePassword
    keyPassword=mykeyPassword
    keyAlias=myKeyAlias
    storeFile=myStoreFileLocation
3.  In your module's `build.gradle` file, add code to load your `keystore.properties` file before the `android {}` block.

    ...

    // Create a variable called keystorePropertiesFile, and initialize it to your
    // keystore.properties file, in the rootProject folder.
    def keystorePropertiesFile \= rootProject.file("keystore.properties")

    // Initialize a new Properties() object called keystoreProperties.
    def keystoreProperties \=  new  Properties()

    // Load your keystore.properties file into the keystoreProperties object.
    keystoreProperties.load(new  FileInputStream(keystorePropertiesFile))android { ...
    }

    **Note:** You could choose to store your `keystore.properties` file in another location (for example, in the module folder rather than the root folder for the project, or on your build server if you are using a continuous integration tool). In that case, you should modify the code above to correctly initialize `keystorePropertiesFile` using your actual `keystore.properties` file's location.

4.  You can refer to properties stored in `keystoreProperties` using the syntax `keystoreProperties['propertyName']`. Modify the `signingConfigs` block of your module's `build.gradle` file to reference the signing information stored in `keystoreProperties` using this syntax.

    android {signingConfigs {config {
                keyAlias keystoreProperties\['keyAlias'\]
                keyPassword keystoreProperties\['keyPassword'\]
                storeFile file(keystoreProperties\['storeFile'\])
                storePassword keystoreProperties\['storePassword'\] } } ... }

5.  Open the **Build Variants** tool window and ensure that the release build type is selected.
6.  Select an option under **Build > Build Bundle(s) / APK(s)** to build either an APK or app bundle of your release build. You should see the build output in the `build/outputs/` directory for your module.

Because your build files no longer contain sensitive information, you can now include them in source control or upload them to a shared codebase. Be sure to keep the `keystore.properties` file secure. This may include removing it from your source control system.

Was this page helpful?