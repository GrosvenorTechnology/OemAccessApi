# Batched Heartbeat for Line Header (Gateway)

The batched heartbeat endpoint is a performance enhancement to the standard per device heartbeat that has been implemented for performance reasons on a line header.

The Line Header is required to send a request to the service on an interval defined in the device configuration.  This request should contain the serial numbers for all devices the line header is acting as a proxy for. These heartbeats will be used to allow the service to detect which controllers have gone offline. If the service is capable,
the response payload will return the id of any endpoints or queues that requires an immediate poll.

## URL

/grosvenor-oem/batch/heartbeat

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
[
    {
        "serialNumber": "OEM-ADV-C2-LH~00002001",
        "downStreamOnline": true,
        "includeCommands": true
    },
    {
        "serialNumber": "OEM-ADV-C2-SNG~00002003",
        "downStreamOnline": true,
        "includeCommands": true
    }
]
````

The Line header will post a heartbeat notification for itself and all downstream controllers configured in the application config file.

The `downStreamOnline` property indicates if the Line Header has active 485 communication with the controller.
The `includeCommands` property indicates that the server should return the first 10 pending commands for the in the heartbeat response.

## Success Response

> **Code:** 200
>
> The body contains the ids of the queues that have pending data. It is allowable to return an empty activity array.  Optionally the response may include the time on the server in UTC.  This is intended for cases where the controller does not have NTP access.  The timestamp will be used to correct the controller's time when drift is detected.
>
> ````json
> {
>     "serverTime": "2019-02-25T12:53:16Z",
>     "responses": [
>        {
>           "serialNumber": "OEM-ADV-C2-SNG~00002003",
>           "activity": [ "platformConfig", "changes" ],
>           "commands": [
>              {
>                 "messageId": "3a146f76afee41648677887ffced72d8",
>                 "body": {
>                     ...
>                 }
>              },{
>                 "messageId": "59ddbc558edf4c55828737a743f6f52e",
>                 "body": {
>                     ...
>                 }
>              }
>           ]
>        }
>     ]
> }
>````

OR

> **Code:** 204
>
> Heartbeat accepted, and either smart heartbeats are disabled, or no data is queued on the service.

## Error Response

> ### **Code:** 400 BAD REQUEST
>
> The JSON payload could not be parsed.

OR

> ### **Code:** 401 UNAUTHORIZED
>
> Authorised header not supplied.

OR

> ### **Code:** 403 FORBIDDEN
>
> Supplied authorisation credentials does not allow access to this resource.

OR

> ### **Code:** 404 NOT FOUND
>
> Requested resource does not exist, i.e. unknown device serial number.

## Notes

It is the responsibility of the service to validate that the line header that is posting heartbeat data for downstream controllers is actually authorised to do so.

Valid Activities

- bootConfig
- platformConfig
- applicationConfig
- changes
- commands
- states

The body of the command messages in the request body above has been omitted for clarity, it has the same content as messages received via the command queue as shown [here](Commands.md
