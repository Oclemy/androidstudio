# Install OEM USB drivers

- [Install OEM USB drivers](#install-oem-usb-drivers)
  - [Install a USB driver](#install-a-usb-driver)
    - [Windows 10](#windows-10)
    - [Windows 8.1](#windows-81)
    - [Windows 7](#windows-7)
  - [Get OEM drivers](#get-oem-drivers)

If you are developing on Windows and want to connect a device for testing, then you need to install the appropriate USB driver. This page provides links to the web sites for several original equipment manufacturers (OEMs), where you can download the appropriate USB driver for your device.

If you're developing on Mac OS X or Linux, then you shouldn't need a USB driver. Instead read [Using Hardware Devices](https://developer.android.com/tools/device).

To connect and debug with any of the Google Nexus devices using Windows, you need to [install the Google USB driver](https://developer.android.com/studio/run/win-usb).

## Install a USB driver

First, find the appropriate driver for your device from the [OEM drivers](#Drivers) table below.

Once you've downloaded your USB driver, follow the instructions below to install or upgrade the driver, based on your version of Windows and whether you're installing for the first time or upgrading an existing driver. Then see [Using Hardware Devices](https://developer.android.com/tools/device) for other important information about using an Android device for development.

**Caution:** You may make changes to `android_winusb.inf` file found inside `usb_driver\` (for example, to add support for other devices), however, this will lead to security warnings when you install or upgrade the driver. Making any other changes to the driver files may break the installation process.

### Windows 10

To install the Android USB driver on Windows 10 for the first time, do the following:

1.  Connect your Android device to your computer's USB port.
2.  From Windows Explorer, open **Computer Management**.
3.  In the **Computer Management** left pane, select **Device Manager**.
4.  In the **Device Manager** right pane, locate and expand **Portable Devices** or **Other Devices**, depending on which one you see.
5.  Right\-click the name of the device you connected, and then select **Update Driver Software**.
6.  In the **Hardware Update wizard**, select **Browse my computer for driver software** and click **Next**.
7.  Click **Browse** and then locate the USB driver folder. For example, the Google USB Driver is located in `android_sdk\extras\google\usb_driver\`.
8.  Click **Next** to install the driver.

### Windows 8.1

To install the Android USB driver on Windows 8.1 for the first time, do the following:

1.  Connect your Android device to your computer's USB port.
2.  Access search, as follows:

    Touch screen: On your computer, swipe in from the right edge of the screen and tap **Search**.

    Using a mouse: Point to the lower\-right corner of the screen, move the mouse pointer up, and then click **Search**.

3.  In the search box, type into and then click **Device Manager**.
4.  Double\-click the device category, and then double\-click the device you want.
5.  Click the **Driver** tab, click **Update Driver**, and follow the instructions.

### Windows 7

To install the Android USB driver on Windows 7 for the first time, do the following:

1.  Connect your Android device to your computer's USB port.
2.  Right\-click on **Computer** from your desktop or Windows Explorer, and select **Manage**.
3.  Select **Devices** in the left pane.
4.  Locate and expand **Other device** in the right pane.
5.  Right\-click the device name (such as **Nexus S**) and select **Update Driver Software**. This will launch the Hardware Update Wizard.
6.  Select **Browse my computer for driver software** and click **Next**.
7.  Click **Browse** and locate the USB driver folder. (The Google USB Driver is located in `android_sdk\extras\google\usb_driver\`.)
8.  Click **Next** to install the driver.

Or, to *upgrade* an existing Android USB driver on Windows 7 and higher with the new driver:

1.  Connect your Android device to your computer's USB port.
2.  Right\-click on **Computer** from your desktop or Windows Explorer, and select **Manage**.
3.  Select **Device Manager** in the left pane of the Computer Management window.
4.  Locate and expand **Android Phone** in the right pane.
5.  Right\-click on **Android Composite ADB Interface** and select **Update Driver**. This will launch the Hardware Update Wizard.
6.  Select **Install from a list or specific location** and click **Next**.
7.  Select **Search for the best driver in these locations**; uncheck **Search removable media**; and check **Include this location in the search**.
8.  Click **Browse** and locate the USB driver folder. (The Google USB Driver is located in `android_sdk\extras\google\usb_driver\`.)
9.  Click **Next** to upgrade the driver.

## Get OEM drivers

| OEM | Driver URL |
| --- | --- |
| Acer | [http://www.acer.com/worldwide/support/](http://www.acer.com/worldwide/support/) |
| alcatel one touch | [http://www.alcatelonetouch.com/global\-en/support/](http://www.alcatelonetouch.com/global-en/support/) |
| Asus | [https://www.asus.com/support/Download\-Center/](https://www.asus.com/support/Download-Center/) |
| Blackberry | [https://swdownloads.blackberry.com/Downloads/entry.do?code=4EE0932F46276313B51570F46266A608](https://swdownloads.blackberry.com/Downloads/entry.do?code=4EE0932F46276313B51570F46266A608) |
| Dell | [http://support.dell.com/support/downloads/index.aspx?c=us&cs=19&l=en&s=dhs&~ck=anavml](http://support.dell.com/support/downloads/index.aspx?c=us&cs=19&l=en&s=dhs&~ck=anavml) |
| Fujitsu | [http://www.fmworld.net/product/phone/sp/android/develop/](http://www.fmworld.net/product/phone/sp/android/develop/) |
| HTC | [http://www.htc.com/support](http://www.htc.com/support) |
| Huawei | [http://consumer.huawei.com/en/support/index.htm](http://consumer.huawei.com/en/support/index.htm) |
| Intel | [http://www.intel.com/software/android](http://www.intel.com/software/android) |
| Kyocera | [http://www.kyocera\-wireless.com/support/phone\_drivers.htm](http://www.kyocera-wireless.com/support/phone_drivers.htm) |
| Lenovo | [http://support.lenovo.com/us/en/GlobalProductSelector](http://support.lenovo.com/us/en/GlobalProductSelector) |
| LGE | [http://www.lg.com/us/support/software\-firmware](http://www.lg.com/us/support/software-firmware) |
| Motorola | [https://motorola\-global\-portal.custhelp.com/app/answers/detail/a\_id/88481/](https://motorola-global-portal.custhelp.com/app/answers/detail/a_id/88481) |
| MTK | [http://online.mediatek.com/Public%20Documents/MTK\_Android\_USB\_Driver.zip](http://online.mediatek.com/Public%20Documents/MTK_Android_USB_Driver.zip) (ZIP download) |
| Samsung | [http://developer.samsung.com/galaxy/others/android\-usb\-driver\-for\-windows](http://developer.samsung.com/galaxy/others/android-usb-driver-for-windows) |
| Sharp | [http://k\-tai.sharp.co.jp/support/](http://k-tai.sharp.co.jp/support/) |
| Sony Mobile Communications | [http://developer.sonymobile.com/downloads/drivers/](http://developer.sonymobile.com/downloads/drivers/) |
| Toshiba | [http://support.toshiba.com/sscontent?docId=4001814](http://support.toshiba.com/sscontent?docId=4001814) |
| Xiaomi | [http://www.xiaomi.com/c/driver/index.html](http://www.xiaomi.com/c/driver/index.html) |
| ZTE | [http://support.zte.com.cn/support/news/NewsDetail.aspx?newsId=1000442](http://support.zte.com.cn/support/news/NewsDetail.aspx?newsId=1000442) |

If you don't see a link for the manufacturer of your device here, go to the support section of the manufacturer's website and search for USB driver downloads for your device.
