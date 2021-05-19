# Create a project

- [Create a project](#create-a-project)
  - [Choose your project](#choose-your-project)
  - [Configure your project](#configure-your-project)
  - [Import an existing project](#import-an-existing-project)

Android Studio makes it easy to create Android apps for various form factors, such as handsets, tablets, TV, and Wear devices. This page shows you how to start a new Android app project or import an existing project.

If you don't have a project opened, Android Studio shows the Welcome screen, where you can create a new project by clicking **Start a new Android Studio project**.

If you do have a project opened, you start creating a new project by selecting **File > New > New Project** from the main menu.

You should then see the **Create New Project** wizard, which lets you choose the type of project you want to create and populates with code and resources to get you started. This page guides you through creating a new project using the **Create New Project** wizard.

## Choose your project

In the **Choose your project** screen that appears, you can select the type of project you want to create from categories of device form factors, which are shown as tabs near the top of the wizard. For example, figure 1 shows a project with a basic Android Activity for a phone and tablet selected.

![](https://developer.android.com/studio/images/projects/new-project-wizard-choose_2x.png)

**Figure 1.** In the first screen of the wizard, choose the type of project you want to create.

By selecting the type of project you want to create, Android Studio can include sample code and resources to help you get started.

After you make a selection, click **Next**.

## Configure your project

The next step is to configure some settings and create your new project, as described below and shown in figure 2. If you're creating a **Native C++** project, you can learn more about the options you need to configure by reading [Create a new project with C/C++ support](https://developer.android.com/studio/projects/add-native-code#new-project).

![](https://developer.android.com/studio/images/projects/new-project-wizard-configure-2x.png)

**Figure 2.** Configure your new project with a few settings.

1.  Specify the **Name** of your project.
2.  Specify the **Package name**. By default, this package name also becomes your [application ID](https://developer.android.com/studio/build/application-id), which you can change later.
3.  Specify the **Save location** where you want to locally store your project.
4.  Select the **Language** you want Android Studio to use when creating sample code for your new project. Keep in mind, you are not limited to using only that language creating the project.
5.  Select the **Minimum API level** you want your app to support. When you select a lower API level, your app can rely on fewer modern Android APIs. However, a larger percentage of Android devices are able to run your app. The opposite is true when selecting a higher API level. If you want to see more data to help you decide, click **Help me choose**.
6.  If you want your project to use AndroidX libraries by default, which are improved replacements of the Android Support libraries, check the box next to **Use AndroidX artifacts**. To learn more, read the [AndroidX overview](https://developer.android.com/jetpack/androidx).
7.  When you're ready to create your project, click **Finish**.

Android Studio creates your new project with some basic code and resources to get you started. If you later decide to add support for a different device form factor, you can [add a module](https://developer.android.com/studio/projects/add-app-module) to your project later. And if you want to share code and resources between modules, you can do so by creating an [Android library](https://developer.android.com/studio/projects/android-library).

For more information about the Android project structure and module types, read the [Projects overview](https://developer.android.com/studio/projects). If you're new to Android development, altogether, start with [Getting Started on Android](https://developer.android.com/training).

## Import an existing project

To import an existing, local project into Android Studio, proceed as follows:

1.  Click **File** > **New** > **Import Project**.
2.  In the window that appears, navigate to the root directory of the project you want to import.
3.  Click **OK**.

Android Studio then opens the project in a new IDE window and indexes its contents.

If you are importing a project from version control, use the **File** > **New** > **Project from Version Control** menu. For more information about importing projects from version control, read IntelliJ’s [VCS\-Specific Procedures](https://www.jetbrains.com/help/idea/version-control-integration.html).

If you are importing an existing Eclipse ADT project into Android Studio, how you add the project depends on its structure. To read more about importing projects from Eclipse, see [Migrating from Eclipse](https://developer.android.com/studio/intro/migrate#migrate-eclipse).