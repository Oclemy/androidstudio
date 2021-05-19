# Install Android Studio

Setting up Android Studio takes just a few clicks.

First, be sure you [download the latest version of Android Studio](https://developer.android.com/studio).

## Windows

To install Android Studio on Windows, proceed as follows:

1.  If you downloaded an `.exe` file (recommended), double\-click to launch it.

    If you downloaded a `.zip` file, unpack the ZIP, copy the **android\-studio** folder into your **Program Files** folder, and then open the **android\-studio > bin** folder and launch `studio64.exe` (for 64\-bit machines) or `studio.exe` (for 32\-bit machines).

2.  Follow the setup wizard in Android Studio and install any SDK packages that it recommends.

That's it. The following video shows each step of the setup procedure when using the recommended `.exe` download.

As new tools and other APIs become available, Android Studio tells you with a pop\-up, or you can check for updates by clicking **Help > Check for Update**.

## Mac

To install Android Studio on your Mac, proceed as follows:

1.  Launch the Android Studio DMG file.
2.  Drag and drop Android Studio into the Applications folder, then launch Android Studio.
3.  Select whether you want to import previous Android Studio settings, then click **OK**.
4.  The Android Studio Setup Wizard guides you through the rest of the setup, which includes downloading Android SDK components that are required for development.

That's it. The following video shows each step of the recommended setup procedure.

As new tools and other APIs become available, Android Studio tells you with a pop\-up, or you can check for updates by clicking **Android Studio > Check for Updates**.

**Note:** If you use Android Studio on macOS Mojave or later, you might see a prompt to allow the IDE to access your calendar, contacts, or photos. This prompt is caused by new privacy protection mechanisms for applications that access files under the home directory. So, if your project includes files and libraries in your home directory, and you see this prompt, you can select **Don't Allow**.

## Linux

To install Android Studio on Linux, proceed as follows:

1.  Unpack the `.zip` file you downloaded to an appropriate location for your applications, such as within `/usr/local/` for your user profile, or `/opt/` for shared users.

    If you're using a 64\-bit version of Linux, make sure you first install the [required libraries for 64\-bit machines](#64bit-libs).

2.  To launch Android Studio, open a terminal, navigate to the `android-studio/bin/` directory, and execute `studio.sh`.
3.  Select whether you want to import previous Android Studio settings or not, then click **OK**.
4.  The Android Studio Setup Wizard guides you through the rest of the setup, which includes downloading Android SDK components that are required for development.

**Tip:** To make Android Studio available in your list of applications, select **Tools > Create Desktop Entry** from the Android Studio menu bar.

### Required libraries for 64\-bit machines

If you are running a 64\-bit version of Ubuntu, you need to install some 32\-bit libraries with the following command:

sudo apt\-get install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2\-1.0:i386

If you are running 64\-bit Fedora, the command is:

sudo yum install zlib.i686 ncurses\-libs.i686 bzip2\-libs.i686

That's it. The following video shows each step of the recommended setup procedure.

As new tools and other APIs become available, Android Studio tells you with a pop\-up, or you can check for updates by clicking **Help > Check for Update**.

## Chrome OS

Follow these steps to install Android Studio on Chrome OS:

1.  If you haven't already done so, [install Linux for Chrome OS](https://support.google.com/chromebook/answer/9145439).
2.  Open the **Files** app and locate the DEB package you downloaded in the **Downloads** folder under **My files**.
3.  Right\-click the DEB package and select **Install with Linux (Beta)**.

    ![The target file location for DEB package on Chrome OS.](https://developer.android.com/studio/images/studio-install-chromeos.png)

    *   If you have installed Android Studio before, select whether you want to import previous Android Studio settings, then click **OK**.
4.  The Android Studio **Setup Wizard** guides you through the rest of the setup, which includes downloading Android SDK components that are required for development.

5.  After installation is complete, launch Android Studio either from the Launcher, or from the Chrome OS Linux terminal by running `studio.sh` in the default installation directory:

    `/opt/android-studio/bin/studio.sh`

That's it. As new tools and other APIs become available, Android Studio tells you with a pop\-up, or you can check for updates by clicking **Help > Check for Update**.