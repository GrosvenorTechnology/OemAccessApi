# Getting started with Unknown token download

## Overview

The actions functions of the Advance controller allows you to cause an event that happens within the controller to trigger a command on another part of the controller.

In the example we set up below, we're going to walk through setting up a controller so that an output tracks the state of the lock; when the door unlocks, the output turns on and turns off when the door locks.

## Getting set up

For this walk through we're going to use our OEM-Simulator application rather than a real controller as it's easier to get up and running, but the real controller will work the same.  

We shall be using our test server application as the server component of this setup, and we use Postman to load data into Postman.

Once the test server is running, we must tell it about the controller we intend to use.  This is done by sending a POST request to

> POST <http://localhost:8080/configuration/devices>

With this JSON body

```json
{
   "serialNumber":"VIR-ADV-C-MLT~00001001",
   "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY="
}
```

The test server should log the text 'Adding Device :: VIR-ADV-C-MLT~00001001'

Start the OEM-Simulator app, check that it is using the default boot config

```json
{
   "boot": {
      "defaultUri": "http://localhost:8080/grosvenor-oem/",
      "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY="
   }
}
```

And a platform config such as the following example, here we have an override to accelerate the heartbeat for demo purposes and we're filtering the states and events just to make the output cleaner for these examples.

```json
{
    "platform": {
        "Uris": {
            "Heartbeat": [
                {
                    "Uri": "device/{deviceSerial}/heartbeat",
                    "Frequency": 3
                }
            ],
            "eventSubmission": [
                {
                    "uri": "device/{deviceSerial}/events",
                    "filters": [
                        "#NoStateEvents",
                        "AccessControl.Reader."
                        "AccessControl.Portal."
                    ]
                }
            ],
            "stateSubmission": [
                {
                    "uri": "device/{deviceSerial}/states",
                    "filters": [
                        "AccessControl.Portal."
                    ]
                }
            ]
        }
    }
}
```

We're also going to need a minimal application configuration file that has a door and reader and output set up

```json
{
    "controller": {
        "description": "Virtual Test Controller",
        "id": "7054fb73-eab8-402b-a98f-78cabafa34df",
        "timeZone": "Europe/London",
        "readers": [
            {
                "address": "1-0-1",
                "id": "reader-1",
                "description": "Reader 1",
                "tokenFormatType": "RDSATEONPRO"
            }
        ],
        "portals": [
            {
                "address": "1-0-1",
                "id": "door-1",
                "description": "Door 1",
                "entry": {
                    "readers": [
                        "reader-1"
                    ],
                    "accessPermissions": [
                        "General-Access"
                    ]
                }
            }
        ],
         "outputs": [{
            "id": "output-1",
            "description": "Linked Door Output",
            "address": "2-0-1"
        }],
    }
}
```

Start the OEM-Simulator, you should see heartbeats and a bunch of state & event messages display in the test server, when things have settled down, click the update button on the 'Application Config' tab to apply the application configuration.

## Sending a user to the controller

We now need to download a valid user to the controller.  The door has been set up to allow access to users with the `General-Access` permission, so we must include that.

To do this we must POST the sample json below to the following URL

> POST <http://localhost:8080/configuration/VIR-ADV-C-MLT~00001001/change>

```json
{
  "entity": {
    "description":"Harry Stamper",
    "type": "AccessControl.User",
    "id": "fa8ebeb2-a3bf-44e6-bff1-ac19483ac627",
    "identifiers": [{
        "type": "AccessControl.Token",
        "id": "f955d968-73a9-4257-b76f-c30d87562446",
        "data": "123456"
    }],
    "permissions": {
        "General-Access": [{
            "type": "always"
        }]
    }
  }
}
```

It should now be possible to use the `Tokens` tab on the right of the simulator app to send the token `123456` to reader 1-0-1

This should produce the following events in the test server

```ascii
[18/07/2019 17:11:04] Event :
        [SourceTime] : 2019-07-18T17:11:04.8844604+01:00
        [Entity]     : AccessControl.Portal:door-1
        [EventName]  : RequestEntry
        [EventType]  : CommandResponse
        [Content]    : OriginatingEntity:Hardware.Reader:reader-1, RequestingEntity:, PermissionedEntity:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627, PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627, TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446, Result:Success

[18/07/2019 17:11:04] StateNotification :
        [LastChanged]  : 2019-07-18T17:11:04.8924571+01:00
        [Entity]       : AccessControl.Portal:door-1
        [StateName]    : PortalLockState
        [StateType]    : StateChange
        [StateValue]   : unlocked

[18/07/2019 17:11:04] StateNotification :
        [LastChanged]  : 2019-07-18T17:11:04.8994537+01:00
        [Entity]       : AccessControl.Portal:door-1
        [StateName]    : PortalState
        [StateType]    : StateChange
        [StateValue]   : UnlockedNoSensor

[18/07/2019 17:11:04] Event :
        [SourceTime] : 2019-07-18T17:11:04.9888218+01:00
        [Entity]     : AccessControl.Portal:door-1
        [EventName]  : PortalEntryUsed
        [EventType]  : Plain
        [Content]    : PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627, Result:Success

[18/07/2019 17:11:09] StateNotification :
        [LastChanged]  : 2019-07-18T17:11:09.8859532+01:00
        [Entity]       : AccessControl.Portal:door-1
        [StateName]    : PortalLockState
        [StateType]    : StateChange
        [StateValue]   : locked

[18/07/2019 17:11:09] StateNotification :
        [LastChanged]  : 2019-07-18T17:11:09.8889544+01:00
        [Entity]       : AccessControl.Portal:door-1
        [StateName]    : PortalState
        [StateType]    : StateChange
        [StateValue]   : LockedNoSensor
```

