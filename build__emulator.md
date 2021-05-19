# Run apps on the Android Emulator

- [Run apps on the Android Emulator](#run-apps-on-the-android-emulator)
  - [Requirements and recommendations](#requirements-and-recommendations)
    - [Deprecation for 32\-bit Windows systems](#deprecation-for-32-bit-windows-systems)
  - [Install the emulator](#install-the-emulator)
  - [Android virtual devices](#android-virtual-devices)
  - [Run an app on the Android Emulator](#run-an-app-on-the-android-emulator)
  - [Launch the Android Emulator without first running an app](#launch-the-android-emulator-without-first-running-an-app)
  - [Run the Android Emulator directly in Android Studio](#run-the-android-emulator-directly-in-android-studio)
      - [Limitations](#limitations)
  - [Install and add files](#install-and-add-files)
  - [Snapshots](#snapshots)
    - [Save Quick Boot snapshots](#save-quick-boot-snapshots)
    - [Save general snapshots](#save-general-snapshots)
    - [Delete a snapshot](#delete-a-snapshot)
    - [Load a snapshot](#load-a-snapshot)
    - [Disable Quick Boot](#disable-quick-boot)
    - [Cold boot once](#cold-boot-once)
    - [Snapshot requirements and troubleshooting](#snapshot-requirements-and-troubleshooting)
  - [Navigate the emulator screen](#navigate-the-emulator-screen)
  - [Perform common actions in the emulator](#perform-common-actions-in-the-emulator)
  - [Screen recording](#screen-recording)
  - [Screenshots](#screenshots)
  - [Camera support](#camera-support)
    - [Virtual scene camera and ARCore](#virtual-scene-camera-and-arcore)
    - [Test common AR actions with macros](#test-common-ar-actions-with-macros)
  - [Extended controls, settings, and help](#extended-controls-settings-and-help)
  - [Wi\-Fi](#wi-fi)
  - [Limitations](#limitations-1)

The Android Emulator simulates Android devices on your computer so that you can test your application on a variety of devices and Android API levels without needing to have each physical device.

The emulator provides almost all of the capabilities of a real Android device. You can simulate incoming phone calls and text messages, specify the location of the device, simulate different network speeds, simulate rotation and other hardware sensors, access the Google Play Store, and much more.

Testing your app on the emulator is in some ways faster and easier than doing so on a physical device. For example, you can transfer data faster to the emulator than to a device connected over USB.

The emulator comes with predefined configurations for various Android phone, tablet, Wear OS, and Android TV devices.

Watch the following video for an overview of some emulator features.

You can use the emulator manually through its graphical user interface and programmatically through the command line and the emulator console. For a comparison of the features available through each interface, see [Comparison of Android Emulator tools](https://developer.android.com/studio/run/emulator-comparison).

## Requirements and recommendations

The Android Emulator has additional requirements beyond the basic [system requirements for Android Studio](https://developer.android.com/studio#Requirements), which are described below:

*   [SDK Tools](https://developer.android.com/studio/releases/sdk-tools) 26.1.1 or higher
*   64\-bit processor
*   Windows: CPU with UG (unrestricted guest) support
*   HAXM 6.2.1 or later (HAXM 7.2.0 or later recommended)

The use of [hardware acceleration](https://developer.android.com/studio/run/emulator-acceleration) has additional requirements on Windows and Linux:

*   Intel processor on Windows or Linux: Intel processor with support for Intel VT\-x, Intel EM64T (Intel 64), and Execute Disable (XD) Bit functionality
*   AMD processor on Linux: AMD processor with support for AMD Virtualization (AMD\-V) and [Supplemental Streaming SIMD Extensions 3 (SSSE3)](https://en.wikipedia.org/wiki/SSSE3)
*   AMD processor on Windows: Android Studio 3.2 or higher and Windows 10 April 2018 release or higher for [Windows Hypervisor Platform (WHPX)](https://docs.microsoft.com/en-us/virtualization/api/) functionality

To work with Android 8.1 (API level 27) and higher system images, an attached webcam must have the capability to capture 720p frames.

### Deprecation for 32\-bit Windows systems

The Android Emulator was deprecated in June 2019 for 32\-bit Windows systems. Support for the 32\-bit Windows emulator continues until June 2020, including critical bug fixes, but no new features will be added. If you are using the emulator on a 32\-bit Windows system, you should plan to migrate to a 64\-bit Windows system.

If you are using the emulator on a 32\-bit Windows system, you can use the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager) to install the latest version of the emulator for 32\-bit Windows.

## Install the emulator

To install the Android Emulator, select the **Android Emulator** component in the **SDK Tools** tab of the **SDK Manager**. For instructions, see [Update your tools using the SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager).

## Android virtual devices

Each instance of the Android Emulator uses an *Android virtual device (AVD)* to specify the Android version and hardware characteristics of the simulated device. To effectively test your app, you should create an AVD that models each device on which your app is designed to run. To create and manage AVDs, use the [AVD Manager](https://developer.android.com/tools/devices/managing-avds).

Each AVD functions as an independent device, with its own private storage for user data, SD card, and so on. By default, the emulator stores the user data, SD card data, and cache in a directory specific to that AVD. When you launch the emulator, it loads the user data and SD card data from the AVD directory.

## Run an app on the Android Emulator

You can run an app from an Android Studio project, or you can run an app that's been installed on the Android Emulator as you would run any app on a device.

To start the Android Emulator and run an app in your project:

1.  In Android Studio, [create an Android Virtual Device (AVD)](https://developer.android.com/studio/run/managing-avds#createavd) that the emulator can use to install and run your app.
2.  In the toolbar, select the AVD that you want to run your app on from the target device drop\-down menu.

    ![Target device drop-down menu.](https://developer.android.com/studio/images/run/deploy-run-app.png)

3.  Click **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png).

    If you receive an error or warning message at the top of the dialog, click the link to correct the problem or to get more information.

    Some errors you must fix before you can continue, such as certain Hardware Accelerated Execution Manager (Intel HAXM) errors.

    For macOS, if you see a `Warning: No DNS servers found` error when starting the emulator, check to see whether you have an `/etc/resolv.conf` file. If you don't have this file, enter the following command in a terminal window:

    ln \-s /private/var/run/resolv.conf /etc/resolv.conf

## Launch the Android Emulator without first running an app

To start the emulator:

1.  [Open the AVD Manager](https://developer.android.com/tools/devices/managing-avds).

2.  Double\-click an AVD, or click **Run**

    ![Run icon](https://developer.android.com/studio/images/buttons/toolbar-run.png).

    The Android Emulator loads.

While the emulator is running, you can run Android Studio projects and choose the emulator as the target device. You can also drag one or more APKs onto the emulator to install them, and then run them.

## Run the Android Emulator directly in Android Studio

Run the Android Emulator directly in Android Studio to conserve screen real estate, to navigate quickly between the emulator and the editor window using hotkeys, and to organize your IDE and emulator workflow in a single application window.

![The emulator launching in a tool window in Android Studio.](https://developer.android.com/studio/images/releases/emulator-tool-window.gif)

To run the emulator in Android Studio, make sure you're using Android Studio 4.1 or higher with version 30.0.10 or higher of the Android Emulator, then follow these steps:

1.  Click **File > Settings > Tools > Emulator** (or **Android Studio > Preferences > Tools > Emulator on macOS**), then select **Launch in a tool window** and click **OK**.
2.  If the Emulator window didn't automatically appear, open it by clicking **View > Tool Windows > Emulator**.
3.  Start your virtual device using the **AVD Manager** or by targeting it when running your app.

#### Limitations

Currently, you can't use the emulator's extended controls when it's running in a tool window. If your development workflow depends heavily on the extended controls, continue to use the Android Emulator as a standalone application. In addition, certain virtual devices—such as Android TV and foldable devices—can't be run in Android Studio because they have specialized UI requirements or important functions in the extended controls.

## Install and add files

To install an APK file on the emulated device, drag an APK file onto the emulator screen. An APK Installer dialog appears. When the installation completes, you can view the app in your apps list.

To add a file to the emulated device, drag the file onto the emulator screen. The file is placed in the `/sdcard/Download/` directory. You can view the file from Android Studio using the [Device File Explorer](https://developer.android.com/studio/debug/device-file-explorer), or find it from the device using the **Downloads** or **Files** app, depending on the device version.

## Snapshots

A *snapshot* is a stored image of an AVD (Android Virtual Device) that preserves the entire state of the device at the time that it was saved – including OS settings, application state, and user data. You can return to a saved system state by loading a snapshot whenever you choose, saving you the time of waiting for the operating system and applications on the virtual device to restart, as well as saving you the effort of bringing your app back to the state at which you want to resume your testing. Starting a virtual device by loading a snapshot is much like waking a physical device from a sleep state, as opposed to booting it from a powered\-off state.

For each AVD, you can have one *Quick Boot* snapshot and any number of general snapshots.

The simplest way to take advantage of snapshots is to use Quick Boot snapshots: By default, each AVD is set to automatically save a Quick Boot snapshot on exit and load from a Quick Boot snapshot on start.

The first time that an AVD starts, it must perform a *cold boot*, just like powering on a device. If Quick Boot is enabled, all subsequent starts load from the specified snapshot, and the system is restored to the state saved in that snapshot.

Snapshots are valid for the system image, AVD configuration, and emulator features with which they are saved. When you make a change in any of these areas, all snapshots of the affected AVD become invalid. Any update to the Android Emulator, system image, or AVD settings resets the AVD's saved state, so the next time you start the AVD, it must perform a cold boot.

Most controls for saving, loading, and managing snapshots are in the **Snapshots** and **Settings** tabs in the **Snapshots** pane in the emulator's [Extended controls](#extended) window.

![](https://developer.android.com/studio/images/run/snapshots-screenshot_2x.png)

You can also control the Quick Boot options when [starting the emulator from the command line](https://developer.android.com/studio/run/emulator-commandline#startup-options).

### Save Quick Boot snapshots

When you close an AVD, you can specify whether the emulator automatically saves a snapshot when you close. To control this behavior, proceed as follows:

1.  Open the emulator's [Extended controls](#extended) window.
2.  In the **Snapshots** category of controls, navigate to the **Settings** tab.
3.  Use the **Auto\-save current state to Quickboot** drop\-down menu to select one of the following options:

    *   **Yes**: Always save an AVD snapshot when you close the emulator. This is the default.

        **Note:** When automatic Quick Boot snapshots are enabled, you can skip saving a Quick Boot snapshot by holding the `Shift` key down when you close the emulator.

    *   **No**: Don't save an AVD snapshot when you close the emulator.

Your selection applies only to the AVD that is currently open. You cannot save snapshots while ADB is offline (such as while the AVD is still booting).

### Save general snapshots

Whereas you can only have one Quick Boot snapshot for each AVD, you can have multiple general snapshots for each AVD.

To save a general snapshot, open the emulator's [Extended controls](#extended) window, select the **Snapshots** category, and click the **Take snapshot** button in the lower\-right corner of the window.

To edit the name and description of the selected snapshot, click the edit ![](https://developer.android.com/studio/images/buttons/edit-snapshot-button.png) button at the bottom of the window.

### Delete a snapshot

To manually delete a snapshot, open the emulator's [Extended controls](#extended) window, select the **Snapshots** category, select the snapshot, and click the delete ![](https://developer.android.com/studio/images/buttons/delete-snapshot-button.png) button at the bottom of the window.

You can also specify whether you would like the emulator to automatically delete snapshots when they become invalid, such as when the AVD settings or emulator version change. By default, the emulator will ask you if you'd like for it to delete invalid snapshots. You can change this setting with the **Delete invalid snapshots** menu in the **Settings** tab of the **Snapshots** pane.

### Load a snapshot

To load a snapshot at any time, open the emulator's [Extended controls](#extended) window, select the **Snapshots** category, choose a snapshot, and click the load ![](https://developer.android.com/studio/images/buttons/load-snapshot-button.png) button at the bottom of the window.

In Android Studio 3.2 and higher, each device configuration includes a **Boot option** control in the advanced settings in the [Virtual Device Configuration](https://developer.android.com/studio/run/managing-avds) dialog with which you can specify which snapshot to load when starting that AVD.

### Disable Quick Boot

If you want to disable Quick Boot so your AVD always performs a cold boot, do the following:

1.  Select **Tools > AVD Manager** and click **Edit this AVD** ![](https://developer.android.com/studio/images/buttons/avd-edit.png).
2.  Click **Show Advanced Settings** and scroll down to **Emulated Performance**.
3.  Select **Cold boot**.

### Cold boot once

Instead of disabling Quick Boot completely, you can cold boot just once by clicking **Cold Boot Now** from the AVD's drop\-down menu in the **AVD Manager**.

![](https://developer.android.com/studio/images/run/avd-coldboot-callout_2x.png)

### Snapshot requirements and troubleshooting

*   Snapshots do not work with Android 4.0.4 (API level 15) or lower.
*   Snapshots do not work with ARM system images for Android 8.0 (API level 26).
*   If the emulator fails to boot from a snapshot, select **Cold Boot Now** for the AVD in the AVD Manager and [submit a bug report](https://developer.android.com/studio/report-bugs).
*   Snapshots are not reliable when software rendering is enabled. If snapshots do not work, click **Edit this AVD** ![](https://developer.android.com/studio/images/buttons/avd-edit.png) in the **AVD Manager** and change **Graphics** to either **Hardware** or **Automatic**.
*   Loading or saving a snapshot is a memory\-intensive operation. If you do not have enough RAM free when a load or save operation begins, the operating system may swap the contents of RAM to the hard disk, which can greatly slow the operation. If you experience very slow snapshot loads or saves, you may be able to speed these operations by freeing RAM. Closing applications that are not essential for your work is a good way to free RAM.

## Navigate the emulator screen

Use your computer mouse pointer to mimic your finger on the touchscreen; select menu items and input fields; and click buttons and controls. Use your computer keyboard to type characters and enter emulator shortcuts.

**Table 1.** Gestures for navigating the emulator

| Feature | Description |
| --- | --- |
| Swipe the screen | Point to the screen, press and hold the primary mouse button, swipe across the screen, and then release. |
| Drag an item | Point to an item on the screen, press and hold the primary mouse button, move the item, and then release. |
| Tap
(touch)

 | Point to the screen, press the primary mouse button, and then release. For example, you could click a text field to start typing in it, select an app, or press a button. |
| Double tap | Point to the screen, press the primary mouse button quickly twice, and then release. |
| Touch and hold | Point to an item on the screen, press the primary mouse button, hold, and then release. For example, you could open options for an item. |
| Type | You can type in the emulator by using your computer keyboard, or using a keyboard that pops up on the emulator screen. For example, you could type in a text field after you selected it. |
| Pinch and spread |

Pressing Control (Command on Mac) brings up a pinch gesture multi\-touch interface. The mouse acts as the first finger, and across the anchor point is the second finger. Drag the cursor to move the first point.

Clicking the left mouse button acts like touching down both points, and releasing acts like picking both up.

 |
| Vertical swipe | Open a vertical menu on the screen and use the scroll wheel (mouse wheel) to scroll through the menu items until you see the one you want. Click the menu item to select it. |

## Perform common actions in the emulator

To perform common actions with the emulator, use the panel on the right side, as described in table 2.

You can use keyboard shortcuts to perform many common actions in the emulator. For a complete list of shortcuts in the emulator, press F1 (Command+/ on Mac) to open the Help pane in the [**Extended controls**](#extended) window.

**Table 2.** Common actions in the emulator

| Feature | Description |
| --- | --- |
| Close
![Close icon](https://developer.android.com/images/tools/e-iclose.png) | Close the emulator. |
| Minimize
![Minimize icon](https://developer.android.com/images/tools/e-iminimize.png) | Minimize the emulator window. |
| Resize | Resize the emulator as you would any other operating system window. The emulator maintains an aspect ratio appropriate for your device. |
| Power
![Power icon](https://developer.android.com/images/tools/e-ipower.png) | Click to turn the screen on or off.
Click and hold to turn the device on or off. |
| Volume up
![Volume Up icon](https://developer.android.com/images/tools/e-ivolumeup.png) | Click to view a slider control and turn the volume up. Click again to turn it up more, or use the slider control to change the volume. |
| Volume down
![Volume Down icon](https://developer.android.com/images/tools/e-ivolumedown.png) | Click to view a slider control and turn the volume down. Click again to turn it down more, or use the slider control to change the volume. |
| Rotate left
![Rotate Left icon](https://developer.android.com/images/tools/e-irotateleft.png) | Rotate the device 90 degrees counterclockwise. |
| Rotate right
![Rotate Right icon](https://developer.android.com/images/tools/e-irotateright.png) | Rotate the device 90 degrees clockwise. |
| Take screenshot
![Take Screenshot icon](https://developer.android.com/images/tools/e-itakescreenshot.png) | Click to take a screenshot of the device. For details, see [Screenshots](#screenshots). |
| Enter zoom mode
![Enter Zoom Mode icon](https://developer.android.com/images/tools/e-izoom.png) |
Click so the cursor changes to the zoom icon. To exit zoom mode, click the button again.

Zoom in and out in zoom mode:

*   Left\-click the screen to zoom in by 25%, up to a maximum of about twice the screen resolution of the virtual device.
*   Right\-click to zoom out.
*   Left\-click and drag to select a box\-shaped area to zoom in on.
*   Right\-click and drag a selection box to reset to default zoom.

To pan in zoom mode, hold Control (Command on Mac) while pressing the arrow keys on the keyboard.

To tap the device screen in zoom mode, Control\-click (Command\-click on Mac).

 |
| Back
![Back icon](https://developer.android.com/images/tools/e-iback.png) | Return to the previous screen, or close a dialog box, an options menu, the Notifications panel, or the onscreen keyboard. |
| Home
![Home icon](https://developer.android.com/images/tools/e-ihome.png) | Return to the Home screen. |
|

Overview
![Overview icon](https://developer.android.com/images/tools/e-ioverview.png)

(Recent Apps)

 | Tap to open a list of thumbnail images of apps you’ve worked with recently. To open an app, tap it. To remove a thumbnail from the list, swipe it left or right. This button isn't supported for Wear OS. |
| Fold
![Fold icon](https://developer.android.com/images/tools/e-fold.png) | For foldable devices, fold the device to display its smaller screen configuration. |
| Unfold
![Unfold icon](https://developer.android.com/images/tools/e-unfold.png) | For foldable devices, unfold the device to display its larger screen configuration. |
| Menu | Press Control+M (Command+M on Mac) to simulate the Menu button. |
| More
![More icon](https://developer.android.com/images/tools/e-imore.png) | Click to access other features and settings, described in the next table. |

## Screen recording

You can record video and audio from the Android Emulator and save the recording to a WebM or animated GIF file.

The screen recording controls are in the **Screen record** tab of the [**Extended Controls**](https://developer.android.com/studio/run/emulator#extended) window.

**Tip:** You can also open the screen recording controls by pressing Control + Shift + R (Command + Shift + R on Mac).

To begin screen recording, click the **Start recording** button in the **Screen record** tab. To stop recording, click **Stop recording**.

Controls for playing and saving the recorded video are at the bottom of the **Screen record** tab. To save the video, choose **WebM** or **GIF** from the menu at the bottom of the tab and click **Save**.

You can also record and save a screen recording from the emulator using the following command on the command line:

`adb emu screenrecord start --time-limit 10 [path to save video]/sample_video.webm`

## Screenshots

To take a screenshot of the virtual device, click the **Take screenshot** ![Take Screenshot icon](https://developer.android.com/images/tools/e-itakescreenshot.png) button.

The emulator creates a PNG file with the name `Screenshot_yyyymmdd-hhmmss.png` using the year, month, day, hour, minute, and second of the capture. For example, `Screenshot_20160219-145848.png`.

By default, the screenshot is saved on your comupter desktop. To change the location to which screenshots are saved, use the **Screenshot save location** control in the **Settings** category in the emulator's [Extended controls](#extended) window.

You can also take screenshots from the command line with either of the following commands:

*   `screenrecord screenshot [destination-directory]`
*   `adb emu screenrecord screenshot [destination-directory]`

## Camera support

The emulator supports the use of basic camera functionality on your virtual device for earlier Android versions. Android 11 and higher supports the following additional Android Emulator camera capabilities:

*   RAW capture
*   YUV reprocessing
*   Level 3 devices
*   Logical camera support
*   Emulating sensor orientation by using data from the sensor manager
*   Applying video stabilization by reducing handshake frequency
*   Applying edge enhancement by removing the upscaling usually done in the YUV pipeline
*   Concurrent cameras

### Virtual scene camera and ARCore

You can use the virtual scene camera in a virtual environment to experiment with augmented reality (AR) apps made with [ARCore](https://developers.google.com/ar/discover/).

For information on using the virtual scene camera in the emulator, see [Run AR apps in Android Emulator](https://developers.google.com/ar/develop/java/emulator).

When using the emulator with a camera app, you can import an image in PNG or JPEG format to be used within a virtual scene. To choose an image for use in a virtual scene, click **Add image** in the **Camera > Virtual scene images** tab in the **Extended controls** window. This feature can be used to import custom images such as QR codes for use with any camera\-based app. For more information, see [Add Augmented Images to the scene](https://developers.google.com/ar/develop/java/emulator#add_augmented_images_to_the_scene).

### Test common AR actions with macros

You can greatly reduce the time it takes to test common AR actions by using the preset macros in the emulator. For example, you can use a macro to reset all the device's sensors to their default state.

Before using macros, follow the steps in [Run AR apps in Android Emulator](https://developers.google.com/ar/develop/java/emulator) to set up the virtual scene camera for your app, run your app on the emulator, and update ARCore. Then, follow these steps to use emulator macros:

1.  With the emulator running and your app connected to ARCore, click **More** ![](https://developer.android.com/studio/images/buttons/emulator-extended-controls.png) in the emulator panel.
2.  Select **Record and Playback > Macro Playback**.
3.  Choose a macro that you want to use, then click **Play**.

    During playback, you can interrupt a macro by clicking **Stop**.

## Extended controls, settings, and help

Use the extended controls to send data, change device properties, control apps, and more. To open the **Extended controls** window, click **More** ![Emulator extended controls icon](https://developer.android.com/studio/images/buttons/emulator-extended-controls.png) in the emulator panel.

You can use keyboard shortcuts to perform many of these tasks. For a complete list of shortcuts in the emulator, press F1 (Command+/ on Mac) to open the Help pane.

**Table 3.** Extended controls details

| Feature | Description |
| --- | --- |
| Location |
The emulator lets you simulate "my location" information: the location where the emulated device is currently located. For example, if you click My Location ![My Location icon](https://developer.android.com/images/tools/e-ilocation.png) in Google Maps and then send a location, the map shows it.

Controls for the device's location information are organized under two tabs: **Single points** and **Routes**.

**Single points**

In the **Single points** tab, you can use the Google Maps webview to search for points of interest, just as you would when using Google Maps on a phone or in a browser. When you search for (or click on) a location in the map, you can save the location by selecting **Save point** near the bottom of the map. All of your saved locations are listed on the right side of the **Extended controls** window.

To set the emulators location to the location you have selected on the map, click the **Set location** button near the bottom right of the **Extended controls** window.

**Routes**

Similar to the **Single points** tab, the **Routes** tab provides a Google Maps webview that you can use to create a route between two or more locations. To create and save a route, do the following:

1.  In the map view, use the text field to search for the first destination in your route.
2.  Select the location from the search results.
3.  Select the **Navigate** ![](https://developer.android.com/studio/images/buttons/emulator-navigate_icon.png) button.
4.  Select the starting point of your route from the map.
5.  (Optional) Click **Add destination** to add more stops to your route.
6.  Save your route by clicking **Save route** in the map view.
7.  Specify a name for the route and click **Save**.

To simulate the emulator following the route you saved, select the route from the list of **Saved routes** and click **Play route** near the bottom right of the **Extended controls** window. To stop the simulation, click **Stop route**.

To continuously simulate the emulator following the specified route, enable the switch next to Repeat playback. To change how quickly the emulator follows the specified route, select an option from the Playback speed dropdown.

**Import GPX and KML data**

To use geographic data from a GPS exchange format (GPX) or Keyhole Markup Language (KML) file:

1.  Click **Load GPX/KML**.
2.  In the file dialog, select a file on your computer and click **Open**.
3.  Optionally select a **Speed**.

The speed defaults to the **Delay** value (**Speed 1X**). You can increase the speed by double (**Speed** **2X**), triple ( **Speed 3X**), and so on.

5.  Click **Run** ![Run icon](https://developer.android.com/studio/images/buttons/toolbar-run.png).

 |
| Displays |

The emulator allows you to deploy your app to multiple displays, which support customizable dimensions and can help you test apps that support [multi\- window](https://developer.android.com/guide/topics/ui/foldables#multi-window) and [multi\- display](https://developer.android.com/guide/topics/ui/foldables#multi-display). While a virtual device is running, you can add up to two additional displays as follows:

1.  Add another display by clicking **Add secondary display**.
2.  From the dropdown menu under **Secondary displays**, do one of the following:
    1.  Select one of the preset aspect ratios.
    2.  Select **custom** and set the **height**, **width**, and **dpi** for your custom display.
3.  (Optional) Click **Add secondary display** to add a third display.
4.  Click **Apply changes** to add the specified display(s) to the running virtual device.

 |
| Cellular |

The emulator lets you simulate various network conditions. You can approximate the network speed for different network protocols, or you can specify **Full**, which transfers data as quickly as your computer allows. Specifying a network protocol is always slower than **Full**. You can also specify the voice and data network status, such as roaming. The defaults are set in the AVD.

Select a **Network type**:

*   **GSM**: Global System for Mobile Communications
*   **HSCSD**: High\-Speed Circuit\-Switched Data
*   **GPRS**: Generic Packet Radio Service
*   **EDGE**: Enhanced Data rates for GSM Evolution
*   **UMTS**: Universal Mobile Telecommunications System
*   **HSPDA**: High\-Speed Downlink Packet Access
*   **LTE**: Long\-Term Evolution
*   **Full** (default): Use the network as provided by your computer

Select a **Signal strength**:

*   **None**
*   **Poor**
*   **Moderate** (default)
*   **Good**
*   **Great**

Select a **Voice status**, **Data status**, or both:

*   **Home** (default)
*   **Roaming**
*   **Searching**
*   **Denied (emergency calls only)**
*   **Unregistered (off)**

 |
| Battery |

You can simulate the battery properties of a device to see how your app performs under different conditions. To select a **Charge level**, use the slider control.

Select a **Charger connection** value:

*   **None**
*   **AC charger**

Select a **Battery health** value:

*   **Good** (default)
*   **Failed**
*   **Dead**
*   **Overvoltage**
*   **Overheated**
*   **Unknown**

Select a **Battery status** value:

*   **Unknown**
*   **Charging** (default)
*   **Discharging**
*   **Not charging**
*   **Full**

 |
| Phone |

The emulator lets you simulate incoming phone calls and text messages.

To initiate a call to the emulator:

1.  Select or type a phone number in the **From** field.
2.  Click **Call Device**.
3.  Optionally click **Hold Call** to put the call on hold.
4.  To end the call, click **End Call**.

To send a text message to the emulator:

1.  Select or type a phone number in the **From** field.
2.  Type a message in the **SMS message** field.
3.  Click **Send Message**.

 |
| Directional Pad |

If the AVD has the directional pad enabled in the hardware profile, you can use the directional pad controls with the emulator. However, not all devices can support the directional pad; for example, an Android watch. The buttons simulate the following actions:

![Directional Pad Control](https://developer.android.com/images/tools/e-dpad.png) |
| Fingerprint |

This control can simulate 10 different fingerprint scans. You can use it to test fingerprint integration in your app. This feature is disabled for Android 5.1 (API level 22) and lower, and for Wear OS.

To simulate a fingerprint scan on the virtual device:

1.  Prepare an app to receive a fingerprint.
2.  Select a **Fingerprint** value.
3.  Click **Touch Sensor**.

 |
| Virtual sensors > Accelerometer |

This control lets you test your app against changes in device position, orientation, or both. For example, you can simulate gestures such as tilt and rotation. The accelerometer doesn't track the absolute position of the device: it just detects when a change is occurring. The control simulates the way accelerometer and magnetometer sensors would respond when you move or rotate a real device.

You must enable the accelerometer sensor in your [AVD](https://developer.android.com/studio/run/managing-avds#hpproperties) to use this control.

The control reports [`TYPE_ACCELEROMETER`](https://developer.android.com/reference/android/hardware/Sensor#TYPE_ACCELEROMETER) events on the x, y, and z axis. These values include gravity. For example, if the device is suspended in outer space, it would experience zero acceleration (all of x, y, and z will be 0). When the device is on Earth and laying screen\-up on top of a table, the acceleration is 0, 0, and 9.8 because of gravity.

The control also reports [`TYPE_MAGNETIC_FIELD`](https://developer.android.com/reference/android/hardware/Sensor#TYPE_MAGNETIC_FIELD) events, which measure the ambient magnetic field on the x, y and z axis in microteslas (μT).

To rotate the device around the x, y, and z axes, select **Rotate** and do one of the following:

*   Adjust the **Yaw**, **Pitch**, and **Roll** sliders and observe the position in the upper pane.
*   Move the device representation in the upper pane and observe the **Yaw**, **Pitch**, and **Roll** and how the resulting accelerometer values change.

See [Computing the device's orientation](https://developer.android.com/guide/topics/sensors/sensors_position#sensors-pos-orient) for more information about how yaw, pitch, and roll are calculated.

To move the device horizontally (x) or vertically (y), select **Move** and do one of the following:

*   Adjust the **X** and **Y** sliders and observe the position in the upper pane.
*   Move the device representation in the upper pane and observe the **X** and **Y** slider values and how the resulting accelerometer values change.

To position the device at 0, 90, 180, or 270 degrees:

*   In the **Device rotation** area, select a button to change the [rotation](https://developer.android.com/reference/android/view/Display#getRotation()).

As you adjust the device, the **Resulting values** fields change accordingly. These are the values that an app can access.

For more information about these sensors, see [Sensors overview](https://developer.android.com/guide/topics/sensors/sensors_overview), [Motion sensors](https://developer.android.com/guide/topics/sensors/sensors_motion), and [Position sensors](https://developer.android.com/guide/topics/sensors/sensors_position).

 |
| Virtual sensors > Additional sensors |

The emulator can simulate various position and environment sensors. It lets you adjust the following sensors so you can test them with your app:

*   **Ambient temperature**: This environmental sensor measures ambient air temperature.
*   **Magnetic field**: This position sensor measures the ambient magnetic field on the X, Y, and Z axes, respectively. The values are in microteslas (μT).
*   **Proximity**: This position sensor measures the distance from an object; for example, it can notify a phone that a face is close to it to make a call. The proximity sensor must be enabled in your [AVD](https://developer.android.com/studio/run/managing-avds#hpproperties) to use this control.
*   **Light**: This environmental sensor measures illuminance. The values are in lux units.
*   **Pressure**: This environmental sensor measures ambient air pressure. The values are in millibar (hPa) units.
*   **Relative Humidity**: This environmental sensor measures ambient relative humidity.

For more information about these sensors, see [Sensors overview](https://developer.android.com/guide/topics/sensors/sensors_overview), [Position sensors](https://developer.android.com/guide/topics/sensors/sensors_position), and [Environment sensors](https://developer.android.com/guide/topics/sensors/sensors_environment).

 |
| Snapshots | See [Snapshots](#snapshots). |
| Screen record | See [Screen recording](#screen-recording). |
| Settings > General |

*   **Emulator window theme**: Select Light or Dark.
*   **Send keyboard shortcuts to:** By default, some keyboard combinations will trigger emulator control shortcuts. If you’re developing an app that includes keyboard shortcuts, such as one targeted at devices with Bluetooth keyboards, you can change this setting to send *all* keyboard input to the virtual device, including input that would be a shortcut in the emulator.
*   **Screenshot save location**: Click the folder icon to specify a location to save screenshots of the emulator screen.
*   **Use detected ADB location**: If you're running the emulator from Android Studio, you should select this setting (the default). If you run the emulator from outside Android Studio and want it to use a specific adb executable, deselect this option and specify the SDK Tools location. If this setting is incorrect, features such as screenshot capture and drag\-and\-drop app installation won't work.
*   **When to send crash reports**: Select Always, Never, or Ask.
*   **Show window frame around device**: By default, emulators with device skin files are shown without a surrounding window frame.

 |
| Settings > Proxy |

By default, the emulator uses the Android Studio HTTP proxy settings, but this screen allows you to manually define an HTTP proxy configuration for the emulator. For more information, see [Using the emulator with a proxy](https://developer.android.com/studio/run/emulator-networking#proxy).

 |
| Settings > Advanced |

*   **OpenGL ES renderer**: Select the graphics acceleration type. (This is equivalant to the [`-gpu` command line option](https://developer.android.com/studio/run/emulator-acceleration#command-gpu)).
    *   **Autodetect based on host**: Let the emulator choose hardware or software graphics acceleration based on your computer setup. It checks if your GPU driver matches a list of known faulty GPU drivers, and if it does, the emulator disables graphics hardware emulation and instead uses the CPU.
    *   **ANGLE**: (Windows only.) Use [ANGLE Direct3D](https://chromium.googlesource.com/angle/angle/+/master/README.md) to render graphics in software.
    *   **SwiftShader**: Use [SwiftShader](https://swiftshader.googlesource.com/SwiftShader) to render graphics in software.
    *   **Desktop native OpenGL**: Use the GPU on your host computer. This option is typically the fastest. However, some drivers have issues with rendering OpenGL graphics, so it might not be a reliable option.
*   **OpenGL ES API level**: Select the maximum version of OpenGL ES to use in the emulator.
    *   **Autoselect**: Let the emulator choose the OpenGL ES version based on the host and guest support.
    *   **Renderer maximum (up to OpenGL ES 3.1)**: Attempt to use the maximum version of OpenGL ES.
    *   **Compatibility (OpenGL ES 1.1/2.0)**: Use the version of OpenGL ES that is compatible with most environments.

 |
| Help > Keyboard Shortcuts |

This pane provides a complete list of keyboard shortcuts for the emulator. To open this pane while working in the emulator, press F1 (Command+/ on Mac).

For the shortcuts to work, the **Send keyboard shortcuts** option in the **General** settings pane must be set to **Emulator controls (default)**.

 |
| Help > Emulator Help |

To go to the online documentation for the emulator, click **Documentation**.

To file a bug against the emulator, click **Send feedback**. For more information, see [how to report emulator bugs](https://developer.android.com/studio/report-bugs#emulator-bugs).

 |
| Help > About |

See which adb port the emulator uses, as well as the Android and emulator version numbers. Compare the latest available emulator version with your version to determine if you have the latest software installed.

The emulator serial number is **emulator\-**adb\_port, which you can specify as an adb command line option, for example.

 |

## Wi\-Fi

When using an AVD with API level 25 or higher, the emulator provides a simulated Wi\-Fi access point ("AndroidWifi"), and Android automatically connects to it.

You can disable Wi\-Fi in the emulator by running the emulator with the [command\-line parameter](https://developer.android.com/studio/run/emulator-commandline) `-feature -Wifi`.

## Limitations

The Android Emulator doesn't include virtual hardware for the following:

*   Bluetooth
*   NFC
*   SD card insert/eject
*   Device\-attached headphones
*   USB

The watch emulator for Wear OS doesn't provide the Overview (Recent Apps) button, D\-pad, and fingerprint sensor.