# Debug Your layout with Layout Inspector and Layout Validation

- [Debug Your layout with Layout Inspector and Layout Validation](#debug-your-layout-with-layout-inspector-and-layout-validation)
  - [Open the Layout Inspector](#open-the-layout-inspector)
  - [Select a view](#select-a-view)
  - [Isolate a view](#isolate-a-view)
  - [Hide layout borders and view labels](#hide-layout-borders-and-view-labels)
  - [Compare app layout to a reference image overlay](#compare-app-layout-to-a-reference-image-overlay)
  - [Live Layout Inspector](#live-layout-inspector)
  - [Layout Validation](#layout-validation)
    - [Pixel Devices](#pixel-devices)
    - [Custom](#custom)
    - [Color Blind](#color-blind)
    - [Font Sizes](#font-sizes)

The [Layout Inspector](#layout-inspector) in Android Studio allows you to compare your app layout with design mockups, display a magnified or 3D view of your app, and examine details of its layout at runtime. This is especially useful when your layout is built at runtime rather than entirely in XML and the layout is behaving unexpectedly.

[Layout Validation](#layout-validation) allows you to simultaneously preview layouts on different devices and display configurations, including variable font sizes or user languages, making it easy to test for a variety of common layout problems.

## Open the Layout Inspector

To open the Layout Inspector, do the following:

1.  [Run your app](https://developer.android.com/studio/run) on a connected device or emulator.
2.  Click **Tools > Layout Inspector**.

As shown in figure 1, the Layout Inspector displays the following:

1.  **Component Tree:** The hierarchy of views in the layout.
2.  **Layout Display:** Rendering of app layout as it appears on your device or emulator, with layout bounds shown for each view.
3.  **Layout Inspector toolbar:** Tools for the Layout Inspector.
4.  **Attributes:** The layout attributes for the selected view.

![Screenshot of the Layout Inspector with labels indicating Component Tree, Layout Inspector toolbar, Layout Display,
                                                                   and Attributes](https://developer.android.com/studio/images/debug/layout-inspector-callouts.png)

**Figure 1.** The Layout Inspector

## Select a view

To select a view, click it in the **Component Tree** or the **Layout Display**. All of the layout attributes for the selected view appear in the **Attributes** panel.

If your layout includes overlapping views, you can select a view that is not in front by clicking it in the **Component Tree** or by [rotating the layout](#3d-view) and clicking the desired view.

## Isolate a view

To work with complex layouts, you can isolate individual views so that only a subset of the layout is shown in the **Component Tree** and rendered in the **Layout Display**.

To isolate a view, right\-click the view in the **Component Tree** and select **Show Only Subtree** or **Show Only Parents**.

To return to the full view, right\-click the view and select **Show All**.

## Hide layout borders and view labels

To hide the bounding box or view labels for a layout element, click **View Options** ![Live Layout Inspector view options icon](https://developer.android.com/studio/images/buttons/live-layout-inspector-view-options-icon.png) at the top of the **Layout Display** and toggle **Show Borders** or **Show View Label**.

## Compare app layout to a reference image overlay

To compare your app layout with a reference image, such as a UI mockup, you can load a bitmap image overlay in the Layout Inspector.

*   To load an overlay, click **Load Overlay** ![](https://developer.android.com/studio/images/buttons/layout-inspector-load-overlay.png) at the top of the Layout Inspector. The overlay is scaled to fit the layout.
*   To adjust the transparency of the overlay, use the **Overlay Alpha** slider.
*   To remove the overlay, click **Clear Overlay** ![](https://developer.android.com/studio/images/buttons/live-layout-inspector-remove-overlay-icon.png).

## Live Layout Inspector

The Live Layout Inspector provides complete, real\-time insights into your app’s UI while it’s deployed to a device or emulator running API level 29 or higher.

To enable the Live Layout Inspector, go to **File > Settings > Experimental**, and check the box next to **Enable Live Layout Inspector**. Then click the checkbox next to **Live updates** above the **Layout Display**.

The Live Layout Inspector includes a dynamic layout hierarchy, updating the **Component Tree** and **Layout Display** as views on the device change.

In addition, the property values resolution stack allows you to investigate where a resource property value originates in the source code and navigate to its location by following the hyperlinks in the properties pane.

![Property values resolution stack](https://developer.android.com/studio/images/debug/layout_inspector_properties.png)

**Figure 2.** Property values in the Attributes panel with hyperlinks to the properties' definitions.

Lastly, the **Layout Display** features an advanced 3D visualization of your app’s view hierarchy at runtime. To use this feature, in the Live Layout Inspector window simply click on the Layout and rotate it by dragging the mouse. To expand or contract the layers of the Layout, use the **Layer Spacing** slider.

![Layout inspector: 3D view](https://developer.android.com/studio/images/debug/live-layout-inspector-3d-view.png)

**Figure 3.** Rotated 3D view of a Layout.

## Layout Validation

Layout Validation is a visual tool for simultaneously previewing layouts for different devices and display configurations, helping you catch problems in your layouts earlier in the process. To access this feature, click on the **Layout Validation** tab in the top\-right corner of the IDE window:

![Screenshot of the Layout Validation tab in Android Studio IDE](https://developer.android.com/studio/images/debug/layout-validation-tab.png)

To switch between the available configuration sets, select one of the following from the dropdown at the top of the Layout Validation window:

*   Pixel Devices
*   Custom
*   Color Blind
*   Font Sizes

![Screenshot of dropdown menu in the Layout Validation tool](https://developer.android.com/studio/images/debug/layout-validation-dropdown.gif)

### Pixel Devices

Preview how your layout appears on Pixel devices:

![Screenshot of layout previews for different Pixel devices](https://developer.android.com/studio/images/debug/layout-validation-pixel-devices.gif)

**Figure 4.** Pixel device previews in the Layout Validation tool

### Custom

To customize a display configuration to preview, choose from a variety of setings including language, device, or screen orientation:

![Animation showing how to customize a device display in the Layout Validation tool](https://developer.android.com/studio/images/debug/layout-validation-custom.gif)

**Figure 5.** Configure a custom display in the Layout Validation tool

### Color Blind

To help make your app more accessible for users who are color blind, validate your layout with simulations of common types of color blindness:

![Screenshot of simulation previews for different types of color blindness](https://developer.android.com/studio/images/debug/layout-validation-color-blind.png)

**Figure 6.** Color blindness simulation previews in the Layout Validation tool

### Font Sizes

Validate your layouts at various font sizes, and improve your app's accessibility for visually impaired users by testing your layouts with larger fonts:

![Previews of app layouts at different font sizes with visible layout errors for large fonts](https://developer.android.com/studio/images/debug/layout-validation-font-sizes.png)

**Figure 7.** Variable font size previews in the Layout Validation tool
