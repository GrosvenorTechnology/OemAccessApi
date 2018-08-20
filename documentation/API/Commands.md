# Commands

When a Command needs to be sent to a controller it is queued on the service to
the commands queue. The queue uses the standard server queue semantics to push 
message to the client.

## Queue URL

~/device/`deviceSerialNumber`/commands/messages

## URL Parameters

### Required

`deviceSerialNumber=[string]`

## Message types

The command queue will return messages with the following format

````json
{
    "messageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
    "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
    "previousMessageId": null,
    "timestamp": "2017-10-1T15:39:00Z",
    "ttl": "00:00:05.00",
    "entity": "AccessControl.Output:3DCB81BB-ACBB-4497-9F06-8942B0C20622",
    "commandName": "Operate",
    "parameters": {
        "period": "2:00.00"
    }
}

````