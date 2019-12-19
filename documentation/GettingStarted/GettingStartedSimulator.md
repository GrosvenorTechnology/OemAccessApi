# Getting started with the test server and simulator

## Overview

This getting started guid will walk you through getting the simulator up and running with the test server so that you can download users and tokens to the controller.

## The Setup

For this walk through we're going to use our OEM-Simulator application rather than a real controller as it's easier to get up and running at first, but the real controller will work the same.  

We shall be using our test server application as the server component of this setup.  For production systems you'll be replacing the test server with your own implementation, but the test server has all the endpoints implemented to get you started.  The test server has no UI, so we will be using [Postman](https://www.getpostman.com/) to interact with it.

> The test server uses ports 8080 and 8081, if you're having problems please ensure that these ports are allowed in your local firewall.

Both tools are available from our SDK download, the latest version of which can be found [here](../appRelease.md)

## Starting the test server and telling it about the simulator

Start the test server and once it is running, we must tell it about the controller we intend to use.  This is done by sending a POST request to

> POST <http://localhost:8080/configuration/devices>

With this JSON body

```json
{
   "serialNumber":"VIR-ADV-C-MLT~00001001",
   "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY="
}
```

The test server should log the text 'Adding Device :: VIR-ADV-C-MLT~00001001'

The test server stores all data in memory, so if you want to start over fresh at any point, just stop and restart the app.

Each controller uses a shared key to authenticate it's requests to the server, for all our demo's we will use the same key `9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY=`, if this every gets out of sync between a device and te test server you will see red authentication errors logged to the console.  If this happens just update the key value in the above request and resubmit it and the existing controller registration will be updated.

## Starting the simulator

The advance controllers require 3 files to get them up and running

- Boot Configuration
- Platform Configuration
- Application Configuration

The boot set up the low level settings for the controller and most can not be changed without restart the application.

The Platform configuration file contains details of all network URLs used by the system

The application configuration file contains all configuration details related to blade hardware and other access control specific setup.

It is expected that once a controller is installed, the boot and platform configuration will not change and the configuration being largely common across all controllers.

Lets start the simulator application and set up some basic config files

### Sample boot configuration

The following simple boot config file is the default used by the simulator and is all you need to get the simulator up and running, it uses the same shared key as we used when registering the device with the test server above.

The [EventContentExtension](../BootConfiguration/Overview.md#features) feature enables a feature that people find helpful when getting started, is simplifies working with the events from a portal at the expense of larger messages.

```json
{
   "boot": {
      "defaultUri": "http://localhost:8080/grosvenor-oem/",
      "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY=",
      "features": [
         "EventContentExtension"
      ]
   }
}
```

### Sample platform configuration

The simulators default platform config overrides heartbeat for demo purposes, it speeds up the default to make it more interactive.  In productions systems you can tune the frequency to whatever works the best for you.

```json
{
   "platform": {
      "Uris": {
         "Heartbeat": [
            {
               "Uri": "device/{deviceSerial}/heartbeat",
               "Frequency": 3
            }
         ]
      }
   }
}
```

### Sample Application configuration

Out the box, the simulator will open with the application config populated with a simple application config file that has a couple of inputs and outputs configured.  If the controller has never received an application config file no blade hardware will be initialized, i.e. Readers and Locks.

As our intent for this demo is to experiment with users and tokens, we're going to change the default for one that has a reader and door defined.

One option we could use is the simulator has some example files, if you select the `Configuration` tab on the right hand side you will see a drop down for `Application Configuration` which by default is `app-default.json` if you change this to `appFullBlade.json`, this will populate the the `Application Configuration` tab with a fully populated app config file.

> You can define your own selection of config files for the simulator or edit the ones we ship, they are in the `configuration` directory in the simulator directory. If you have a favourite combination of files, if you load or edit the files to what you want, then click the `Save Configuration as Default` the configuration will be saved into the `configuration\defaultConfig.json` file and will be loaded each time the simulator app is opened.

For our example we're going to keep things a bit simpler, so copy and paste the file below into the `Application Configuration` tab and click `Save Configuration as Default`.

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
        ]
    }
}
```

This example file is telling the controller there is [reader](../Entities/HardwareReader.md) present on address 1-0-1, which means Blade 1, Bus 0 (Wiegand/Clock&Data), position 1.  If you do not define a reader, it will not be powered up and the controller will not publish events from that device; the same holds true for other devices such as [portals](../Entities/AccessControlPortal.md), [inputs](../Entities/HardwareInput.md), and [outputs](../Entities/HardwareOutput.md).

We're also setting up a [portal](../Entities/AccessControlPortal.md) that has the reader linked to it.  It's the portals that have access permissions rather than the individual readers; for this example users will require an access permission called `General-Access` to get a valid access to the door.

> `RDSATEONPRO` is a special inbuilt reader type, custom reader types can be built using [Token Formats](../ApplicationConfiguration/TokenFormat.md)

### Start the simulator

Click the `Start` button on the OEM-Simulator, and you should see progress messages being written to the `Log` and `Network Events` tabs and heartbeats and a bunch of state & event messages display in the test server.

By default the controller will not auto load the contents of the `application configuration`, to load the door and reader configuration click the `update` button on the application configuration tab.  After a few seconds you should see a configuration downloaded event as below as well as a bunch of other events as the blade is initialised.

```ascii
[19/07/2019 15:58:37] Event :
        [SourceTime] : 2019-07-19T15:58:37.6874471+01:00
        [Entity]     : Hardware.Controller:PANEL
        [EventName]  : ConfigurationDownload
        [EventType]  : Plain
        [Content]    : Result:Success
