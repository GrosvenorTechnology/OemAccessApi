# Entity Request

This endpoint provides access to entities which define non-device specific
configuration.

## URL

/grosvenor-oem/configuration/`<namespace>`/`<typeName>`/`<id>`

## Method

`GET`

## URL Parameters

### Required

`namespace=[string]`

`typeName=[string]`

`id=[string]`

## Header Params

### Required

`Authorization`

### Optional

`If-None-Match`

## Success Response

> **Code:** 200
>
> If-None-Match is not provided or ETags do not match, the requested entity
will be returned in full with a new ETag header.

OR

> **Code:** 304
>
> If-None-Match matches current ETag, no body provided.

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
> Requested resource does not exist, i.e. unknown time schedule id

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

### Supported Entity Types

- Common.TimeTable
