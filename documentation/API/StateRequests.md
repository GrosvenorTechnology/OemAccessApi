# State Request

When a state requests needs to be sent to a controller it is queued on the service to the commands queue. The queue uses the standard server queue semantics to push message to the client.

## Queue URL

~/device/`deviceSerialNumber`/states/messages

## URL Parameters

### Required

`deviceSerialNumber=[string]`

## Message types

The state request queue will return messages with the following format

````json
{
    "messageId": "F8F3249A-7FD3-479F-A312-2DD1A049FD94",
    "correlationId": "1D758BE0-1DE8-4D9B-8D87-BCB3B0E0E400",
    "entity": "Hardware.Input:PerimeterFenceAlarm",
    "stateName": "InputState",
}
````