```

> Once loaded this configuration is stored in a local database and will be loaded whenever you started the simulator - Please note that this stored configuration is not the same as the file displayed in the `Application Configuration` tab.

> To clear any stored configuration, check the `Delete DB on Startup` button on the `Configuration` tab on the right before clicking the `Start` button.

## Testing an unknown token

Now the controller is running, expand the 'Tokens' tab on the right and click the default '123456' token, you should immediately see the beeper of reader 1 flash red and an 'Unknown Token' event as below will be logged in the test server.

```ascii
[11/07/2019 14:33:47] Hub : Network Event Event Submitted to Group :: VIR-ADV-C-MLT~00001001
[11/07/2019 14:33:47] Event :
        [SourceTime] : 2019-07-11T14:33:47.7109951+01:00
        [Entity]     : Hardware.Reader:reader-1
        [EventName]  : Read
        [EventType]  : Plain
        [Content]    : TokenData:123456
                       Result:UnknownToken
```

> You can add your own tokens to the list by just typing in the the number in the edit box next to the `Add` button then clicking add.  You can remove them by clicking the `X` next to the token.

> To can change the reader you send the token to by changing the selected reader at the top of the reader fly-out.

## Downloading a user to the controller

The next step is to download a user with a token to the controller.  This is done by first posting the user record into the test server, and the controller will then pick the user record up from the server and save it locally.

To do this we will use `Postman` to `POST` a JSON payload to the test server, the URL we'll use is

> <http://localhost:8080/configuration/VIR-ADV-C-MLT~00001001/change>

When using postman, having set the URL to the above, ensure to change the HTTP verb from GET to POST.  On the body tab select `raw` and paste in the JSON example below and finally set the content type to `application/json`

> Full details of users and tokens can be found [here](../Entities/AccessControlUser.md)

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
    "permissions": { }
  }
}
```

In the test server log you should see

```ascii
[19/07/2019 16:17:46] Trace : Heartbeat VIR-ADV-C-MLT~00001001 :: Activities [changes]
[19/07/2019 16:17:47] Hub : Network Event ChangesRequest Submitted to Group :: VIR-ADV-C-MLT~00001001
[19/07/2019 16:17:47] Trace : Returning 1 items from ChangeQueueController :: VIR-ADV-C-MLT~00001001
[19/07/2019 16:17:47] Hub : Network Event ChangesRequest Submitted to Group :: VIR-ADV-C-MLT~00001001
[19/07/2019 16:17:47] Trace : Returning 0 items from ChangeQueueController :: VIR-ADV-C-MLT~00001001
```

