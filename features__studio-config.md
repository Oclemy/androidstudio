# Configure Android Studio

- [Configure Android Studio](#configure-android-studio)
  - [Find your configuration files](#find-your-configuration-files)
      - [Windows](#windows)
      - [macOS](#macos)
      - [Linux](#linux)
  - [Customize your VM options](#customize-your-vm-options)
    - [Maximum heap size](#maximum-heap-size)
  - [Export and import IDE settings](#export-and-import-ide-settings)
  - [Customize your IDE properties](#customize-your-ide-properties)
  - [Configure the IDE for low\-memory machines](#configure-the-ide-for-low-memory-machines)
  - [Set the JDK version](#set-the-jdk-version)
  - [Set proxy settings](#set-proxy-settings)
    - [Set up the Android Studio proxy](#set-up-the-android-studio-proxy)
    - [Android plugin for Gradle HTTP proxy settings](#android-plugin-for-gradle-http-proxy-settings)
  - [Optimize Android Studio performance on Windows](#optimize-android-studio-performance-on-windows)
    - [Minimize the impact of antivirus software on build speed](#minimize-the-impact-of-antivirus-software-on-build-speed)
      - [Customize directory locations for Group Policy controlled environments](#customize-directory-locations-for-group-policy-controlled-environments)

Android Studio provides wizards and templates that verify your system requirements, such as the Java Development Kit (JDK) and available RAM, and configure default settings, such as an optimized default Android Virtual Device (AVD) emulation and updated system images. This document describes additional configuration settings you may want to use to customize your use of Android Studio.

Android Studio provides access to two configuration files through the **Help** menu:

*   [`studio.vmoptions`](#customize_vm): Customize options for Studio's Java Virtual Machine (JVM), such as heap size and cache size. Note that on Linux machines this file may be named `studio64.vmoptions`, depending on your version of Android Studio.
*   [`idea.properties`](#customize_ide): Customize Android Studio properties, such as the plugins folder path or maximum supported file size.

For specific documentation about emulator and device setup and use, see the following topics:

*   [Managing virtual devices](https://developer.android.com/tools/devices)
*   [Using hardware devices](https://developer.android.com/tools/device)
*   [OEM USB drivers](https://developer.android.com/tools/extras/oem-usb)

## Find your configuration files

Both configuration files are stored in the configuration folder for Android Studio. The name of the folder depends on your Studio version. Here are the locations for Android Studio 4.1 and higher:

#### Windows

Syntax: `%APPDATA%\Google\<product><version>`

Example: `C:\Users\YourUserName\AppData\Roaming\Google\AndroidStudio4.1`

#### macOS

Syntax: `~/Library/Application Support/Google/<product><version>`

Example: `~/Library/Application Support/Google/AndroidStudio4.1`

#### Linux

Syntax: `~/.config/Google/<product><version>`

Example: `~/.config/Google/AndroidStudio4.1`

For Android Studio versions 4.0 and lower, configuration files are located in the following locations:

*   Windows: `%USERPROFILE%\.CONFIGURATION_FOLDER`
*   macOS: `~/Library/Preferences/CONFIGURATION_FOLDER`
*   Linux: `~/.CONFIGURATION_FOLDER`

**Note:** An easy way to locate your configuration directory is to navigate to **Help > Edit Custom VM Options** in Android Studio. This will open a configuration file. Inspect the path of the configuration file to find your configuration directory.

You can also use the following environment variables to point to specific override files elsewhere:

*   `STUDIO_VM_OPTIONS`: set the name and location of the `.vmoptions` file
*   `STUDIO_PROPERTIES`: set the name and location of the `.properties` file
*   `STUDIO_JDK`: set the JDK with which to run Studio

## Customize your VM options

The `studio.vmoptions` file allows you to customize options for Android Studio's JVM. To improve Studio's performance, the most common option to adjust is the maximum heap size, but you can also use the `studio.vmoptions` file to override other default settings such as initial heap size, cache size, and Java garbage collection switches.

To create a new `studio.vmoptions` file or to open your existing one, use the following steps:

1.  Click **Help** > **Edit Custom VM Options**. If you have never edited VM options for Android Studio before, the IDE prompts you to create a new `studio.vmoptions` file. Click **Yes** to create the file.
2.  The `studio.vmoptions` file opens in the editor window of Android Studio. Edit the file to add your own customized VM options. For a full list of customizable JVM options, see Oracle's [Java HotSpot VM Options page](http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html).

The `studio.vmoptions` file you create gets added to the default `studio.vmoptions` file, located in the `bin/` directory inside your Android Studio installation folder.

Note that you should never directly edit the `studio.vmoptions` file found inside the Android Studio program folder. While you can access the file to view Studio's default VM options, editing only your own `studio.vmoptions` file ensures that you don't override important default settings for Android Studio. Therefore, in your `studio.vmoptions` file, override only the attributes you care about and allow Android Studio to continue using default values for any attributes you have not changed.

### Maximum heap size

By default, Android Studio has a maximum heap size of 1280MB. If you are working on a large project, or your system has a lot of RAM, you can improve performance by increasing the maximum heap size for Android Studio processes, such as the core IDE, Gradle daemon, and Kotlin daemon.

Android Studio automatically checks for possible heap size optimizations and notifies you if it detects that performance can be improved.

![The memory settings, which let you configure maximum amount of RAM
          for Android Studio processes.](https://developer.android.com/studio/images/memory-settings-notification.png)

**Figure 1.** A notification about recommended memory settings.

If you use a 64\-bit system that has at least 5 GB of RAM, you can also adjust the heap sizes for your project manually. To do so, follow these steps:

1.  Click **File > Settings** from the menu bar (or **Android Studio > Preferences** on macOS).
2.  Click **Appearance & Behavior > System Settings > Memory Settings**.

    ![The memory settings, which let you configure maximum amount of RAM
       for Android Studio processes.](https://developer.android.com/studio/images/memory-settings.png)

3.  Adjust the heap sizes to match your desired amounts.

4.  Click **Apply**.

    If you changed the heap size for the IDE, you must restart Android Studio before the new memory settings are applied.

**Note:** Allocating too much memory can degrade performance.

## Export and import IDE settings

You can export a `Settings.jar` file that contains all or a subset of your preferred IDE settings for a project. You can then import the JAR file into your other projects and/or make the JAR file available to your colleagues to import into their projects.

For more information, see [Exporting and Importing Settings](https://www.jetbrains.com/help/idea/2020.1/exporting-and-importing-settings.html) at IntelliJ IDEA.

## Customize your IDE properties

The `idea.properties` file allows you to customize IDE properties for Android Studio, such as the path to user installed plugins and the maximum file size supported by the IDE. The `idea.properties` file gets merged with the default properties for the IDE so you can specify just the override properties.

To create a new `idea.properties` file or to open your existing file, use the following steps:

1.  Click **Help** > **Edit Custom Properties**. If you have never edited the IDE properties before, Android Studio prompts you to create a new `idea.properties` file. Click **Yes** to create the file.
2.  The `idea.properties` file opens in the editor window of Android Studio. Edit the file to add your own customized IDE properties.

The following `idea.properties` file includes the commonly customized IDE properties. For a complete list of properties, read about the [`idea.properties` file for IntelliJ IDEA](https://www.jetbrains.com/help/idea/2020.1/file-idea-properties.html).

```
#---------------------------------------------------------------------
# Uncomment this option if you want to customize path to user installed plugins folder. Make sure
# you're using forward slashes.
#---------------------------------------------------------------------
# idea.plugins.path=${idea.config.path}/plugins
#---------------------------------------------------------------------
# Maximum file size (kilobytes) IDE should provide code assistance for.
# The larger file is the slower its editor works and higher overall system memory requirements are
# if code assistance is enabled. Remove this property or set to very large number if you need
# code assistance for any files available regardless their size.
#---------------------------------------------------------------------
idea.max.intellisense.filesize=2500
#---------------------------------------------------------------------
# This option controls console cyclic buffer: keeps the console output size not higher than the
# specified buffer size (Kb). Older lines are deleted. In order to disable cycle buffer use
# idea.cycle.buffer.size=disabled
#---------------------------------------------------------------------
idea.cycle.buffer.size=1024
#---------------------------------------------------------------------
# Configure if a special launcher should be used when running processes from within IDE.
# Using Launcher enables "soft exit" and "thread dump" features
#---------------------------------------------------------------------
idea.no.launcher=false
#---------------------------------------------------------------------
# To avoid too long classpath
#---------------------------------------------------------------------
idea.dynamic.classpath=false
#---------------------------------------------------------------------
# There are two possible values of idea.popup.weight property: "heavy" and "medium".
# If you have WM configured as "Focus follows mouse with Auto Raise" then you have to
# set this property to "medium". It prevents problems with popup menus on some
# configurations.
#---------------------------------------------------------------------
idea.popup.weight=heavy
#---------------------------------------------------------------------
# Use default anti-aliasing in system, i.e. override value of
# "Settings|Editor|Appearance|Use anti-aliased font" option. May be useful when using Windows
# Remote Desktop Connection for instance.
#---------------------------------------------------------------------
idea.use.default.antialiasing.in.editor=false
#---------------------------------------------------------------------
# Disabling this property may lead to visual glitches like blinking and fail to repaint
# on certain display adapter cards.
#---------------------------------------------------------------------
sun.java2d.noddraw=true
#---------------------------------------------------------------------
# Removing this property may lead to editor performance degradation under Windows.
#---------------------------------------------------------------------
sun.java2d.d3d=false
#---------------------------------------------------------------------
# Workaround for slow scrolling in JDK6
#---------------------------------------------------------------------
swing.bufferPerWindow=false
#---------------------------------------------------------------------
# Removing this property may lead to editor performance degradation under X Window.
#---------------------------------------------------------------------
sun.java2d.pmoffscreen=false
#---------------------------------------------------------------------
# Workaround to avoid long hangs while accessing clipboard under Mac OS X.
#---------------------------------------------------------------------
# ide.mac.useNativeClipboard=True
#---------------------------------------------------------------------
# Maximum size (kilobytes) IDEA will load for showing past file contents -
# in Show Diff or when calculating Digest Diff
#---------------------------------------------------------------------
# idea.max.vcs.loaded.size.kb=20480

```

## Configure the IDE for low\-memory machines

If you are running Android Studio on a machine with less than the recommended specifications (see [System Requirements](https://developer.android.com/studio#Requirements)), you can customize the IDE to improve performance on your machine, as follows:

*   **Reduce the maximum heap size available to Android Studio:** Reduce the maximum heap size for Android Studio to 512Mb. For more information on changing maximum heap size, see [Maximum heap size](#adjusting_heap_size).
*   **Update Gradle and the Android plugin for Gradle:** Update to the latest versions of Gradle and the Android plugin for Gradle to ensure you are taking advantage of the latest improvements for performance. For more information about updating Gradle and the Android plugin for Gradle, see the [Android plugin for Gradle Release Notes](https://developer.android.com/studio/releases/gradle-plugin).
*   **Enable Power Save Mode:** Enabling Power Save Mode turns off a number of memory\- and battery\-intensive background operations, including error highlighting and on\-the\-fly inspections, autopopup code completion, and automatic incremental background compilation. To turn on Power Save Mode, click **File > Power Save Mode**.
*   **Disable unnecessary lint checks:** To change which lint checks Android Studio runs on your code, do the following:

    1.  Click **File > Settings** (on macOS, **Android Studio > Preferences**) to open the **Settings** dialog.
    2.  In the left pane, expand the **Editor** section and click **Inspections**.
    3.  Click the checkboxes to select or deselect lint checks as appropriate for your project.
    4.  Click **Apply** or **OK** to save your changes.
*   **Debug on a physical device:** Debugging on an emulator uses more memory than debugging on a physical device, so you can improve overall performance for Android Studio by [debugging on a physical device](https://developer.android.com/studio/run/device).

*   **Include only necessary Google Play services as dependencies:** Including Google Play Services as dependencies in your project increases the amount of memory necessary. Only include necessary dependencies to improve memory usage and performance. For more information, see [Add Google Play Services to Your Project](https://developers.google.com/android/guides/setup#add_google_play_services_to_your_project).

*   **Reduce the maximum heap size available for Gradle:** Gradle's default maximium heap size is 1,536 MB. Reduce the value by overriding the `org.gradle.jvmargs` property in the `gradle.properties` file, as shown below:

    ```
    # Make sure to gradually decrease this value and note# changes in performance. Allocating too little memory may# also decrease performance.org.gradle.jvmargs = -Xmx1536m
    ```

*   **Do not enable parallel compilation:** Android Studio can compile independent modules in parallel, but if you have a low\-memory system you should not turn on this feature. To check this setting, do the following:

    1.  Click **File > Settings** (on macOS, **Android Studio > Preferences**) to open the **Settings** dialog.
    2.  In the left pane, expand **Build, Execution, Deployment** and then click **Compiler**.
    3.  Ensure that the **Compile independent modules in parallel** option is unchecked.
    4.  If you have made a change, click **Apply** or **OK** for your change to take effect.

## Set the JDK version

A copy of the latest OpenJDK comes bundled with Android Studio 2.2 and higher, and this is the JDK version we recommend you use for your Android projects. To use the bundled JDK, do the following:

1.  Open your project in Android Studio and select **File > Project Structure** in the menu bar.
2.  In the **SDK Location** page and under **JDK location**, check the **Use embedded JDK** checkbox.
3.  Click **OK**.

By default, the Java language version used to compile your project is based on your project's [`compileSdkVersion`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.BaseExtension.html#com.android.build.gradle.BaseExtension:compileSdkVersion) (because different versions of Android support different versions of Java). If necessary, you can override this default Java version by adding the following [`CompileOptions {}`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.CompileOptions.html) block to your `build.gradle` file:

```
android {    compileOptions {        sourceCompatibility JavaVersion.VERSION\_1\_6        targetCompatibility JavaVersion.VERSION\_1\_6    }}
```

For more information about where `compileSdkVersion` is defined, read about [the module\-level build file](https://developer.android.com/studio/build#module-level).

## Set proxy settings

Proxies serve as intermediary connection points between HTTP clients and web servers that add security and privacy to internet connections.

To support running Android Studio behind a firewall, set the proxy settings for the Android Studio IDE. Use the Android Studio IDE HTTP Proxy settings page to set the HTTP proxy settings for Android Studio.

When running the Android plugin for Gradle from the command line or on machines where Android Studio is not installed, such as continuous integration servers, set the proxy settings in the Gradle build file.

**Note:** After the initial installation of the Android Studio bundle, Android Studio can run with internet access or off\-line. However, Android Studio requires an internet connection for Setup Wizard synchronization, 3rd\-party library access, access to remote repositories, Gradle initialization and synchronization, and Android Studio version updates.

### Set up the Android Studio proxy

Android Studio supports HTTP proxy settings so you can run Android Studio behind a firewall or secure network. To set the HTTP proxy settings in Android Studio:

1.  From the menu bar, click **File > Settings** (on macOS, click **Android Studio > Preferences**).
2.  In the left pane, click **Appearance & Behavior > System Settings > HTTP Proxy**. The HTTP Proxy page appears.
3.  Select **Auto\-detect proxy settings** to use an automatic proxy configuration URL for the proxy settings or **Manual proxy configuration** to enter each of the settings yourself. For a detailed explanation of these settings, see [HTTP Proxy](https://www.jetbrains.com/help/idea/2020.1/http-proxy.html).
4.  Click **Apply** or **OK** for your changes to take effect.

### Android plugin for Gradle HTTP proxy settings

When running the Android plugin from the command line or on machines where Android Studio is not installed, set the Android plugin for Gradle proxy settings in the Gradle build file.

For application\-specific HTTP proxy settings, set the proxy settings in the `build.gradle` file as required for each application module.

```
apply plugin: 'com.android.application'android {    ...    defaultConfig {        ...        systemProp.http.proxyHost=proxy.company.com        systemProp.http.proxyPort=443        systemProp.http.proxyUser=userid        systemProp.http.proxyPassword=password        systemProp.http.auth.ntlm.domain=domain    }    ...}
```

For project\-wide HTTP proxy settings, set the proxy settings in the `gradle/gradle.properties` file.

```
# Project-wide Gradle settings....systemProp.http.proxyHost=proxy.company.comsystemProp.http.proxyPort=443systemProp.http.proxyUser=usernamesystemProp.http.proxyPassword=passwordsystemProp.http.auth.ntlm.domain=domainsystemProp.https.proxyHost=proxy.company.comsystemProp.https.proxyPort=443systemProp.https.proxyUser=usernamesystemProp.https.proxyPassword=passwordsystemProp.https.auth.ntlm.domain=domain...
```

For information about using Gradle properties for proxy settings, see the [Gradle User Guide](http://www.gradle.org/docs/current/userguide/build_environment.html).

**Note:** When using Android Studio, the settings in the Android Studio IDE HTTP proxy settings page override the HTTP proxy settings in the `gradle.properties` file.

## Optimize Android Studio performance on Windows

Android Studio performance on Windows can be impacted by a variety of factors. This section describes how you can optimize Android Studio settings to get the best possible performance on Windows.

### Minimize the impact of antivirus software on build speed

Some antivirus software can interfere with the Android Studio build process, causing builds to run dramatically slower. When you run a build in Android Studio, Gradle compiles your app’s resources and source code and then packages the compiled resources together in an APK. During this process, many files are created on your computer. If your antivirus software has real\-time scanning enabled, the antivirus can force the build process to halt each time a file is created while the antivirus scans that file.

To avoid this issue, you can exclude certain directories from real\-time scanning in your antivirus software.

**Caution:** To ensure that your computer is safe from malicious software, you should not completely disable real\-time scanning or your antivirus software.

The following list shows the default location of each Android Studio directory that you should exclude from real\-time scanning:

Gradle cache

`%USERPROFILE%\.gradle`

Android Studio projects

`%USERPROFILE%\AndroidStudioProjects`

Android SDK

`%USERPROFILE%\AppData\Local\Android\SDK`

**Android Studio system files**

Syntax: `%LOCALAPPDATA%\Google\<product><version>`

Example: `C:\Users\YourUserName\AppData\Local\Google\AndroidStudio4.1`

#### Customize directory locations for Group Policy controlled environments

If a Group Policy limits which directories you can exclude from real\-time scanning on your computer, you can move your Android Studio directories to one of the locations that the centralized Group Policy already excludes.

The following list shows how to customize the location of each Android Studio directory, where `C:\WorkFolder` is the directory that your Group Policy already excludes:

Gradle cache

Define the `GRADLE_USER_HOME` environment variable to point to `C:\WorkFolder\.gradle`.

Android Studio projects

Move or create project directories in an appropriate subdirectory of `C:\WorkFolder`. For example, `C:\WorkFolder\AndroidStudioProjects`.

Android SDK

Follow these steps:

1.  In Android Studio, open the **Settings** dialog (**Preferences** on macOS), then navigate to **Appearance & Behavior > System Settings > Android SDK**.

2.  Change the value of **Android SDK Location** to `C:\WorkFolder\AndroidSDK`.

    To avoid downloading the SDK again, make sure to copy the existing SDK directory, located at `%USERPROFILE%\AppData\Local\Android\SDK` by default, to the new location.

Android Studio system files

Follow these steps:

1.  In Android Studio, click **Help > Edit Custom Properties**.

    Android Studio prompts you to create an `idea.properties` file if you don't already have one.

2.  Add the following line to your `idea.properties` file:

    idea.system.path=c:/workfolder/studio/caches/trunk\-system