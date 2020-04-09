# Post Batched State Notifications

To improve performance, state notification messages can be delivered to the service in batches.

## URL

/grosvenor-oem/device/\<DeviceSerialNumber\>/batchedstates

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
[
    {
        "messageId": "C5D6194D-3C5F-4A30-92B0-D45F8E92D71D",
        "correlationId": "5E3F03E9-A382-4604-80CF-B96729D3E07A",
        "previousMessageId": null,
        "entity": "AccessControl.Input:Reception-Lights",
        "stateName": "Tamper",
        "stateValue": "Active",
        "lastChanged": "2017-10-1T15:39:00Z"
    },
    {...},
    {...}
]
````

## Success Response

> **Code:** 204
>
> The state notifications have been stored.

## Error Response

> ### **Code:** 400 BAD REQUEST
>
> The JSON payload could not be parsed.

OR

> ### **Code:** 401 UNAUTHORIZED
>
> Authorised header not supplied

OR

> ### **Code:** 403 FORBIDDEN
>
> Supplied authorisation credentials does not allow access to this resource

OR

> ### **Code:** 404 NOT FOUND
>
> Requested resource does not exist, i.e. unknown device serial number

## Notes

### Versioning

The structure of the state notifications sent to the batch endpoint are the same as for the single endpoint as described [here](PostStateNotification.md)
