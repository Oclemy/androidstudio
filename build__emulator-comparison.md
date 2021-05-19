# Comparison of Android Emulator tools

The following table compares the tasks you can perform using the [emulator UI](https://developer.android.com/studio/run/emulator), AVD Manager, commonly used [command\-line startup options](https://developer.android.com/studio/run/emulator-commandline#startup-options), and the [Emulator Console](https://developer.android.com/studio/run/emulator-console). In the AVD Manager, you can set [hardware profile](https://developer.android.com/studio/run/managing-avds#hpproperties) and [AVD](https://developer.android.com/studio/run/managing-avds#avdproperties) properties.

The table compares features that are similar, but not necessarily with identical functionality, between different emulator tools.

Although not listed in the table, the adb utility also supports commands for interacting with virtual devices, as described in [Android Debug Bridge](https://developer.android.com/studio/command-line/adb). For example, you can install an app on a virtual device by using [Android Studio](https://developer.android.com/studio/run/emulator#runningapp), the [emulator UI](https://developer.android.com/studio/run/emulator#tasks), or [adb](https://developer.android.com/studio/command-line/adb#move). You can copy a file to a virtual device using the emulator UI or [adb](https://developer.android.com/studio/command-line/adb#copyfiles), and copy a file from a virtual device using adb.

Remember that to use certain features of the emulator with your apps, you need to enable them through various [`<uses-feature>`](https://developer.android.com/guide/topics/manifest/uses-feature-element) elements and [manifest permission](https://developer.android.com/reference/android/Manifest.permission) constants, just as you would if you ran them on a hardware device.

**Table 4.** Comparison of Android Emulator tools

| User interface control | Hardware profile property | AVD property | Command\-line startup option | Console command |
| --- | --- | --- | --- | --- |
| *Device hardware* |
|  | Device type: Phone/Tablet, Wear OS, Android TV |  |  |  |
| Back, Home, Overview, Menu | Input: Has Hardware Buttons (Back/Home/Menu) |  |  |  |
| Settings: Send keyboard shortcuts to | Input: Has Hardware Keyboard | Keyboard Input |  |  |
|
Directional Pad

 | Navigation Style: None, D\-pad, Trackball, Wheel |  |  |  |
| Zoom Mode |  |  |  |  |
| Battery |  |  |  | `power` command |
| Phone |  |  |  | `gsm` and `sms send` commands |
| Rotate | Supported Device States | Startup: Orientation |  | `rotate` command |
|  | Camera | Camera

(can choose webcam)

 | `-camera-back`
`-camera-front`
`-webcam-list` |  |
|

Take Screenshot

Settings: Screenshot save location

 |  |  |  |  |
| Orientation
Location
Virtual sensors | Sensors: Accelerometer, Gyroscope, GPS, Proximity Sensor |  |  | `geo` command |
| Fingerprint |  |  |  | `finger` command |
| Volume |  |  | `-noaudio`

`-no-audio`

 |  |
|  |  |  |  | `event` command |
| *Disk Images and Memory* |
|  |  | Memory and Storage: SD Card | `-sdcard` |  |
|  |  | Memory and Storage: Internal Storage |  |  |
|  | RAM | Memory and Storage: RAM | `-memory` |  |
|  |  | Memory and Storage: VM Heap |  | `vm` command |
|  | In the **Your Virtual Devices** page of the AVD Manager, right\-click an AVD and select [Wipe Data](https://developer.android.com/studio/run/managing-avds#emulator). | `-wipe-data` |  |
| *Network* |
|  |  |  |  | `network status` command |
|  |  |  | `-dns-server` |  |
|  |  |  | `-http-proxy` |  |
|  |  | Network: Latency | `-netdelay` | `network delay` command |
|  |  |  | `-netfast` |  |
| Cellular: Network Type |  | Network: Speed | `-netspeed` | `network speed` command |
| Voice status
Data status |  |  |  | `gsm` command |
| Signal strength |  |  |  |  |
| Settings: Use detected ADB location |  |  | `-port`

`-ports`

 |  |
|  |  |  |  | `redir` command |
|  |  |  | `-tcpdump` | `network capture start` and `network capture stop` commands |
| *System* |
|  | In the [**System Image**](https://developer.android.com/studio/run/managing-avds#systemimagepage) page of the AVD Manager, select a system image capable of [VM acceleration](https://developer.android.com/studio/run/emulator-acceleration#accel-vm). | `-accel`
`-accel-check`
`-no-accel` |  |
|  | Multi\-Core CPU |  |  |  |
|  |  |  | `-engine` |  |
|  | Emulated Performance: Graphics — hardware, software, or auto |  | `-gpu` |  |
|  |  |  | `-nojni`

`-no-jni`

 |  |
|  |  |  | `-selinux {disabled|permissive}` |  |
|  |  |  | `-timezone` |  |
|  |  |  | `-version` |  |
| *UI* |
| Settings: Emulator window theme |  |  |  |  |
| Resize | Screen Size and

Screen Resolution

 |  |  | `window` command |
|  | Round |  |  |  |
|  |  |  | `-no-boot-anim` |  |
|  | Default Skin | Device Frame and

Custom Skin Definition

 |  |  |
|  |  |  | `-screen {touch|multi-touch|no-touch}` |  |
| *Debug* |
|  |  |  | `-debug tags`
`-debug-tag`
`-debug-no-tag` |  |
|  |  |  | `-logcat` |  |
|  |  |  | `-show-kernel` |  |
|  |  |  | `-verbose` |  |

