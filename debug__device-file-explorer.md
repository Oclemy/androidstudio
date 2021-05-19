# View on\-device files with Device File Explorer

The Device File Explorer allows you to view, copy, and delete files on an Android device. This is useful when examining files that are created by your app or if you want to transfer files to and from a device.

**Note:** Most device data is not visible unless you are using a rooted device or an emulator with a standard Android (AOSP) system image (not one of the Google APIs or Google Play system images). And when using a connected device, be sure you [enable USB debugging](https://developer.android.com/studio/debug/dev-options).

To work with a device's file system, proceed as follows:

1.  Click **View > Tool Windows > Device File Explorer** or click the **Device File Explorer**![](https://developer.android.com/studio/images/buttons/toolbar-device-file-explorer.png) button in the tool window bar to open the Device File Explorer.
2.  Select a device from the drop down list.
3.  Interact with the device content in the file explorer window. Right\-click on a file or directory to create a new file or directory, save the selected file or directory to your machine, upload, delete, or synchronize. Double\-click a file to open it in Android Studio.

    Android Studio saves files you open this way in a temporary directory outside of your project. If you make modifications to a file you opened using the Device File Explorer, and would like to save your changes back to the device, you must manually upload the modified version of the file to the device.

![](https://developer.android.com/studio/images/debug/devicefileexplorer-callouts_2x.png)

**Figure 1.** The Device File Explorer tool window

When exploring a device's files, the following directories are particularly useful:

`data/data/app_name/`

Contains data files for your app stored on [internal storage](https://developer.android.com/guide/topics/data/data-storage#filesInternal)

`sdcard/`

Contains user files stored on [external user storage](https://developer.android.com/guide/topics/data/data-storage#filesExternal) (pictures, etc.)

**Note:** Not all files on a hardware device are visible in the Device File Explorer. For example, in the `data/data/` directory, entries corresponding to apps on the device that are not debuggable cannot be expanded in the Device File Explorer.
