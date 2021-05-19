# Use the Maven Publish plugin

Android Gradle plugin 3.6.0 and higher include support for the [Maven Publish Gradle plugin](https://docs.gradle.org/current/userguide/publishing_maven.html), which allows you to publish build artifacts to an Apache Maven repository. The Android Gradle plugin creates a [*component*](https://docs.gradle.org/current/userguide/dependency_management_terminology.html#sub:terminology_component) for each build variant artifact in your app or library module that you can use to customize a [*publication*](https://docs.gradle.org/current/userguide/publishing_maven.html#publishing_maven:publications) to a Maven repository.

The components that the Android plugin creates depend on whether the module uses the application or library plugin, as described in the table below.

| Android Gradle plugin | Publication artifact | Component name |
| --- | --- | --- |
| `com.android.library` | AAR | `components.variant` |
| `com.android.application` | A ZIP of APK(s), and available ProGuard or R8 mappings files | `components.variant_apk` |
| `com.android.application` | An Android App Bundle (AAB) | `components.variant_aab` |

**Note:** If your app includes feature modules, you cannot publish it as an APK. Instead, publish your app using an app bundle.

The following code sample creates a publication for the release and debug build variants of an AAR library. Each publication applies the matching component and customizes attributes of the generated POM, such as the Maven coordinates.

```
// Because the components are created only during the afterEvaluate phase, you must// configure your publications using the afterEvaluate() lifecycle method.afterEvaluate {    publishing {        publications {            // Creates a Maven publication called "release".            release(MavenPublication) {                // Applies the component for the release build variant.                from components.release                // You can then customize attributes of the publication as shown below.                groupId = 'com.example.MyLibrary'                artifactId = 'final'                version = '1.0'            }            // Creates a Maven publication called “debug”.            debug(MavenPublication) {                // Applies the component for the debug build variant.                from components.debug                groupId = 'com.example.MyLibrary'                artifactId = 'final-debug'                version = '1.0'            }        }    }
```

To create a publication that publishes your app as either a ZIP file of APKs or an Android App Bundle (AAB), simply use the appropriate component, as shown below.

```
afterEvaluate {    publishing {        publications {            paidRelease(MavenPublication) {              // The following applies a component to this publication              // which results in publishing an app bundle.              from components.paidRelease_aab              groupId = 'com.example.MyApp'              artifactId = 'paid-release-aab'              version = '1.0'            }            paidDebug(MavenPublication) {              // The following applies a component to this publication              // which results in publishing APKs in a ZIP file.              from components.paidDebug_apk              groupId = 'com.example.MyApp'              artifactId = 'paid-debug-apks'              version = '1.0'            }        }    }
```

After you create publications, the Maven Publish plugin creates [publishing tasks](https://docs.gradle.org/current/userguide/publishing_maven.html#publishing_maven:tasks) that you can use to publish your artifact to the [repositories](https://docs.gradle.org/current/userguide/publishing_maven.html#publishing_maven:repositories) that you specify.
Was this page helpful?