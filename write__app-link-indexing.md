# Add Android App Links

- [Add Android App Links](#add-android-app-links)
  - [Add intent filters](#add-intent-filters)
  - [Handle incoming links](#handle-incoming-links)
  - [Associate your app with your website](#associate-your-app-with-your-website)
  - [Test your App Links](#test-your-app-links)
  - [Add Firebase App Indexing](#add-firebase-app-indexing)

Android App Links are HTTP URLs that bring users directly to specific content in your Android app. Android App Links can drive more traffic to your app, help you discover which app content is used most, and make it easier for users to share and find content in an installed app.

To add support for Android App Links:

1.  Create intent filters in your manifest.
2.  Add code to your app’s activities to handle incoming links.
3.  Associate your app and your website with Digital Asset Links.

The App Links Assistant in Android Studio 2.3 and higher simplifies the process in a step\-by\-step wizard, as described below.

For more information about how app links work and the benefits they offer, read [Handling Android App Links](https://developer.android.com/training/app-links).

## Add intent filters

The App Links Assistant in Android Studio can help you create [intent filters](https://developer.android.com/training/app-links/deep-linking#adding-filters) in your manifest and map existing URLs from your website to activities in your app. The App Links Assistant also adds template Java code in each corresponding activity to handle the intent.

To add intent filters and URL handling, follow these steps:

1.  Select **Tools > App Links Assistant**.
2.  Click **Open URL Mapping Editor** and then click **Add** ![](https://developer.android.com/studio/images/buttons/ic_plus.png) at the bottom of the **URL Mapping** list to add a new URL mapping.
3.  Add details for the new URL mapping:
    ![The App Links Assistant walks you through basic URL mapping](https://developer.android.com/studio/images/write/app-links-assistant-URL-mapping_2x.png)

    **Figure 1.** Add basic details about your site's link structure to map URLs to activities in your app.

    1.  Enter your website's URL in the **Host** field.
    2.  Add a [**path**, **pathPrefix**, or **pathPattern**](https://developer.android.com/guide/topics/manifest/data-element#path) for the URLs you want to map. For example, if you have a recipe\-sharing app, with all the recipes available in the same activity, and your corresponding website's recipes are all in the same */recipe* directory, use **pathPrefix** and enter */recipe.* This way, the URL *http://www.recipe\-app.com/recipe/grilled\-potato\-salad* maps to the activity you select in the following step.
    3.  Select the **Activity** the URLs should take users to.
    4.  Click **OK.**
4.  The App Links Assistant adds intent filters based on your URL mapping to the `AndroidManifest.xml` file, and highlights it in the **Preview** field. If you'd like to make any changes, click **Open AndroidManifest.xml** to edit the intent filter. (Learn more about [intent filters in Android](https://developer.android.com/training/app-links/deep-linking#adding-filters).)

    **Note:** To support more links without updating your app, you should define a URL mapping that supports URLs that you'll add in the future. Also, remember to include a URL for your app home screen so it's included in search results.

5.  To verify your URL mapping works properly, enter a URL in the **Check URL Mapping** field and click **Check Mapping**. If it's working correctly, the success message shows that the URL you entered maps to the activity you selected.

## Handle incoming links

Once you've verified that your URL mapping is working correctly, add logic to handle the intent you created.

1.  Click **Select Activity** from the App Links Assistant.
2.  Select an activity from the list and click **Insert Code**.

The App Links Assistant adds code to your activity's Java file, similar to the following ( Please note: Currently the App Links Assistant does not support Kotlin so you will need to add this code manually):

[Kotlin](#kotlin) [Java](#java) [More](#)

override  fun onCreate(savedInstanceState:  Bundle?)  { super.onCreate(savedInstanceState) val appLinkIntent \= intent val appLinkAction \= appLinkIntent.action val appLinkData \= appLinkIntent.data

}

// ATTENTION: This was auto\-generated to handle app links.
Intent appLinkIntent \= getIntent();
String appLinkAction \= appLinkIntent.getAction();
Uri appLinkData \= appLinkIntent.getData();

However, this code isn't complete on its own. You must now take an action based on the URI in `appLinkData`, such as display the corresponding content. For example, for the recipe\-sharing app, your code might look like the following sample:

[Kotlin](#kotlin) [Java](#java) [More](#)

override  fun onCreate(savedInstanceState:  Bundle?)  { super.onCreate(savedInstanceState) ...
    handleIntent(intent)
}

override  fun onNewIntent(intent:  Intent)  { super.onNewIntent(intent)
    handleIntent(intent)
}

private  fun handleIntent(intent:  Intent)  { val appLinkAction \= intent.action val appLinkData:  Uri?  \= intent.data if  (Intent.ACTION\_VIEW \== appLinkAction)  {
        appLinkData?.lastPathSegment?.also { recipeId \-> Uri.parse("content://com.recipe\_app/recipe/") .buildUpon() .appendPath(recipeId) .build().also { appData \->
                        showRecipe(appData) } } }
}

protected  void onCreate(Bundle savedInstanceState)  { super.onCreate(savedInstanceState); ...
  handleIntent(getIntent());
}

protected  void onNewIntent(Intent intent)  { super.onNewIntent(intent);
  handleIntent(intent);
}

private  void handleIntent(Intent intent)  { String appLinkAction \= intent.getAction(); Uri appLinkData \= intent.getData(); if  (Intent.ACTION\_VIEW.equals(appLinkAction)  && appLinkData !=  null){ String recipeId \= appLinkData.getLastPathSegment(); Uri appData \=  Uri.parse("content://com.recipe\_app/recipe/").buildUpon() .appendPath(recipeId).build();
        showRecipe(appData); }
}

## Associate your app with your website

After setting up URL support for your app, the App Links Assistant generates a [Digital Asset Links](https://developer.android.com/training/app-links/verify-site-associations#web-assoc) file you can use to associate your website with your app.

As an alternative to using the Digital Asset Links file, you can [associate your site and app in Search Console](https://support.google.com/webmasters/answer/6212023).

To associate your app and your website using the App Links Assistant, click **Open Digital Asset Links File Generator** from the App Links Assistant and follow these steps:

![The App Links Assistant walks you through basic URL mapping](https://developer.android.com/studio/images/write/app-links-assistant-dal-file-generator_2x.png)

**Figure 2.** Enter details about your site and app to generate a Digital Asset Links file.

1.  Enter your **Site domain** and your [**Application ID**](https://developer.android.com/studio/build/application-id).
2.  To include support in your Digital Asset Links file for [Smart Lock for Passwords](https://developers.google.com/identity/smartlock-passwords/android/associate-apps-and-sites), select **Support sharing credentials between the app and the website** and enter your site's login URL. This adds the following string to your Digital Asset Links file declaring that your app and website share sign\-in credentials: `delegate_permission/common.get_login_creds`. Learn more about supporting [Smart Lock for Passwords](https://developers.google.com/identity/smartlock-passwords/android/) in your app.
3.  Specify the [signing config](https://developer.android.com/studio/publish/app-signing#sign-auto) or select a [keystore file](https://developer.android.com/studio/publish/app-signing#overview). Make sure you select the right config or keystore file for either the release build or debug build of your app. If you want to set up your production build, use the release config. If you want to test your build, use the debug config.
4.  Click **Generate Digital Asset Links file**.
5.  Once Android Studio generates the file, click **Save file** to download it.
6.  Upload the `assetlinks.json` file to your site, with read\-access for everyone, at `https://<yoursite>/.well-known/assetlinks.json`.

    **Important:** The system verifies the Digital Asset Links file via the encrypted HTTPS protocol. Make sure that the `assetlinks.json` file is accessible over an HTTPS connection, regardless of whether your app's intent filter includes `https`.

7.  Click **Link and Verify** to confirm that you've uploaded the correct Digital Asset Links file to the correct location.

Learn more about associating your website with your app through the Digital Asset Links file in [Declare Website Associations](https://developer.android.com/training/app-links/verify-site-associations#web-assoc).

## Test your App Links

To verify that your links open the correct activity, follow these steps:

1.  Click **Test App Links** in the App Links Assistant.
2.  Enter the URL you want to test in the **URL** field, for example, *http://recipe\-app.com/recipe/grilled\-potato\-salad*.

![](https://developer.android.com/studio/images/write/app-links-assistant-link-testing_2x.png)

**Figure 3.** The App Links Assistant displays a success message and opens your app to the specified content when the URL you're testing successfully maps to an activity in your app.

4.  Click **Run Test**.
5.  If the URL mapping isn't set up properly or doesn't exist, an error message appears under the URL in the **Test App Links** window. If the URL mapping exists, Android Studio launches your app in the device or emulator at the specified activity without showing the disambiguation dialog (app "chooser"), and shows a success message in the **App Link Testing** window. If Android Studio can't successfully launch the app, an error message appears in Android Studio's **Run** window.

To test Android App Links through the App Links Assistant, you must have a device connected or a virtual device available running Android 6.0 (API level 23) or higher. For more information, see how to [connect a device](https://developer.android.com/studio/run/device) or [create an AVD](https://developer.android.com/studio/run/managing-avds#createavd).

## Add Firebase App Indexing

After adding Android App Links to your app, you can add Firebase App Indexing code to an activity to get re\-engagement to your app from additional Google Search features, including autocomplete suggestions and In Apps search. Learn more about Firebase App Indexing in the [Firebase App Indexing documentation.](https://firebase.google.com/docs/app-indexing/)

To add Firebase App Indexing to your app, use the [Firebase Assistant](https://developer.android.com/studio/write/firebase) in Android Studio and expand the **App Indexing** section for step\-by\-step instructions.