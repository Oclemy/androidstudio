# Design app themes with Theme Editor

- [Design app themes with Theme Editor](#design-app-themes-with-theme-editor)
  - [Theme Editor basics](#theme-editor-basics)
    - [Accessing the Theme Editor](#accessing-the-theme-editor)
    - [Navigating the Theme Editor](#navigating-the-theme-editor)
  - [Themes and colors](#themes-and-colors)
    - [Creating new themes](#creating-new-themes)
    - [Renaming themes](#renaming-themes)
    - [Changing color resources](#changing-color-resources)
      - [Select a color](#select-a-color)
      - [Define a new color](#define-a-new-color)
      - [Modify a color](#modify-a-color)
      - [Match material color](#match-material-color)
    - [Viewing state lists and colors](#viewing-state-lists-and-colors)
  - [Device\-specific configurations](#device-specific-configurations)

**Warning:** Starting with version 3.3, Theme Editor is no longer included with Android Studio.

Android Studio includes a visual assistant called Theme Editor that helps you:

*   Create and modify [themes](https://developer.android.com/guide/topics/ui/themes) for your app.
*   Adjust themes for different resource classifiers.
*   Visualize the effect of color changes on common UI elements.

This page introduces the fundamental tasks that you can perform with the Theme Editor, and explains how to do so.

## Theme Editor basics

This section describes how to access the Theme Editor, and how it is laid out.

### Accessing the Theme Editor

There are two ways to open the Theme Editor:

*   From an open styles XML file, such as `styles.xml`, click **Open editor** near the top\-right of the file window.
*   From the **Tools** menu, choose **Theme Editor**.

### Navigating the Theme Editor

The Theme Editor's main screen is divided into two sections. The left side of the editor shows what specific UI elements, such as the app bar or a raised button, look like when you apply the current theme to them. The right side of the editor displays the name of the current theme being previewed, the module where the theme is defined, and the settings for theme resources, such as **Theme parent** and **colorPrimary**. You can modify design themes by changing these resource settings.

## Themes and colors

The Theme Editor allows you to create new themes, modify existing ones, and manage the colors that make up the themes.

### Creating new themes

To create a theme, follow these steps:

1.  Open the **Theme** dropdown menu near the top of the right side of the Theme Editor.
2.  Click **Create New Theme**.
3.  In the **New Theme** dialog, enter a name for the new theme.
4.  In the **Parent theme name** list, click on the parent from which the theme inherits initial resources.

### Renaming themes

To rename a theme, perform the following steps:

1.  Open the **Theme** dropdown menu near the top of the right side of the Theme Editor.
2.  Click **Rename theme\-name**.
3.  In the **Rename** dialog, enter a new name for the theme.
4.  (optional) To see how the changes will look, click **Preview**.
5.  To apply the changes, click **Refactor**.

### Changing color resources

To change an existing color resource, such as **colorPrimary**, follow these steps:

1.  In the Theme Editor, click the colored square next to the name of the resource you want to change. The **Resources** dialog appears, which displays a list of color groups on the left side, and settings and information for the currently selected resource color on the right.
2.  Set a color for the theme resource by either selecting a color from the left panel, or by defining a new color, as described below.

#### Select a color

The colors are listed in the left column of the **Resources** dialog and arranged into the following groups.

*   **Project:** These are colors inside your project. Some can be edited because they are part of your project sources, and some cannot be edited because they are part of the libraries you have included in your project.
*   **android:** These are color resources that belong to the android namespace. They are part of the Android framework and cannot be edited.
*   **Theme Attributes:** These are attributes of the currently selected theme. They are referenced by the theme and can change depending on what theme you have selected. Theme attributes are never editable from inside the **Resources** dialog.

#### Define a new color

![](https://developer.android.com/studio/images/write/theme-editor-custom-color_2x.png)

**Figure 1.** The Color editor

2.  From the drop\-down menu in the top\-right of the **Resources** dialog, click **Add new resource > New color Value**. The **Resources** dialog displays an editable right pane with a blank **Name** field so you can enter a name for your custom color.
3.  Create the custom color as follows. The steps correspond to the callouts in figure 1.
    1.  Type a name for your new color value in the empty **Name** field. No spaces or special characters allowed in the name. Underscores and numbers are okay.
    2.  Color scale, **Custom color** field, and dropper:
        *   On the color scale, click the color you want. The color displays in the **Custom color** field.
        *   You can use the dropper to the left of the **Custom color** field to select a color. Click the dropper ![](https://developer.android.com/studio/images/buttons/color-editor-dropper.png), and then click something visible anywhere on your computer screen. The color in the **Custom color** field changes to the color you selected.
    3.  Just below the **Custom color** field, use the editable fields and drop\-down menu on the left to specify an `RGB`, `HSB`, or `ARGB` color by numeric values. The HEX equivalent of your color displays in the editable field to the far right.
    4.  To set the color opacity and hue, move the sliders below the color scale.
    5.  Select one of the available predefined color squares. The color displays in the **Custom Color** field and the name changes to the name of the color you selected.
4.  Click **OK** to save your settings. The **Resources** dialog closes and returns you to the Theme Editor.

#### Modify a color

You can modify any editable color. If a color is editable, you see editable color, hue, opacity, name field, and device configuration fields. See [Select Color](#selectcolor) if you want to know why some fields are editable and others are not.

1.  Adjust the settings.
2.  Click **OK**.

#### Match material color

If you have defined or modified a custom project color, you can ensure that the color matches the closest [material palette color](https://material.io/guidelines/style/color.html) by clicking **CLOSEST MATERIAL COLOR**, located next to **Custom color**. Android Studio changes the color and opacity values of the color you picked to the material color most like it, and replaces **Custom color** with the name of the color from the material palette.

**Note:** The **CLOSEST MATERIAL COLOR** feature is visible only when the color is not already a material color.

### Viewing state lists and colors

The Theme Editor allows you to preview [colors associated with different states.](https://developer.android.com/guide/topics/resources/color-list-resource) To do so, open the **Resources** dialog by clicking on the color set square next to the name of an editable state list resource. The **Resources** dialog displays a list of states, such as *Selected*, and the color value associated with the state. Click the color for a state to choose a different color value.

To more fully control the states themselves, you can directly view and edit their properties in the XML file that defines them. For more information, see the documentation for the `[ColorStateList](https://developer.android.com/reference/android/content/res/ColorStateList)` class.

## Device\-specific configurations

When a color is editable, you can choose [device\-specific](https://developer.android.com/guide/topics/resources/providing-resources#Compatibility) configurations for your app to support. Perform the following steps to do so:

1.  Open the **Resources** dialog. For information about how to open the **Resources** dialog, see [Changing color resources](#changing).
2.  Select a **Project** color, and then in the bottom of the right pane, click to expand **Device Configuration**, revealing the source set and name of the XML file containing the resource, and a list of configuration\-specific directories in which to place that file.
3.  If necessary, change the XML file name.
4.  Check the boxes next to the directories corresponding to the device\-specific configurations you wish to support. Any configuration for which you do not specify a directory defaults to using the `values` directory.

For more information about the relationship between directory names and configurations, see [Supporting Multiple Screens](https://developer.android.com/guide/practices/screens_support#ConfigurationExamples). For more information about supported directory names, see [Providing Resources](https://developer.android.com/guide/topics/resources/providing-resources#ResourceTypes).