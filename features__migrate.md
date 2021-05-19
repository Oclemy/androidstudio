# Migrate to Android Studio

*   [Table of contents](#top_of_page)
*   [Android Studio basics](#android_studio_basics)
    *   [Project and module organization](#project_and_module_organization)
    *   [Gradle\-based build system](#gradle-based_build_system)
    *   [Dependencies](#dependencies)
    *   [Test code](#test_code)
*   [Migrating from Eclipse](#migrating_from_eclipse)
    *   [Migration prerequisites](#migration_prerequisites)
    *   [Import Eclipse projects to Android Studio](#import_eclipse_projects_to_android_studio)
*   [Migrating from IntelliJ](#migrating_from_intellij)
    *   [Import a Gradle\-based IntelliJ project](#import_a_gradle-based_intellij_project)
    *   [Import a non\-Gradle IntelliJ project](#import_a_non-gradle_intellij_project)
*   [Next steps](#next_steps)
    *   [Configure version control](#configure_version_control)
    *   [Android Support repository and Google Play Services repository](#android_support_repository_and_google_play_services_repository)
    *   [App signing](#app_signing)
    *   [Adjusting Android Studio’s maximum heap size](#adjusting_android_studio’s_maximum_heap_size)
    *   [Software updates](#software_updates)

Migrating your projects to Android Studio requires adapting to a new project structure, build system, and IDE functionality. If you are migrating an Android project from Eclipse, Android Studio provides an import tool so you can quickly move your existing code into Android Studio projects and Gradle\-based build files. For more information, see [Migrating from Eclipse](#migrate-eclipse).

If you are migrating from IntelliJ and your project already uses Gradle, you can simply open your existing project from Android Studio. If you are using IntelliJ, but your project does not already use Gradle, you will need to do a little bit of manual preparation before you can import your project into Android Studio. For more information, see [Migrating from IntelliJ](#migrate-intellij).

## Android Studio basics

Here are some of the key differences you should be aware of as you prepare to migrate to Android Studio.

### Project and module organization

Android Studio is based on the [IntelliJ IDEA](https://www.jetbrains.com/idea/) IDE. To become familiar with the IDE basics, such as navigation, code completion, and keyboard shortcuts, see [Meet Android Studio](https://developer.android.com/studio/intro).

Android Studio does not use workspaces, so separate projects open in separate Android Studio windows. Android Studio organizes code into projects, which contain everything that defines your Android app, from app source code to build configurations and test code. Each project contains one or more modules, which allow you to divide your project into discrete units of functionality. Modules can be independently built, tested, and debugged.

For more information about Android Studio projects and modules, see the [Projects overview](https://developer.android.com/tools/projects).

### Gradle\-based build system

Android Studio’s build system is based on [Gradle](http://gradle.org) and uses build configuration files written in Groovy syntax for ease of extensibility and customization.

Gradle\-based projects offer significant features for Android development, including the following:

*   Support for binary libraries (AARs). You no longer need to copy library sources into your own projects, you can simply [declare a dependency](https://developer.android.com/studio/build/build-variants#dependencies) and the library is automatically downloaded and merged into your project. This includes automatically merging in resources, manifest entries, proguard exclusion rules, custom lint rules, and so on at build time.
*   Support for [build variants](https://developer.android.com/studio/build/build-variants). For example, build variants let you build different versions of your app (such as a free version and a pro version) from the same project.
*   Easy [build configuration](https://developer.android.com/studio/build) and customization. For example, you can pull version names and version codes from Git tags as part of the build.
*   Gradle can be used from the IDE but also from the [command line](https://developer.android.com/studio/build/building-cmdline) and from continuous integration servers like Jenkins, providing the same build everywhere, every time.

For more information about using and configuring Gradle, see [Configure your build](https://developer.android.com/tools/building/plugin-for-gradle).

### Dependencies

Library dependencies in Android Studio use Gradle dependency declarations and Maven dependencies for well\-known local source and binary libraries with Maven coordinates. For more information see, [Configure build variants](https://developer.android.com/tools/building/configuring-gradle#dependencies).

### Test code

With Eclipse ADT, instrumentation tests are written in separate projects and integrated through the `<instrumentation>` element in your manifest file. Android Studio provides an `androidTest/` directory in your project's main sourceset so you can easily add and maintain your instrumentation test code within the same project view. Android Studio also provides a `test/` directory in your project's main sourceset for local JVM tests.

## Migrating from Eclipse

Android Studio offers an automated import tool for existing Android projects created using Eclipse.

### Migration prerequisites

Before migrating your app from Eclipse to Android Studio, review the following steps to make sure your project is ready for conversion, and verify you have the tool configuration you need in Android Studio:

#### In Eclipse ADT:

*   Make sure the Eclipse ADT root directory contains the `AndroidManifest.xml` file. Also, the root directory must contain either the `.project` and `.classpath` files from Eclipse or the `res/` and `src/` directories.
*   Build your project to ensure your latest workspace and project updates are saved and included in the import.
*   Comment out any references to Eclipse ADT workspace library files in the `project.properties` or`.classpath` files for import. You can add these references in the `build.gradle` file after the import. For more information, see [Configure your build](https://developer.android.com/tools/building/plugin-for-gradle).
*   It may be useful to record your workspace directory, path variables, and any actual path maps that could be used to specify any unresolved relative paths, path variables, and linked resource references. Android Studio allows you to manually specify any unresolved paths during the import process.

#### In Android Studio:

*   If you don't have it, [download Android Studio](https://developer.android.com/studio). If you do have Android Studio, verify that it is the latest stable release by clicking **Help > Check for Updates** (on Mac, **Android Studio > Check for Updates**).
*   Because Android Studio does not migrate any third\-party Eclipse ADT plugins, make a note of any third\-party plugins you use in Eclipse. You can check for equivalent features in Android Studio or search for a compatible plugin in the [IntelliJ Android Studio Plugins](https://plugins.jetbrains.com/?androidstudio) repository. Use the **File > Settings > Plugins** menu option to manage plugins in Android Studio.
*   If you plan to run Android Studio behind a firewall, be sure to set the proxy settings for Android Studio and the SDK Manager. Android Studio requires an internet connection for Setup Wizard synchronization, 3rd\-party library access, access to remote repositories, [Gradle](http://www.gradle.org/) initialization and synchronization, and Android Studio version updates. For more information, see [Proxy settings](https://developer.android.com/tools/studio/studio-config#proxy).

### Import Eclipse projects to Android Studio

You should decide how you will import your existing Eclipse ADT projects depending on their structure:

*   If you have multiple related projects sharing the same workspace in Eclipse ADT, import the first project [as a project](#import-project), then add subsequent related projects [as modules within that project](#import-module).
*   If your Eclipse ADT projects share dependencies within the same workspace but are not otherwise related, import each Eclipse ADT project individually into Android Studio as a separate project. Android Studio maintains the shared dependencies across the newly created projects as part of the import process.
*   If your Eclipse ADT project includes native (C/C++) libraries, see [Link Gradle to your native library](https://developer.android.com/studio/projects/gradle-external-native-builds) for instructions on how to include your native libraries as Gradle build dependencies.

#### Import as a project:

1.  Start Android Studio and close any open Android Studio projects.
2.  From the Android Studio menu click **File > New > Import Project**.
    *   Alternatively, from the Welcome screen, click **Import project (Eclipse ADT, Gradle, etc.)**.
3.  Select the Eclipse ADT project folder with the `AndroidManifest.xml` file and click **Ok**.

    ![](https://developer.android.com/studio/images/intro/select-project-to-import_2-1_2x.png)

4.  Select the destination folder and click **Next**.

    ![](https://developer.android.com/studio/images/intro/project-destination-folder_2-1_2x.png)

5.  Select the import options and click **Finish**.
6.  The import process prompts you to migrate any library and project dependencies to Android Studio, and add the dependency declarations to the `build.gradle` file. See [Create an Android library](https://developer.android.com/studio/projects/android-library) for more information about this process.

    The import process also replaces any well\-known source libraries, binary libraries, and JAR files that have known Maven coordinates with Maven dependencies, so you no longer need to maintain these dependencies manually. The import options also allow you to enter your workspace directory and any actual path maps to handle any unresolved relative paths, path variables, and linked resource references.

    ![](https://developer.android.com/studio/images/intro/select-import-options_2-1_2x.png)

7.  Android Studio imports the app and displays the project import summary. Review the summary for details about the project restructuring and the import process.

    ![](https://developer.android.com/studio/images/intro/import-summary_2-1_2x.png)

After importing the project from Eclipse ADT into Android Studio, each app module folder in Android Studio contains the complete source set for that module, including the `src/main/` and `src/androidTest/` directories, resources, build file, and Android manifest. Before starting app development, you should resolve any issues shown in the project import summary to make sure the project re\-structuring and import process completed properly.

#### Import as a module:

1.  Start Android Studio and open the project you’d like to add the module to.
2.  From the Android Studio menu click **File > New > Import Module**.
3.  Select the Eclipse ADT project folder with the `AndroidManifest.xml` file and click **Ok**.
4.  Modify the module name if desired, and click **Next**.
5.  The import process prompts you to migrate any library and project dependencies to Android Studio, and add the dependency declarations to the `build.gradle` file. For more about migrating library and project dependencies, see [Create an Android library](https://developer.android.com/studio/projects/android-library). The import process also replaces any well\-known source libraries, binary libraries, and JAR files that have known Maven coordinates with Maven dependencies, so you no longer need to maintain these dependencies manually. The import options also allow you to enter your workspace directory and any actual path maps to handle any unresolved relative paths, path variables, and linked resource references.
6.  Click **Finish**.

#### Validate imported projects

After completing the import process, use the Android Studio Build and Run menu options to build your project and verify the output. If your project is not building properly, check the following settings:

*   Verify the installed versions of your tools match the settings for your Eclipse project by opening the [SDK Manager](https://developer.android.com/tools/help/sdk-manager#sdk-manager) (click the Android SDK Manager button in Android Studio or **Tools > SDK Manager**). Android Studio inherits the SDK Manager and JDK settings from your imported Eclipse project.
*   To verify additional Android Studio Settings, click **File > Project Structure** and inspect the following:

    *   Under **SDK Location** verify Android Studio has access to the correct SDK, NDK, and JDK locations and versions.

    **Note:** If you used the default settings, Eclipse ADT installed Android SDK in `User\user-name\android-sdks\` on Windows, and in `Users/user-name/Library/Android/sdk/` on Mac.

    *   Under **Project** verify the Gradle version, Android plugin version, and related repositories.
    *   Under **Modules** verify the app and module settings, such as signing configuration and library dependencies.
*   If your project depends on another project, make sure that dependency is defined properly in the `build.gradle` file in the app module folder. For more information about defining dependencies, see [Configure build variants](https://developer.android.com/tools/building/configuring-gradle#dependencies).

If there still are unexpected issues when building and running your project in Android Studio after you have checked these settings, consider modifying the Eclipse ADT project and re\-starting the import process.

**Note:** Importing an Eclipse ADT project to Android Studio creates a new Android Studio project and does not impact the existing Eclipse ADT project.

## Migrating from IntelliJ

If your IntelliJ project uses the Gradle build system, you can automatically import your project directly into Android Studio. If your IntelliJ project uses Maven or another build system, you need to set it up to work with Gradle before you can migrate to Android Studio.

### Import a Gradle\-based IntelliJ project

If you are already using Gradle with your IntelliJ project, you can open it in Android Studio using the following steps:

1.  Click **File > New > Import Project**.
2.  Select your IntelliJ project directory, and click **OK**. Your project will open in Android Studio.

### Import a non\-Gradle IntelliJ project

If your IntelliJ project does not already use the Gradle build system, you have two options for importing your project into Android Studio:

*   Create a new empty Android Studio project and copy your existing source code into the directories associated with the new project. For more information, see [Migrate by creating a new empty project](#empty-project).

*   Manually create a new Gradle build file for your project and then import the project and new build file into Android Studio. For more information see [Migrate by creating a custom Gradle build file](#custom-gradle).

#### Migrate by creating a new empty project

To migrate your project into Android Studio by creating a new empty project and copying your source files into the new directories, proceed as follows:

1.  Open Android Studio, and click **File > New > New Project**.
2.  Enter a name for your app project and specify the location where it should be created, and then click **Next**.
3.  Select the form factors your app will run on, and then click **Next**.
4.  Click **Add No Activity**, and then click **Finish**.
5.  In the **Project** tool window, click the arrow to open the view dropdown, and select the **Project** view to see and explore the organization of your new Android Studio project. To read more about changing views and how Android Studio structures projects, see [Project Files](https://developer.android.com/studio/projects#ProjectFiles).
6.  Navigate to the location you selected for your new project and move the code, unit tests, instrumentation tests, and resources from your old project directories into the correct locations in your new project structure.
7.  In Android Studio, click **File > Project Structure** to open the Project Structure dialog. Ensure that your app's module is selected in the left pane.
8.  Make any necessary modifications in the **Properties** tab for your project (for example, modifying the `minSdkVersion` or `targetSdkVersion`).
9.  Click **Dependencies** and add any libraries your project depends on as Gradle dependencies. To add a new dependency, click **Add** ![](https://developer.android.com/studio/images/buttons/ic_plus.png), then select the type of dependency you would like to add and follow the prompts.
10.  Click **OK** to save your modifications.
11.  Click **Build > Make Project** to test building your project, and resolve any outstanding errors.

#### Migrate by creating a custom Gradle build file

To migrate your project into Android Studio by creating a new Gradle build file to point to your existing source files, proceed as follows:

1.  Before you begin, be sure to back up your project files in a separate location, as the migration process will modify the contents of your project in place.
2.  Next, create a file in your project directory called `build.gradle`. The `build.gradle` file will contain all the information required for Gradle to run your build.

    By default, Android Studio expects your project to be organized as shown in figure 1.

    ![](https://developer.android.com/images/tools/studio/project-structure_2x.png)

    **Figure 1.** The default project structure for an Android app module.

    Since your IntelliJ project does not use the same structure, your `build.gradle` file needs to point the source directories for the build to your existing folders (for example, `res/` and `src/`) instead of the default new directory structure. The following example `build.gradle` file includes the basic setup for a Gradle build, as well as a `sourceSets{}` block inside the `android{}` block to define the correct source directories and move the tests and build types to avoid naming conflicts. Copy the code block below into your `build.gradle` file and make any changes necessary to work with your existing project setup. For example, you may have additional dependencies to include, be using a different target SDK version, or need to specify different locations for your source directories.

    // This buildscript{} block configures the code driving the buildbuildscript {
       /\*\*
        \* The nested repositories{} block declares that this build uses the
        \* jcenter repository.
        \*/repositories {
            jcenter() }

       /\*\*
        \* This block declares a dependency on the 4.0.0 version
        \* of the Gradle plugin for the buildscript.
        \*/dependencies {classpath 'com.android.tools.build:gradle:4.0.0' }
    }

    /\*\*
     \* This line applies the com.android.application plugin. Note that you should
     \* only apply the com.android.application plugin. Applying the Java plugin as
     \* well will result in a build error.
     \*/
    apply plugin:  'com.android.application'

    /\*\*
     \* This dependencies block includes any dependencies for the project itself. The
     \* following line includes all the JAR files in the libs directory.
     \*/dependencies {
        compile fileTree(dir:  'libs', include:  \['\*.jar'\]) // Add other library dependencies here (see the next step)
    }

    /\*\*
     \* The android{} block configures all of the parameters for the Android build.
     \* You must provide a value for at least the compilation target.
     \*/android {compileSdkVersion 28 /\*\*
        \* This nested sourceSets block points the source code directories to the
        \* existing folders in the project, instead of using the default new
        \* organization.
        \*/sourceSets {main {
                manifest.srcFile 'AndroidManifest.xml'
                java.srcDirs \=  \['src'\]
                resources.srcDirs \=  \['src'\]
                aidl.srcDirs \=  \['src'\]
                renderscript.srcDirs \=  \['src'\]
                res.srcDirs \=  \['res'\]
                assets.srcDirs \=  \['assets'\] } // Move the tests to tests/java, tests/res, etc...
            instrumentTest.setRoot('tests')

           /\*\*
            \* Move the build types to build\-types/<type>
            \* For instance, build\-types/debug/java, build\-types/debug/AndroidManifest.xml, ...
            \* This moves them out of them default location under src/<type>/... which would
            \* conflict with src/ being used by the main source set.
            \* Adding new build types or product flavors should be accompanied
            \* by a similar customization.
            \*/
            debug.setRoot('build\-types/debug')
            release.setRoot('build\-types/release')
         }
    }

    For more information about setting up and customizing a Gradle build file, read [Configure your build.](https://developer.android.com/studio/build)
3.  Next, identify which library projects you are using. With Gradle, you no longer need to add these libraries as source code projects, you can refer to them in the `dependencies{}` block of your build file instead. The build system then handles these libraries for you, including downloading libraries, merging in resources, and merging manifest entries. The following example adds the declaration statements for Google Play Services and a number of support libraries to the `dependencies{}` block shown in the example build file above.

    ...dependencies {
        compile fileTree(dir:  'libs', include:  \['\*.jar'\]) // Google Play Servicescompile 'com.google.android.gms:play\-services:9.8.0' // Support Librariescompile 'com.android.support:appcompat\-v7:28.0.0'compile 'com.android.support:cardview\-v7:28.0.0'compile 'com.android.support:design:28.0.0'compile 'com.android.support:gridlayout\-v7:28.0.0'compile 'com.android.support:leanback\-v17:28.0.0'compile 'com.android.support:mediarouter\-v7:28.0.0'compile 'com.android.support:palette\-v7:28.0.0'compile 'com.android.support:recyclerview\-v7:28.0.0'compile 'com.android.support:support\-annotations:28.0.0'compile 'com.android.support:support\-v13:28.0.0'compile 'com.android.support:support\-v4:28.0.0' // Note: these libraries require the "Google Repository" and "Android Repository" //       to be installed via the SDK manager.
    }

    For help determining the correct declaration statements for your libraries, [Gradle, please](http://gradleplease.appspot.com/) can provide you with the correct declaration statements based on Maven Central.
4.  Save your `build.gradle` file, then close the project in IntelliJ. Navigate to your project directory, and delete the `.idea` directory and any `.iml` files inside your project.
5.  Launch Android Studio, and click **File > New > Import Project**.
6.  Locate your project directory, click the `build.gradle` file you created above to select it, and then click **OK** to import your project.
7.  Click **Build > Make Project** to test your build file by building your project and address any errors you find.

## Next steps

Once you have migrated your project to Android Studio, learn more about building with Gradle and running your app in Android Studio by reading [Build and run your app](https://developer.android.com/studio/run).

Depending on your project and workflow, you may also wish to read more about using version control, managing dependencies, signing and packaging your app, or configuring and updating Android Studio. To get started using Android Studio, read [Meet Android Studio](https://developer.android.com/studio/intro).

### Configure version control

Android Studio supports a variety of version control systems, including Git, GitHub, CVS, Mercurial, Subversion, and Google Cloud Source Repositories.

After importing your app into Android Studio, use the Android Studio VCS menu options to enable VCS support for the desired version control system, create a repository, import the new files into version control, and perform other version control operations:

1.  From the Android Studio VCS menu, click **Enable Version Control Integration**.
2.  Select a version control system to associate with the project root from the drop down menu, then click **OK**. The VCS menu now displays a number of version control options based on the system you selected.

**Note:** You can also use the **File > Settings > Version Control** menu option to set up and modify the version control settings.

For more information about working with Version Control see [IntelliJ Version Control Reference](https://www.jetbrains.com/help/idea/2020.1/version-control-reference.html).

### Android Support repository and Google Play Services repository

While Eclipse ADT uses the Android Support Library and Google Play services Library, Android Studio replaces these libraries during the import process with the Android Support Repository and Google Repository to maintain compatible functionality and support new Android features. Android Studio adds these dependencies as Maven dependencies using the known Maven coordinates, so these dependencies do not require manual updates.

In Eclipse, in order to use a Support Library, you must modify your project's classpath dependencies within your development environment for each Support Library you want to use. In Android Studio, you no longer need to copy library sources into your own projects, you can simply declare a dependency and the library is automatically downloaded and merged into your project. This includes automatically merging in resources, manifest entries, ProGuard exclusion rules, and custom lint rules at build time. For more information about dependencies, see [Configure build variants](https://developer.android.com/studio/build/build-variants).

### App signing

If your app used a debug certificate in Eclipse ADT, Android Studio continues to reference that certificate. Otherwise, the debug configuration uses the Android Studio generated debug keystore, with a known password and a default key with a known password located in `$HOME/.android/debug.keystore`. The debug build type is set to use this debug configuration automatically when you run or debug your project from Android Studio.

When building your app for release, Android Studio applies the release certificate used in Eclipse ADT. If no release certificate was located during the import process, add the release signing configuration to the `build.gradle` file or use the **Build > Generate Signed APK** menu option to open the *Generate Signed APK Wizard*. For more information about signing your app, see [Sign your app](https://developer.android.com/studio/publish/app-signing).

### Adjusting Android Studio’s maximum heap size

By default, Android Studio has a maximum heap size of 1280MB. If you are working on a large project, or your system has a lot of RAM, you can improve performance by [increasing the maximum heap size](https://developer.android.com/studio/intro/studio-config#adjusting_heap_size).

### Software updates

Android Studio updates separately from the Gradle plugin, the build tools, and the SDK tools. You can specify which versions you would like to use with Android Studio.

By default, Android Studio provides automatic updates whenever a new stable version is released, but you can choose to update more frequently and also receive preview or beta versions.

For more information about updating Android Studio and using preview and beta versions, see [Stay updated](https://developer.android.com/studio/intro/update).