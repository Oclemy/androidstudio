# Troubleshoot build performance

- [Troubleshoot build performance](#troubleshoot-build-performance)
  - [Get started](#get-started)
    - [Which tasks determine build duration?](#which-tasks-determine-build-duration)
  - [Plugins with tasks determining the build’s duration](#plugins-with-tasks-determining-the-builds-duration)
  - [Tasks determining the build’s duration](#tasks-determining-the-builds-duration)
  - [Inspect warnings](#inspect-warnings)

In Android Studio 4.0 and higher with Android Gradle plugin 4.0.0 and higher, you can use the Build Analyzer to inspect the build performance of your project. The performance of your build might be affected by several different factors that can change between builds. For each build you perform, the Build Analyzer tries to present the most important information about each build, so that you can quickly identify and resolve regressions in your build performance.

This page provides an overview of the Build Analyzer, and some guidance on how to use the tool to improve the build performance of your project. If you haven't already done so, try additional strategies for improving build performance by reading [Optimize your build speed](https://developer.android.com/studio/build/optimize-your-build).

The data that the analyzer presents changes based on each build. Particularly with large, complex builds, many factors can change between builds, which changes the data that the analyzer surfaces to you as important factors in the build’s duration. So, it can be useful to build your project multiple times and compare the data in the analyzer to identify patterns.

## Get started

Each time you build your app, the Build Analyzer creates a report and displays data from the latest report in the **Build** window. To get started, proceed as follows:

1.  Make sure you’re using the latest version of Android Studio with Android Gradle plugin 4.0.0 and higher.
2.  If you haven’t already done so, build your app in one of the following ways:
    *   Click **Build > Make Project** from the menu bar.
    *   Build an Android App Bundle or APK by clicking **Build > Build Bundle(s) / APK(s) > Build Bundle(s)** or **Build > Build Bundle(s) / APK(s) > Build APK(s)** from the menu bar.
3.  Open the **Build** window by selecting **View > Tool Windows > Build** from the menu bar.
4.  In the **Build** window, click the **Build Analyzer** tab to view the build report in the **Build Analyzer**.

![](https://developer.android.com/studio/images/build/build-analyzer/plugins-breakdown.png)

**Figure 1.** The Build Analyzer provides a breakdown of plugins most responsible for your build's duration.

When you open the **Build Analyzer**, the window shows you a breakdown of the plugins that contributed the most to your build’s duration, as well as a tree to help you navigate other useful data about your build, as shown in figure 1.

It’s important to note that the Build Analyzer doesn't display data for all tasks in your build. Instead, it focuses on only the tasks that determine your build’s total duration.

### Which tasks determine build duration?

Gradle determines task execution by task interdependencies, project structure, and CPU load, and executes tasks either sequentially or in parallel. For a given build, the Build Analyzer highlights the set of sequentially executed tasks that determined the duration of the current build. Addressing inefficiencies in these highlighted tasks is the best starting point for reducing your overall build time.

Keep in mind, you might see a different set of tasks determining build duration with each build you execute. For example, if you make changes to the build configuration, run a build with a different set of tasks (such as an incremental build), or run a build under different constraints (for example, heavier CPU load), the Build Speed window may highlight a different set of tasks that most impacted that build's duration. Because of this variability, you might want to use the Build Speed window across multiple builds to consistently reduce build duration.

## Plugins with tasks determining the build’s duration

When you first open the Build Analyzer after completing a build, the window displays data about the plugins that introduce tasks determining your build’s duration, as shown in figure 1. On the right, the window provides a visual breakdown of these plugins and their relative contributions to the build duration. This allows you to quickly identify plugins that you might want to inspect first.

In the left pane, you can expand the node next to **Plugins with tasks determining the build’s duration** to see more data that the Build Analyzer has gathered for each plugin. For example, each plugin includes a child node that groups its tasks that Gradle executed during the last build and the analyzer considers as significant to the total build duration. Each node displays the total time taken to execute the direct children of the node. These durations can help you focus on inspecting the tasks with the highest impact on your build’s duration.

If the Build Analyzer asserts that some tasks could be configured to run more efficiently, it groups those tasks under a node labelled **Warnings**. You can also inspect all warnings for the build by expanding the top\-level **Warnings** node near the bottom of the left pane.

## Tasks determining the build’s duration

When you select the **Tasks determining this build’s duration** node from the tree, the Build Analyzer displays a visual breakdown of tasks that had the greatest impact on the build's duration. That is, this node doesn’t group tasks by plugin, and instead ranks each task by the time it took to execute.

Each task is color\-coded as follows:

*   **Pink:** The task belongs to the Android Gradle plugin, Java Gradle plugin, or Kotlin Gradle plugin.
*   **Blue:** The task belongs to a third party or custom plugin. For example, a plugin you might have applied after creating a new project using Android Studio.
*   **Purple:** The task doesn’t belong to a plugin but is used to dynamically modify project properties at runtime. For example, these are tasks that you might define within your `build.gradle` files.
*   **Light blue:** The task, relative to other tasks highlighted by the analyzer, does not have a high impact on the build's duration.

After expanding the **Tasks determining this build’s duration** node, you can click on each child task to learn more about its execution. For example, the analyzer provides additional detail such as the parent plugin, task type, other tasks that caused this task to run, and whether the task was executed incrementally, as shown in figure 2. The analyzer might also raise warnings that might help you configure the task to run more efficiently.

![](https://developer.android.com/studio/images/build/build-analyzer/task-breakdown.png)

**Figure 2.** Build Analyzer displaying details about a specific task execution.

## Inspect warnings

The Build Analyzer provides warnings when it detects tasks that might not be configured to run efficiently. For example, a task that does not use the appropriate Gradle APIs to specify its inputs and outputs isn't able to take advantage of [incremental builds](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks). If your build includes such tasks, the Build Analyzer might flag them with an **Always\-run** warning, because they must execute with every build, regardless of whether their inputs change.

To see all the warnings the analyzer has identified for your build, expand the top\-level **Warnings** node. After expanding the node, you can see the warnings grouped by category. For example, the **Always\-run** node groups tasks that the analyzer thinks are not properly configured for incremental builds.

When you click on a child node, the analyzer describes the nature of the warning and possible steps you can take to resolve the issue. Additionally, if the task belongs to a plugin you applied to your project, the analyzer provides a **Generate report** link, as shown in figure 3.

![](https://developer.android.com/studio/images/build/build-analyzer/inspect-warning.png)

**Figure 3.** Detailed information about Build Analyzer warnings.

When you click the **Generate report** link, a dialog appears with additional information that might help the plugin developer resolve the issue in a new version of the plugin. You can click **Copy** to save the text to your clipboard to more easily paste the information into a bug report to the plugin developer.