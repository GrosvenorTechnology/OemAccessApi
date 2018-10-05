# Getting Started

This guide is a companion to this walk-through video https://youtu.be/W_xtpKFG7dM

## General Set-up

- Turn off Firewall or alternatively add rules to enable the [Test Server application](https://github.com/GrosvenorTechnology/OemAccessTestServer) to listen on ports 8080 and 8081
- Run [Test Server application](https://github.com/GrosvenorTechnology/OemAccessTestServer)  as Administrator
- We will assume that the PC running the Testing Server is reachable by the IP address `192.168.1.1`, you should replace this value with something appropriate to your system.
- For our demo, our device has a serial number of `TEST-OEM~00001234`, you should replace this value in steps below with the serial number of your device.

## Using the Configurator tool to bind controller to the server

**Please note the tool shown is an early pre-releases tool due for release Q1-2019**

- Open the Configurator tool
- Select discovered controller and click install
- Enter Default Uri `http://192.168.1.1:8080/`
- Save Shared Key (you will need it to activate the controller on the Test Server)
- Click ok

The Janus C4 configurator will generate the basic boot configuration file which is necessary to bind the controller to server and will post it to the controller. It is possible to Enable SSH functionality by ticking SSH Enable Checkbox.

## Activating Controller on the Testing Server

To send configuration the controller, it has to be sent via the Testing Server. To interact with the Testing Server we are using [Postman](https://www.getpostman.com/).

- Send a `POST` command to the server to create a device.  We have to do this to tell the test server what the PSK for the device is so it can be authenticated. Uri: `http://192.168.1.1:8080/configuration/devices` Content type: `application/json`

```json
{
  "serialNumber":"TEST-OEM~00001234",
  "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY="
}
```

- Send a `POST` command to the server to add a platform configuration file. We're using this file to tell the controller where to get firmware updates and we're also speeding up the heartbeat frequency.  Uri: `http://192.168.1.1:8080/configuration/TEST-OEM~00001234/platform` Content type: `application/json`

```json
{
  "platform": {
    "applicationVersion": "2.0.20447+release",
    "firmwareVersion": "1.0.0+release",
    "Uris": {
      "firmware": { "uri": "https://firmware.customexchange.net/adv" },
      "heartbeat": [
        {
          "uri": "device/{deviceSerial}/heartbeat", "frequency": 5
        }
      ]
    }
  }
}
```

## Controllers LED light and process explanation

After submitting boot configuration (using usb drive or Janus C4 Configurator) the controllers light will start blinking yellow – this indicates that the controller is waiting for the platform and application configuration to be passed from the Server

The Purple LED shows that the controller is now downloading the correct application version and firmware version

The Green LED indicates that application and firmware were updated to the version specified by platform configuration and controller now will reboot.

The Blue LED indicates that the controller is booting up.

After controller boots up user should see “Heartbeat” events showing up on the Test Server Log

## Setting up Application Configuration

Now we have the controller connected to the Testing Server, the next step is to load an application configuration that defines how the hardware is configured and how the access control side of the device should behave; i.e. what inputs, outputs, readers and portals it contains and how they are wired up. Detailed information about application configuration can be found by following this [link](https://github.com/GrosvenorTechnology/OemAccessApi/blob/master/documentation/ApplicationConfiguration/Overview.md).  Again we want to use Postman to send the file to the Testing Server.

Uri: `http://192.168.1.1:8080/configuration/TEST-OEM~00001234/application` Content type : `application/json` Body:

```json
{
    "controller": {
        "id": "Controller",
        "timezone": "Europe/London",
        "inputs": [
            {
                "id": "Input-1",
                "address": "1-0-1",
                "normallyOpen": true
            },
            {
                "id": "Input-2",
                "address": "1-0-2",
                "normallyOpen": true
            }
        ],
        "outputs": [
            {
                "id": "Output-1",
                "address": "1-0-1"
            },
            {
                "id": "Output-2",
                "address": "1-0-2"
            }
        ]
    }
}
```