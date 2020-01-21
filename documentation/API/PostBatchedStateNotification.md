# Post Batched State Notification

When using controllers in 485 mode, the controllers will forward their state notifications to the line header, which will then send these states up to the service.
To improve performance of the line header, messages from all controllers are delivered to the service in batches. 

## URL

/grosvenor-oem/batch/states

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
[
    {
        "serialNumber": "OEM-ADV-C2-SNG~00002003",
        "message":{
            {
                "messageId": "C5D6194D-3C5F-4A30-92B0-D45F8E92D71D",
                "correlationId": "5E3F03E9-A382-4604-80CF-B96729D3E07A",
                "previousMessageId": null,
                "entity": "AccessControl.Input:Reception-Lights",
                "stateName": "Tamper",
                "stateValue": "Active",
                "lastChanged": "2017-10-1T15:39:00Z"
            }
        }
    },
    {...},
    {...}
]
````

## Success Response

> **Code:** 204
>
> The state notification has been stored.

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

The structure of the state notifications sent to the batch endpoint are the same as for the per device endpoints as described [here](PostStateNotification.md)

It is the responsibility of the service to validate that the line header that is posting state data for downstream controllers is actually authorised to do so.
