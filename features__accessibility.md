# Accessibility features

- [Accessibility features](#accessibility-features)
  - [Screen reader compatibility](#screen-reader-compatibility)
  - [Activate support for screen readers](#activate-support-for-screen-readers)
  - [Use the keyboard](#use-the-keyboard)
    - [Activate the main menu](#activate-the-main-menu)
    - [Navigate between files and tool windows](#navigate-between-files-and-tool-windows)
    - [Use the navigation bar](#use-the-navigation-bar)
  - [Disable code folding](#disable-code-folding)
  - [Disable auto\-insertion features](#disable-auto-insertion-features)
  - [Disable automatic code completion popup](#disable-automatic-code-completion-popup)
  - [Access errors, warnings, and code inspections](#access-errors-warnings-and-code-inspections)
    - [Review errors in all files in a project](#review-errors-in-all-files-in-a-project)
    - [Review errors and code inspections in a single file](#review-errors-and-code-inspections-in-a-single-file)
  - [Use tab indentation](#use-tab-indentation)
  - [Use Layout Editor](#use-layout-editor)

This document describes accessibility features in Android Studio 2.2 and higher.

## Screen reader compatibility

Screen readers are currently only supported for Android Studio on Windows.

A variety of screen readers are available for Windows, but the compatibility with Android Studio varies:

*   **NVDA**: We recommend using [NVDA 2015 or later](http://www.nvaccess.org/download/), as this was the prevalent screen reader during the development of accessibility support for Android Studio. If you use the 32\-bit version of NVDA, you must install the 32\-bit JRE on your machine, as this version of NVDA requires `C:\Windows\SysWOW64\WindowsAccessBridge-32.DLL` to work with Android Studio. If NVDA cannot locate this file, the NVDA Event Log window displays the message, "Java Access Bridge not available".
*   **JAWS**: We do not recommend using Android Studio with JAWS because you can encounter significant performance issues when navigating within some places in Android Studio while using JAWS.
*   **Windows Eyes**: We do not recommend using Android Studio with Windows Eyes, as during setup, Windows Eyes overwrites the accessibility libraries of all JDKs and JREs on your machine with older versions that are not compatible with Android Studio.
*   **Built\-in Windows Narrator**: We do not recommend Windows Narrator, as it is not compatible with Java accessibility in general.

## Activate support for screen readers

To set up a screen reader with Android Studio on a Windows machine, use the following steps:

1.  Install your screen reader.
2.  Ensure you have installed the proper Java version for your screen reader, as follows:

    *   If your screen reader is 32\-bit, install the 32\-bit JRE version 1.7 or higher.
    *   If your screen reader is 64\-bit, install the 64\-bit JRE version 1.7 or higher.
3.  To turn on the Java Access Bridge, open the command prompt and type `JRE_HOME\bin\jabswitch -enable`, where `JRE_HOME` is the directory of the JRE on your machine.

4.  Your machine may have multiple versions of some important components of the Java Access Bridge, which may not be compatible across versions. To verify your Java Access Bridge configuration is correct, confirm that you have the correct versions of the following important files as described below:

    **If your screen reader is 32\-bit:**

    *   Ensure that `C:\Windows\SysWOW64\WindowsAccessBridge-32.DLL` is present and has a version number of 7.x.x.x or higher. The file's description should read "Java(TM) Platform SE 7".
    *   Confirm that the Date Modified of the `JRE_HOME\lib\ext\access-bridge-64.jar` and `JRE_HOME\jre\lib\ext\jaccess.jar` files (where `JRE_HOME` is the home directory of the JRE on your machine) are the same as the other files in their directories. If those files' last modified dates differ from those of the other files in their respective directories, they may have been overwritten by the Windows Eyes installer; in this case, you must re\-install the JDK/JRE to ensure that you're running the correct versions of these files.

    **If your screen reader is 64\-bit:**

    *   Ensure that `C:\Windows\System32\WindowsAccessBridge-64.DLL` is present and has a version number of 7.x.x.x or higher. The file's description should read "Java(TM) Platform SE 7".
    *   Confirm that the Date Modified of the `JRE_HOME\lib\ext\access-bridge-64.jar` and `JRE_HOME\jre\lib\ext\jaccess.jar` files (where `JRE_HOME` is the home directory of the JRE on your machine) are the same as the other files in their directories. If those files' last modified dates differ from those of the other files in their respective directories, they may have been overwritten by the Windows Eyes installer; in this case, you must re\-install the JDK/JRE to ensure that you're running the correct versions of these files.
5.  To activate support for screen readers in Android Studio, click **File > Settings > Appearance & Behavior > System Settings**, check the **Support screen readers** checkbox, and then restart Android Studio. This setting ensures that the Android Studio experience is optimized for screen reader users, for example by customizing keyboard navigation. If you are using Jaws, remember that you must start Jaws before you start Android Studio in order for this setting to take effect.

## Use the keyboard

You can use keyboard shortcuts to navigate through all controls in Android Studio.

**Note:** Some Android Studio elements are keyboard\-accessible only when using the object navigation (or similar) feature of your screen reader. For help with object navigation or equivalent, consult your screen reader documentation, such as the [NVDA user guide](http://www.nvaccess.org/files/nvda/documentation/userGuide.html).

This section includes the most useful keyboard shortcuts for navigating around Android Studio. For a complete guide to the default Android Studio keyboard shortcuts, read [Keyboard shortcuts](https://developer.android.com/studio/intro/keyboard-shortcuts).

### Activate the main menu

To open the main menu and other top\-level menus, use these shortcuts:

*   To open the main menu, press F10.
*   To open a top\-level menu on a Windows machine, press Alt+\[*mnemonic*\]. For example, to open the File menu, press Alt+F.

### Navigate between files and tool windows

To navigate between files and tool windows, use these shortcuts:

*   To move to a tool window, press Alt+\[*number*\] (on Mac, Command+\[ *number* \]). For example, to move to the Project structure tool window, press Alt+0 (on Mac, Command+0).
*   To move between files and tool windows, press Control+Tab (on Mac, Command+Tab). To move through all entries, keep pressing Control+Tab.

### Use the navigation bar

The navigation bar lets you move between files in a project using the following shortcuts:

*   To activate the navigation bar, press Alt+Home (on Mac, Option+Fn+Left).
*   To switch between items in the navigation hierarchy, press the left arrow or the right arrow.
*   To open a popup window displaying the contents of the current item, press the spacebar.

## Disable code folding

By default, the Android Studio editor folds part of the text into expandable regions. For example, the "import" list at the beginning of a Java source file is folded into a single line containing the text "`import …`".

When using a screen reader, code folding can make navigation difficult. To modify code folding options, click **File > Settings > Editor > General > Code Folding** (on a Mac, **File > Properties > Editor > General > Code Folding**).

## Disable auto\-insertion features

By default, Android Studio automatically inserts closing curly braces, quotes, or parentheses.

When using a screen reader, automatic insertion may not be useful. To modify automatic insertion options, click **File > Settings > Editor > General > Smart Keys** (on a Mac, **File > Properties > Editor > General > Smart Keys**).

## Disable automatic code completion popup

By default, Android Studio automatically shows the code completion popup when certain keystrokes are typed, and if it finds only a single match, auto\-inserts that match. This behavior can create a confusing experience with screen readers.

To modify autopopup and auto\-insertion options for code completion, click **File > Settings > Editor > General > Code Completion** (on a Mac, **File > Properties > Editor > General > Code Completion**).

## Access errors, warnings, and code inspections

Use your keyboard to view and navigate through errors, warnings, and code inspections.

### Review errors in all files in a project

When you click **Build > Make Project**, all warnings and errors appear in the **Messages** window. The **Messages** window is accessible as of Android Studio 2.2. ( [View bug history](https://code.google.com/p/android/issues/detail?id=198013).)

To review errors in the **Messages** window, use these shortcuts:

*   To activate the **Messages** window, press Alt+0 (on Mac, Option+0).
*   To navigate through all messages, press the up and down arrows.

Alternatively, you can use the text editor to view and navigate through all errors. To use the editor to review errors, press Control+Alt+ Up/Down (on Mac, Command+Option+Up/Down).

### Review errors and code inspections in a single file

To review errors in a single file, use these shortcuts:

*   To go to the next or previous error, press F2 or Control+F2 (on Mac, Command+F2).
*   To open a tooltip containing the error message, press Control+F1 (on Mac, Command+F1).

To navigate to all code inspections—not just errors—click **File > Settings > Editor > General** (on a Mac, **File > Preferences > Editor > General**), and then uncheck **'Next error' action goes to high priority problems only**.

## Use tab indentation

By default, Android Studio uses the space character for indentation. Screen reader users may prefer tab indentation because the verbalization is more concise.

To change to tab indentation, click **File > Settings > Editor > Code Style > Java > Tabs and Indents** (on a Mac, **File > Preferences > Editor > Code Style > Java > Tabs and Indents**) and then check the **Use tab character** checkbox.

## Use Layout Editor

As of Android Studio 2.2, [Layout Editor](https://developer.android.com/studio/write/layout-editor) is not fully accessible. ( [View bug history](https://code.google.com/p/android/issues/detail?id=199182).) A temporary workaround is to open layout files in text mode by default. ( [View bug history](https://code.google.com/p/android/issues/detail?id=199181).)

To open layout files in text view mode by default, click **File > Settings > Editor > Layout Editor** (on a Mac, **File > Preferences > Editor > Layout Editor**) and check the **Prefer XML Editor** checkbox.