This is showing that the controller sent a heartbeat to the server, which responded telling the controller that there is data in the `changes` queue.  The controller then proceeded to request the data from the changes queue until the queue is empty.

You should also see a line in the simulator log tab

```ascii
[16:17:48.674] [Debug] Added fa8ebeb2-a3bf-44e6-bff1-ac19483ac627 to Itac.OemAccess.Core.Entites.Person table.
```

If you now swipe the same 123456 token on reader 1-0-1 you should now get two events, one identifying the user at the reader and the second denying them access through the door.

```txt
[19/07/2019 16:22:59] Event :
        [SourceTime] : 2019-07-19T16:22:59.2816484+01:00
        [Entity]     : Hardware.Reader:reader-1
        [EventName]  : Read
        [EventType]  : Plain
        [Content]    : TokenData:123456
                       TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       OperationNumber:0
                       Result:Success

[19/07/2019 16:22:59] Event :
        [SourceTime] : 2019-07-19T16:22:59.3224965+01:00
        [Entity]     : AccessControl.Portal:door-1
        [EventName]  : RequestEntry
        [EventType]  : CommandResponse
        [Content]    : Hardware.Reader_Read_EntitySource:Hardware.Reader:reader-1
                       Hardware.Reader_Read_TokenData:123456
                       Hardware.Reader_Read_TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Hardware.Reader_Read_PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       Hardware.Reader_Read_OperationNumber:0
                       Hardware.Reader_Read_Result:Success
                       OriginatingEntity:Hardware.Reader:reader-1
                       RequestingEntity:
                       PermissionedEntity:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Result:FailedOnPermissions
```

The important part of the second `RequestEntry` event is right at the end `Result:FailedOnPermissions` showing the user does not have the correct permissions to allow them through the door.

> As an aside, what you can see in the second event is the effect of the `EventContentExtension` included in the boot config.  When a user attempts to go through a door a series of events occur, which are all correlated together.  When you implement your server you get the choice of either dealing with all the events yourself and stitching them together on the server which results in smaller messages but more of them; or you can let the controller do some of the work for you.
> In the example above the controller has remembered the `Read` event and added it into the `RequestEntry` event. This means you can listen only for the `RequestEntry` event if you only want to log `Access Granted` or `Access Denied` events.

As a final step we're now going to update the user to give them permissions to use the door, so in `Postman` update the json to the example below and post it again to the test server

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

> The way permissions work in the OemAccess application is once that controller has worked out what door a user is attempting to gain access to, it will look at the door and extract the list of permissions the door will accept (only one must work).  In our example we've called this `General-Access` but we could have called it `Door-1-Entry`; the naming and logical scheme is up to you.  Once the controller has this list in hand, it loads the user and looks at the list of permissions on the user; it then starts evaluating any that the Door and the User have in common.  As soon as one of these common permissions evaluates as true, the controller stops and allows the user through the door.

Once you have seen the controller update it's Person's table a second time you can swipe the card again and you will see a successful `PortalEntryUsed` event logged and the valid LED on the blade light up.

```txt
[19/07/2019 16:29:58] Event :
        [SourceTime] : 2019-07-19T16:29:58.0204746+01:00
        [Entity]     : AccessControl.Portal:door-1
        [EventName]  : RequestEntry
        [EventType]  : CommandResponse
        [Content]    : Hardware.Reader_Read_EntitySource:Hardware.Reader:reader-1
                       Hardware.Reader_Read_TokenData:123456
                       Hardware.Reader_Read_TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Hardware.Reader_Read_PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       Hardware.Reader_Read_OperationNumber:0, Hardware.Reader_Read_Result:Success
                       OriginatingEntity:Hardware.Reader:reader-1
                       RequestingEntity:
                       PermissionedEntity:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Result:Success
```

## Extending the example to use shared timetables

Now we have a user able to get through a door, were going to limit the time the user can use that door.

