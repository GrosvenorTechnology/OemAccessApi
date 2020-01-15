# Post State Notification

This endpoint is used by the controller to push state notification messages to the service.

## URL

/grosvenor-oem/batch/states

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
{
    "messageId": "C5D6194D-3C5F-4A30-92B0-D45F8E92D71D",
    "correlationId": "5E3F03E9-A382-4604-80CF-B96729D3E07A",
    "previousMessageId": null,
    "entity": "AccessControl.Input:Reception-Lights",
    "stateName": "Tamper",
    "stateValue": "Active",
    "lastChanged": "2017-10-1T15:39:00Z"
}
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

## Samples

The body of the call can be expressed in C# as

````c#
public class StateNotification
{
    public Guid MessageId { get; set; }
    public Guid CorrelationId { get; set; }
    public Guid? PreviousMessageId { get; set; }
    public string Entity { get; set; }
    public string StateName { get; set; }
    public string StateValue { get; set; }
    public StateNotificationType StateNotificationType { get; set; }
    public bool DiagState { get; set; }
    public DateTime LastChanged { get; set; }
}
````

## Notes

### Versioning

New entries may be added to the state notification schema in future version, therefore it is best practice to ignore unknown properties rather than cause an exception on the unknown data.  Removal of properties will be deemed a braking change.
