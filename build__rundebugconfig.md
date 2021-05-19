# Create and edit run/debug configurations

- [Create and edit run/debug configurations](#create-and-edit-rundebug-configurations)
  - [About run/debug configurations](#about-rundebug-configurations)
    - [Default run/debug configuration](#default-rundebug-configuration)
    - [Project scope and version control](#project-scope-and-version-control)
  - [Open the Run/Debug Configurations dialog](#open-the-rundebug-configurations-dialog)
  - [Create a new run/debug configuration](#create-a-new-rundebug-configuration)
    - [Start a configuration based on a template](#start-a-configuration-based-on-a-template)
    - [Start a configuration from a copy](#start-a-configuration-from-a-copy)
    - [Define a configuration for an item in the project](#define-a-configuration-for-an-item-in-the-project)
    - [Define a test configuration for a class or method](#define-a-test-configuration-for-a-class-or-method)
  - [Run or debug an app using a saved configuration](#run-or-debug-an-app-using-a-saved-configuration)
  - [Edit a run/debug configuration](#edit-a-rundebug-configuration)
  - [Edit a default run/debug configuration template](#edit-a-default-rundebug-configuration-template)
  - [Sort and group configurations](#sort-and-group-configurations)
  - [Define before\-launch operations](#define-before-launch-operations)
  - [Default run/debug configuration templates](#default-rundebug-configuration-templates)
    - [Unsupported templates](#unsupported-templates)
    - [Name and Share configuration options](#name-and-share-configuration-options)
    - [Android App](#android-app)
      - [General tab](#general-tab)
      - [Miscellaneous tab](#miscellaneous-tab)
      - [Debugger tab](#debugger-tab)
      - [Profiling tab](#profiling-tab)
    - [Android Tests](#android-tests)
      - [General tab](#general-tab-1)
      - [Miscellaneous tab](#miscellaneous-tab-1)
      - [Debugger tab](#debugger-tab-1)
    - [App Engine DevAppServer](#app-engine-devappserver)

When you run, debug, or test your code, Android Studio uses a run/debug configuration to determine how to perform the operation. Typically, the default configuration is sufficient to run or debug an app. However, you can modify and create new configurations, and modify the default templates to suit your development process as described on this page.

For more information, also see the following IntelliJ documentation:

*   [Run/Debug Configurations](https://www.jetbrains.com/help/idea/2020.1/run-debug-configurations.html)
*   [Creating and Editing Run/Debug Configurations](https://www.jetbrains.com/help/go/creating-and-editing-run-debug-configurations.html)

## About run/debug configurations

Run/debug configurations specify details such as app installation, launch, and test options. You can define a configuration for one\-time use, or save it for future use. After you save it, you can select the configuration from the **Select Run/Debug Configuration** drop\-down list within the [toolbar](https://developer.android.com/studio/intro#user-interface). Android Studio saves configurations as part of the project.

### Default run/debug configuration

When you first create a project, Android Studio creates a default run/debug configuration for the main activity based on the [Android App template](#android-application). To run or debug your project, you must always have at least one run/debug configuration defined. For this reason, we recommend that you don’t delete the default configuration.

### Project scope and version control

Run/debug configurations and template changes apply to the current project only. You can share a run/debug configuration (but not a template) through your version control system. For more information about how to share a configuration, see [Name and Share Configuration Options](#nameshare).

## Open the Run/Debug Configurations dialog

To open the Run/Debug Configurations dialog, select **Run** > **Edit Configurations**. The **Run/Debug Configurations** dialog appears, as shown in figure 1.

![](https://developer.android.com/studio/images/rdc-rundebugconfg_2-3_2x.png)

**Figure 1**. The **Run/Debug Configurations** dialog

The dialog displays [default templates](#default-template) in the left panel under the **Defaults** folder, and groups your defined configurations by template type above the **Defaults** folder. You can resize the dialog to see any hidden items.

In this dialog, you can:

*   [Create new run/debug configurations](#creating).
*   [Edit run/debug configurations.](#editing)
*   [Edit default templates.](#editing-template)
*   [Sort and group configurations.](#sorting)

## Create a new run/debug configuration

You can define new run/debug configurations from the **Run/Debug Configurations** dialog, the **Project** window, or the Code Editor. The new configuration must be based on a [default template](#default-template).

The Run/Debug Configurations dialog displays your run/debug configurations and the available default templates. You can start a new configuration directly from a template, or from a copy of another configuration. You can then change the field values as needed.

Alternatively, you can right\-click an item in the **Project** window to automatically create a configuration specific to that item. For example, if you want to run a particular activity, you can right\-click the activity Java file and select **Run**. Depending on the item, Android Studio uses an [Android App](#android-application), [Android Instrumented Tests](#android-tests), or [Android JUnit](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-junit.html) default template to create the configuration.

In the Code Editor, you can easily create a test and run/debug configuration for a class or method, and then execute it.

When you create a configuration outside of the **Run/Debug Configurations** dialog, the configuration is temporary unless you save it. By default, you can have up to five temporary configurations in the project before Android Studio starts to remove them. To change this default, in the **Run/Debug Configurations** dialog, click the **Defaults** folder and type a value in the **Temporary Configurations Limit** field. For more information about temporary configurations, see [Creating and Saving Temporary Run/Debug Configurations](https://www.jetbrains.com/help/idea/2020.1/creating-and-saving-temporary-run-debug-configurations.html).

### Start a configuration based on a template

To define a run/debug configuration based on a template, follow these steps:

1.  [Open the Run/Debug Configurations dialog](#opening).
2.  Click **Add New Configuration** ![](https://developer.android.com/studio/images/rdc-iaddnewconfig_2-1.png).
3.  Select a [default template](#default-template).
4.  Type a name in the **Name** field.
5.  Modify the configuration, as needed.

Be sure to correct any errors displayed at the bottom of the dialog.

7.  Click **Apply** or **OK**.

### Start a configuration from a copy

To define a run/debug configuration starting from a copy of another configuration, follow these steps:

1.  [Open the Run/Debug Configurations dialog](#opening).
2.  Select an existing run/debug configuration in the left pane.
3.  Click **Copy Configuration** ![](https://developer.android.com/studio/images/rdc-icopyconfig_2-1.png).
4.  Type a name in the **Name** field.
5.  Modify the configuration, as needed.

Be sure to correct any errors displayed at the bottom of the dialog.

7.  Click **Apply** or **OK**.

### Define a configuration for an item in the project

Android Studio can create a run/debug configuration for some items displayed in the **Project** window. The configuration is based on a default template, as follows:

*   Activity Java file: The [Android App](#android-application) template.
*   Package: [Android Instrumented Tests](#android-tests) or [Android JUnit](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-junit.html) template, depending on your [source set](https://developer.android.com/studio/build/build-variants#sourcesets). If you select an [instrumented test](https://developer.android.com/training/testing/unit-testing), then it uses the Android Instrumented Tests template. If you select a [local unit test](https://developer.android.com/training/testing/unit-testing), then it uses the Android JUnit template. For a local unit test, you can optionally run with code coverage. For more information, see [Code Coverage](https://www.jetbrains.com/help/idea/2020.1/code-coverage.html).

To create a run/debug configuration for an item in your project, follow these steps:

1.  Open a project in [Android or Project view](https://developer.android.com/studio/projects#ProjectFiles).
2.  In the **Project** window, right\-click a testable item and select either **Run filename** or **Debug filename**. Android Studio creates a temporary run/debug configuration and launches your app.
3.  Select **Save** from the **Select Run/Debug Configuration** drop\-down list in the toolbar. If you do not see the toolbar, select **View > Toolbar**.

![](https://developer.android.com/studio/images/run/rundebug-saveconfiguration_2x.png)

**Figure 2**. Save the configuration

**Note:** If you right\-click and run or debug the same item (but not an activity), Android Studio creates a new configuration.

### Define a test configuration for a class or method

Android Studio lets you define a test run configuration for a class or method, and then execute it. For example, if you create a new class, you can create and run a test for it. If the test passes, you can then run the tests for the rest of the project to make sure that your new code doesn’t break anything somewhere else.

Android Studio uses the [Android Instrumented Tests](#android-tests) or [Android JUnit](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-junit.html) template, depending on your [source set](https://developer.android.com/studio/build/build-variants#sourcesets). For a local unit test, you can optionally run with [code coverage](https://www.jetbrains.com/help/idea/2020.1/code-coverage.html).

To create a run/debug configuration for a class or method in your Java code, follow these steps:

1.  Open a project in [Android or Project view](https://developer.android.com/studio/projects#ProjectFiles).
2.  Open a Java file in the Code Editor.
3.  Select a class or method in the code, and then press Control+Shift+T (Command+Shift+T).
4.  Select **Create New Test** from the menu that appears.
5.  In the **[Create Test](https://www.jetbrains.com/help/idea/2020.1/create-test.html)** dialog, optionally change or set the values and click **OK**.
6.  In the **Choose Destination Directory** dialog, select where in the project you want Android Studio to place the test. You can specify the location by directory structure or by selecting a neighboring class.
7.  Click **OK**.

    The new test appears in the **Project** window in the corresponding test source set.

8.  To run the test, do one of the following:
    *   In the **Project** window, right\-click the test and select **Run** or **Debug**.
    *   In the Code Editor, right\-click a class definition or method name in the test file and select **Run** or **Debug** to test all methods in the class.
9.  Select **Save** from the **Select Run/Debug Configuration** drop\-down list in the toolbar.

![](https://developer.android.com/studio/images/run/rundebug-saveconfiguration_2x.png)

**Figure 3**. Save the configuration

## Run or debug an app using a saved configuration

If you’ve saved a run/debug configuration, you can select it before you run or debug your app.

To use a saved run/debug configuration, follow these steps:

1.  Select the run/debug configuration from the **Select Run/Debug Configuration** drop\-down list within the [toolbar](https://developer.android.com/studio/intro#user-interface).

The drop\-down list is to the left of **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png) and **Debug** ![](https://developer.android.com/studio/images/buttons/toolbar-debug.png) ; for example, ![](https://developer.android.com/studio/images/rdc-itoolbar_2-1.png).

3.  Select **Run** > **Run** or **Run** > **Debug**.

Alternatively, click **Run** ![](https://developer.android.com/studio/images/buttons/toolbar-run.png) or **Debug** ![](https://developer.android.com/studio/images/buttons/toolbar-debug.png).

## Edit a run/debug configuration

To edit a run/debug configuration, follow these steps:

1.  [Open the Run/Debug Configurations dialog](#opening).
2.  Select a configuration in the left pane.
3.  Modify the configuration, as needed.

Be sure to correct any errors displayed at the bottom of the dialog.

5.  Click **Apply** or **OK**.

## Edit a default run/debug configuration template

You can edit the default templates provided by Android Studio to suit your development process. When you edit a template, it doesn’t affect existing configurations that use the template. So, for example, if you need to create a number of configurations of a certain type, you can edit the template and then change it back when you’re done.

Although you can’t create new templates, you can create configurations to use similar to a template. You can [copy a configuration](#starting-copy) and edit the copy to create new configurations.

To edit a template, follow these steps:

1.  [Open the Run/Debug Configurations dialog](#opening).
2.  Expand the **Defaults** folder and select a [default template](#default-template).
3.  Modify the configuration, as needed.

Be sure to correct any errors displayed at the bottom of the dialog.

5.  Click **Apply** or **OK**.

## Sort and group configurations

In the **Run/Debug Configurations** dialog, you can order your configurations to find them quickly. You can sort the items in the folder alphabetically, and create new folders to group configurations.

To sort configurations alphabetically, follow these steps:

1.  [Open the Run/Debug Configurations dialog](#opening).
2.  Select a folder that contains configurations.
3.  Click **Sort Configurations** ![](https://developer.android.com/studio/images/rdc-isortconfig_2-1.png).
4.  Click **Apply** or **OK**.

To group configurations in folders, follow these steps:

1.  [Open the Run/Debug Configurations dialog](#opening).
2.  Select a folder that contains configurations.
3.  Click **Create New Folder** ![](https://developer.android.com/studio/images/rdc-icreatenewfolder_2-1.png).
4.  Type a name in the **Folder Name** field.
5.  Drag items from the same template category into the folder.
6.  Order the folders and configurations in the same template category by dragging them into position or by using **Move Up** ![](https://developer.android.com/studio/images/rdc-imoveup_2-1.png) and **Move Down** ![](https://developer.android.com/studio/images/rdc-imovedown_2-1.png).
7.  Click **Apply** or **OK**.

## Define before\-launch operations

You can specify tasks to execute before applying the run/debug configuration. The tasks are performed in the order they appear in the list.

**Note:** Defining before launch tasks is an advanced feature. Instead of using this feature, we recommend that you put any preparation logic as tasks in your `build.gradle` file so they’ll be executed when you build from the command line.

To create a task list, follow these steps:

1.  At the bottom of the **Run/Debug Configurations** dialog under **Before launch**, click **Add** ![](https://developer.android.com/studio/images/rdc-iaddnewconfig_2-1.png) and select a task type. If a dialog opens, fill in the fields and click **OK**.
2.  Add more tasks as needed.
3.  To order the tasks, select a task and click **Up** ![](https://developer.android.com/studio/images/rdc-imoveup_2-1.png) and **Down** ![](https://developer.android.com/studio/images/rdc-imovedown_2-1.png) to move it up or down in the list.
4.  Select **Show this page** if you want to display the run/debug configuration settings before applying them.

    This option is deselected by default.

5.  Select **Active tool window** if you want the [Run](https://www.jetbrains.com/help/idea/2020.1/run-tool-window.html) or [Debug](https://www.jetbrains.com/help/idea/2020.1/debug-tool-window.html) tool window to be activated when you run or debug your app.

    This option is selected by default.

To remove a task from the list, follow these steps:

1.  Select a task.
2.  Click **Remove** ![](https://developer.android.com/studio/images/rdc-iremove_2-1.png).

To edit a task, follow these steps:

1.  Select a task.
2.  Click **Edit** ![](https://developer.android.com/studio/images/rdc-iedit_2-1.png).
3.  Edit the task settings in the dialog that opens, and then click **OK**.

The following table lists the available tasks you can add.

| Task | Description |
| --- | --- |
| Run External tool | Run an application that’s external to Android Studio. In the **[External Tools](https://www.jetbrains.com/help/idea/2020.1/create-edit-copy-tool-dialog.html)** dialog, select one or more applications that you want to run and then click **OK**. If the application isn’t defined in Android Studio yet, add its definition in the **[Create Tools](https://www.jetbrains.com/help/idea/2020.1/create-edit-copy-tool-dialog.html)** dialog. For more information, see [Configuring Third\-Party Tools](https://www.jetbrains.com/help/idea/2020.1/configuring-third-party-tools.html) and [External Tools](https://www.jetbrains.com/help/idea/2020.1/external-tools.html). |
| Run Another Configuration | Execute one of the existing run/debug configurations. In the **Choose Configuration to Execute** dialog, select a configuration to execute and then click **OK**. |
| Make | Compile the project or the module. Android Studio executes the [Make Module command](https://www.jetbrains.com/help/idea/2020.1/compilation-types.html#make_module) if the run/debug configuration specifies a particular module, or it executes the [Make Project command](https://www.jetbrains.com/help/idea/2020.1/compilation-types.html#make_project) if no modules are specified. |
| Make Project | Compile the project. Android Studio executes the [Make Project command](https://www.jetbrains.com/help/idea/2020.1/compilation-types.html#make_project). |
| Make, no error check | This option is the same as **Make**, except that Android Studio executes the run/debug configuration irrespective of the compilation result. |
| Build Artifacts | Unsupported in Android Studio. |
| Run Gradle task | Run a Gradle task. In the [dialog](https://www.jetbrains.com/help/idea/2020.1/create-run-debug-configuration-for-gradle-tasks.html) that opens, specify the details and then click **OK**. For more information, see [Gradle](https://www.jetbrains.com/help/idea/2020.1/gradle.html). |
| Gradle\-aware Make | Compile the project and run Gradle. |
| App Engine Gradle builder | The App Engine Gradle builder task syncs the project and then builds the module. |

## Default run/debug configuration templates

Android Studio provides default configuration templates to help you get started quickly. The following sections describe the templates that apply to Android development with Android Studio:

*   [Android App](#android-application)
*   [Android Tests](#android-tests)
*   [App Engine DevAppServer](#app-engine)

**Note:** Android Studio 2.1.*x* and lower had a Native Application template, which newer versions don't have. If you have a Native Application template in a project, Android Studio converts it to Android App when you load the project. A **Convert Project** dialog guides you through the process.

### Unsupported templates

The following unsupported templates come from IntelliJ IDEA and aren’t specific to Android development with Android Studio. For information about using these templates, follow the links to the IntelliJ IDEA documentation.

*   [Application](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-application.html)
*   [Compound](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-compound.html)
*   [Gradle](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-gradle.html)
*   [Groovy](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-groovy.html)
*   [JAR Application](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-jar-application.html)
*   [JUnit Test Discovery](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-junit-test-discovery.html)
*   [Java Scratch](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-java-scratch.html)
*   [Remote](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-remote.html)
*   [TestNG](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-testng.html)
*   [TestNG Test Discovery](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-testng-test-discovery.html)

### Name and Share configuration options

The **Name** and **Share** options apply to configurations only. When you [create a configuration](https://developer.android.com/studio/run/rundebugconfig#creating), give it a name, and if you want to share the configuration, check the **Share** checkbox. You cannot name and share a template.

Use the **Share** checkbox to make the run/debug configuration available to other team members through version control. If you select this option, when the project is checked into a version control system, the run/debug configuration is included when someone checks the project out.

Android Studio stores the shared run/debug configuration in individual XML files under the `project_directory/.idea/runConfigurations/` folder. For more information, see [Directory\-based Project Format](https://www.jetbrains.com/help/idea/2020.1/project.html#newprojectformat).

### Android App

You can run or debug Android apps and activities on virtual or hardware devices by using configurations based on this template.

#### General tab

In the **General** tab, you can specify installation, launch, and deployment options. The **Miscellaneous** tab also contains installation options.

| Field | Description |
| --- | --- |
| Module | Select a [module](https://developer.android.com/studio/projects#ApplicationModules) to apply this configuration to. |
| Installation Options: Deploy |
Select an option:

*   **Default APK** \- Build and deploy an APK for your [currently selected variant](https://developer.android.com/studio/run#changing-variant).
*   **APK from app bundle** \- Build and deploy your app from an [Android App Bundle](https://developer.android.com/guide/app-bundle). That is, Android Studio first converts your app project into an app bundle that includes all your app’s compiled code and resources. Then, from that app bundle, Android Studio generates only the APKs that are required to deploy your app to the connected device. You should typically use this option when testing the app bundle you intend to upload to Google Play because deploying from an app bundle extends the total build time.
*   **Custom Artifact** \- Unsupported in Android Studio.
*   **Nothing** \- Don’t install an APK on a device. For example, if you prefer to manually install the APK, you don’t need Android Studio to install it.

 |
| Installation Options: Deploy as instant app | If your app supports instant experiences—that is, you either add support for instant apps when you [create a new project](https://developer.android.com/studio/projects/create-project), or you create one or more [instant\-enabled feature modules](https://developer.android.com/studio/projects/dynamic-delivery#create_instant_enabled)—you can choose to deploy those instant\-enabled modules by checking the box next to **Deploy as instant app**. |
| Installation Options: Features to deploy | If your app includes [feature modules](https://developer.android.com/studio/projects/dynamic-delivery#dynamic_feature_modules), check the box next to each feature you want to include when deploying your app. You see this option only if your app includes feature modules.

**Note:** If you want to test downloading and installing feature modules on demand, you must do so after you publish your app bundle and then use the Play Console internal test track. To learn more, read [Upload your app bundle to the Play Console](https://developer.android.com/studio/publish/upload-bundle).

 |
| Installation Options: Install Flags | Type any adb `[pm install](https://developer.android.com/studio/command-line/adb#pm)` options you want to use. Format the options the same way that you would on the command line, but without a path. Here are some examples:

`-i foo.bar.baz -r /path/to/apk`

and

`-d -r`

Default: no options.

 |
| Launch Options: Launch |

Select an option:

*   **Nothing** \- Don’t launch anything when you select \*\*Run\*\* or \*\*Debug\*\*. However, if your app is already running and you select \*\*Debug\*\*, Android Studio attaches the debugger to your app process.
*   **Default Activity** \- Launch the activity you’ve marked as startup in the manifest. For example:

    <intent\-filter> <action  android:name\="android.intent.action.MAIN"  /> <category  android:name\="android.intent.category.LAUNCHER"  />
    </intent\-filter>

*   **Specified Activity** \- Launch a particular app activity in your module. When selected, the **Activity** field appears below, where you can type the name of the activity you want to launch, or click **More** to select an activity from a list.
*   **URL** \- Launch a URL that matches an intent filter in your app's manifest. When selected, the **URL** field appears below, where you can enter the URL.

    You must fill in this field to launch an [Android Instant App](https://developer.android.com/topic/instant-apps). You may also use this to test your [Android App Links](https://developer.android.com/studio/write/app-link-indexing).

 |
| Launch Options: Launch Flags | Type any adb `[am start](https://developer.android.com/studio/command-line/adb#am)` options you want to use. Format the options the same way that you would on the command line, but without an intent. For example:

`-W`

This option doesn’t appear if you chose a **Launch** value of **Nothing**.

Default: no options.

 |
| Deployment Target Options: Target |

Select an option:

*   **Open Select Deployment Target Dialog** \- Open the **Select Deployment Target** dialog to select a virtual or hardware device.
*   **USB Device** \- Use a hardware device connected to your development computer through a USB port. If there’s more than one, a dialog appears so you can select it.
*   **Emulator** \- Use a virtual device. In a configuration, you can select an AVD; otherwise, it just uses the first AVD in the list.

 |
|

Deployment Target Options: Use same device for future launches

 | By default this option is deselected so that every time you run an app, the **Select Deployment** dialog appears for you to select a device. When you select this option and then run an app, the **Select Deployment** dialog appears for you to select a device. Then, every time you run the app, it launches on the device you selected without displaying the **Select Deployment** dialog. To run the app on a different device, either deselect **Use same device for future launches**, or stop the app with **Run > Stop app** or **Stop** ![](https://developer.android.com/studio/images/buttons/toolbar-stop.png), and then start it again. The **Select Deployment** dialog will display so you can select a device. |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |

#### Miscellaneous tab

In the **Miscellaneous** tab, you can specify logcat, installation, launch, and deployment options. The **General** tab also contains installation options.

| Field | Description |
| --- | --- |
| Logcat: Show logcat automatically | When this option is selected, the **Logcat** window opens every time you successfully deploy and launch an app using this configuration. Default: selected. |
| Logcat: Clear log before launch | Select this option if you want Android Studio to remove data from previous sessions from the log file before starting the app. Default: deselected. |
| Installation Options: Skip installation if APK has not changed. | When selected, Android Studio doesn’t redeploy your APK if it detects that it’s unchanged. If you want Android Studio to force an install of the APK, even if it hasn’t changed, then deselect this option. Default: selected |
| Installation Options: Force stop running application before launching activity |
If selected, when Android Studio detects that it doesn't have to reinstall an APK because it hasn’t changed, it will force\-stop the app so that the app starts from the default launcher activity. If this option is deselected, Android Studio doesn’t force\-stop the app.

This option works with the previous option that controls whether an APK is installed or not. For both **Installation Options** fields, leave them at the default unless you explicitly want to force an install every time.

In some cases you might want to deselect this option. For example, if you’re writing an input method engine (IME), force\-stopping the app deselects it as the current keyboard, which you might not want.

Default: selected

 |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |

#### Debugger tab

Specify debug options in the **Debugger** tab.

For C and C++ code, Android Studio uses the [LLDB](http://lldb.llvm.org/) debugger. In addition to the normal Android Studio UI, the debugger window has an **LLDB** tab that lets you enter LLDB commands during debugging. You can enter the same commands that Android Studio uses to display information in the debugger UI, and you can perform additional operations.

For C and C++ projects, you can add symbol directories, as well as LLDB startup and post attach commands, in the **Debugger** tab. To do so, you use buttons similar to the following:

*   **Add** ![](https://developer.android.com/studio/images/rdc-iaddnewconfig_2-1.png) \- Add a directory or command.
*   **Remove** ![](https://developer.android.com/studio/images/rdc-iremove_2-1.png) \- Select a directory or command, and then click this button to remove the item.
*   **Up** ![](https://developer.android.com/studio/images/rdc-imoveup_2-1.png) \- Select a directory or command, and then click this button to move the item up in the list.
*   **Down** ![](https://developer.android.com/studio/images/rdc-imovedown_2-1.png) \- Select a directory or command, and then click this button to move the item down in the list.

See [Debug Your App](https://developer.android.com/studio/debug) for more information about debugging in Android Studio.

| Field | Description |
| --- | --- |
| Debug type |
Select one of the following options:

*   **Java** \- Debug Java code only.
*   **Auto** \- Let Android Studio choose the best debug type for your project.
*   **Native** \- Debug native C or C++ code.
*   **Dual** \- Debug Java and native code in two separate debug sessions.

The **Auto** option is recommended because it chooses the right debug type for your project.

 |
| Symbol Directories |

If you want to add symbol files to provide the debugger with C or C++ information generated outside of Android Studio, you can add one or more directories here. Android Studio preferentially uses any files within these directories over files generated by the [Android Plugin for Gradle](https://developer.android.com/studio/releases/gradle-plugin.html). The debugger searches the directories from top to bottom, in order, until it finds what it needs. It searches recursively through the files in the directory. To optimize the list and save time, put the directories used most often toward the top of the list.

If you specify a directory high in the tree, it can take longer to search all of the subdirectories. If you add a very specific directory, it takes less time to search. You need to find the right balance between speed and finding the files you need for debugging. For example, if you have a directory that contains subdirectories for different [Android Binary Interfaces](https://developer.android.com/ndk/guides/abis.html?hl=ID) (ABIs), you can choose to add a directory for a specific ABI or for all ABIs. Although it can take longer to search through the upper\-level directory, it’s also more foolproof if you decide to debug on a different device.

Note that you don’t have to add directories containing Gradle symbol files because the debugger uses them automatically.

 |
| LLDB Startup Commands |

Add LLDB commands that you want to execute before the debugger attaches to the process. For example, you can define settings for the environment, as shown in the following command:

`settings set target.max-memory-read-size 2048`

LLDB executes the commands in order from top to bottom.

 |
| LLDB Post Attach Commands |

Add LLDB commands that you want to execute right after the debugger attaches to the process. For example:

`process handle SIGPIPE -n true -p true -s false`

LLDB executes the commands in order from top to bottom.

 |
| Host working directory | Specify the LLDB working directory. |
| Logging: Target channels |

Specify LLDB log options. Android Studio sets the default options based on the team’s experience — so it’s not too slow but contains needed information for troubleshooting issues. The log is often requested for Android Studio bug reports. This default is

`lldb process:gdb-remote packets`

You can change the default to gather more information. For example, the following log options gather information about a specific `*platform*`:

`lldb process *platform*:gdb-remote packets`

For a complete list of log commands, enter the `log list` command from an LLDB shell window in Android Studio.

Android Studio places device logs in the following location, where `*[ApplicationId](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:applicationId)*` is the unique application ID that’s used in your built APK manifest, and identifies your app on your device and in the Google Play Store:

`/data/data/*ApplicationId*/lldb/log`

Or, if multiple users access a device, it places the logs in the following location, where `*[AndroidUserId](https://source.android.com/devices/tech/admin/multi-user.html)*` is a unique identifier for a user on the device:

`/data/user/*AndroidUserId*/*ApplicationId*/lldb/log`

For information about using LLDB for remote debugging, see [Remote Debugging](http://lldb.llvm.org/remote.html).

 |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |

#### Profiling tab

The **Enable advanced profiling** option must be checked to enable certain features in the [Android Profiler](https://developer.android.com/studio/profile/android-profiler) when your device is running Android 7.1 or lower.

### Android Tests

The test template that you should use depends on your [source set](https://developer.android.com/studio/build/build-variants#sourcesets). The Android Instrumented Tests template is for an [instrumented test](https://developer.android.com/training/testing/unit-testing). The Android JUnit template is for a local unit test.

**Note:** If you're using Firebase Test Lab to test on a variety of devices, you can use the Android JUnit template to define your instrumented tests. For more information, see [Run Your Tests with Firebase Test Lab](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests#run-ctl).

The following tab descriptions cover the tabs and fields for the Android Instrumented test template. For information about the tabs and fields for the Android JUnit test template, see the IntelliJ [Run/Debug Configuration: JUnit](https://www.jetbrains.com/help/idea/2020.1/run-debug-configuration-junit.html) page.

#### General tab

In **General** tab, you can specify test location, instrumentation runner, adb shell, and deployment options.

| Field | Description |
| --- | --- |
| Module | Select a [module](https://developer.android.com/studio/projects#ApplicationModules) to apply this configuration to. |
| Test |
In this area, specify the location of tests that you want to run:

*   **All in module** \- Launch all tests from the selected module.
*   **All in package** \- Launch all tests from the package specified in the **Package** field. Type the name, or click **More** to select the package from a dialog.
*   **Class** \- Launch tests of the class specified in the **Class** field. Type the name, or click **More** to select the class from a dialog.
*   **Method** \- Launch a test method. In the **Class** field, specify the class that contains the method. In the **Method** field, specify the method. Type the name, or click **More** to select the class or method from a dialog.

 |
| Specific instrumentation runner (optional) | Type the location of the [instrumentation runner](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:testInstrumentationRunner); click **More** to use a dialog. The `build.gradle` file specifies the location of the instrumentation runner; this value overrides it. The default is typically the `[AndroidJUnitRunner](https://developer.android.com/reference/androidx/test/runner/AndroidJUnitRunner)` class from [AndroidX Test](https://developer.android.com/training/testing). |
| Extra options |

Type any adb `[am instrument](https://developer.android.com/studio/command-line/adb#am)` options you want to use. Don’t type the component. For example, if you’re using `[AndroidJUnitRunner](https://developer.android.com/reference/androidx/test/runner/AndroidJUnitRunner)` from [AndroidX Test](https://developer.android.com/training/testing), you can use this field to pass additional options to the runner, such as `-e size small`.

**Default:** no options

 |
| Deployment Target Options: Target |

Select an option:

*   **Open Select Deployment Target Dialog** \- Open the **Select Deployment Target** dialog to select a virtual or hardware device.
*   **USB Device** \- Use a hardware device connected to your development computer through a USB port. If there’s more than one, a dialog appears so you can select it.
*   **Emulator** \- Use a virtual device. In a configuration, you can select an AVD; otherwise, it just uses the first AVD in the list.
*   **Firebase Test Lab Device Matrix** \- See [Run Your Tests with Firebase Test Lab](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests#run-ctl).

 |
| Deployment Target Options: Use same device for future launches | If you want to automatically use the device you chose through the **Select Deployment Target** dialog in the future, select this option. If the device isn’t available, you’ll receive a dialog. Default: deselected |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |

#### Miscellaneous tab

The **Miscellaneous** tab contains logcat and installation options.

| Field | Description |
| --- | --- |
| Logcat: Clear log before launch | Select this option if you want Android Studio to remove data from previous sessions from the log file before starting the app. Default: deselected. |
| Installation Options: Skip installation if APK has not changed | When selected, Android Studio doesn’t redeploy your APK if it detects that it’s unchanged. If you want Android Studio to force an install of the APK, even if it hasn’t changed, then deselect this option. Default: selected |
| Installation Options: Force stop running application before launching activity |
If selected, when Android Studio detects that it doesn't have to reinstall an APK because it hasn’t changed, it will force\-stop the app so that the app starts from the default launcher activity. If this option is deselected, Android Studio doesn’t force\-stop the app.

This option works in conjunction with the previous option that controls whether an APK is installed or not. For both **Installation Options** fields, leave them at the default unless you explicitly want to force an install every time.

In some cases you might want to deselect this option. For example, if you’re writing an input method engine (IME), force\-stopping the app deselects it as the current keyboard, which you might not want.

Default: selected

 |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |

#### Debugger tab

Specify debug options in the **Debugger** tab.

For C and C++ code, Android Studio uses the [LLDB](http://lldb.llvm.org/) debugger. In addition to the normal Android Studio UI, the debugger window has an **LLDB** tab that lets you enter LLDB commands during debugging. You can enter the same commands that Android Studio uses to display information in the debugger UI, and you can perform additional operations.

For C and C++ projects, you can add symbol directories, as well as LLDB startup and post attach commands, in the **Debugger** tab. To do so, you use buttons similar to the following:

*   **Add** ![](https://developer.android.com/studio/images/rdc-iaddnewconfig_2-1.png) \- Add a directory or command.
*   **Remove** ![](https://developer.android.com/studio/images/rdc-iremove_2-1.png) \- Select a directory or command, and then click this button to remove the item.
*   **Up** ![](https://developer.android.com/studio/images/rdc-imoveup_2-1.png) \- Select a directory or command, and then click this button to move the item up in the list.
*   **Down** ![](https://developer.android.com/studio/images/rdc-imovedown_2-1.png) \- Select a directory or command, and then click this button to move the item down in the list.

See [Debug Your App](https://developer.android.com/studio/debug) for more information about debugging in Android Studio.

| Field | Description |
| --- | --- |
| Debug type |
Select one of the following options:

*   **Java** \- Debug Java code only.
*   **Auto** \- Let Android Studio choose the best debug type for your project.
*   **Native** \- Debug native C or C++ code.
*   **Dual** \- Debug Java and native code in two separate debug sessions.

The **Auto** option is recommended because it chooses the right debug type for your project.

 |
| Symbol Directories |

If you want to add symbol files to provide the debugger with C or C++ information generated outside of Android Studio, you can add one or more directories here. Android Studio preferentially uses any files within these directories over files generated by the [Android Plugin for Gradle](https://developer.android.com/studio/releases/gradle-plugin.html). The debugger searches the directories from top to bottom, in order, until it finds what it needs. It searches recursively through the files in the directory. To optimize the list and save time, put the directories used most often toward the top of the list.

If you specify a directory high in the tree, it can take longer to search all of the subdirectories. If you add a very specific directory, it takes less time to search. You need to find the right balance between speed and finding the files you need for debugging. For example, if you have a directory that contains subdirectories for different [Android Binary Interfaces](https://developer.android.com/ndk/guides/abis.html?hl=ID) (ABIs), you can choose to add a directory for a specific ABI or for all ABIs. Although it can take longer to search through the upper\-level directory, it’s also more foolproof if you decide to debug on a different device.

Note that you don’t have to add directories containing Gradle symbol files because the debugger uses them automatically.

 |
| LLDB Startup Commands |

Add LLDB commands that you want to execute before the debugger attaches to the process. For example, you can define settings for the environment, as shown in the following command:

`settings set target.max-memory-read-size 2048`

LLDB executes the commands in order from top to bottom.

 |
| LLDB Post Attach Commands |

Add LLDB commands that you want to execute right after the debugger attaches to the process. For example:

`process handle SIGPIPE -n true -p true -s false`

LLDB executes the commands in order from top to bottom.

 |
| Host working directory | Specify the LLDB working directory. |
| Logging: Target channels |

Specify LLDB log options. Android Studio sets the default options based on the team’s experience — so it’s not too slow but contains needed information for troubleshooting issues. The log is often requested for Android Studio bug reports. This default is

`lldb process:gdb-remote packets`

You can change the default to gather more information. For example, the following log options gather information about a specific `*platform*`:

`lldb process *platform*:gdb-remote packets`

For a complete list of log commands, enter the `log list` command from an LLDB shell window in Android Studio.

Android Studio places device logs in the following location, where `*[ApplicationId](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:applicationId)*` is the unique application ID that’s used in your built APK manifest, and identifies your app on your device and in the Google Play Store:

`/data/data/*ApplicationId*/lldb/log`

Or, if multiple users access a device, it places the logs in the following location, where `*[AndroidUserId](https://source.android.com/devices/tech/admin/multi-user.html)*` is a unique identifier for a user on the device:

`/data/user/*AndroidUserId*/*ApplicationId*/lldb/log`

For information about using LLDB for remote debugging, see [Remote Debugging](http://lldb.llvm.org/remote.html).

 |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |

### App Engine DevAppServer

This run/debug configuration applies to the Google Cloud Platform. For more information, see [Running, Testing, and Deploying the Backend](https://cloud.google.com/tools/android-studio/app_engine/run_test_deploy). When you follow these steps and sync your project to the `build.gradle` file, Android Studio creates an App Engine DevAppServer configuration for you.

Note that the IntellJ IDEA [App Engine Server](https://www.jetbrains.com/help/pycharm/2020.1/run-debug-configuration-app-engine-server.html) template is a different template that’s not available in Android Studio.

| Field | Description |
| --- | --- |
| Single instance only | If you want to make sure that only one instance of the run/debug configuration is currently executed, select this option. It doesn't allow multiple runs of the same configuration at the same time. Default: selected |
| Module | Select a [module](https://developer.android.com/studio/projects#ApplicationModules) to apply this configuration to. |
| Synchronize with build.gradle configuration | If you add an App Engine module and sync to the `build.gradle` file, the App Engine DevAppServer configuration fields are filled in for you (recommended). Selecting **File > Sync Project with Gradle Files** also syncs the project. Default: selected |
| App Engine SDK | Type a path to a [Google App Engine SDK](https://cloud.google.com/appengine/downloads#Google_App_Engine_SDK_for_Java) for Java on the local machine. Click **...** to select it from a dialog. |
| War Path | Type a path to the Web Application Archive (WAR) directory of the app you’re deploying on the local development server. Click **...** to select it from a dialog. |
| VM Args |
Specify the command\-line options you want to pass to the VM for launching the DevAppServer. When specifying the options:

*   Use spaces to separate different options.
*   For options that have spaces, enclose the space in quotation marks ( `"` `"`).
*   If an option includes quotation marks, add a backslash before the quotation mark (`\"`).

For more information about VM options, see the documentation for your J2SE version, such as [`java` JDK 7](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) and [`java` JDK 8](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html).

Default: no options

 |
| Server Address | Type the host [address](https://cloud.google.com/appengine/docs/java/tools/devserver#The_Development_Console) to use for the server. You might need to provide the address to be able to access the development server from another computer on your network. An address of 0.0.0.0 allows both localhost access and hostname access. Default: localhost |
| Server Port | Type the [port](https://cloud.google.com/appengine/docs/java/tools/devserver#The_Development_Console) number to use for the server. Default: 8080 |
| Disable Check for App Engine SDK Updates | If given, the development server will [not contact App Engine](https://cloud.google.com/appengine/docs/java/tools/devserver#The_Development_Console) to check for the availability of a new release of the SDK. By default, the server checks for a new version on startup, and prints a message if a new version is available. |
| Before Launch | See [Defining Before Launch Operations](#definingbefore). |
