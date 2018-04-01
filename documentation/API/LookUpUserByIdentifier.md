# User Lookup by Identifier

This endpoint allows the controller to directly access the controller specific
data for a user by an identifier associated to the user. The main use case for
this is to allow a controller to make access decisions immediately after
downloading its initial device configuration, while user data is still being
streamed. The controller on receiving an unknown identifier e.g. from a reader,
will query this endpoint and attempt to access the user data directly.
Implementation of this endpoint is optional, in which case the controller will
deny access until the user data has been loaded via other means.

## URL

/grosvenor-oem/device/`<deviceSerialNumber>`/userlookup/byidentifier?type=`<type>`&data=`<data>`

## Method

`GET`

## URL Parameters

### Required

`deviceSerialNumber=[string]`

`type=[string]` – The identifier type, the type is limited to identifiers that can be expressed as short strings, such as numeric tokens or car number plates.

`data=[string]` – The identifier data, e.g. for a numeric token tokens 569874514

## Header Params

### Required

`Authorization`

## Success Response

> **Code:** 200
>
> The requested user was found and a device specific record for the controller was returned.
>
> ````json
> {
>     "entity": {
>         "type": "AccessControl.User",
>         "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
>         "identifiers": [{
>             "type": "AccessControl.Token",
>             "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
>             "data": "1559635345"
>         }],
>         "permissions": [{
>                 "Portal-1_Entry": [{
>                     "type": "time",
>                     "data": "Common.TimeTable:Mon-Friday-9-17"
>                 }]
>             },
>             {
>                 "Portal-1_Exit": [{
>                     "type": "always"
>                 }]
>             }
>         ]
>     }
> }
> ````

OR

> **Code:** 204
>
> User was not found

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

OR

> ### **Code:** 501 FEATURE NOT IMPLEMENTED
>
> Feature not implemented by the server

## Notes
