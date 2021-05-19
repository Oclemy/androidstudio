# Take a screenshot

On many Android devices, you can capture a screenshot with a key\-combination: Simultaneously press\-and\-hold Power and Volume\-down. You can also capture a screenshot with Android Studio as follows:

1.  Run your app on a connected device or emulator. If using a connected device, be sure you have [enabled USB debugging](https://developer.android.com/studio/run/device#setting-up).
2.  In Android Studio, select **View > Tool Windows > Logcat** to open [Logcat](https://developer.android.com/studio/debug/am-logcat).
3.  Select the device and a process from the drop\-down at the top of the window.
4.  Click **Screen Capture** ![](https://developer.android.com/studio/images/buttons/monitor-screenshot.png) on the left side of the window.

    The screenshot appears in a **Screenshot Editor** window.

    **Tip:** On Android 7.0 and higher, open Settings, select **Developer options > Demo mode**, and enable **Show demo mode**. This resets the status bar to temporarily remove notifications and sets the signal and battery levels to full. For more information, see [Configure On\-Device Developer Options](https://developer.android.com/studio/debug/dev-options).

    ![](https://developer.android.com/studio/images/debug/screenshot-editor_2x.png)

    **Figure 1.** Screenshot editor

5.  Optionally change the image:
    *   **Recapture**: Take a new screenshot.
    *   **Rotate Left**: Rotate the image 90 degrees counter\-clockwise.
    *   **Rotate Right**: Rotate the image 90 degrees clockwise.
    *   **Frame Screenshot**: Choose a device to wrap your screenshot with real device artwork. Select **Drop Shadow**, **Screen Glare**, or both to add these effects to your image.

        **Note:** If you select a device for the screenshot frame that differs from the actual device you captured, the editor stretches your image to match the dimensions of the device frame. You instead might want to use the online [Device Art Generator](https://developer.android.com/distribute/tools/promote/device-art), which offers device frames for other popular devices.

6.  Click **Save**, specify the location and filename, and then click **OK**.
