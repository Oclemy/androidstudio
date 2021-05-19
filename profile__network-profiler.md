# Inspect network traffic with Network Profiler

- [Inspect network traffic with Network Profiler](#inspect-network-traffic-with-network-profiler)
  - [Why you should profile your app's network activity](#why-you-should-profile-your-apps-network-activity)
  - [Network Profiler overview](#network-profiler-overview)
  - [Network connection troubleshooting](#network-connection-troubleshooting)

The Network Profiler displays realtime network activity on a timeline, showing data sent and received, as well as the current number of connections. This lets you examine how and when your app transfers data, and optimize the underlying code appropriately.

To open the Network Profiler, follow these steps:

1.  Click **View > Tool Windows > Profiler** (you can also click **Profile** ![](https://developer.android.com/studio/images/buttons/toolbar-android-profiler.png) in the toolbar).
2.  Select the device and app process you want to profile from the Android Profiler toolbar. If you've connected a device over USB but don't see it listed, ensure that you have [enabled USB debugging](https://developer.android.com/studio/debug/dev-options#enable).
3.  Click anywhere in the **NETWORK** timeline to open the Network Profiler.

To select a portion of the timeline, inspect a list of network requests sent and responses received, or view detailed information about a selected file, you must [enable advanced profiling](https://developer.android.com/studio/profile/android-profiler#advanced-profiling).

## Why you should profile your app's network activity

When your app makes a request to the network, the device must use the power\-hungry mobile or WiFi radios to send and receive packets. The radios not only use power to transfer data, but also use extra power to turn on and to stay awake.

Using the Network Profiler, you can look for frequent, short spikes of network activity, which mean that your app requires the radios to turn on frequently, or to stay awake for long periods to handle many short requests close together. This pattern indicates that you may be able to optimize your app for improved battery performance by batching network requests, thereby reducing the number of times the radios must turn on to send or receive data. This also allows the radios to switch into low\-power mode to save battery in the longer gaps between batched requests.

For more information about techniques to optimize your app's network activity, read [Reducing network battery drain](https://developer.android.com/topic/performance/power/network).

## Network Profiler overview

At the top of the window, you can see the event timeline. On the timeline, you can 1 click and drag to select a portion of the timeline to inspect the traffic.

![](https://developer.android.com/studio/images/profile/networkprofiler_2x.png)

**Figure 1.** The Network Profiler window

In the 2 pane below the timeline, select one of the following tabs for more detail about the network activity during the selected portion of the timeline:

*   **Connection View**: Lists files that were sent or received during the selected portion of the timeline across all of your app's CPU threads. For each request, you can inspect the size, type, status, and transmission duration. You can sort this list by clicking any of the column headers. You also see a detailed breakdown of the selected portion of the timeline, showing when each file was sent or received.
*   **Thread View**: Displays network activity of each of your app's CPU threads. As shown in the figure 2, this view allows you to inspect which of your app's threads are responsible for each network request. ![](https://developer.android.com/studio/images/profile/network_profiler_thread_view-2X.png)

    **Figure 2.** Inspecting network requests by app thread in the **Thread View**

From either the **Connection View** or **Thread View**, click a request name to inspect 3 detailed information about the data sent or received. Click the tabs to view the response header and body, request header and body, or call stack.

On the **Response** and **Request** tabs, click the **View Parsed** link to display formatted text and click the **View Source** link to display raw text.

![](https://developer.android.com/studio/images/profile/network-profiler-text_2X.png)

**Figure 3.** Toggle between raw text (left) and formatted text (right) by clicking the corresponding link

**Note:** If you are using the [`HttpURLConnection`](https://developer.android.com/reference/java/net/HttpURLConnection) API, you won't see headers in the **Request** tab unless you include it in your code using the [`setRequestProperty`](https://developer.android.com/reference/java/net/URLConnection#setRequestProperty(java.lang.String,%20java.lang.String)) method, as shown in the sample below.

```
URL url = new URL(MY_URL_EN);HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();...// Sets acceptable encodings in the request header.urlConnection.setRequestProperty("Accept-Encoding", "identity");
```

## Network connection troubleshooting

If the Network Profiler detects traffic values, but cannot identify any supported network requests, you will receive the following error message:

```
**Network Profiling Data Unavailable:** There is no information for thenetwork traffic you've selected.
```

Currently, the Network Profiler supports only the [`HttpURLConnection`](https://developer.android.com/reference/java/net/HttpURLConnection) and [`OkHttp`](http://square.github.io/okhttp/) libraries for network connections. If your app uses another network connection library, you may not be able to view your network activity in the Network Profiler. If you have received this error message, but your app *does* use `HttpURLConnection` or `OkHttp`, please [report a bug](https://developer.android.com/studio/report-bugs), or [search the issue tracker](https://issuetracker.google.com/issues?q=componentid:317727%2B) to include your feedback in an existing report that's related to your issue. You can also use these resources to request support for additional libraries.
