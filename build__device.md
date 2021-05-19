# Run apps on a hardware device

*   [Table of contents](#top_of_page)
*   [Set up a device for development](#setting-up)
*   [Connect to your device](#connect)
*   [Troubleshoot device connection with the Connection Assistant](#assistant)
    *   [Resolve USB connection issues](#resolve-usb-issues)
*   [RSA security key](#rsa)

When building an Android app, it's important that you always test your app on a real device before releasing it to users. This page describes how to set up your development environment and Android device for testing and debugging over an *Android Debug Bridge (ADB)* connection.

**Note:** Use the [Android emulator](https://developer.android.com/studio/run/emulator) to test your app on different versions of the Android platform and different screen sizes. Also consider using [Firebase Test Lab](https://firebase.google.com/docs/test-lab/) to run your app on a wide variety of real devices hosted in a cloud\-based infrastructure.

## Set up a device for development

Before you can start debugging on your device, there are a few things you must do:

1.  On the device, open the **Settings** app, select **Developer options**, and then enable **USB debugging**.

    **Note:** If you do not see **Developer options**, follow the instructions to [enable developer options](https://developer.android.com/studio/debug/dev-options).

2.  Set up your system to detect your device.

    *   **Chrome OS**: No additional configuration required.
    *   **macOS**: No additional configuration required.
    *   **Ubuntu Linux**: There are two things that need to be set up correctly: each user that wants to use adb needs to be in the plugdev group, and the system needs to have udev rules installed that cover the device.

        **plugdev group**: If you see an error message that says you're not in the plugdev group, you'll need to add yourself to the plugdev group:

        ```
        sudo usermod -aG plugdev $LOGNAME

        ```

        Note that groups only get updated on login, so you'll need to log out for this change to take effect. When you log back in, you can use `id` to check that you're now in the plugdev group.

        **udev rules**: The `android-sdk-platform-tools-common` package contains a community\-maintained default set of udev rules for Android devices. To install:

        ```
        apt-get install android-sdk-platform-tools-common

        ```
    *   **Windows**: Install a USB driver for ADB. For an installation guide and links to OEM drivers, see the [Install OEM USB drivers](https://developer.android.com/studio/run/oem-usb) document.

## Connect to your device

When you are set up and plugged in over USB, you can click **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png) in Android Studio to [build and run your app on the device](https://developer.android.com/studio/run).

You can also use [adb](https://developer.android.com/studio/command-line/adb) to issue commands, as follows:

*   Verify that your device is connected by running the `adb devices` command from your `android_sdk/platform-tools/` directory. If connected, you'll see the device listed.
*   Issue any [adb command](https://developer.android.com/studio/command-line/adb#issuingcommands) with the `-d` flag to target your device.

## Troubleshoot device connection with the Connection Assistant

The Connection Assistant provides step\-by\-step instructions to help you set up and use a device over the ADB connection.

To start the assistant, choose **Tools > Connection Assistant**.

The Connection Assistant provides instructions, in\-context controls, and a list of connected devices in a series of pages in the **Assistant** panel. Use the **Next** and **Previous** buttons at the bottom of the **Assistant** panel to work through the pages as needed:

*   **Connect your device over USB**: The Connection Assistant begins by prompting you to connect your device over USB, and it provides a **Rescan USB devices** button with which you can start a new scan for connected devices.
*   **Enable USB debugging**: The Connection Assistant then tells you how to enable USB debugging in the on\-device developer options.
*   **Restart the ADB server**: Finally, if you still don't see your device on the list of available devices, you can use the **Restart ADB server** button on the last page of the Connection Assistant. Restarting the ADB server also causes ADB to scan for devices again. If you still don't see your device on the list of available devices, try the troubleshooting steps in the next section of this page.

### Resolve USB connection issues

If the Connection Assistant is not detecting your device over USB, you can try the following troubleshooting steps to resolve the issue:

#### Check that Android Studio can connect to the Android Emulator

To check if the issue is being caused by a connection problem between Android Studio and the Android Emulator, follow these steps:

1.  Open the [AVD Manager](https://developer.android.com/studio/run/managing-avds#viewing).
2.  [Create a new AVD](https://developer.android.com/studio/run/managing-avds#createavd) if you don't already have one.
3.  [Run the emulator](https://developer.android.com/studio/run/managing-avds#emulator) using your AVD.
4.  Do one of the following:
    *   If Android Studio can't connect to the emulator, download the latest [SDK Platform Tools](https://developer.android.com/studio/releases/platform-tools) and then try again.
    *   If the emulator starts successfully, [check the USB cable](#check-usb-cable).

#### Check the USB cable

To check if the issue is being caused by a faulty USB cable, follow the steps in this section.

If you have another USB cable:

1.  Connect the device using the secondary cable.
2.  Check if the Connection Assistant can now detect the device.
3.  If the device is not detected, try the primary cable again.
4.  If the device still isn't detected, assume that the problem is with the device and [check if the device is set up for development](#check-device-setup).

If you don't have another USB cable but you do have another Android device:

1.  Connect the secondary device to your computer.
2.  If the Connection Assistant can detect the secondary device, assume that the problem is with the primary device and [check if the device is set up for development](#check-device-setup).

    If the secondary device is not detected, the problem might be with the USB cable.

#### Check if the device is set up for development

To check if the issue is being caused by settings on the device, follow these steps:

1.  Follow the steps in the [Set up a device for development](#setting-up) section.
2.  If this does not resolve the problem, contact the device OEM's customer support for help. Tell the customer support representative that the device won't connect to Android Studio using ADB.

## RSA security key

When you connect a device running Android 4.2.2 (API level 17) or higher to your computer, the system shows a dialog asking whether to accept an RSA key that allows debugging through this computer. This security mechanism protects user devices because it ensures that USB debugging and other adb commands cannot be executed unless you're able to unlock the device and acknowledge the dialog.
