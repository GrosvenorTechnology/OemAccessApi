# Getting started with OSDP

This guide will walk you through the basic steps that are essential in order to get OSDP readers working. It also provides links to additional information that may be required for you to get OSDP readers working according to your needs.

## Hardware

* There can be up to 4 OSDP devices connected to a bus (RS485) and there are two buses on a Door Blade.
* OSDP readers require power (normally +12V), GND and the two data lines, RS485-A and RS485-B. If there is more than one reader on a bus, these need to be 'daisy-chained' from one to the other. The last OSDP device on the bus should have a 100 ohm resistor across the data lines (a terminating resistor). A terminating resistor on the blade end is not required, as it is built-in (though it can be disabled in the software).
* The data lines should always connect A to A and B to B. So all the 'A's are connected and all the 'B's are connected. You should link from one reader to the next 'daisy chained', not branch out or have a 'star' configuration. This becomes particularly important on long runs and high baud rates. You can get away with not being so strict if the runs are short and low baud rate.
* If you have a Door Blade version **1V4** and the line is terminated (internally or externally), it is necessary to **put 1k ohm "pull-up" resistor between +5V and RS485-A.** This is often essential for the controller to communicate with the reader.

## Software

OSDP readers can only be configured in Oem Application version 3 or above.  If necessary please update the application using the platform config.

## Application Configuration

To start using OSDP readers you must update Application Configuration and include the following points:

* The **"type"** must be set to **"Osdp.Reader"**.
* The **"address"** is in the form "{blade}-{bus}-{osdp address}" for example **"1-1-0"**. The value for the blade can be 1 to 4. The value for the bus is 1 or 2. The OSDP address can be set to 0 to 8.
* Optionally, you can change **idleLedMode, idleLedOnColour, idleLedOffColour** as shown in the example.
* Add the blades entity with OSDP baud rate and encryption key as shown in the example below.
* Assign readers to the portal as shown in the example below.

***Additional information:***

* Additional information about Platform configuration and Application updates can be found in the [Platform Configuration Overview](PlatformConfiguration/Overview.md) document.
* Additional information about OSDP readers can be found in the [OSDP-Reader Entity](Entities/OsdpReader.md) document.
* Additional information about Blade parameters and supported baud rates can be found in the [Hardware-Blade Entity](Entities/HardwareBlade.md) document.
* Additional information about portal and Application configuration can be found in the [Application Configuration Overview](ApplicationConfiguration/Overview.md) document.

```json
{
    "controller": {
        "areaMode": "local",
        "timeZone": "Europe/London",
        "readers" : [
        {
            "id": "Reader-1",
            "description": "OSDP Reader 1",
            "type": "Osdp.Reader",
            "address": "1-1-0",
            "operationalMode": "tokenOnly",
            "tokenFormatType": "RDSATEONPRO",
            "idleLedMode": "nonUrgentPulse",
            "idleLedOnColour": "amber",
            "idleLedOffColour": "off"
        },
        {
            "id": "Reader-2",
            "description": "OSDP Reader 2",
            "type": "Osdp.Reader",
            "address": "1-2-0",
            "operationalMode": "tokenOnly",
            "tokenFormatType": "RDSATEONPRO",
            "idleLedMode": "nonUrgentPulse",
            "idleLedOnColour": "amber",
            "idleLedOffColour": "off"
        }],
        "blades": [
        {
            "id": "blade1",
            "osdpBaudRate": "baud9600",
            "osdpMKey": ""
        }],
        "portals": [
            {
                "address": "1-0-1",
                "operationalMode": "normal",
                "id": "Portal-1",
                "type": "AccessControl.Portal",
                "lockType": "energiseToLock",
                "entry": {
                    "readers": ["Reader-1"]
                },
                "exit": {
                    "readers": ["Reader-2"]
                }
            }
        ]
    }
}
```

## Additional information on specific OSDP readers

### Deister PRD5/3 OSDP Reader

#### Connection

On the reader connector the following connections are required:

| **Pin** | **Connection** |
|---------|----------------|
| 2 | GND |
| 3 | +12V |
| 9 | RS485-A |
| 10 | RS485-B |

#### Configuration

The "idle LED" is not configurable and is constant amber when on-line and flashing amber when off-line (not communicating with the blade). Please set the following:

* The **"idleLedMode"** parameter should be set to **"constant"**.
* The **"idleLedOnColour"** parameter should be set to **"off"**.

### HID iCLASS, multiCLASS SE OSDP Readers

#### Connection

On the reader connector the following connections are required:

| **Pin** | **Connection** |
|---------|----------------|
| RTN | GND |
| +VDC | +12V |
| GPIO1 | RS485-A |
| GPIO2 | RS485-B |

#### Configuration

* The PIN reader version may need to be programmed by HID's OSDP configuration software to enable the '*' and '#' buttons to work as expected.
* It is possible to have communications problems without the use of a pull-up resistor (see above [Hardware](#Hardware) section). This is due to the HID readers turning the line drive off prematurely and so shortening the last stop bit. Not terminating the line may also circumvent the problem. HID are aware of this issue.