# Set Up continuous integration

Continuous integration systems let you automatically build and test your app every time you check in updates to your source control system. You can use any continuous integration tool that can initiate a Gradle build to build your Android Studio projects.

To run tests as part of the build, you need to either configure your continuous integration server to use the [Android Emulator](https://developer.android.com/studio/run/emulator-commandline) or use [Firebase Test Lab](https://firebase.google.com/docs/test-lab/) to run your tests.

For specific information about configuring continuous integration for your Android project using Jenkins and Firebase Test Lab, see [Using Firebase Test Lab for Android with continuous integration systems](https://firebase.google.com/docs/test-lab/continuous).

**Note:** You must use the SDK Manager to accept the license agreements for any packages your app requires on each machine where you build your app. If you have not installed Android Studio on your continuous integration server, you must manually export the license agreements to your build server from a machine where they have been accepted using the SDK Manager before you can build your app on the server. To learn more about the license export process, read [Auto\-download missing packages with Gradle.](https://developer.android.com/studio/intro/update#download-with-gradle)