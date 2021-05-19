# Set up Android Emulator networking

- [Set up Android Emulator networking](#set-up-android-emulator-networking)
  - [Network address space](#network-address-space)
  - [Local networking limitations](#local-networking-limitations)
  - [Using network redirection](#using-network-redirection)
      - [Setting up redirection through the Emulator Console](#setting-up-redirection-through-the-emulator-console)
      - [Setting up redirection through adb](#setting-up-redirection-through-adb)
  - [Configuring the emulator DNS settings](#configuring-the-emulator-dns-settings)
  - [Using the emulator with a proxy](#using-the-emulator-with-a-proxy)
  - [Interconnecting emulator instances](#interconnecting-emulator-instances)
  - [Sending a voice call or SMS to another emulator instance](#sending-a-voice-call-or-sms-to-another-emulator-instance)

The emulator provides versatile networking capabilities that you can use to set up complex modeling and testing environments for your app. The following sections introduce the emulator network architecture and capabilities.

## Network address space

Each instance of the emulator runs behind a virtual router/firewall service that isolates it from your development machine network interfaces and settings and from the internet. An emulated device can't see your development machine or other emulator instances on the network. Instead, it sees only that it is connected through Ethernet to a router/firewall.

The virtual router for each instance manages the 10.0.2/24 network address space — all addresses managed by the router are in the form of 10.0.2. xx, where xx is a number. Addresses within this space are pre\-allocated by the emulator/router as follows:

| Network Address | Description |
| --- | --- |
| 10.0.2.1 | Router/gateway address |
| 10.0.2.2 | Special alias to your host loopback interface (i.e., 127.0.0.1 on your development machine) |
| 10.0.2.3 | First DNS server |
| 10.0.2.4 / 10.0.2.5 / 10.0.2.6 | Optional second, third and fourth DNS server (if any) |
| 10.0.2.15 | The emulated device network/ethernet interface |
| 127.0.0.1 | The emulated device loopback interface |

Note that the same address assignments are used by all running emulator instances. That means that if you have two instances running concurrently on your machine, each will have its own router and, behind that, each will have an IP address of 10.0.2.15. The instances are isolated by a router and can *not* see each other on the same network. For information about how to let emulator instances communicate over TCP/UDP, see [Interconnecting Emulator Instances](#connecting).

Also note that the address 127.0.0.1 on your development machine corresponds to the emulator's own loopback interface. If you want to access services running on your development machine loopback interface (a.k.a. 127.0.0.1 on your machine), you should use the special address 10.0.2.2 instead.

Finally, note that the pre\-allocated addresses of an emulated device are specific to the Android Emulator and will probably be very different on real devices (which are also very likely to be NAT\-ed, specifically, behind a router/firewall).

## Local networking limitations

Android apps running in an emulator can connect to the network available on your workstation. However, apps connect through the emulator, not directly to hardware, and the emulator acts like a normal app on your workstation. This can cause some limitations:

*   Communication with the emulated device may be blocked by a firewall program running on your machine.
*   Communication with the emulated device may be blocked by another (physical) firewall/router to which your machine is connected.

The emulator virtual router should be able to handle all outbound TCP and UDP connections/messages on behalf of the emulated device, provided your development machine network environment allows it to do so. There are no built\-in limitations on port numbers or ranges except the one imposed by your host operating system and network.

Depending on the environment, the emulator might not be able to support other protocols (such as ICMP, used for "ping"). Currently, the emulator does not support IGMP or multicast.

## Using network redirection

To communicate with an emulator instance behind its virtual router, you need to set up network redirection on the virtual router. Clients can then connect to a specified guest port on the router, while the router directs traffic to/from that port to the emulated device host port.

To set up the network redirection, you create a mapping of host and guest ports/addresses on the emulator instance. There are two ways to set up network redirection: using emulator console commands and using the adb tool, as described below.

#### Setting up redirection through the Emulator Console

Each emulator instance provides a control console that you can connect to, to issue commands that are specific to that instance. You can use the `redir` console command to set up redirection as needed for an emulator instance.

First, determine the console port number for the target emulator instance. For example, the console port number for the first emulator instance launched is 5554. Next, connect to the console of the target emulator instance, specifying its console port number, as follows:

```
telnet localhost 5554
```

Once connected, use the `redir` command to work with redirection. To add a redirection, use:

```
add <protocol>:<host-port>:<guest-port>
```

where `<protocol>` is either `tcp` or `udp`, and `<host-port>` and `<guest-port>` set the mapping between your own machine and the emulated system, respectively.

For example, the following command sets up a redirection that handles all incoming TCP connections to your host (development) machine on 127.0.0.1:5000 and will pass them through to the emulated system on 10.0.2.15:6000:

redir add tcp:5000:6000

To delete a redirection, you can use the `redir del` command. To list all redirection for a specific instance, you can use `redir list`. For more information about these and other console commands, see [Using the Emulator Console](https://developer.android.com/studio/run/emulator-console).

Note that port numbers are restricted by your local environment. This typically means that you cannot use host port numbers under 1024 without special administrator privileges. Also, you won't be able to set up a redirection for a host port that is already in use by another process on your machine. In that case, `redir` generates an error message to that effect.

#### Setting up redirection through adb

The Android Debug Bridge (adb) tool provides port forwarding, an alternate way for you to set up network redirection. For more information, see [Forwarding Ports](https://developer.android.com/studio/command-line/adb#forwardports) in the adb documentation.

Note that adb does not currently offer any way to remove a redirection, except by killing the adb server.

## Configuring the emulator DNS settings

At startup, the emulator reads the list of DNS servers that your system is currently using. It then stores the IP addresses of up to four servers on this list and sets up aliases to them on the emulated addresses 10.0.2.3, 10.0.2.4, 10.0.2.5 and 10.0.2.6 as needed.

On Linux and OS X, the emulator obtains the DNS server addresses by parsing the file `/etc/resolv.conf`. On Windows, the emulator obtains the addresses by calling the `GetNetworkParams()` API. Note that this usually means that the emulator ignores the content of your "hosts" file ( `/etc/hosts` on Linux/OS X, `%WINDOWS%/system32/HOSTS` on Windows).

When starting the emulator at the command line, you can also use the `-dns-server <serverList>` option to manually specify the addresses of DNS servers to use, where `<serverList>` is a comma\-separated list of server names or IP addresses. You might find this option useful if you encounter DNS resolution problems in the emulated network (for example, an "Unknown Host error" message that appears when using the web browser).

## Using the emulator with a proxy

On many corporate networks, direct connections to the internet don't work (they're refused by the network administrators), except if they happen through a specific proxy. Web browsers and other corporate apps are preconfigured to use the proxy, so you can browse the web. For regular apps, like the emulator, they need to know that there's a proxy and that they need to connect to it.

Due to the nature of HTTP, a direct web server connection and a connection through a proxy result in different GET requests. The emulator transparently rewrites the GET requests from the virtual device before talking to the proxy so it works.

If your emulator must access the internet through a proxy server, you can configure a custom HTTP proxy from the emulator's **Extended controls** screen. With the emulator open, click **More** ![](https://developer.android.com/studio/images/buttons/emulator-extended-controls.png), and then click **Settings** and **Proxy**. From here, you can define your own HTTP proxy settings.

![](https://developer.android.com/studio/images/run/emulator-proxy-settings_2x.png)

Alternatively, you can configure a proxy from the command line with the `-http-proxy <proxy>` option when starting the emulator. In this case, you specify proxy information in `<proxy>` in one of these formats:

http://<machineName>:<port>

or

http://<username>:<password>@<machineName>:<port>

The `-http-proxy` option forces the emulator to use the specified HTTP/HTTPS proxy for all outgoing TCP connections. Redirection for UDP is not currently supported.

Alternatively, you can define the environment variable `http_proxy` to the value you want to use for `<proxy>`. In this case, you do not need to specify a value for `<proxy>` in the `-http-proxy` command — the emulator checks the value of the `http_proxy` environment variable at startup and uses its value automatically, if defined.

You can use the `-debug-proxy` option to diagnose proxy connection problems.

## Interconnecting emulator instances

To allow one emulator instance to communicate with another, you must set up the necessary network redirection as illustrated below.

Assume that your environment is

*   A is your development machine
*   B is your first emulator instance, running on A
*   C is your second emulator instance, also running on A

and you want to run a server on B, to which C will connect, here is how you could set it up:

1.  Set up the server on B, listening to `10.0.2.15:<serverPort>`
2.  On the B console, set up a redirection from `A:localhost:<localPort>` to `B:10.0.2.15:<serverPort>`
3.  On C, have the client connect to `10.0.2.2:<localPort>`

For example, if you wanted to run an HTTP server, you can select `<serverPort>` as 80 and `<localPort>` as 8080:

*   B listens on 10.0.2.15:80
*   On the B console, issue `redir add tcp:8080:80`
*   C connects to 10.0.2.2:8080

## Sending a voice call or SMS to another emulator instance

The emulator automatically forwards simulated voice calls and SMS messages from one instance to another. To send a voice call or SMS, use the dialer app or SMS app, respectively, from one of the emulators.

To initiate a simulated voice call to another emulator instance:

1.  Launch the dialer app on the originating emulator instance.
2.  As the number to dial, enter the console port number of the instance you'd like to call. You can determine the console port number of the target instance by checking its window title, where the console port number is reported as "Android Emulator (<port>).
3.  Press "Dial". A new inbound call appears in the target emulator instance.

To send an SMS message to another emulator instance, launch the SMS app (if available). Specify the console port number of the target emulator instance as as the SMS address, enter the message text, and send the message. The message is delivered to the target emulator instance.

You can also connect to an emulator console to simulate an incoming voice call or SMS. For more information, see [Telephony Emulation](https://developer.android.com/studio/run/emulator-console#telephony) and [SMS Emulation](https://developer.android.com/studio/run/emulator-console#sms).