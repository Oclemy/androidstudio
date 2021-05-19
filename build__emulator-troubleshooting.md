# Troubleshooting and known issues for Android Emulator and system images

- [Troubleshooting and known issues for Android Emulator and system images](#troubleshooting-and-known-issues-for-android-emulator-and-system-images)
  - [Checking for adequate disk space](#checking-for-adequate-disk-space)
  - [Antivirus software](#antivirus-software)
  - [HAXM on older, unsupported versions of Mac OS](#haxm-on-older-unsupported-versions-of-mac-os)
  - [Android Emulator runs slowly after an update](#android-emulator-runs-slowly-after-an-update)
  - [Windows: Free RAM and commit charge](#windows-free-ram-and-commit-charge)

This page lists known issues, workarounds, and troubleshooting tips for the Android Emulator.

If you encounter an issue not listed here or are unable to successfully use a workaround listed here, please [report a bug](https://developer.android.com/studio/report-bugs#emulator-bugs).

## Checking for adequate disk space

To avoid crashes and hangs caused by not having enough free disk space, the emulator checks for sufficient free disk space on startup, and will not start unless at least 2 GB is free. If the emulator fails to start for you, check to see that you have adequate free disk space.

## Antivirus software

Because many security and antivirus software packages work by monitoring every read and write operation, use of such software can decrease performance of tools like the Android Emulator.

Many antivirus packages provide the ability to add specific applications to a list of trusted applications, which enables these applications to operate without performance degradation. If you are experiencing poor performance with saving or loading of AVD snapshots, you may improve this performance by adding the Android Emulator application as a trusted application in your antivirus software.

The performance impact differs between various antivirus software packages. If you have additional antivirus software installed beyond that which is included with your operating system, you can run simple tests to determine which antivirus software has a greater performance effect on emulator load and save operations.

Some antivirus software may be incompatible with the Android Emulator.

If you're using Avast software and are having trouble running the Android Emulator, try disabling **Use nested virtualization when available** and **Enable Hardware assisted virtualization** in the Avast **Troubleshooting** settings. In addition, after Avast hardware virtualization is disabled, ensure that HAXM is set up properly again with a full re\-installation of the latest HAXM from the SDK Manager.

## HAXM on older, unsupported versions of Mac OS

If you are using Android Emulator on older, unsupported versions of Mac OS X (such as 10.9), then you may need to use an older version of HAXM (6.1.2), too.

## Android Emulator runs slowly after an update

A number of external factors can cause the Android Emulator to begin running slowly after an update. To begin troubleshooting, we recommend the following steps:

*   If you are running Android Emulator on Windows, check to see if you have installed Windows Updates [KB4013429](https://support.microsoft.com/en-us/help/4013429/windows-10-update-kb4013429) and [KB4015217](https://support.microsoft.com/en-us/help/4015217/windows-10-update-kb4015217). Some users reported improved Android Emulator performance after uninstalling these updates. Users also reported improved Emulator performance after *installing* Windows Update [KB4015438](https://support.microsoft.com/en-us/help/4015438/windows-10-update-kb4015438).
*   If you have an Intel GPU (and in particular, the Intel HD 4000), ensure you have downloaded and installed the latest Intel graphics driver.
*   If your machine has both an Intel HD 4000 GPU and a discrete GPU, disable the Intel HD 4000 GPU in Device Manager to ensure you are using the discrete GPU.
*   Try running the emulator using the `-gpu angle`, `-gpu swiftshader`, or `-gpu guest` modes. For more information about configuring graphics acceleration options on the command line, see [Configure hardware acceleration](https://developer.android.com/studio/run/emulator-acceleration#command-gpu).
*   Ensure that your router is not using IPv6 addresses if you do not have an IPv6 connection.

If you are still experiencing problems with the Android Emulator running slowly, [Report a bug](https://developer.android.com/studio/report-bugs#emulator-bugs), including the necessary Android Emulator Details so we can investigate.

## Windows: Free RAM and commit charge

When the emulator starts, it needs to initialize the Android guest operating system's RAM. On Windows, the emulator asks Windows to account for the full size of guest memory at start time, even though during actual operation, the memory may be paged in on demand. The emulator requests the full amount of guest memory at start time because Windows is conservative in ensuring that there is enough physical RAM and pagefile available to hold the entire potential working set; this prepares for the worst case, in which all guest memory is touched quickly, without any opportunity to discard or otherwise free memory.

Sometimes, when the emulator asks Windows to account for this full guest memory size, the request exceeds the current *commit limit*, which is the total of the available physical RAM and pagefile. In this case, Windows can't guarantee that the worst\-case working set will fit in either physical RAM or pagefile, and the emulator fails to start.

In typical cases, the amount of hard drive space allocated for the pagefile plus physical RAM is more than enough for most use cases of the emulator. However, if you experience failures to start the emulator because of exceeding the commit limit, we recommend examining the current commit charge, which can be seen in the **Performance** tab in the Windows Task Manager. (To open the Task Manager, press Ctrl+Shift+Esc.)

You can lower the likelihood of exceeding the commit limit in various ways:

*   Free physical RAM before launching the emulator by closing unused applications and files.
*   Disable third\-party memory management and memory compression utilities. These utilities can inefficiently cause an excess commit charge and bring your system closer to the commit limit.
*   Use a *system managed size* for the Windows pagefile, which can more flexibly and dynamically increase the pagefile size (and therefore the commit limit) in response to increased demand from the emulator and other applications.

    For more information on commit charges and why a flexible setting works best, see [this Microsoft article](https://blogs.technet.microsoft.com/markrussinovich/2008/11/17/pushing-the-limits-of-windows-virtual-memory/).
