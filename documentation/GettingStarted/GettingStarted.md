# Getting Started
## General Setup
1.	Turn off Firewall
2.	Alternatively add rules to enable the [Test Server application](https://github.com/GrosvenorTechnology/OemAccessTestServer)  to listen on ports 8080 and 8081
3.	Run [Test Server application](https://github.com/GrosvenorTechnology/OemAccessTestServer)  as Administrator

## Using Janus C4 Configurator to bind controller to the server
4.	Open Janus C4 Configurator
5.	Select discovered controller and click install
6.	Enter Default Uri ```http://192.168.43.111:8080/```
7.	Save Shared Key (you will need it to activate the controller on the Test Server)
8.	Click ok

The Janus C4 configurator will generate the basic boot configuration file which is necessary to bind the controller to server and will post it to the controller.

It is possible to Enable SSH functionality by ticking SSH Enable Checkbox.



## Activating Controller on the Testing Server
To activate the controller, and interact with the Testing Server we are using [Postman]( https://www.getpostman.com/). 

9.	Send Post command to Create Device. Uri: ```http://192.168.43.111:8080/configuration/devices``` Content type : ```application/json```  Body:

```json
{
  "serialNumber":"OEM-ADV-C-MLT~00000613",
  "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY="
}
```

10.	Send Post command to add Platform Configuration. Uri: ```http://192.168.43.111:8080/configuration/platform``` Content type : ```application/json```  Body:

```json
{
  "platform": {
    "applicationVersion": "2.0.20447+release",
    "firmwareVersion": "1.0.0+release",
    "Uris": {
      "firmware": { "uri": "https://firmware.customexchange.net/adv" },
      "Heartbeat": [
        {
          "Uri": "http://192.168.43.111:8080/grosvenor-oem/device/{deviceSerial}/heartbeat",
          "Frequency": 10
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
To fully use the controller the installer must send the application configuration that indicates how the controller should act i.e. what inputs, outputs, readers and portals it contains and how they are wired up.
It is done using postman and posting Application configuration.  Detailed information about application configuration can be found by fallowing this [link]( https://github.com/GrosvenorTechnology/OemAccessApi/blob/master/documentation/ApplicationConfiguration/Overview.md) 

Uri: ```http://192.168.43.111:8080/configuration/platform``` 

Content type : ```application/json``` 

Body:

```json
{
  "controller": {
   "inputs": [
      {
        "normallyOpen": true,
        "inputType": "unsupervised",
        "inputOperationType": "normal",
        "pirInhibit": "00:00:30",
        "pirFault": "00:01:00",
        "address": "1-0-1",
        "operationalMode": "enabled",
        "id": "Input-1",
        "type": "Hardware.Input"
      },
      {
        "normallyOpen": true,
        "inputType": "unsupervised",
        "inputOperationType": "normal",
        "pirInhibit": "00:00:30",
        "pirFault": "00:01:00",
        "address": "1-0-2",
        "operationalMode": "enabled",
        "id": "Input-2",
        "type": "Hardware.Input"
      }
    ],
    "outputs": [
      {
        "pulseLength": "00:00:01",
        "defaultOutputMode": "constant",
        "outputStateType": "activeHigh",
        "address": "1-0-1",
        "operationalMode": "normal",
        "id": "Output-1",
        "type": "Hardware.Output"
      },
      {
        "pulseLength": "00:00:01",
        "defaultOutputMode": "constant",
        "outputStateType": "activeHigh",
        "address": "1-0-2",
        "operationalMode": "normal",
        "id": "Output-2",
        "type": "Hardware.Output"
      }
    ],
    "id": "Controller"
  },
  "messageId": "00000000-0000-0000-0000-000000000000"
}
```