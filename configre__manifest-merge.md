# Merge multiple manifest files

- [Merge multiple manifest files](#merge-multiple-manifest-files)
  - [Merge priorities](#merge-priorities)
  - [Merge conflict heuristics](#merge-conflict-heuristics)
  - [Merge rule markers](#merge-rule-markers)
    - [Node markers](#node-markers)
    - [Attribute markers](#attribute-markers)
    - [Marker selector](#marker-selector)
    - [Override <uses\-sdk> for imported libraries](#override-uses-sdk-for-imported-libraries)
    - [Implicit system permissions](#implicit-system-permissions)
  - [Inspect the merged manifest and find conflicts](#inspect-the-merged-manifest-and-find-conflicts)
  - [Appendix: Merge policies](#appendix-merge-policies)

Your APK file can contain just one `AndroidManifest.xml` file, but your Android Studio project may contain several—provided by the main source set, build variants, and imported libraries. So when building your app, the Gradle build merges all manifest files into a single manifest file that's packaged into your APK.

The manifest merger tool combines all XML elements from each file by following some merge heuristics and by obeying merge preferences that you have defined with special XML attributes. This page describes how manifest merging works and how you can apply merge preferences to resolve merge conflicts.

**Tip:** Use the [**Merged Manifest** view](#inspect_the_merged_manifest_and_find_conflicts) to preview the results of your merged manifest and find conflict errors.

## Merge priorities

The merger tool combines all the manifest files into one file by merging them sequentially based on each manifest file's priority. For example, if you have three manifest files, the lowest priority manifest is merged into the next highest priority, and then that is merged into the highest priority manifest, as illustrated in figure 1.

![](https://developer.android.com/studio/images/build/manifest-merger_2x.png)

**Figure 1.** The process to merge three manifest files, lowest priority (left) into highest priority (right)

There are three basic types of manifest files that may be merged into each other, and their merge priorities are as follows (highest priority first):

1.  **Manifest file for your [build variant](https://developer.android.com/studio/build/build-variants)**

    If you have multiple source sets for your variant, their manifest priorities are as follows:

    1.  Build variant manifest (such as `src/demoDebug/`)
    2.  Build type manifest (such as `src/debug/`)
    3.  Product flavor manifest (such as `src/demo/`)

        If you're using flavor dimensions, the manifest priorities correspond to the order each dimension is listed in the `flavorDimensions` property (first is highest priority).

2.  **Main manifest file for the app module**
3.  **Manifest file from an included library**

    If you have multiple libraries, their manifest priorities match the dependency order (the order they appear in your Gradle `dependencies` block).

For example, a library manifest is merged into the main manifest, then the main manifest is merged into the build variant manifest.

**Note:** These are the same merge priorities for all source sets, as described in [Build with source sets](https://developer.android.com/studio/build/build-variants#sourceset-build).

**Important:** Build configurations from the `build.gradle` file override any corresponding attributes in the merged manifest file. For example, the [`minSdkVersion`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:minSdkVersion(int)) from the `build.gradle` file overrides the matching attribute in the [`<uses-sdk>`](https://developer.android.com/guide/topics/manifest/uses-sdk-element) manifest element. To avoid confusion, you should simply leave out the `<uses-sdk>` element and define these properties only in the `build.gradle` file. For more details, see [Configure your build](https://developer.android.com/studio/build).

## Merge conflict heuristics

The merger tool can logically match every XML element from one manifest to a corresponding element in the other manifest. (For details about how matching works, see the appendix about [merge policies](#appendix_merge_policies)).

If an element from the lower\-priority manifest does not match any elements in the higher\-priority manifest, then it is added to the merged manifest. However, if there *is* a matching element, then the merger tool attempts to combine all attributes from each into the same element. If the tool finds that both manifests contain the same attribute with different values, then a merge conflict occurs.

Table 1 depicts the possible outcomes when the merger tool attempts to combine all attributes into the same element.

**Table 1.** Default merge behavior for attribute values

| High priority attribute | Low priority attribute | Attribute's merged result |
| --- | --- | --- |
| No value | No value | No value (use default value) |
| Value B | Value B |
| Value A | No value | Value A |
| Value A | Value A |
| Value B | **Conflict error**—you must add a [merge rule marker](#merge_rule_markers) |

However, there are a few situations in which the merger tool behaves differently to avoid merge conflicts:

*   Attributes in the `<manifest>` element are never merged together—only the attributes from the highest priority manifest are used.
*   The `android:required` attribute in the [`<uses-feature>`](https://developer.android.com/guide/topics/manifest/uses-feature-element) and [`<uses-library>`](https://developer.android.com/guide/topics/manifest/uses-library-element) elements use an *OR* merge, such that if there is a conflict, `"true"` is applied and the feature or library required by one manifest is always included.
*   Attributes in the [`<uses-sdk>`](https://developer.android.com/guide/topics/manifest/uses-sdk-element) element always use the value from the higher\-priority manifest, except in the following situations:
    *   When the lower\-priority manifest has a `minSdkVersion` value that's *higher*, an error occurs unless you apply the [`overrideLibrary`](#override_wzxhzdk35uses-sdk_for_imported_libraries) merge rule.
    *   When the lower\-priority manifest has a `targetSdkVersion` value that's *lower*, the merger tool uses the value from the higher\-priority manifest, but it also adds any system permissions that are necessary to ensures that the imported library continues to function properly (for cases in which the higher Android version has increased permission restrictions). For more information about this behavior, see the section about [implicit system permissions](#implicit_system_permissions) .
*   The `<intent-filter>` element is never matched between manifests. Each is treated as unique and is added to the common parent element in the merged manifest.

For all other conflicts between attributes, you'll receive an error and you must instruct the merger tool how to resolve it by adding a special attribute in the higher\-priority manifest file (see the next section about [merge rule markers](#merge_rule_markers)).

**Do not depend on default attribute values.** Because all unique attributes are combined into the same element, this might cause unexpected results if the higher\-priority manifest actually depends on the default value of an attribute without declaring it. For example, if the higher\- priority manifest *does not* declare the `android:launchMode` attribute, then it uses the default value of `"standard"`; but if the lower\-priority manifest declares this attribute with a different value, that value is applied to the merged manifest (overriding the default value). So you should explicity define each attribute as you want it to be. (Default values for each attribute are documented in the [Manifest reference](https://developer.android.com/guide/topics/manifest/manifest-intro).)

## Merge rule markers

A merge rule marker is an XML attribute you can use to express your preference about how to resolve merge conflicts or remove unwanted elements and attributes. You can apply a marker to either an entire element or to just specific attributes in an element.

When merging two manifest files, the merger tool looks for these markers in the higher\-priority manifest file.

All markers belong to the Android `tools` namespace, so you must first declare this namespace in the `<manifest>` element as shown here:

<manifest  xmlns:android\="http://schemas.android.com/apk/res/android" package\="com.example.myapp" **xmlns:tools\="http://schemas.android.com/tools"**\>

### Node markers

To apply a merge rule to an entire XML element (to all attributes in a given manifest element and to all its child tags), use the following attributes:

`tools:node="merge"`

Merge all attributes in this tag and all nested elements when there are no conflicts using the [merge conflict heuristics](#merge_conflict_heuristics). **This is the default behavior for elements.**

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:windowSoftInputMode\="stateUnchanged"\> <intent\-filter> <action  android:name\="android.intent.action.SEND"  /> <category  android:name\="android.intent.category.DEFAULT"  /> </intent\-filter>
</activity>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" **tools:node\="merge"**\>
</activity>

Merged manifest result:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" android:windowSoftInputMode\="stateUnchanged"\> <intent\-filter> <action  android:name\="android.intent.action.SEND"  /> <category  android:name\="android.intent.category.DEFAULT"  /> </intent\-filter>
</activity>

`tools:node="merge-only-attributes"`

Merge attributes in this tag only; do not merge nested elements.

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:windowSoftInputMode\="stateUnchanged"\> <intent\-filter> <action  android:name\="android.intent.action.SEND"  /> <data  android:type\="image/\*"  /> <category  android:name\="android.intent.category.DEFAULT"  /> </intent\-filter>
</activity>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" **tools:node\="merge\-only\-attributes"**\>
</activity>

Merged manifest result:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" android:windowSoftInputMode\="stateUnchanged"\>
</activity>

`tools:node="remove"`

Remove this element from the merged manifest. Although it seems like you should instead just delete this element, using this is necessary when you discover an element in your merged manifest that you don't need, and it was provided by a lower\-priority manifest file that's out of your control (such as an imported library).

Low priority manifest:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  android:name\="cow" android:value\="@string/moo"/> <meta\-data  android:name\="duck" android:value\="@string/quack"/>
</activity\-alias>

High priority manifest:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  android:name\="cow" **tools:node\="remove"**/>
</activity\-alias>

Merged manifest result:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  android:name\="duck" android:value\="@string/quack"/>
</activity\-alias>

`tools:node="removeAll"`

Like `tools:node="remove"`, but it removes all elements matching this element type (within the same parent element).

Low priority manifest:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  android:name\="cow" android:value\="@string/moo"/> <meta\-data  android:name\="duck" android:value\="@string/quack"/>
</activity\-alias>

High priority manifest:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  **tools:node\="removeAll"**/>
</activity\-alias>

Merged manifest result:

<activity\-alias  android:name\="com.example.alias"\>
</activity\-alias>

`tools:node="replace"`

Replace the lower\-priority element completely. That is, if there is a matching element in the lower\-priority manifest, ignore it and use this element exactly as it appears in this manifest.

Low priority manifest:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  android:name\="cow" android:value\="@string/moo"/> <meta\-data  android:name\="duck" android:value\="@string/quack"/>
</activity\-alias>

High priority manifest:

<activity\-alias  android:name\="com.example.alias" **tools:node\="replace"**\> <meta\-data  android:name\="fox" android:value\="@string/dingeringeding"/>
</activity\-alias>

Merged manifest result:

<activity\-alias  android:name\="com.example.alias"\> <meta\-data  android:name\="fox" android:value\="@string/dingeringeding"/>
</activity\-alias>

`tools:node="strict"`

Generate a build failure any time this element in the lower\-priority manifest does not exactly match it in the higher\-priority manifest (unless resolved by other merge rule markers). This overrides the [merge conflict heuristics](#merge_conflict_heuristics). For example, if the lower\-priority manifest simply includes an extra attribute, the build fails (whereas the default behavior adds the extra attribute to the merged manifest).

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:windowSoftInputMode\="stateUnchanged"\> <intent\-filter> <action  android:name\="android.intent.action.SEND"  /> <category  android:name\="android.intent.category.DEFAULT"  /> </intent\-filter>
</activity>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" **tools:node\="strict"**\>
</activity>

**This creates a manifest merge error.** The two manifest elements cannot differ at all in strict mode. So you must apply other merge rule markers to resolve these differences. (Ordinarily, these two will merge together fine as shown in the above example for `tools:node="merge"`.)

### Attribute markers

To instead apply a merge rule only to specific attributes in a manifest tag, use the following attributes. Each attribute accepts one or more attribute names (including the attribute namespace), separated by commas.

`tools:remove="attr, ..."`

Remove the specified attributes from the merged manifest. Although it seems like you could instead just delete these attributes, it's necessary to use this when the lower\-priority manifest file *does include* these attributes and you want to ensure they do not go into the merged manifest.

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:windowSoftInputMode\="stateUnchanged"\>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" **tools:remove\="android:windowSoftInputMode"**\>

Merged manifest result:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait"\>

`tools:replace="attr, ..."`

Replace the specified attributes in the lower\-priority manifest with those from this manifest. In other words, always keep the higher\-priority manifest's values.

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:theme\="@oldtheme" android:exported\="false" android:windowSoftInputMode\="stateUnchanged"\>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:theme\="@newtheme" android:exported\="true" android:screenOrientation\="portrait" **tools:replace\="android:theme,android:exported"**\>

Merged manifest result:

<activity  android:name\="com.example.ActivityOne" android:theme\="@newtheme" android:exported\="true" android:screenOrientation\="portrait" android:windowSoftInputMode\="stateUnchanged"\>

`tools:strict="attr, ..."`

Generate a build failure any time these attributes in the lower\-priority manifest do not exactly match them in the higher\-prority manifest. **This is the default behavior for all attributes**, except for those with special behaviors as described in the [merge conflict heuristics](#merge_conflict_heuristics).

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="landscape"\>
</activity>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:screenOrientation\="portrait" **tools:strict\="android:screenOrientation"**\>
</activity>

**This creates a manifest merge error.** You must apply other merge rule markers to resolve the conflict. (Remember: This is the default behavior, so the above example has the same result if you remove `tools:strict="screenOrientation"`.)

You can also apply multiple markers to one element as follows.

Low priority manifest:

<activity  android:name\="com.example.ActivityOne" android:theme\="@oldtheme" android:exported\="false" android:allowTaskReparenting\="true" android:windowSoftInputMode\="stateUnchanged"\>

High priority manifest:

<activity  android:name\="com.example.ActivityOne" android:theme\="@newtheme" android:exported\="true" android:screenOrientation\="portrait" **tools:replace\="android:theme,android:exported"** **tools:remove\="android:windowSoftInputMode"**\>

Merged manifest result:

<activity  android:name\="com.example.ActivityOne" android:theme\="@newtheme" android:exported\="true" android:allowTaskReparenting\="true" android:screenOrientation\="portrait"\>

### Marker selector

If you want to apply the merge rule markers to only a specific imported library, add the `tools:selector` attribute with the library package name.

For example, with the following manifest, the `remove` merge rule is applied only when the lower\-priority manifest file is from the `com.example.lib1` library.

<permission  android:name\="permissionOne" tools:node\="remove" **tools:selector\="com.example.lib1"**\>

If the lower\-priority manifest is from any other source, the `remove` merge rule is ignored.

**Note:** If you use this with one of the attribute markers, then it applies to all attributes specified in the marker.

### Override <uses\-sdk> for imported libraries

By default, when importing a library with a `minSdkVersion` value that's *higher* than the main manifest file, an error occurs and the library cannot be imported. To make the merger tool ignore this conflict and import the library while keeping your app's lower `minSdkVersion` value, add the `overrideLibrary` attribute to the `<uses-sdk>` tag. The attribute value can be one or more library package names (comma\-separated), indicating the libraries that can override the main manifest's `minSdkVersion`.

For example, if your app's main manifest applies `overrideLibrary` like this:

<manifest  xmlns:android\="http://schemas.android.com/apk/res/android" package\="com.example.app" xmlns:tools\="http://schemas.android.com/tools"\> <uses\-sdk  tools:overrideLibrary\="com.example.lib1, com.example.lib2"/>
...

Then the following manifest can be merged without an error regarding the `<uses-sdk>` tag, and the merged manifest keeps `minSdkVersion="2"` from the app manifest.

<manifest  xmlns:android\="http://schemas.android.com/apk/res/android" package\="com.example.lib1"\>
   <uses\-sdk  android:minSdkVersion\="4"  />
...

### Implicit system permissions

Some Android APIs that were once freely accessible by apps have become restricted by [system permissions](https://developer.android.com/guide/topics/security/permissions) in recent versions of Android. To avoid breaking apps that expect access to these APIs, the recent versions of Android allow apps to continue accessing those APIs without the permission if they have set the `targetSdkVersion` to a value lower than the version in which the restriction was added. This behavior effectively grants the app an *implicit permission* to allow access to the APIs. So, this can affect merged manifests that have different values for `targetSdkVersion` in the following way.

If the lower\-priority manifest file has a lower value for `targetSdkVersion` that provides it an implicit permission, and the higher\-priority manifest *does not* have the same implicit permission (because its `targetSdkVersion` is equal to or higher than the version in which the restriction was added), then the merger tool *explicitly* adds the system permission to the merged manifest.

For example, if your app sets `targetSdkVersion` to 4 or higher and imports a library with `targetSdkVersion` set to 3 or lower, the merger tool adds the [`WRITE_EXTERNAL_STORAGE`](https://developer.android.com/reference/android/Manifest.permission#WRITE_EXTERNAL_STORAGE) permission to the merged manifest. Table 2 lists all the possible permissions that could be added to your merged manifest.

**Note:** If you've set your app's `targetSdkVersion` to 23 or higher, then you must perform run\-time permission requests for any dangerous permissions when your app attempts to access the APIs protected by those permissions. For more guidance, see [Working with system permissions](https://developer.android.com/training/permissions).

**Table 2.** List of permissions the merger tool may add to the merged manifest

| Lower\-priority manifest declares | Permissions added to the merged manifest |
| --- | --- |
| `targetSdkVersion` is 3 or lower | `[WRITE_EXTERNAL_STORAGE](https://developer.android.com/reference/android/Manifest.permission#WRITE_EXTERNAL_STORAGE)`, `[READ_PHONE_STATE](https://developer.android.com/reference/android/Manifest.permission#READ_PHONE_STATE)` |
| `targetSdkVersion` is 15 or lower and using `[READ_CONTACTS](https://developer.android.com/reference/android/Manifest.permission#READ_CONTACTS)` | `[READ_CALL_LOG](https://developer.android.com/reference/android/Manifest.permission#READ_CALL_LOG)` |
| `targetSdkVersion` is 15 or lower and using `[WRITE_CONTACTS](https://developer.android.com/reference/android/Manifest.permission#WRITE_CONTACTS)` | `[WRITE_CALL_LOG](https://developer.android.com/reference/android/Manifest.permission#WRITE_CALL_LOG)` |

## Inspect the merged manifest and find conflicts

Even before you build your APK, you can see a preview of what your merged manifest looks by opening your `AndroidManifest.xml` file in Android Studio, and then clicking the **Merged Manifest** tab at the bottom of the editor.

The Merged Manifest view shows the results of the merged manifest on the left and information about each merged manifest file on the right, as shown in figure 2. The elements that were merged in from lower\-priority manifest files are highlighted in different colors on the left. The key for each color is specified under **Manifest Sources** on the right.

![](https://developer.android.com/studio/images/build/manifest-merged-view_2x.png)

**Figure 2.** The Merged Manifest view

Manifest files that were part of the build but did not contribute elements or attributes are listed under **Other Manifest Files** on the right.

To see information about where an element came from, click it on the left and the details appear under **Merging Log** on the right.

If any conflicts occur, they appear under **Merging Errors** on the right side with a recommendation for how to resolve the conflict using the [merge rule markers](#merge_rule_markers). The errors are also printed in the **Event Log** window (select **View > Tool Windows > Event Log**).

If you'd like to see a complete log of the merging decision tree, you can find the log file in your module's `build/outputs/logs/` directory, named `manifest-merger-buildVariant-report.txt`.

## Appendix: Merge policies

The manifest merger tool can logically match every XML element from one manifest file to a corresponding element in another file. The merger matches each element using a "match key": either a unique attribute value (such as `android:name`) or the natural uniqueness of the tag itself (for example, there can be only one `<supports-screen>` element). If two manifests have the same XML element, then the tool merges the two elements together using one of three merge policies:

Merge

Combine all non\-conflicting attributes into the same tag and merge child elements according to their respective merging policy. If any attributes conflict with each other, merge them together with the [merge rule markers](#merge_rule_markers).

Merge children only

Do not combine or merge the attributes (keep only the attributes provided by the highest priority manifest file) and merge child elements according to their merging policy.

Keep

Leave the element "as is" and add it to the common parent element in the merged file. This is used only when it is acceptable for there to be several declarations of the same element.

Table 1 lists each element type, the type of merge policy used, and the key used to determine an element match between two manifests.

**Table 3.** Manifest element merge policies and match keys

| Element | Merge policy | Match key |
| --- | --- | --- |
| `<action>` | Merge | `android:name` attribute |
| `<activity>` | Merge | `android:name` attribute |
| `<application>` | Merge | There is only one per `<manifest>` |
| `<category>` | Merge | `android:name` attribute |
| `<data>` | Merge | There is only one per `<intent-filter>` |
| `<grant-uri-permission>` | Merge | There is only one per `<provider>` |
| `<instrumentation>` | Merge | `android:name` attribute |
| `<intent-filter>` | Keep | No matching; several declarations within the parent element are allowed |
| `<manifest>` | Merge children only | There is only one per file |
| `<meta-data>` | Merge | `android:name` attribute |
| `<path-permission>` | Merge | There is only one per `<provider>` |
| `<permission-group>` | Merge | `android:name` attribute |
| `<permission>` | Merge | `android:name` attribute |
| `<permission-tree>` | Merge | `android:name` attribute |
| `<provider>` | Merge | `android:name` attribute |
| `<receiver>` | Merge | `android:name` attribute |
| `<screen>` | Merge | `android:screenSize` attribute |
| `<service>` | Merge | `android:name` attribute |
| `<supports-gl-texture>` | Merge | `android:name` attribute |
| `<supports-screen>` | Merge | There is only one per `<manifest>` |
| `<uses-configuration>` | Merge | There is only one per `<manifest>` |
| `<uses-feature>` | Merge | `android:name` attribute (if not present, then the `android:glEsVersion` attribute) |
| `<uses-library>` | Merge | `android:name` attribute |
| `<uses-permission>` | Merge | `android:name` attribute |
| `<uses-sdk>` | Merge | There is only one per `<manifest>` |
| Custom elements | Merge | No matching; these are unknown to the merger tool so they are always included in the merged manifest |
