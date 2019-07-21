# Getting started with Unknown token download

## Overview

The Unknown token function provides a way for the controller to request a token from the server when it can be found in the controllers local database.
The server should return a full user record, which will be inserted into the database and used for all future uses of the token.

This feature was first supported in application version 4.0.24734.

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
      "sharedKey": "9nF2W3A18UG8XOGI7gsk2UV+CdpsSCZ3YHGvQjkKtKY=",
      "features": [
         "EventContentExtension"
      ]
   }
}
```

And a simple platform config such as the following, the over ride we have to to accelerate the heartbeat for demo purposes.

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

We also need a minimal application configuration file that has a door and reader set up

```json
{
    "controller": {
        "description": "Virtual Test Controller",
        "id": "7054fb73-eab8-402b-a98f-78cabafa34df",
        "timeZone": "Europe/London",
        "readers": [
            {
                "address": "1-0-1",
                "id": "f16d69f3-c6eb-40a5-ad40-bf579f6bc32a",
                "description": "Reader 1",
                "tokenFormatType": "RDSATEONPRO"
            }
        ],
        "portals": [
            {
                "address": "1-0-1",
                "id": "d23dba20-7362-4883-90cb-7f341b82eeb4",
                "description": "Door 1",
                "entry": {
                    "readers": [
                        "f16d69f3-c6eb-40a5-ad40-bf579f6bc32a"
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

Start the OEM-Simulator, you should see heartbeats and a bunch of state & event messages display in the test server, when things have settled down, click the update button
on the 'Application Config' tab to apply the application configuration.

## Testing an unknown token

Now the controller is running, expand the 'Tokens' tab on the right and click the default '123456' token, you should immediately see the beeper of reader 1 flash red and an 'Unknown Token' event as below will be logged in the test server.

```ascii
[11/07/2019 14:33:47] Hub : Network Event Event Submitted to Group :: VIR-ADV-C-MLT~00001001
[11/07/2019 14:33:47] Itac.OemAccess.TestingServer.Model.Event :
        [SourceTime] : 2019-07-11T14:33:47.7109951+01:00
        [Entity]     : Hardware.Reader:f16d69f3-c6eb-40a5-ad40-bf579f6bc32a
        [EventName]  : Read
        [EventType]  : Plain
        [Content]    : TokenData:123456, Result:UnknownToken
```

## Enabling token download

To enable the controller to query the server for unknown tokens, add the setting `"unknownTokenRequestEnabled": true` to the application config under the `controller` property.

The setting `unknownTokenTimeout` controls how often the controller will check the server when the same card is repeatedly presented.  The default timeout is 1 day, to make things easier to test, we've reduced the timeout to the minimum of 10 seconds.

Copy the configuration below to the application config tab and click update.

```json
{
    "controller": {
        "description": "Virtual Test Controller",
        "id": "7054fb73-eab8-402b-a98f-78cabafa34df",
        "timeZone": "Europe/London",
        "unknownTokenRequestEnabled": true,
        "unknownTokenTimeout": "00:00:10",
        "readers": [
            {
                "address": "1-0-1",
                "id": "f16d69f3-c6eb-40a5-ad40-bf579f6bc32a",
                "description": "Reader 1",
                "tokenFormatType": "RDSATEONPRO"
            }
        ],
        "portals": [
            {
                "address": "1-0-1",
                "id": "d23dba20-7362-4883-90cb-7f341b82eeb4",
                "description": "Door 1",
                "entry": {
                    "readers": [
                        "f16d69f3-c6eb-40a5-ad40-bf579f6bc32a"
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

If the token is presented, you should see the following lines output in the network log tab

```ascii
[11/07/2019 14:33:47] [Debug] [VIR-ADV-C-MLT~00001001] Unknown token request - Requesting user for token 123456
[11/07/2019 14:33:47] [Debug] [VIR-ADV-C-MLT~00001001] Unknown token request - UnknownTokenNumber: 5ms
```

The test server by default will return a response with a 204 status code indicating that the token is unknown to the server.

## Configuring the test server to return real responses

The test server has server URLs that can be used for testing the unknown token function of the controller.

> POST <http://localhost:8080/configuration/tokenresponses/123456>

This allows you to set the repose to a given token number, in this case `123456`  You supply the response in the body of the request, the following example will create a response after 300ms with a 204 response code, the server does not know the token.

```json
{
    "responseCode": 204,
    "delay": 300,
    "payload": null
}
```

To produce a valid response POST the following body to the test server

```json
{
    "responseCode": 200,
    "delay": 300,
    "payload": {
        "type": "AccessControl.User",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
        "identifiers": [{
            "type": "AccessControl.Token",
            "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
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

This will cause the following response to be returned to the controller

```json
{
    "type": "AccessControl.User",
    "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
    "identifiers": [{
        "type": "AccessControl.Token",
        "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
        "data": "123456"
    }],
    "permissions": {
        "General-Access": [{
            "type": "always"
        }]
    }
}
```

The expected sequence of events from the user with the token:

1. User badges token
2. Controller flashes access denied
3. User badges token again and is granted access
4. Some time later user uses same token on same controller and is immediately granted access

## Cleaning up

To delete the downloaded user from the controller to allow further testing, submit the following chunk of json to the test server using the URL

> POST <http://localhost:8080/configuration/VIR-ADV-C-MLT~00001001/change>

```json
{
    "delete": {
        "type": "AccessControl.User",
        "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9"
    }
}
```

To remove a response from the test server and revert to the default 204 response, send this delete to the test server

> DELETE <http://localhost:8080/configuration/tokenresponses/123456>
