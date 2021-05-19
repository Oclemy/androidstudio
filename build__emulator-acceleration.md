# Configure hardware acceleration for the Android Emulator

*   [Table of contents](#top_of_page)
*   [Configure graphics acceleration](#accel-graphics)
    *   [Requirements](#dependencies-gpu)
    *   [Configure graphics acceleration in the AVD Manager](#avd-gpu)
    *   [Configure graphics acceleration from the command line](#command-gpu)
    *   [Enable Skia rendering for Android UI](#skia-emulator)
*   [Configuring VM acceleration](#accel-vm)
    *   [General requirements](#dependencies)
    *   [Restrictions](#vm-accel-restrictions)
    *   [About hypervisors](#hypervisors)
    *   [Check if a hypervisor is installed](#accel-check)
    *   [Configure VM acceleration on Windows](#vm-windows)
    *   [Configure VM acceleration on macOS](#vm-mac)
    *   [Configure VM acceleration on Linux](#vm-linux)

The [Android Emulator](https://developer.android.com/studio/run/emulator) can use hardware acceleration features to improve performance, sometimes drastically. This page describes how you can configure graphics and virtual machine (VM) acceleration to get higher performance from the emulator.

## Configure graphics acceleration

Graphics acceleration uses your computer's hardware (typically the GPU) to make screen rendering faster. Android devices use OpenGL for Embedded Systems ( [OpenGL ES](https://developer.android.com/guide/topics/graphics/opengl) or GLES) for rendering both 2D and 3D graphics on the screen.

When you create an Android Virtual Device (AVD) in the AVD Manager, you can specify whether the emulator should use hardware or software to emulate the GPU of the AVD. Hardware acceleration is recommended and is typically faster. However, you might need to use software acceleration if your computer uses graphics drivers that aren't compatible with the emulator.

By default, the emulator decides whether to use hardware or software graphics acceleration based on your computer setup. If your GPU hardware and drivers are compatible, the emulator uses the GPU. Otherwise, the emulator uses software acceleration (using your computer's CPU) to simulate GPU processing.

If you start the emulator from the [command line](https://developer.android.com/studio/run/emulator-commandline#starting), you can also override the graphics acceleration setting in the AVD for that virtual device instance.

### Requirements

To use graphics acceleration, your development environment must meet the following requirements:

*   SDK Tools: Latest release recommended (version 17 minimum)
*   SDK Platform: Latest release recommended (Android 4.0.3, Revision 3, minimum)

### Configure graphics acceleration in the AVD Manager

To configure graphics acceleration for an AVD, follow these steps:

1.  Open the [AVD Manager](https://developer.android.com/studio/run/managing-avds#viewing).
2.  [Create a new AVD](https://developer.android.com/studio/run/managing-avds#createavd) or [edit an existing AVD](https://developer.android.com/studio/run/managing-avds#workingavd).
3.  On the [**Verify Configuration**](https://developer.android.com/studio/run/managing-avds#verifyconfigpage) page, find the **Emulated Performance** section.
4.  Select a value for the **Graphics:** option.
5.  Click **Finish**.

### Configure graphics acceleration from the command line

To specify a graphics acceleration type when you run an AVD from the command line, include the `-gpu` option, as shown in the following example:

emulator \-avd avd\_name \-gpu mode \[{\-option \[value\]} ... \]

The value of `mode` can be set to one of the following options:

*   `auto`: Let the emulator choose between hardware or software graphics acceleration based on your computer setup.
*   `host`: Use the GPU on your computer for hardware acceleration. This option typically provides the highest graphics quality and performance for the emulator. However, if your graphics drivers have issues rendering OpenGL, you might need to use the `swiftshader_indirect` or `angle_indirect` options.
*   `swiftshader_indirect`: Use a Quick Boot\-compatible variant of [SwiftShader](https://swiftshader.googlesource.com/SwiftShader) to render graphics using software acceleration. This option is a good alternative to `host` mode if your computer can't use hardware acceleration.
*   `angle_indirect`: (Windows only) Use a Quick Boot\-compatible variant of [ANGLE Direct3D](https://chromium.googlesource.com/angle/angle/+/master/README.md) to render graphics using software acceleration. This option is a good alternative to `host` mode if your computer can't use hardware acceleration. In most cases, ANGLE performance should be similar to using `host` mode because ANGLE uses Microsoft DirectX instead of OpenGL. On Windows, Microsoft DirectX drivers typically have fewer issues than OpenGL drivers. This option uses Direct3D 11 and requires Windows 10, Windows 8.1, or Windows 7 SP1 with the Windows 7 Platform Update.
*   `guest`: Use guest\-side software rendering. This option provides the lowest graphics quality and performance for the emulator.

**Note:** If you enable a graphics acceleration option that your computer doesn't support, you are likely to see incorrect images displayed during emulation.

The following `mode` options are deprecated:

*   `swiftshader`: Deprecated in version 27.0.2 Use `swiftshader_indirect` instead.
*   `angle`: Deprecated in version 27.0.2. Use `angle_indirect` instead (Windows only).
*   `mesa`: Deprecated in version 25.3. Use `swiftshader_indirect` instead.

### Enable Skia rendering for Android UI

When using images for API level 27 or higher, the emulator can render the Android UI with [Skia](https://skia.org/). Skia helps the emulator render graphics more smoothly and efficiently.

To enable Skia rendering, use the following commands in adb shell:

```
su
setprop debug.hwui.renderer skiagl
stop
start

```

## Configuring VM acceleration

VM acceleration uses your computer's processor to significantly improve the execution speed of the emulator. A tool called a hypervisor manages this interaction using virtualization extensions that your computer's processor provides. This section outlines the requirements for using VM acceleration and describes how to set up VM acceleration on each operating system.

### General requirements

To use VM acceleration with the emulator, your computer must meet the general requirements in this section. Your computer also needs to meet other requirements that are specific to your operating system.

#### Development environment requirements

To use VM acceleration, your development environment must meet the following requirements:

*   SDK Tools: minimum version 17; recommended version 26.1.1 or later
*   AVD with an x86\-based system image, available for Android 2.3.3 (API level 10) and higher

**Warning:** AVDs that use ARM\- or MIPS\-based system images can't use the VM acceleration that is described on this page.

#### Virtualization extension requirements

In addition to the development environment requirements, your computer's processor must support one of the following virtualization extensions technologies:

*   Intel Virtualization Technology (VT, VT\-x, vmx) extensions
*   AMD Virtualization (AMD\-V, SVM) extensions

Most modern processors support these virtualization extensions. If you're not sure whether your processor supports these extensions, check the specifications for your processor on the manufacturer's site. If your processor doesn't support one of these extensions, then you can't use VM acceleration.

**Note:** Virtualization extensions are typically enabled through your computer BIOS and are frequently turned off by default. Check the documentation for your motherboard to find out how to enable virtualization extensions.

### Restrictions

VM acceleration has the following restrictions:

*   You can't run a VM\-accelerated emulator inside another VM, such as a VM hosted by VirtualBox, VMWare, or Docker. You must run a VM\-accelerated emulator directly on your host computer.
*   Depending on your operating system and hypervisor, you can't run software that uses another virtualization technology at the same time that you run a VM\-accelerated emulator. For example, VirtualBox, VMWare, and Docker currently use a different virtualization technology, so you can't run them at the same time as the accelerated emulator.

### About hypervisors

VM acceleration requires a *hypervisor*.

Without a hypervisor and VM acceleration, the emulator must translate the machine code from the VM block by block to conform to the architecture of the host computer. This process can be quite slow. With a hypervisor, the VM and the architecture of the host computer match, so the emulator can run code directly on the host processor using the hypervisor. This improvement drastically increases both the speed and performance of the emulator.

The hypervisor that will work best for you depends on your computer's operating system and configuration. For more information, see one of the following sections:

*   [Configure VM acceleration on Windows](#vm-windows)
*   [Configure VM acceleration on macOS](#vm-mac)
*   [Configure VM acceleration on Linux](#vm-linux)

### Check if a hypervisor is installed

You can use the emulator `-accel-check` command\-line option to check if a hypervisor is currently installed on your computer.

The following examples show how to use use the emulator `accel-check` option. In each example, `sdk` is the location of the Android SDK:

Windows:

c:\\Users\\janedoe\\AppData\\Local\\Android> sdk\\emulator\\emulator \-accel\-check
accel:
0
HAXM version 7.3.2 (4) is installed and usable.
accel

macOS:

janedoe:~/Android$ ./sdk/emulator/emulator \-accel\-check
accel:
0
HAXM version 7.3.2 (4) is installed and usable.
accel

Linux:

janedoe\-macbookpro:Android janedoe$ ./sdk/emulator/emulator \-accel\-check
accel:
0
KVM (version 12) is installed and usable.

### Configure VM acceleration on Windows

VM acceleration on Windows can use one of three hypervisors: the Intel Hardware Accelerated Execution Manager (HAXM), the Android Emulator Hypervisor Driver for AMD Processors or the Windows Hypervisor Platform (WHPX).

#### Choose a hypervisor on Windows

Use the following criteria to determine which hypervisor you should use:

| Criteria | Hypervisor |
| --- | --- |
| You have an Intel processor and **do not need** to run Hyper\-V at the same time as the Android Emulator. | Use Intel [HAXM](#vm-windows-haxm-intel). |
| You have an Intel processor and **do need** to run Hyper\-V at the same time as the Android Emulator. | Use [WHPX](#vm-windows-whpx). |
| You have an AMD processor and **do not need** to run Hyper\-V at the same time as the Android Emulator. | Use [Android Emulator Hypervisor Driver for AMD Processors](#vm-windows-aehdamd). |
| You have an AMD processor and **do need** to run Hyper\-V at the same time as the Android Emulator. | Use [WHPX](#vm-windows-whpx). |

#### Double\-check when disabling Hyper\-V

Hyper\-V must be disabled to use Intel HAXM or the Android Emulator Hypervisor Driver for AMD Processors. However, unchecking "Hyper\-V" in Windows Features Dialog might not guarantee that Hyper\-V is disabled. Quite a few features in Windows 10 will enable Hyper\-V implicitly. Users may not even know Hyper\-V is activated when they enable one of these features.

To our best knowledge, these includes **Virtual Machine Platform**, **Windows Hypervisor Platform**, **Windows Sandbox**, **Core Isolation**, **Credential Guard**. Additionally, Windows Subsystem for Linux version 2 requires **Virtual Machine Platform**, meaning that it implicitly requires Hyper\-V. This list is not exhaustive, please notify us on our bug tracker if you find an item that should be included here.

Double\-check that the features listed above are also disabled when disabling Hyper\-V. Reference Microsoft documentation and the examples below to find out how each individual feature can be disabled.

![](https://developer.android.com/studio/images/run/windows-features.png)

![](https://developer.android.com/studio/images/run/core-isolation.png)

Some platforms fail to immediately disable Hyper\-V when it is switched off. When this occurs, there is no error returned by the Windows OS and Hyper\-V appears disabled in the Windows Features Dialog. Please file a bug on Microsoft's issue tracker when this occurs.

#### Configure VM acceleration using Intel HAXM on Windows

Before you can install and use Intel HAXM, your computer must meet the following requirements:

*   Intel processor with Virtualization Technology (VT\-x), Intel EM64T (Intel 64) features, and Execute Disable (XD) Bit functionality enabled
*   64\-bit Windows 10, Windows 8, or Windows 7 (or 32\-bit version of the operating system on a 64\-bit processor)
*   To use Intel HAXM on Windows 10 or 8, you must turn off Hyper\-V in the Windows Features Dialog.
    **Note:** Installing certain software might turn Hyper\-V back on. Refer to [Disabling Hyper\-V](#disable-hyper-v) for more details.

To install the Intel HAXM driver, follow these steps:

1.  Open the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager).
2.  Click the **SDK Update Sites** tab and then select **Intel HAXM**.
3.  Click **OK**.
4.  After the download finishes, run the installer. Typically, you can find the installer in the following location: `sdk\extras\intel\Hardware_Accelerated_Execution_Manager\intelhaxm-android.exe`
5.  Use the wizard to complete the installation.
6.  After installing Intel HAXM, confirm that the virtualization driver is operating correctly by entering the following command in a Command Prompt window:

    ```
    sc query intelhaxm

    ```

    You should see a status message that includes the following information:

    ```
    SERVICE_NAME: intelhaxm
           ...
           STATE              : 4  RUNNING
           ...

    ```

For more information, see [Installation Instructions for Intel HAXM on Windows](https://github.com/intel/haxm/wiki/Installation-Instructions-on-Windows).

You can adjust the amount of memory available to the Intel HAXM kernel extension by running the installer again.

You can uninstall Intel HAXM using either the installer or the Windows Control Panel. Before you uninstall Intel HAXM, shut down any x86 emulators that are currently running.

#### Configure VM acceleration using Android Emulator Hypervisor Driver for AMD Processors on Windows

Before you can install and use the Android Emulator Hypervisor Driver for AMD Processors, your computer must meet the following requirements:

*   AMD processor with Secure Virtual Machine (SVM) Technology
*   64\-bit Windows 10, Windows 8, or Windows 7 (32\-bit Windows is not supported)
*   To use Android Emulator Hypervisor Driver for AMD Processors on Windows 10 or 8, you must turn off Hyper\-V in the Windows Feaures Dialog.
    **Note:** Installing certain software might turn Hyper\-V back on. Refer to [Disabling Hyper\-V](#disable-hyper-v) for more details.

The Android Emulator Hypervisor Driver for AMD Processors can be installed via the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager) in Android Studio 4.0 Canary 5 or later, or from Github (see below). To install from the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager), follow these steps:

1.  Open **Tools\->**[SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager).
2.  Click the **SDK Tools** tab and then select **Android Emulator Hypervisor Driver for AMD Processors**.
3.  Click **OK** to download and install the Android Emulator Hypervisor Driver for AMD processors.
4.  After installation, confirm that the driver is operating correctly by entering the following command in a Command Prompt window:

    ```
    sc query gvm

    ```

    You should see a status message that includes the following information:

    ```
    SERVICE_NAME: gvm
           ...
           STATE              : 4  RUNNING
           ...

    ```

    The following error message means either that SVM is not enabled in your BIOS or that Hyper\-V is not disabled (Refer to [Disable Hyper\-V](#disable-hyper-v)).

    ```
    SERVICE_NAME: gvm
           ...
           STATE              : 1  STOPPED
           WIN32_EXIT_CODE    : 4294967201 (0xffffffa1)
           ...

    ```

The Android Emulator Hypervisor Driver for AMD Processors can also be downloaded and installed from [github](https://github.com/google/android-emulator-hypervisor-driver-for-amd-processors/releases). After unpacking the driver package, run "silent\_install.bat" in a Command Prompt with Administrator privileges. Successfully completing a new installation would have the following output:

![](https://developer.android.com/studio/images/run/aehd-install-new.png)

Upgrading an installation would result in the following output, which includes messages regarding the old driver's uninstallation:

![](https://developer.android.com/studio/images/run/aehd-install-update.png)

You can uninstall Android Emulator Hypervisor Driver for AMD Processors using the following commands in a Command Prompt with Administrator privileges.

 ```
   sc stop gvm
   sc delete gvm

```

**Note**: Shut down any x86 emulators before uninstalling the Android Emulator Hypervisor Driver for AMD Processors.

#### Configure VM acceleration using Windows Hypervisor Platform

Before you can enable WHPX, your computer must meet the following requirements:

*   Intel processors: Support for Virtualization Technology (VT\-x), Extended Page Tables (EPT), and Unrestricted Guest (UG) features. VT\-x must be enabled in your computer's BIOS settings.
*   AMD processors: AMD Ryzen processor recommended. Virtualization or SVM must be enabled in your computer's BIOS settings.
*   Android Studio 3.2 Beta 1 or higher (download from [developer.android.com](https://developer.android.com/studio/preview))
*   Android Emulator version 27.3.8 or higher (download using the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager))
*   Windows 10 with April 2018 Update or higher

To install WHPX on Windows, follow these steps:

1.  From the Windows desktop, right\-click the Windows icon and select **Apps and features**.
2.  Under **Related settings**, click **Programs and Features**.
3.  Click **Turns Windows Features on or off**.
4.  Select **Windows Hypervisor Platform**

    ![](https://developer.android.com/studio/images/run/whpx_windows10_1803.png)

5.  Click **OK**.

6.  Once the installation has finished, restart your computer.

### Configure VM acceleration on macOS

On Mac OS X v10.10 Yosemite and higher, the Android Emulator uses the built\-in [Hypervisor.Framework](https://developer.apple.com/documentation/hypervisor) by default, and falls back to using Intel HAXM if Hypervisor.Framework fails to initialize.

To use VM acceleration on macOS when the Hypervisor.Framework is not available, you must install the Intel HAXM kernel extension.

**Note:** For users of macOS 10.13 High Sierra and higher: macOS 10.13 disables installation of kernel extensions by default. Because Intel HAXM is a kernel extension, you might need to manually enable its installation. For more details, see [Known Issues](https://developer.android.com/studio/known-issues).

To install the Intel HAXM kernel extension, follow these steps:

1.  Open the [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager).
2.  Click the **SDK Update Sites** tab and then select **Intel HAXM**.
3.  Click **OK**.
4.  After the download finishes, run the installer. Typically, you can find the installer in the following location: `sdk/extras/intel/Hardware_Accelerated_Execution*Manager/IntelHAXM*version.dmg`
5.  Follow the on\-screen instructions to complete the installation.
6.  After installation finishes, confirm that the new kernel extension is operating correctly by opening a terminal window and running the following command:

    kextstat | grep intel

    You should see a status message containing the following extension name, indicating that the kernel extension is loaded:

    ```
    com.intel.kext.intelhaxm

    ```

For more information, see [Installation Instructions for Intel HAXM](https://github.com/intel/haxm/wiki/Installation-Instructions-on-macOS).

You can adjust the amount of memory available to the Intel HAXM kernel extension by running the installer again.

You can stop using the Intel HAXM kernel extension by uninstalling it. Before you uninstall it, shut down any x86 emulators that are currently running, then run the following command in a terminal window:

sudo /System/Library/Extensions/intelhaxm.kext/Contents/Resources/uninstall.sh

### Configure VM acceleration on Linux

Linux\-based systems support VM acceleration through the [KVM software package.](https://www.linux-kvm.org/page/Main_Page) Follow the instructions for installing KVM on your Linux system, and verify that KVM is enabled. For Ubuntu systems, see [Ubuntu KVM Installation](https://help.ubuntu.com/community/KVM/Installation).

#### Requirements

Running KVM requires specific user permissions. Make sure that you have sufficient permissions as specified in the KVM installation instructions.

To use VM acceleration on Linux, your computer must also meet these requirements:

*   For Intel processors: Support for Virtualization Technology (VT\-x), Intel EM64T (Intel 64) features, and Execute Disable (XD) Bit functionality enabled.
*   For AMD processors: Support for AMD Virtualization (AMD\-V).

#### Check whether KVM is currently installed on Linux

You can use the emulator [`-accel-check`](#accel-check) command\-line option to check whether you have KVM installed. Alternatively, you can install the `cpu-checker` package containing the `kvm-ok` command.

The following example shows how to use the `kvm-ok` command:

```
$ sudo apt-get install cpu-checker
$ egrep -c '(vmx|svm)' /proc/cpuinfo
12
$ kvm-ok
INFO: /dev/kvm exists
KVM acceleration can be used

```

#### Install KVM on Linux

Use the following command to install KVM:

```shell
sudo apt\-get install qemu\-kvm libvirt\-bin ubuntu\-vm\-builder bridge\-utils ia32\-libs\-multiarch
```