The events we're interested in here for this example are the two `PortalLockState` events where you can see the lock state change from locked to unlocked and back.

While we will be using `PortalLockState` here, any event or state notification can be used to trigger an action.

## Configuring the action

The next step is to add an action to the application configuration to tie the door to the output.  We will set the action to watch the state of `PortalLockState` on `door-1` and when it changes to selectively `Operate` or `Reset` command to the output `output-1`

> door-1 : PortalLockState : unlocked -> output-1 : Operate
> door-1 : PortalLockState : locked -> output-1 : Reset

To see the demo work, copy the following to the application configuration tab and click update, then swipe the `123456` token on reader 1-0-1

```json
{
    "controller": {
        "description": "Virtual Test Controller",
        "id": "7054fb73-eab8-402b-a98f-78cabafa34df",
        "timeZone": "Europe/London",
        "readers": [
            {
                "address": "1-0-1",
                "id": "reader-1",
                "description": "Reader 1",
                "tokenFormatType": "RDSATEONPRO"
            }
        ],
        "portals": [
            {
                "address": "1-0-1",
                "id": "door-1",
                "description": "Door 1",
                "entry": {
                    "readers": [
                        "reader-1"
                    ],
                    "accessPermissions": [
                        "General-Access"
                    ]
                }
            }
        ],
         "outputs": [{
            "id": "output-1",
            "description": "Linked Door Output",
            "address": "2-0-1"
        }],
        "actions": [{
                "id": "Output-Follows-Door",
                "sourceType": "AccessControl.Portal",
                "sourceIds": ["door-1"],
                "targetType": "Hardware.Output",
                "targetIds": ["output-1"],
                "map":
                [
                    {
                        "event": "PortalLockState:unlocked",
                        "command": "Operate"
                    },
                    {
                        "event": "PortalLockState:locked",
                        "command": "Reset"
                    }
                ]
            }
        ]
    }
}
```

## Using the Configurator tool to set up an action

Our Configurator action allows the configuration of actions, the same ideas apply an example of what the above configuration would look like in the too is shown below



## Extending the example - Source and targets

As both the `sourceIds` and `targetIds` properties are arrays you can put more than one id in each.  So say you wanted all the doors on the controller to operate the same output, you would configure the fan-in action like this:

```json
{
    "sourceIds": ["door-1", "door-2", "door-3", "door-4"],
    "targetType": "Hardware.Output",
    "targetIds": ["output-1"],
}
```

And the reverse applies if you want a fan-out where a single door to operate multiple outputs

```json
{
    "sourceIds": ["door-1"],
    "targetType": "Hardware.Output",
    "targetIds": ["output-1", "output-2", "output-3", "output-4"],
}
```

If on the other hand you want to `door-1` to operate `output-1` and  `door-2` to operate `output-2` you have to set up multiple actions.

## Using command parameters

If we want to change the example below. so that an unknown token will turn an output on for 5 seconds, where as a token with no access will flash the output for 15 seconds, we can do the following

First we need a new user that does not have access to the door, so send this example to the changes queue

```json
{
  "entity": {
    "description":"Dan Truman",
    "type": "AccessControl.User",
    "id": "f02e702a-8f79-46e1-8083-5f7016981ab6",
    "identifiers": [{
        "type": "AccessControl.Token",
        "id": "6154042b-366b-467d-8dcd-942e81693f6e",
        "data": "987654"
    }],
    "permissions": { }
  }
}
```

Then update the `Application Configuration` to the version below.  This has two actions defined, both target the same output, but the first is looking for an `Read` event from a reader with an `UnknownToken` status; the second end watching for a `RequestEntry` event from the portal (aka door) that fails on permissions `FailedOnPermissions`.

Both events events pass different arguments to the `Output` entity `OperateForPeriod` command.  Details of what arguments can be supplied to a command are listed on each entities docs page, as [here for the OperateForPeriod command](../Entities/HardwareOutput.md#operateforperiod)

```json
{
    "controller": {
        "description": "Virtual Test Controller",
        "id": "7054fb73-eab8-402b-a98f-78cabafa34df",
        "timeZone": "Europe/London",
        "readers": [
            {
                "address": "1-0-1",
                "id": "reader-1",
                "description": "Reader 1",
                "tokenFormatType": "RDSATEONPRO"
            }
        ],
        "portals": [
            {
                "address": "1-0-1",
                "id": "door-1",
                "description": "Door 1",
                "entry": {
                    "readers": [
                        "reader-1"
                    ],
                    "accessPermissions": [
                        "General-Access"
                    ]
                }
            }
        ],
         "outputs": [{
            "id": "output-1",
            "description": "Linked Door Output",
            "address": "2-0-1"
        }],
        "actions": [
            {
                "id": "Unknown-Token-Actions",
                "sourceType": "Hardware.Reader",
                "sourceIds": ["reader-1"],
                "targetType": "Hardware.Output",
                "targetIds": ["output-1"],
                "map":
                [
                    {
                        "event": "Read:UnknownToken",
                        "command": "OperateForPeriod",
                        "arguments":
                        {
                            "period": "0:00:15",
                            "outputMode": "constant"
                        }
                    }
                ]
            },
            {
                "id": "Denied-Token-Actions",
                "sourceType": "AccessControl.Portal",
                "sourceIds": ["door-1"],
                "targetType": "Hardware.Output",
                "targetIds": ["output-1"],
                "map":
                [
                    {
                        "event": "RequestEntry:FailedOnPermissions",
                        "command": "OperateForPeriod",
                        "arguments":
                        {
                            "period": "0:00:05",
                            "outputMode": "urgentPulse"
                        }
                    }
                ]
            }
        ]
    }
}
```

