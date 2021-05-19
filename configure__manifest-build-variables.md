# Inject build variables into the manifest

If you need to insert variables into your `AndroidManifest.xml` file that are defined in your `build.gradle` file, you can do so with the [`manifestPlaceholders`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:manifestPlaceholders) property. This property takes a map of key\-value pairs, as shown here:

```
android {    defaultConfig {        manifestPlaceholders = [hostName:"www.example.com"]    }    ...}
```

You can then insert one of the placeholders into the manifest file as an attribute value like this:

```
<intent-filter ... >    <data android:scheme="https" android:host="${hostName}" ... />    ...</intent-filter>
```

By default, the build tools also provide your app's [application ID](https://developer.android.com/studio/build/application-id) in the `${applicationId}` placeholder. The value always matches the final application ID for the current build (including [changes by build variants](https://developer.android.com/studio/build/application-id#change_the_application_id_for_build_variants). This is useful when you want to use a unique namespace for identifiers such as an intent action, even between your build variants.

For example, if your `build.gradle` file looks like this:

```
android {    defaultConfig {        applicationId "com.example.myapp"    }    productFlavors {        free {            applicationIdSuffix ".free"        }        pro {            applicationIdSuffix ".pro"        }    }}
```

Then you can insert the application ID in your manifest like this:

```
<intent-filter ... >    <action android:name="${applicationId}.TRANSMOGRIFY" />    ...</intent-filter>
```

And the manifest result when you build the "free" product flavor is this:

```
<intent-filter ... >   <action android:name="com.example.myapp.free.TRANSMOGRIFY" />    ...</intent-filter>
```

For more information, read [Set the application ID](https://developer.android.com/studio/build/application-id).
