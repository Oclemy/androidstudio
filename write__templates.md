# Add code from a template

- [Add code from a template](#add-code-from-a-template)
  - [Add a project component](#add-a-project-component)
  - [Select an activity template](#select-an-activity-template)
    - [Basic Activity](#basic-activity)
    - [Bottom Navigation Activity](#bottom-navigation-activity)
    - [Empty Activity](#empty-activity)
    - [Fullscreen Activity](#fullscreen-activity)
    - [Login Activity](#login-activity)
    - [Primary/Detail Flow (Renamed and updated in 4.2 Canary 8)](#primarydetail-flow-renamed-and-updated-in-42-canary-8)
    - [Navigation Drawer Activity](#navigation-drawer-activity)
    - [Scrolling Activity](#scrolling-activity)
    - [Settings Activity](#settings-activity)
    - [Tabbed Activity](#tabbed-activity)

Android Studio provides code templates that follow the Android design and development best practices to get you on the right track to creating beautiful, functional apps. You can use templates to create new app modules, individual activities, or other specific Android project components.

Some templates provide starter code for common usage contexts, such as navigation drawers or login screens. You can choose from these app module and activity templates when you first [create your project](https://developer.android.com/studio/projects/create-project), when you [add a new app module within an existing project](https://developer.android.com/studio/projects/add-app-module), or when you add a new activity within an app module.

In addition to activities, you can also add other Android project components to an existing app using templates. These templates include both code components, such as services and fragments, and non\-code components, such as folders and XML files.

This page discusses how to add Android project components like activities to your project and describes the commonly used activity templates available in Android Studio. Note that most templates depend on the [Android Support Library](https://developer.android.com/tools/support-library/features) to include user interface principles based on [material design](https://developer.android.com/design/material).

## Add a project component

![](https://developer.android.com/studio/images/projects/templates-menu_2-2-beta_2x.png)

**Figure 1**. The templates menu, accessible through the **File** > **New** menu or by right\-clicking in the **Project** window.

The list of templates provided in Android Studio is constantly growing. Android Studio groups templates by the type of component that they add, such as an **Activity** or an **XML** file, as shown in figure 1.

To add an Android project component using a template, use the **Project** ![](https://developer.android.com/studio/images/buttons/window-project.png) window. Right\-click on the folder in which you want to add the new component, and select **New**. Based on what components can be added to the folder you clicked on, you then see a list of template types like those shown in figure 1.

When you select the template you want to add, a corresponding wizard window appears and asks for the component's configuration information, such as its name. After you enter the configuration information, Android Studio creates and opens the files for your new component. It also runs a Gradle build to sync your project.

Although you can also use the **File** > **New** menu of Android Studio to create a new Android project component, navigating to your desired folder in the **Project** window ensures that you create the component in the correct place.

## Select an activity template

One of the most common uses of templates is adding new activities to an existing app module. For example, to create a login screen for your app’s users, add an activity with the Login Activity template.

This section covers the commonly used activity templates for phone and tablet apps. Android Studio also provides templates for a variety of different app module types, including Wear OS, Android TV, and Cloud App Engine. You can view templates for these different module types when [creating an app module](#CreateAppModule). Templates also exist for more API\-specific modules and activities, such as Google AdMobs Ads and Google Maps.

The following phone and tablet templates provide you with the code components for specific usage contexts, such as logging into an account, presenting a list of items with details, or scrolling through a long block of text. Each can serve as either an entire app module or an individual activity.

### Basic Activity

![](https://developer.android.com/studio/images/projects/basic-activity-template_2-2_2x.png)

This template creates a simple app with an app bar and a floating action button. It acts as a starting point for your project by providing commonly used UI components.

This template includes:

*   `[AppBar](https://developer.android.com/training/appbar)`
*   `[FloatingActionButton](https://developer.android.com/reference/com/google/android/material/floatingactionbutton/FloatingActionButton)`
*   Two layout files: one for the activity and one to separate out text content

### Bottom Navigation Activity

![](https://developer.android.com/studio/images/projects/bottomnav-activity-template_2x.png)

This template provides a standard bottom navigation bar for an activity to make it easy for users to explore and switch between top\-level views in a single tap. Use this template when your application has three to five top\-level destinations. For more information, see the [Bottom Navigation Component](https://material.io/guidelines/components/bottom-navigation.html) design guidelines.

This template includes:

*   `[AppBar](https://developer.android.com/training/appbar)`
*   Single layout file with sample layout for bottom navigation

### Empty Activity

![](https://developer.android.com/studio/images/projects/empty-activity-template_2-2_2x.png)

This template creates an empty activity and a single layout file with sample text content. It allows you to start from scratch when building your app module or activity.

This template includes:

*   Single layout file with text content

### Fullscreen Activity

![](https://developer.android.com/studio/images/projects/fullscreen-activity-template_2-2_2x.png)

This template creates an app that alternates between a primary fullscreen view and a view with standard user interface (UI) controls. The fullscreen view is the default and a user can activate the standard view by touching the device screen.

This template includes:

*   Touch listener implementation for hiding the standard view elements
*   Button that appears in the standard view but does not do anything
*   `[AppBar](https://developer.android.com/training/appbar)` for the standard view
*   Single layout file with both the fullscreen view and a frame layout for standard view elements

### Login Activity

![](https://developer.android.com/studio/images/projects/login-activity-template_2-2_2x.png)

This template creates a standard login screen. The user interface includes email and password fields and a sign\-in button. It is more commonly used as an activity template than as an app module template.

This template includes:

*   `[AsyncTask](https://developer.android.com/reference/android/os/AsyncTask)` implementation for handling network operations separately from the main user interface thread
*   Progress indicator during network operations
*   Single layout file with the recommended login UI:
    *   Email and password input fields
    *   Sign\-in button

### Primary/Detail Flow (Renamed and updated in 4.2 Canary 8)

![](https://developer.android.com/studio/images/projects/master-detail-flow-template_2-2_2x.png)

This template creates an app that has both an item list display and a display for the details of an individual item. Clicking on an item on the list screen opens a screen with the item’s details. The layout of the two displays depends on the device that is running the app.

This template also provides API stubs for handling certain mouse and keyboard inputs such as right\-click actions on the list items as well as common keyboard shortcuts.

This template includes:

*   Fragment representing the list of items
*   Fragment displaying an individual item’s details
*   `[FloatingActionButton](https://developer.android.com/reference/com/google/android/material/floatingactionbutton/FloatingActionButton)` on each screen
*   [Collapsing toolbar](https://developer.android.com/reference/com/google/android/material/appbar/CollapsingToolbarLayout) for the item detail screen
*   [Alternative resource](https://developer.android.com/guide/topics/resources/providing-resources#AlternativeResources) layout files for different device configurations
*   [ContextClickListener](https://developer.android.com/reference/android/view/View.OnContextClickListener) on the list items to handle right\-click actions
*   [OnUnhandledKeyEventListener](https://developer.android.com/reference/androidx/core/view/ViewCompat.OnUnhandledKeyEventListenerCompat) to detect keyboard shortcuts on the item list fragment

### Navigation Drawer Activity

![](https://developer.android.com/studio/images/projects/navigation-drawer-activity-template_2-2_2x.png)

This template creates a **Basic Activity** with a navigation drawer menu. The navigation bar expands from the left or right side of your app and appears in addition to the regular app bar.

This template includes:

*   Navigation drawer implementation with a `[DrawerLayout](https://developer.android.com/reference/androidx/drawerlayout/widget/DrawerLayout)`, corresponding event handlers, and example menu options
*   `[AppBar](https://developer.android.com/training/appbar)`
*   `[FloatingActionButton](https://developer.android.com/reference/com/google/android/material/floatingactionbutton/FloatingActionButton)`
*   Layout files for the navigation drawer and the navigation drawer header, in addition to those from the **Basic Activity** template

### Scrolling Activity

![](https://developer.android.com/studio/images/projects/scrolling-activity-template_2-2_2x.png)

This template creates an app with a collapsing toolbar and a scrolling view for long text content. As you scroll down the page, the toolbar, which can serve as a header, automatically condenses, and the floating action button disappears.

This template includes:

*   [Collapsing toolbar](https://developer.android.com/reference/com/google/android/material/appbar/CollapsingToolbarLayout) in place of the regular `[AppBar](https://developer.android.com/training/appbar)`
*   `[FloatingActionButton](https://developer.android.com/reference/com/google/android/material/floatingactionbutton/FloatingActionButton)`
*   Two layout files: one for the activity and one to separate out the text content into a `[NestedScrollView](https://developer.android.com/reference/androidx/core/widget/NestedScrollView)`

### Settings Activity

![](https://developer.android.com/studio/images/projects/settings-activity-template_2-2_2x.png)

This template creates an activity that displays [user preferences or settings](https://developer.android.com/guide/topics/ui/settings) for an app. It extends the `[PreferenceActivity](https://developer.android.com/reference/android/preference/PreferenceActivity)` class and is more commonly used as an activity template than as an app module template.

This template includes:

*   Activity that extends `[PreferenceActivity](https://developer.android.com/reference/android/preference/PreferenceActivity)`
*   XML files (in the `res/xml/` directory of your project) to define the displayed settings

### Tabbed Activity

![](https://developer.android.com/studio/images/projects/tabbed-activity-template_2-2_2x.png)

This template creates an app with multiple sections, [swiping navigation](https://developer.android.com/training/implementing-navigation/lateral), and an app bar. The sections are defined as fragments between which you can swipe left and right to navigate.

This template includes:

*   `[AppBar](https://developer.android.com/training/appbar)`
*   Adapter that extends `[FragmentPagerAdapter](https://developer.android.com/reference/androidx/fragment/app/FragmentPagerAdapter)` and creates a fragment for each section
*   `[ViewPager](https://developer.android.com/reference/androidx/viewpager/widget/ViewPager)` instance, a layout manager for swiping between sections
*   Two layout files: one for the activity and one for individual fragments