To get started, we're going to amend the rules for our test user to allow them only to use the door between 9AM and 5PM using an [inline time](../Entities/AccessControlUser.md#inlinetime) permission gate.

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
            "type": "inlineTime",
            "data": {
                "sets": [
                    {
                        "days": ["Mo","Tu","We","Th","Fr"],
                        "periods": [
                        {
                            "start": "09:00:00",
                            "end": "17:00:00"
                        }]
                    }
                ]
            }
        }]
    }
  }
}
```

This will block the user from using the door outside those hours and a failure event will be emitted for the `RequestEntry` action

```txt
[19/07/2019 17:11:25] Event :
        [SourceTime] : 2019-07-19T17:11:25.0878071+01:00
        [Entity]     : AccessControl.Portal:door-1
        [EventName]  : RequestEntry
        [EventType]  : CommandResponse
        [Content]    : Hardware.Reader_Read_EntitySource:Hardware.Reader:reader-1
                       Hardware.Reader_Read_TokenData:123456
                       Hardware.Reader_Read_TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Hardware.Reader_Read_PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       Hardware.Reader_Read_OperationNumber:0
                       Hardware.Reader_Read_Result:Success
                       OriginatingEntity:Hardware.Reader:reader-1
                       RequestingEntity:
                       PermissionedEntity:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Result:FailedOnPermissions:TimeGateClosed

```

In this example the `Result` is `FailedOnPermissions:TimeGateClosed` which is an example of an event with a reason after the result code.

As easy as the inline time gate is to use for demo usage, or cases where access rules vary wildly from user to user, a better route is the use of a shared common timetable.  A downside of the inline time, is if the rule changed from 9-17 to 9-18, every user would have to be updated.  The use of a shared common timetable solves this.

The first step in this is to update our user to the following:

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
            "type": "time",
            "data": "Mon-Fri-9-18"
        }]
    }
  }
}
```

But if this user tries to use their token as it we will get the following failure event emitted.  I'm going to thin the events down a bit from here as I think we get the idea :)

```txt
[19/07/2019 17:20:18] Event :
        [SourceTime] : 2019-07-19T17:20:18.4551010+01:00
        [Entity]     : AccessControl.Portal:door-1
        [EventName]  : RequestEntry
        [EventType]  : CommandResponse
        [Content]    : ...
                       PersonId:AccessControl.User:fa8ebeb2-a3bf-44e6-bff1-ac19483ac627
                       TokenId:AccessControl.Token:f955d968-73a9-4257-b76f-c30d87562446
                       Result:TargetNotFound:Time Table Entity not found: Mon-Fri-9-18

```

The event is telling us that the user failed because a referenced entity could not be found `TargetNotFound` and a textual description is provided as the `reason`.

So the next step is to get the [TimeTable](../Entities/CommonTimeTable.md) definition sent down to the controller.  Fortunately this is quite easy as it uses the same mechanism as the users to get the change to the controller.  We just post the following JSON payload to the `Test Server` as we did for the user

> <http://localhost:8080/configuration/VIR-ADV-C-MLT~00001001/change>

```json
{
    "entity": {
        "type": "Common.TimeTable",
        "id": "Mon-Fri-9-18",
        "description": "Example Time Table",
        "sets": [
            {
                "days": ["Mo","Tu","We","Th","Fr"],
                "periods": [
                {
                    "start": "09:00:00",
                    "end": "18:00:00"
                }]
            }
        ]
    }
}
```

This time rather than the Person's table, we will see a conformation logged in the simulator that an entry was inserted into the TimeTable's table.

If you now swipe the users card they will be correct granted entry, as long as the time is right.

### What's really happening with all these changes?

When we submit a `change` to the test server, the server is taking the json you submitted and storing it in a queue to be sent to the controller.  When the controller calls into the server it asks for the next n number of changes from the server, the server then looks at it's queue and wraps them up in some more json and sends them back to the controller.  The response from the server to the controller look like this:

```json
[
    {
        "messageId": "3a146f76afee41648677887ffced72d8",
        "body": {
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
                        "type": "time",
                        "data": "Mon-Fri-9-18"
                    }]
                }
            }
        }
    },{
        "messageId": "59ddbc558edf4c55828737a743f6f52e",
        "body": {
            "entity": {
                "type": "Common.TimeTable",
                "id": "Mon-Fri-9-18",
                "description": "Example Time Table",
                "sets": [
                    {
                        "days": ["Mo","Tu","We","Th","Fr"],
                        "periods": [
                        {
                            "start": "09:00:00",
                            "end": "18:00:00"
                        }]
                    }
                ]
            }
        }
    }
]
```

