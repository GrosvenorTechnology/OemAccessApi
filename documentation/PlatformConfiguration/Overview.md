# Platform Configuration

The configuration of the controller is split into two sections, platform and
application configuration and application configuration. This document describes
the platform configuration of the device.

```json
{
    "platform": {
        "applicationVersion": "1.2.3.45678",
        "firmwareVersion": "1.2.0+release",
        "forceExactFirmwareVersion": false,
        "protocolLevel": 1,
        "defaultPollFrequency": 30,
        "defaultQueueBatchSize": 100,
        "services": [
            {"name": "myAccess", "uri": "http://myAccessService.com/api"}
        ],
        "uris": {
            "heartbeat": [
                { "uri": "device/{deviceSerial}/heartbeat", "frequency": 7 },
                { "uri": "http://myAccessService.com/api/device/{deviceSerial}/heartbeat", "frequency": 13 }
            ],
            "hardwareReport": [],
            "applicationConfig": { "uri": "device/{deviceSerial}/configuration" },
            "stateQueue": { "uri": "{myAccess}/device/{deviceSerial}/states/messages", "frequency": 10, "batchSize": 10 },
            "commandQueue": { "uri": "{myAccess}/device/{deviceSerial}/commands/messages", "frequency": 10 },
            "changeQueue": { "uri": "{myAccess}/device/{deviceSerial}/changes/messages", "frequency": 10, "batchSize": 500 },
            "permissionRequest": { "uri": "device/{deviceSerial}/permissionrequest" },
            "eventSubmission": [
                {
                    "uri": "{mgmt}/device/{deviceSerial}/events",
                    "filters": [
                        "Hardware.Controller."
                    ]
                },
                {
                    "uri": "{myAccess}/device/{deviceSerial}/events",
                    "filters": [
                        "Hardware.Controller.",
                        "Hardware.Blade.",
                        "AccessControl.Portal.LockFault"
                    ]
                }
            ],
            "stateSubmission": [
                {
                    "uri": "device/{deviceSerial}/states",
                    "filters": [ "Hardware.Controller." ]
                },
                {
                    "uri": "{myAccess}/device/{deviceSerial}/states",
                    "filters": [ "*" ]
                }
            ]
        }
    }
}
```

## Application Version

Specify the version of the application package that should be installed on the
controller.

The controller will check and update the application, if required, on device boot and update of the platformConfig.json file.

## Firmware Version

Specify the version of the firmware that should be installed on the controller.

The controller will check and update the firmware, if required, on device boot and update of the platformConfig.json file.

## ForceExactFirmwareVersion

### With the parameter set to false (default)

If the firmware on the controller and that defined here in the config are the same major and minor version, the firmware on the controller is only upgraded if the patch level is higher. The firmware will not be downgraded to a lower patch level.

For example:

* Controller has 1.0.2, config requires 1.0.4 => Upgrade
* Controller has 1.0.6, config requires 1.0.4 => No change
* Controller has 1.2.4, config requires 1.0.4 => Downgrade

### With the parameter is set to true

The firmware will always match the defined firmware version.

## Protocol Level

The protocol level the application will run at. Defaults to 1.

## Default Poll Frequency

The default poll frequency (in seconds) that will be used unless otherwise
overridden on a specific URI.

## Default Queue Batch Size

The default batch size to be used when polling a queue unless overridden on a
specific queue.

## Services

[optional] You can add extra service aliases to the ones already defined in the
boot config. If the name of an existing service is specified, the value will be
replaced.

## URI Locations

It is possible to override the default URIs used to communicate with the service
tier, if a specific URI type is present, that uri will be used, otherwise the
default uri will be constructed based off the ‘serviceUri’ specified in the boot
config. If a relative URI is specified, then that URI will be combined with the
serviceUri from the boot config. If the string {deviceSerial} is present in the
URI it will be replaced with the device serial number.

If secondary service URIs are specified, if the name of the service is included
in the URI in braces, i.e. {myAccess}, this will be replaced before the URI is
evaluated as an absolute or relative URI.

If the URI is marked with [multi-uri] then it is possible to specify zero or
more items as an array of URIs if you want the service to call to the management
platform as well as the service tier. It is suggested that you use no more than
two destinations for these endpoints. If the URI is set to an empty array, the
service will be effectively be disabled.

If the URI is of type [polled] then you can set the *uri*, *frequency* of
polling (in seconds).  If you set the *uri* of a [polled] setting to "disabled"
then the system will not attempt to connect to that endpoint.

If the URI is of type [queue] then you can set the *uri*, *frequency* of polling
(in seconds) and *batchSize.*

### Heartbeat

[multi-uri] The uri for the controller to heartbeat to, if multiple URIs are
used any URI can respond with an activity list.

### hardwareReport

[multi-uri] The URI to post the hardware config to.

### applicationConfiguration

[polled] The location of the application configuration.

### stateQueue

[queue] The location of the state queue on the service tier.

### commandQueue

[queue] The location of the command queue on the service tier.

### changeQueue

[queue] The location of the entity change queue on the service tier.

### permissionRequest

[uri] The location of the permission request service. This is used by the `serverPermission` gate in an User entity permission.

### eventSubmission and stateSubmission

All event and state updates send from the controller are sent via a platform
level sender. It is possible to route selected to multiple different endpoints.
Each configured endpoint has a filter associated to it that controls the states
and events that will be forwarded to that URI.

#### uri

The URI the events or states will be sent to as per the OEM-Controller-Protocol
document, Event-Submission.

#### filters

The list of events or states (as defined by the type above) that will be sent to
the Uri. Event and states are defined by the triplets

{namespace}.{type}.{eventname} e.g. Hardware.Portal.Forced

{namespace}.{type}.{statename} e.g. Hardware.Controller.MainsVoltage

The filter will match using StartsWith. A filter value of ‘\*’ will be treated
as match all. If the filter starts with an ‘!’ the filter will be treated as an
exclusion filter. Filters are evaluated in order and the first match wins.

The following special filters exist:

* The event filter `#NoStateEvents` will block the state change events that are automatically generated when an state property changes.

* The state filter `#NoDiagnosticStates` will block all state changes that are tagged as diagnostic.
