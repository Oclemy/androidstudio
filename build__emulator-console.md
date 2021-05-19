# Send Emulator console commands

- [Send Emulator console commands](#send-emulator-console-commands)
  - [Start and stop a console session](#start-and-stop-a-console-session)
  - [Emulator command reference](#emulator-command-reference)
    - [Fingerprint simulation and validation](#fingerprint-simulation-and-validation)

Each running virtual device provides a console that lets you query and control the emulated device environment. For example, you can use the console to manage port redirection, network characteristics, and telephony events while your app is running on the emulator.

The following commands require that you already have an emulator running. For more information about running an emulator, see [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) and [Start the Emulator from the Command Line](https://developer.android.com/studio/run/emulator-commandline).

## Start and stop a console session

To access the console and enter commands, from a terminal window, use `telnet` to connect to the console port and provide your authentication token. Each time the console displays **OK**, it's ready to accept commands. It doesn't have a typical prompt.

To connect to the console of a running virtual device:

1.  Open a terminal window and enter the following command:

telnet localhost console\-port

The emulator window title lists the console port number. For example, the window title for an emulator using console port 5554 could be `Nexus_5X_API_23:5554`. Also, the `adb devices` command prints a list of running virtual devices and their console port numbers. For more information, see [Querying for Emulator/Device Instances](https://developer.android.com/studio/command-line/adb#devicestatus).

**Note:** The emulator listens for connections on ports 5554 to 5585 and accepts connections from localhost only.

5.  After the console displays `OK`, enter the `auth auth_token` command.

Before you can enter [console commands](#querycontrol), the emulator console requires authentication. `auth_token` must match the contents of the `.emulator_console_auth_token` file in your home directory.

If that file doesn't exist, the `telnet localhost console-port` command creates the file, which contains a randomly generated authentication token. To disable authentication, delete the token from the `.emulator_console_auth_token` file or create an empty file if it doesn't exist.

8.  After you're connected to the console, enter [console commands](#querycontrol).

Enter `help`, `help command`, or `help-verbose` to see a list of console commands and learn about specific commands.

10.  To exit the console session, enter `quit` or `exit`.

Here's an example session:

$ **telnet localhost 5554**
Trying ::1...
telnet: connect to address ::1: Connection refused
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^\]'.
Android Console: Authentication required
Android Console: type 'auth <auth\_token>' to authenticate
Android Console: you can find your <auth\_token> in
'/Users/me/.emulator\_console\_auth\_token'
OK
**auth 123456789ABCdefZ**
Android Console: type 'help' for a list of commands
OK
**help\-verbose**
Android console command help:
    help|h|?         print a list of commands
    help\-verbose     print a list of commands with descriptions
    ping             check if the emulator is alive
    automation       manage emulator automation
    event            simulate hardware events
    geo              Geo\-location commands
    gsm              GSM related commands
    cdma             CDMA related commands
    crash            crash the emulator instance
    crash\-on\-exit    simulate crash on exit for the emulator instance
    kill             kill the emulator instance
    restart          restart the emulator instance
    network          manage network settings
    power            power related commands
    quit|exit        quit control session
    redir            manage port redirections
    sms              SMS related commands
    avd              control virtual device execution
    qemu             QEMU\-specific commands
    sensor           manage emulator sensors
    physics          manage physical model
    finger           manage emulator finger print
    debug            control the emulator debug output tags
    rotate           rotate the screen clockwise by 90 degrees
    screenrecord     Records the emulator's display
    fold             fold the device
    unfold           unfold the device
    multidisplay     configure the multi\-display

try 'help <command>' for command\-specific help
OK
**exit**
Connection closed by foreign host.

## Emulator command reference

The following table describes the emulator console commands with their parameters and values.

**Table 1.** Emulator console commands

| General Commands | Description |
| --- | --- |
| `avd {stop|start|status|name}` | Query, control, and manage the virtual device, as follows:
*   `stop`: Stop the execution of the device.
*   `start`: Start the execution of the device.
*   `status`: Query the virtual device status, which can be `running` or `stopped`.
*   `name`: Query the virtual device name.

 |
| `avd snapshot {list|save name|load name|delete name}` | Save and restore the device state in snapshots, as follows:

*   `list`: List all saved snapshots.
*   `save name`: Save the snapshot as name.
*   `load name`: Load the named snapshot.
*   `delete name`: Delete the named snapshot.

The following example saves a snapshot with the name `firstactivitysnapshot`.

avd snapshot save firstactivitysnapshot

 |
| `fold` | Fold the device to display its smaller screen configuration (if the device is foldable and currently unfolded). |
| `unfold` | Unfold the device to display its larger screen configuration (if the device is foldable and currently folded). |
| `kill` | Terminate the virtual device. |
| `ping` | Check whether the virtual device is running. |
| `rotate` | Rotate the AVD counterclockwise in 45 degree increments. |
| Crash the emulator | Description |
| `crash` | Crash the emulator during app execution. |
| `crash-on-exit` | Crash the emulator when the app exits. |
| Debug tags | Description |
| `debug tags ...` |

Enable or disable debug messages from specific parts of the emulator. The tags parameter must be a value from the list of debug tags that appears when you execute `emulator -help-debug-tags`. For more information about the `-help-debug-tags` option, see [Commonly used emulator options](https://developer.android.com/studio/run/emulator-commandline#common).

The following example enables the `radio` tag:

debug radio

 |
| Port redirection | Description |
| `redir list` | List the current port redirection. |
| `redir add protocol:host-port:guest-port` | Add a new port redirection, as follows:

*   `protocol`: Must be either `tcp` or `udp`.
*   `host-port`: The port number to open on the host.
*   `guest-port`: The port number to route data to on the emulator.

 |
| `redir del protocol:host-port` | Delete a port redirection.

*   `protocol`: Must be either `tcp` or `udp`.
*   `host-port`: The port number to open on the host.

 |
| Geographic location | Description |
| Set the geographic location reported to the apps running inside an emulator by sending a GPS fix to the emulator.

You can issue the `geo` command as soon as a virtual device is running. The emulator sets the location you enter by creating a mock location provider. This provider responds to location listeners set by apps, and also supplies the location to the `[LocationManager](https://developer.android.com/reference/android/location/LocationManager)`. Any app can query the location manager to obtain the current GPS fix for the emulated device by calling `[LocationManager](https://developer.android.com/reference/android/location/LocationManager).getLastKnownLocation("gps")`

 |
| `geo fix longitude latitude satellites [altitude]` | Send a simple GPS fix to the emulator. Specify `longitude` and `latitude` in decimal degrees. Use a number from 1 to 12 to specify the number of `satellites` to use to determine the position, and specify `altitude` in meters. |
| `geo nmea sentence` | Send an NMEA 0183 sentence to the emulated device, as if it were sent from an emulated GPS modem. Start `sentence` with '$GP'. Only '$GPGGA' and '$GPRCM' sentences are currently supported. The following example is a GPGGA (Global Positioning System Fix Data) sentence that gets the time, position, and fix data for a GPS receiver:

geo nmea $GPGGA ,hhmmss.ss,llll.ll,a,yyyyy.yy,a,x,xx,x.x,x.x,M,x.x,M,x.x,xxxx

 |
| Fake hardware events | Description |
| `event types` | List all fake event types. For events that have codes, the number of codes is listed in parens on the right.

**event types**
event <type> can be an integer or one of the following aliases:
    EV\_SYN
    EV\_KEY    (405 code aliases)
    EV\_REL    (2 code aliases)
    EV\_ABS    (27 code aliases)
    EV\_MSC
    EV\_SW     (4 code aliases)
    EV\_LED
    EV\_SND
    EV\_REP
    EV\_FF
    EV\_PWR
    EV\_FF\_STATUS
    EV\_MAX
OK

 |
| `event send types [types ...]` | Send one or more fake event types. |
| `event codes type` | List the event codes for the specified fake event type. |
| `event send type[:code]:[value] [...]` | Send one or more fake events with optional codes and code values.

To discover exactly which event to send, you can use the `adb` command, while manually pressing the buttons on the emulator.

These are the events generated when you press the power button:

**adb shell getevent \-lt**

/dev/input/event12: EV\_KEY       KEY\_POWER            DOWN
/dev/input/event12: EV\_SYN       SYN\_REPORT           00000000
/dev/input/event12: EV\_KEY       KEY\_POWER            UP
/dev/input/event12: EV\_SYN       SYN\_REPORT           00000000 In this particular case, we only needed to send the two `EV_KEY` events, keydown, and a keyup event to simulate a long press of the power button: **event send EV\_KEY:KEY\_POWER:0**
OK
**event send EV\_KEY:KEY\_POWER:1**
OK

 |
| `event text message` | Send a string of characters that simulate keypresses. The message must be a UTF\-8 string. Unicode posts are reverse\-mapped according to the current device keyboard, and unsupported characters are discarded silently. |
| Control the power state reported by the emulator to apps | Description |
| `power display` | Display battery and charger state. |
| `power ac {on|off}` | Set AC charging state to `on` or `off`. |
| `power status {unknown|charging|discharging|not-charging|full}` | Change battery status as specified. |
| `power present {true|false}` | Set battery presence state. |
| `power health {unknown|good|overheat|dead|overvoltage|failure}` | Set battery health state. |
| `power capacity percent` | Set remaining battery capacity state as a percent from 0 to 100. |
| Network connection status | Description |
| `network status` | Check the network status and current delay and speed characteristics. |
| `network delay latency` | Change the emulated network latency.

The emulator lets you simulate various network latency levels, so that you can test your app in an environment more typical of the actual conditions in which it will run. You can set a latency level or range at emulator startup, or you can use the console to change the latency while the app is running in the emulator.

To set latency at emulator startup, use the `-netdelay` [emulator option](https://developer.android.com/studio/run/emulator-commandline#common) with a supported `latency` value, as listed in the Network latency format list below. Here are some examples:

emulator \-netdelay gprs
emulator \-netdelay 40,100

To make changes to network delay while the emulator is running, connect to the console and use the `netdelay` command with a supported `latency` value from the Network latency format list below.

network delay gprs
network delay 40 100

**Network latency format:**

The format of network latency is one of the following (numbers are milliseconds):

*   `gprs`: GPRS, which uses a latency range of 150 minimum and 550 maximum.
*   `edge`: EDGE/EGPRS, which uses a latency range of 80 minimum and 400 maximum.
*   `umts`: UMTS/3G, which uses a latency range of 35 minimum and 200 maximum.
*   `none`: No latency.
*   `num`: Emulate the specified latency in milliseconds.
*   `min:max`: Emulate the specified latency range.

 |
| `network speed speed` | The emulator lets you simulate various network transfer rates.

You can set a transfer rate or range at emulator startup, or you can use the console to change the rate, while the app is running in the emulator.

To set the network speed at emulator startup, use the `-netspeed` [emulator option](https://developer.android.com/studio/run/emulator-commandline#common) with a supported `speed` value, as listed in the network speed format list below. Here are some examples:

emulator \-netspeed gsm @Pixel\_API\_26
emulator \-netspeed 14.4,80 @Pixel\_API\_26

To make changes to network speed while the emulator is running, connect to the console and use the `network speed` command with a supported `speed` value from the Network speed format list below.

network speed 14.4 80

**Network speed format:**

The format of network `speed` is one of the following (numbers are kilobits/sec):

*   `gsm`: GSM/CSD, which uses a speed of 14.4 up and 14.4 down.
*   `hscsd`: HSCSD, which uses a speed of 14.4 up and 43.2 down.
*   `gprs`: GPRS, which uses a speed of 40.0 up and 80.0 down.
*   `edge`: EDGE/EGPRS, which uses a speed of 118.4 up and 236.8 down.
*   `umts`: UMTS/3G, which uses a speed of 128.0 up and 1920 down.
*   `hsdpa`: HSDPA, which uses a speed of 348.0 up and 14400.0 down.
*   `lte`: LTE, which uses a speed of 58,000 up and 173,000 down.
*   `evdo`: EVDO, which uses a speed of 75,000 up and 280,000 down.
*   `full`: Unlimited speed, but depends on the connection speed of your computer.
*   `num`: Set an exact rate in kilobits/sec used for both upload and download.
*   `up:down`: Set exact rates in kilobits/sec for upload and download separately.

 |
| `network capture {start|stop} file` | Send packets to a file. The following list describes the parameters and parameter values:

*   `start file`: Start sending packets to the specified file.
*   `stop file`: Stop sending packets to the specified file.

 |
| Telephony emulation | Description |
| The Android emulator includes its own GSM and CDMA emulated modems that let you simulate telephony functions in the emulator. For example, with GSM you can simulate inbound phone calls and establish and terminate data connections. With CDMA you provide a subscription source and the preferred roaming list. The Android system handles simulated calls exactly as it would actual calls. The emulator does not support call audio. |
| `gsm {call|accept|cancel|busy} phonenumber` | The `gsm` parameters are the following:

*   `call`: Simulate an inbound phone call from `phonenumber`.
*   `accept`: Accept an inbound call from `phonenumber` and change the call state to `active`. You can change a call state to `active` only when its current state is `waiting` or `held`.
*   `cancel`: Terminate an inbound or outbound phone call to or from `phonenumber`.
*   `busy`: Close an outbound call to `phonenumber` and change the call state to `busy`. You can change a call state to `busy` only when its current state is `waiting`.

 |
| `gsm {data|voice} state` | The `data state` command changes the state of the GPRS data connection, and the `data voice state` changes the state of the GPRS voice connection, as follows:

*   `unregistered`: No network available.
*   `home`: On local network, non\-roaming.
*   `roaming`: On roaming network.
*   `searching`: Searching networks.
*   `denied`: Emergency calls only.
*   `off`: Same as `unregistered`.
*   `on`: Same as `home`.

 |
| `gsm hold` | Change the state of a call to `hold`. You can change a call state to `hold` only when its current state is `active` or `waiting`. |
| `gsm list` | List all inbound and outbound calls and their states. |
| `gsm status` | Report the current GSM voice/data state. Values are those described for the `voice` and `data` commands. |
| `gsm signal {rssi|ber}` | Change the reported signal strength (rssi) and bit error rate (ber) on the next 15 seconds of update. The following list describes the parameters and their values:

*   `rssi` range is 0 through 31 and 99 for unknown.
*   `ber` range is 0 through 7 and 99 for unknown.

 |
| `gsm signal-profile num` | Set the signal strength profile. Enter a number from 0 through 4. |
| `cdma ssource source` | `source`: Set the current CDMA subscription source where `source` is a network\-based allowlist that contains the CDMA carrier's subscribers and their values, as follows:

*   nv: Read subscription from non\-volatile RAM.
*   ruim: Read subscription from Removable User Identity Module (RUIM).

 |
| `cdma prl_version version` | Dump the current preferred roaming list (PRL) version. The version number is for the PRL database that contains information used during the system selection and acquisition process. |
| Manage Sensors on the emulator | Description |
| These commands relate to which sensors are available in the AVD. Besides using the `sensor` command, you can see and adjust the settings in the emulator in the **Virtual sensors** screen in the **Accelerometer** and **Additional sensors** tabs. For more information, see [Working with the extended controls, settings, and help](https://developer.android.com/studio/run/emulator#extended). |
| `sensor status` | List all sensors and their status. The following is example output for the `sensor status` command: |
| `sensor get sensor-name` | Get the settings for `sensor-name`. The following example gets the value for the acceleration sensor:sensor get acceleration
acceleration = 2.23517e\-07:9.77631:0.812348

The `acceleration` values separated by colons(:) refer to the x, y, and z coordinates for the virtual sensors described in [Working with the extended controls, settings, and help](https://developer.android.com/studio/run/emulator#extended).

 |
| `sensor set sensor-name value-x:value-y:value-z` | Set the values for `sensor-name`. The following example sets the acceleration sensor to the following x, y, and z values separated by colons. For more information, see [Working with the extended controls, settings, and help](https://developer.android.com/studio/run/emulator#extended).

sensor set acceleration 2.23517e\-07:9.77631:0.812348

 |
| SMS emulation | Description |
| `sms send sender-phone-number textmessage` | Generate an emulated incoming SMS. The following list describes the parameter and their values:

*   `sender-phone-number`: Contains an arbitrary numeric string.
*   `textmessage`: The sms message.

The following example sends the message "hi there" to the 4085555555 phone number.

sms send 4085555555 hi there

The console forwards the SMS message to the Android framework, which passes it to an app on the emulator that handles SMS, such as the Messages app. If you pass 10 numbers, the app formats it as a phone number. Longer or shorter numeric strings display the way you sent them.

 |
| Fingerprint simulation | Description |
| `finger touch fingerprint-id` | Simulate a finger touching the sensor. |
| `finger remove` | Simulate finger removal.

For instructions about how to use these commands, see [Fingerprint simulation and validation](#finger-print).

 |

### Fingerprint simulation and validation

![](https://developer.android.com/studio/images/run/fingerprint_2x.png)

**Figure 1.** Fingerprint authentication screen.

Use the `finger` command to simulate and validate fingerprint authentication for your app. You need SDK Tools 24.3 or higher, and Android 6.0 (API level 23) or higher.

To simulate and validate fingerprint authentication, follow these steps:

1.  If you don't yet have a fingerprint ID, enroll a new fingerprint in the emulator by selecting **Settings** > **Security** > **Fingerprint** and following the enrollment instructions.
2.  Set up your app to accept [fingerprint authentication](https://developer.android.com/about/versions/marshmallow/android-6.0#fingerprint-authentication). After you perform this setup, your device displays the fingerprint authentication screen.
3.  While your app displays the fingerprint authentication screen, go to the console and enter the `finger touch` command and the fingerprint ID you created. This simulates a finger touch.
4.  Then, enter the `finger remove` command to simulate finger removal.

    Your app should respond as if a user touched and then removed their finger from the fingerprint sensor.