## Static TimeTables

In some cases you may not want to distribute your time tables via the changes system.  In this case you can statically embedded them in your `Application Configuration` file.  Extending our simple example above to embed the required time table would look like this:

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
        "timeTables": [
        {
            "type": "Common.TimeTable",
                "id": "Mon-Fri-9-18",
                "description": "Example Time Table",
                "sets": [
                    {
                        "days": ["Mo","Tu","We","Th","Fr"],
                        "periods": [
                        {
                            "start": "09:00:00",
                            "end": "18:00:00"
                        }]
                    }
                ]
        }]
    }
}
```

## Controlling the Door (or anything else) using Commands

Now we've granted our users access to the doors they need, we're going to walk through how to send a command to a door to tell it to unlock for 1 minute.  Commands are our way to temporarily change the properties of an entity in the system such as to unlock a door, operate and output or disable a reader on a door we don't want anyone to use.

Commands are in some ways similar to the user changes we used above, they are fragments of JSON that are queued on the server to send to the controller as soon as it's ready to pick them up, it's just that they use their own queue, not the changes queue.

> POST <http://localhost:8080/configuration/VIR-ADV-C-MLT~00001001)/command>

The door unlock command we will send to the controller will look like the following example, the key points are `Entity` - The thing we want the command to operate on, `CommandName` - what to do, and the optional `Parameters` list.

```json
{
    "messageId": "d26e077d-5783-4545-bbb1-6dcfb2cb2118",
    "correlationId": "361e0b56-039e-4394-9638-133798e56ca7",
    "previousMessageId": null,
    "timeStamp": "2019-07-19T17:01:56.551+00:00",
    "ttl": "00:01:00",
    "entity": "AccessControl.Portal:door-1",
    "commandName": "ChangeMode",
    "parameters": {
        "period": "00:01:00",
        "mode": "Unlocked"
    },
    "requestingEntity": "OemServer:TestServer",
    "permissionedEntity": ""
}
```

> The command used in this example changes the `Operational Mode` of the door, more details on this concept can be found [here](../ApplicationConfiguration/ModeOverview.md)

> All entity types have a list of available commands listed on their definition pages, e.g. for Outputs they can be found [here](../Entities/HardwareOutput.md#commands)

Now there's a couple of tricky bits to sending a command to the controller via Postman; the `MessageId` and `CorrelationId` should be unique GUIDs, and the commands have a Time To Live (TTL) which requires the `TimeStamp` property to be set correctly.  Luckily these are quite easy to deal with in Postman.  The body we actually use in Postman is:

```json
{
    "messageId": "{{$guid}}",
    "correlationId": "{{$guid}}",
    "timeStamp": "{{requestTimestamp}}",
    "ttl": "00:01:00",
    "entity": "AccessControl.Portal:door-1",
    "commandName": "ChangeMode",
    "Parameters": {
        "period": "00:01:00",
        "mode": "Unlocked"
    },
    "requestingEntity": "OemServer:TestServer",
    "permissionedEntity": ""
}
```

The text `{{$guid}}` tells postman to create a new GUID and insert it here, the timestamp is a bit more trick as it needs to be in the correct ISO format, so what we can do is tell Postman to use a local variable `{{requestTimestamp}}` and on the `Pre-request Script` tab we can use the following script

```js
var dateNow= new Date();
postman.setEnvironmentVariable("requestTimestamp", dateNow.toISOString());
```

Which gives us nicely formatted commands, which when sent to the controller should result in the door unlocking and the successful `CommandResponse` message being emitted.  The also will also emit a bunch of state messages to tell you what the door is actually doing.

```txt
[19/07/2019 18:11:18] CommandResponse :
        [Message ID]      : 91a988a4-ca72-4e5b-b469-d04a715f0433
        [Prev Message Id] : 55db8a33-a5a9-4648-8eb8-372ef02a580e
        [Result]          : Success
        [Explanation]     :
        [Service]         :
```
