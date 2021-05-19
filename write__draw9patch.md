# Create resizable bitmaps (9\-Patch files)

The Draw 9\-patch tool is a WYSIWYG editor included in Android Studio that allows you to create bitmap images that automatically resize to accommodate the contents of the view and the size of the screen. Selected parts of the image are scaled horizontally or vertically based on indicators drawn within the image.

For an introduction to NinePatch graphics and how they work, please read the section about NinePatch Drawables in the [Canvas and Drawables](https://developer.android.com/guide/topics/graphics/2d-graphics#nine-patch) document.

![](https://developer.android.com/studio/images/write/draw9patch-normal_2-2_2x.png)

**Figure 1.** A NinePatch image in Android Studio's Draw 9\-patch tool.

Here's a quick guide to creating a NinePatch graphic using the Draw 9\-patch tool in Android Studio. You'll need the PNG image with which you'd like to create a NinePatch image.

1.  In Android Studio, right\-click the PNG image you'd like to create a NinePatch image from, then click **Create 9\-patch file**.
2.  Type a file name for your NinePatch image, and click **OK**. Your image will be created with the `.9.png` file extension.
3.  Double\-click your new NinePatch file to open it in Android Studio. Your workspace will now open.

    The left pane is your drawing area, in which you can edit the lines for the stretchable patches and content area. The right pane is the preview area, where you can preview your graphic when stretched.

4.  Click within the 1\-pixel perimeter to draw the lines that define the stretchable patches and (optional) content area. Right\-click (or hold Shift and click, on Mac) to erase previously drawn lines.
5.  When done, click **File** > **Save** to save your changes.

You can open an existing NinePatch file in Android Studio by double\-clicking the file.

To make sure that your NinePatch graphics scale down properly, verify that any stretchable regions are at least 2x2 pixels in size. Otherwise, they may disappear when scaled down. Also, provide one pixel of extra safe space in the graphics before and after stretchable regions to avoid interpolation during scaling that may cause the color at the boundaries to change.

**Note:** A normal PNG file (`*.png`) will be loaded with an empty one\-pixel border added around the image, in which you can draw the stretchable patches and content area. A previously saved NinePatch file (`*.9.png`) will be loaded as\-is, with no drawing area added, because it already exists.

![](https://developer.android.com/studio/images/write/draw9patch-badpatch_2-2_2x.png)

**Figure 2.** A NinePatch image showing content, patches, and bad patches.

Optional controls include:

*   **Zoom**: Adjust the zoom level of the graphic in the drawing area.
*   **Patch scale**: Adjust the scale of the images in the preview area.
*   **Show lock**: Visualize the non\-drawable area of the graphic on mouse\-over.
*   **Show patches**: Preview the stretchable patches in the drawing area (pink is a stretchable patch), as shown in figure 2, above.
*   **Show content**: Highlight the content area in the preview images (purple is the area in which content is allowed), as shown in figure 2.
*   **Show bad patches**: Adds a red border around patch areas that may produce artifacts in the graphic when stretched, as shown in figure 2. Visual coherence of your stretched image will be maintained if you eliminate all bad patches.

