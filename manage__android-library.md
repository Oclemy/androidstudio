# Create an Android library

- [Create an Android library](#create-an-android-library)
  - [Create a library module](#create-a-library-module)
    - [Convert an app module to a library module](#convert-an-app-module-to-a-library-module)
  - [Add your library as a dependency](#add-your-library-as-a-dependency)
  - [Choose resources to make public](#choose-resources-to-make-public)
  - [Development considerations for library modules](#development-considerations-for-library-modules)
  - [Anatomy of an AAR file](#anatomy-of-an-aar-file)

An Android library is structurally the same as an Android app module. It can include everything needed to build an app, including source code, resource files, and an Android manifest. However, instead of compiling into an APK that runs on a device, an Android library compiles into an Android Archive (AAR) file that you can use as a dependency for an Android app module. Unlike JAR files, AAR files offer the following functionality for Android applications:

*   AAR files can contain Android resources and a manifest file, which allows you to bundle in shared resources like layouts and drawables in addition to Java classes and methods.
*   AAR files can [contain C/C++ libraries](https://developer.android.com/studio/build/native-dependencies) for use by the app module's C/C++ code.

A library module is useful in the following situations:

*   When you're building multiple apps that use some of the same components, such as activities, services, or UI layouts.
*   When you're building an app that exists in multiple APK variations, such as a free and paid version and you need the same core components in both.

In either case, simply move the files you want to reuse into a library module then add the library as a dependency for each app module. This page teaches you how to do both.

## Create a library module

To create a new library module in your project, proceed as follows:

1.  Click **File > New > New Module**.
2.  In the **Create New Module** window that appears, click **Android Library**, then click **Next**.

    There's also an option to create a **Java Library**, which builds a traditional JAR file. While a JAR file is useful for many projects— especially when you want to share code with other platforms—it does not allow you to include Android resources or manifest files, which is very useful for code reuse in Android projects. So this guide focuses on creating Android libraries.

3.  Give your library a name and select a minimum SDK version for the code in the library, then click **Finish**.

Once the Gradle project sync completes, the library module appears in the **Project** panel on the left. If you don't see the new module folder, make sure it's displaying the [Android view](https://developer.android.com/studio/projects#ProjectFiles).

### Convert an app module to a library module

If you have an existing app module with all the code you want to reuse, you can turn it into a library module as follows:

1.  Open the module\-level `build.gradle` file.
2.  Delete the line for the `applicationId`. Only an Android app module can define this.
3.  At the top of the file, you should see the following:

    apply plugin:  'com.android.application'

    Change it to the following:

    apply plugin:  'com.android.library'

4.  Save the file and click **File > Sync Project with Gradle Files**.

That's it. The entire structure of the module remains the same, but it now operates as an Android library and the build will now create an AAR file instead of an APK.

When you want to build the AAR file, select the library module in the **Project** window and then click **Build > Build APK**.

## Add your library as a dependency

To use your Android library's code in another app module, proceed as follows:

1.  Add the library to your project in either of the following ways (if you created the library module within the same project, then it's already there and you can skip this step):
    *   Add the pre\-built AAR (or JAR) file:
        1.  Click **File > New > New Module**.
        2.  Click **Import .JAR/.AAR Package** then click **Next**.
        3.  Enter the location of the compiled AAR or JAR file then click **Finish**.

    Android Studio creates a module directory, copies the AAR into the module, and generates a `build.gradle` file for the AAR/JAR, with the following contents:

    configurations.maybeCreate("default")
          artifacts.add("default", file('libraryName')) *   Import the library module with source code to your project (the library source becomes part of your project):

        1.  Click **File > New > Import Module**.
        2.  Enter the location of the library module directory then click **Finish**.

        The library module with source code is copied to your project, so you can actually edit the library code. If you want to maintain a single version of the library code, then this is probably not what you want and you should instead add the compiled AAR file as described above.

2.  Make sure the library is listed at the top of your `settings.gradle` file, as shown here for a library named "my\-library\-module":

    include ':app',  ':my\-library\-module'

3.  Open the app module's `build.gradle` file and add a new line to the `dependencies` block as shown in the following snippet:

    dependencies {
        implementation project(":my\-library\-module")
    }

4.  Click **Sync Project with Gradle Files**.

In this example above, the `implementation` configuration adds the library named `my-library-module` as a build dependency for the entire app module. If you instead want the library only for a specific [build variant](https://developer.android.com/studio/build/build-variants), then instead of `implementation`, use `buildVariantNameImplementation`. For example, if you want to include the library only in your "pro" product flavor, it looks like this:

productFlavors {pro {  ...  }
}dependencies {
    proImplementation project(":my\-library\-module")
}

Any code and resources in the Android library is now accessible to your app module, and the library AAR file is bundled into your APK at build time.

However, if you want to share your AAR file separately, you can find it in `project-name/module-name/build/outputs/aar/` and you can regenerate it by clicking **Build > Make Project**.

**Note**: To learn more about dependency management, read [Use variant\-aware dependency management](https://developer.android.com/studio/build/gradle-plugin-3-0-0-migration#variant_aware).

## Choose resources to make public

All resources in a library default to public. To make all resources implicitly private, you must define at least one specific attribute as public. Resources include all files in your project’s `res/` directory, such as images. To prevent users of your library from accessing resources intended only for internal use, you should use this automatic private designation mechanism by declaring one or more public resources. Alternately, you can make all resources private by adding an empty `<public />` tag , which marks nothing as public, which makes everything else (all resources) private.

To declare a public resource, add a `<public>` declaration to your library’s `public.xml` file. If you haven’t added public resources before, you need to create the `public.xml` file in the `res/values/` directory of your library.

The following example code creates two public string resources with the names `mylib_app_name` and `mylib_public_string`:

<resources> <public  name\="mylib\_app\_name"  type\="string"/> <public  name\="mylib\_public\_string"  type\="string"/>
</resources>

You should make public any resources that you want to remain visible to developers using your library.

Implicitly making attributes private not only prevents users of your library from experiencing code completion suggestions from internal library resources but also allows you to rename or remove private resources without breaking clients of your library. Private resources are filtered out of code completion, and [Lint](https://developer.android.com/studio/write/lint) warns you when you try to reference a private resource.

When building a library, the Android Gradle plugin gets the public resource definitions and extracts them into the `public.txt` file, which is then packaged inside the AAR file.

## Development considerations for library modules

As you develop your library modules and dependent apps, be aware of the following behaviors and limitations.

Once you have added references to library modules to your Android app module, you can set their relative priority. At build time, the libraries are merged with the app one at a time, starting from the lowest priority to the highest.

*   **Resource merge conflicts**

    The build tools merge resources from a library module with those of a dependent app module. If a given resource ID is defined in both modules, the resource from the app is used.

    If conflicts occur between multiple AAR libraries, then the resource from the library listed first in the dependencies list (toward the top of the `dependencies` block) is used.

    To avoid resource conflicts for common resource IDs, consider using a prefix or other consistent naming scheme that is unique to the module (or is unique across all project modules).

*   **In multi\-module builds, JAR dependencies are treated as transitive dependencies**

    When you add a JAR dependency to a library project that outputs an AAR, the JAR is processed by the library module and packaged with its AAR.

    However, if your project includes a library module that is consumed by an app module, the app module treats the library's local JAR dependency as a transitive dependency. In this case, the local JAR is processed by the app module that consumes it, and not by the library module. This is to speed up incremental builds that are caused by changes to a library's code.

    Any Java resource conflicts caused by local JAR dependencies must be resolved in the app module that consumes the library.

*   **A library module can depend on an external JAR library**

    You can develop a library module that depends on an external library. (for example, the Maps external library). In this case, the dependent app must build against a target that includes the external library (for example, the Google APIs Add\-On). Note also that both the library module and the dependent app must declare the external library in their manifest files, in a [`<uses-library>`](https://developer.android.com/guide/topics/manifest/uses-library-element) element.

*   **The app module's `minSdkVersion` must be equal to or greater than the version defined by the library**

    A library is compiled as part of the dependent app module, so the APIs used in the library module must be compatible with the platform version that the app module supports.

*   **Each library module creates its own R class**

    When you build the dependent app modules, library modules are compiled into an AAR file then added to the app module. Therefore, each library has its own `R` class, named according to the library's package name. The `R` class generated from main module and the library module is created in all the packages that are needed including the main module's package and the libraries' packages.

*   **A library module may include its own ProGuard configuration file**

    If you have a library project that you use to build and publish an AAR, you can add a ProGuard configuration file to your library's build configuration and the Android Gradle plugin applies the ProGuard rules that you have specified. The build tools embed this file within the generated AAR file for the library module. When you add the library to an app module, the library's ProGuard file gets appended to the ProGuard configuration file (`proguard.txt`) of the app module.

    By embedding a ProGuard file in your library module, you ensure that app modules that depend on your library do not have to manually update their ProGuard files to use your library. When the Android Studio build system builds your app, it uses the directives from both the app module and the library. So, there's no need to run a code shrinker on the library in a separate step.

    To add the ProGuard rules to your library project, you must specify the file's name with `consumerProguardFiles` property, inside the `defaultConfig` block of your library's `build.gradle` file. For example, the following snippet sets `lib-proguard-rules.txt` as the library's ProGuard configuration file:

    android {defaultConfig {consumerProguardFiles 'lib\-proguard\-rules.txt' } ...
    }

    However, if your library module is a part of a multi\-module build that compiles into an APK and does not generate an AAR, you should run code shrinking on only the app module that consumes the library. To learn more about ProGuard rules and their usage, read [Shrink, obfuscate, and optimize your app](https://developer.android.com/studio/build/shrink-code).

*   **Testing a library module is the same as [testing an app](https://developer.android.com/studio/test)**

    The main difference is that the library and its dependencies are automatically included as dependencies of the test APK. This means that the test APK includes not only its own code, but also the library's AAR and all its dependencies. Because there is no separate "app under test," the `androidTest` task installs (and uninstalls) only the test APK.

    When [merging multiple manifest files](https://developer.android.com/studio/build/manifest-merge), Gradle follows the default priority order and merges the library's manifest into the test APK's main manifest.

## Anatomy of an AAR file

The file extension for an AAR file is `.aar`, and the Maven artifact type should be `aar` as well. The file itself is a zip file. The only mandatory entry is `/AndroidManifest.xml`.

Additionally, an AAR file may include one or more of the following optional entries:

*   `/classes.jar`
*   `/res/`
*   `/R.txt`
*   `/public.txt`
*   `/assets/`
*   `/libs/name.jar`
*   `/jni/abi_name/name.so` (where abi\_name is one of the Android [supported ABIs](https://developer.android.com/ndk/guides/abis#sa))
*   `/proguard.txt`
*   `/lint.jar`
*   `/api.jar`
*   `/prefab/` for [exporting native libraries](https://developer.android.com/studio/build/native-dependencies)