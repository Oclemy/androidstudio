# Manage your app's UI resources with Resource Manager

- [Manage your app's UI resources with Resource Manager](#manage-your-apps-ui-resources-with-resource-manager)
  - [Import drawables into your project](#import-drawables-into-your-project)
    - [Automatically parse drawable densities](#automatically-parse-drawable-densities)
  - [Drag and drop drawables into your layout](#drag-and-drop-drawables-into-your-layout)

Resource Manager is a tool window for importing, creating, managing, and using resources in your app. You can open the tool window by selecting **View > Tool Windows > Resource Manager** from the menu bar or by selecting **Resource Manager** on the left side bar.

![](https://developer.android.com/images/studio/write/resource-manager-2x.png)

**Figure 1:** The Resource Manager

1.  Click **Add** ![](https://developer.android.com/studio/images/buttons/ic_plus_dark.png) to add a new resource to your project. You can add image assets, vector assets, fonts, resource files and values, or you can [import resources](#import) into your project.
2.  The module dropdown lets you view resources specific to a module.
3.  Use the search bar to search for a resource across all modules in your project.
4.  The Resource Manager groups your resources by type. Use these tabs to switch between each resource type. Click the overflow icon ![overflow icon](https://developer.android.com/images/studio/write/overflow-icon-2x.png) to show additional resource types.
5.  The filter button lets you view resources from local dependent modules, external libraries, and the Android framework. You can also use the filter to show theme attributes.
6.  The main content area displays previews of your resources. Right\-click on a resource to see a context menu where, among other things, you can rename the resource and search your app for where the resource is used.
7.  Click these buttons to view your resources as either tiles or lists.
8.  Click these buttons to change the preview size of your resources.

In addition to the features mentioned above, the Resource Manager provides a simple way to bulk import drawables into your project. You can drag and drop your image files—including SVG files—directly onto the Resource Manager, or you can use the **Import Drawables** wizard. For more information, see the [Import resources into your project](#import) section below.

Double\-click on a resource to have the Resource Manager display more detailed information. If you have multiple versions of a resource, this detailed view displays each version along with any associated qualifiers, as shown in figure 2. From here, you can also double\-click on a specific version to open it in an editor window.

![](https://developer.android.com/images/studio/write/resource-manager-flowers-2x.png)

**Figure 2:** The Resource Manager showing versions of an image resource for different screen densities.

## Import drawables into your project

You can import image resources by dragging and dropping the resource files or folders directly onto the Resource Manager. After you drop the resources onto the Resource Manager, the **Import drawables** dialog appears, where you can view the summary of the resources and add any needed qualifiers before importing.

**Note:** For a list of supported image types, see [Supported media formats](https://developer.android.com/guide/topics/media/media-formats#image-formats).

To import image resources into your project, do the following:

1.  Drag and drop your images directly onto the **Resource Manager** window in Android Studio. Alternatively, you can click the plus icon ( **+**), choose **Import Drawables**, as shown in figure 3, and then select the files and folders that you want to import.

    ![](https://developer.android.com/images/studio/write/import-drawables-menu-2x.png)

    **Figure 3:** Select **Import Drawables** from the dropdown menu.

2.  The **Import drawables** dialog appears. As shown in figure 4, this dialog displays a list of the resources you're importing. You can rename resources by clicking on the textbox above each resource preview.

    If you're providing multiple versions of the same resource, be sure to add [device configuration qualifiers](#auto-parse-drawables) that describe the specific configuration that the resource supports. For example, if you're providing multiple versions of the same resource for different screen densities, you can add a **Density** qualifier for each version. Note that if two or more resources have the same name and qualifiers, only one version is imported. For more information on resource qualifiers, see [Providing alternative resources](https://developer.android.com/guide/topics/resources/providing-resources#AlternativeResources).

    ![](https://developer.android.com/images/studio/write/import-drawables-add-qualifiers-2x.png)

    **Figure 4:** The **Import Drawables** dialog.

    Once you've named your resources and added any necessary qualifiers, click **Next**.

3.  The next screen shows a summary of the resources you're importing. When you're ready to import, click **Import**.

In the **Resource Manager** window, your resources are now ready for you to use in your project, as shown in figure 5.

![](https://developer.android.com/images/studio/write/resource-manager-after-import-2x.png)

**Figure 5:** The Resource Manager now shows your imported images.

### Automatically parse drawable densities

When you import a file or folder, and its path contains a density qualifier, the Resource Manager automatically applies the density qualifier as part of the import. The Resource Manager can parse both Android's density qualifiers and iOS's scale factors.

This table lists how different supported densities is represented for both Android and iOS:

| Density | Android density qualifier | iOS scaling factor |
| --- | --- | --- |
| Low\-density (~120dpi) | ldpi | not supported |
| Medium\-density (~160dpi) | mdpi | original scale |
| High\-density (~240dpi) | hdpi | not supported |
| Extra\-high\-density (~320dpi) | xhdpi | @2x |
| Extra\-extra\-high\-density (~480dpi) | xxhdpi | @3x |
| Extra\-extra\-extra\-high\-density (~640dpi) | xxxhdpi | @4x |

Here are some examples of how input paths translate to resource paths after import:

Android density qualifier: hdpi

**Input path:** /UserFolder/icon1/***hdpi***/icon.png
**Resource path:** *<projectFolder>*/*<moduleFolder>*/src/main/res/***drawable\-hdpi***/icon.png

Android density qualifier: xxhdpi

**Input path:** /UserFolder/icon1/abc\-***xxhdpi***/icon.png
**Resource path:** *<projectFolder>*/*<moduleFolder>*/src/main/res/***drawable\-xxhdpi***/icon.png

iOS scaling factor: @2x

**Input path:** /UserFolder/icon1/icon***@2x***.png
**Resource path:** *<projectFolder>*/*<moduleFolder>*/src/main/res/***drawable\-xhdpi***/icon.png

iOS scaling factor: @2x

**Input path:** /UserFolder/icon1/icon***@2x***\_alternate.png
**Resource path:** *<projectFolder>*/*<moduleFolder>*/src/main/res/***drawable\-xhdpi***/icon\_alternate.png

For more information on supporting devices with different pixel densities, see [Support different pixel densities](https://developer.android.com/training/multiscreen/screendensities).

## Drag and drop drawables into your layout

You can drag and drop drawables from the Resource Manager directly onto a layout. When you drop a resource onto a layout, the Resource Manager creates a corresponding `ImageView` for that drawable, as shown in animation 1:

![](https://developer.android.com/images/studio/write/resource-manager-drag-and-drop-design.gif)

**Animation 1:** Dragging and dropping drawables onto a layout in **Design** view.

You can also drag and drop directly onto the XML of the layout, as shown in animation 2:

![](https://developer.android.com/images/studio/write/resource-manager-drag-and-drop-xml.gif)

**Animation 2:** Dragging and dropping drawables onto a layout in **Text** view.

When dropping a drawable onto a layout in the **Text** tab, the generated code differs depending on where you drop the layout:

*   If you drop a drawable onto a blank area, the Resource Manager generates a corresponding `ImageView`.
*   If you drop a drawable onto any attribute in the layout XML, the Resource Manager replaces that attribute value with a reference to the drawable. Note that you can also drag any other resource type onto an XML attribute to replace the attribute value.
*   If you drop a drawable onto an existing `ImageView` element, the Resource Manager replaces the corresponding source attribute.