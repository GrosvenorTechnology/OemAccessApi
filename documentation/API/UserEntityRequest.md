# User Entity Request

**Supported >= 4.0.24734**

This endpoint allows the controller to directly access the controller specific data for a user by an identifier associated to the user. The main use case for this is to allow a controller to make access decisions immediately after downloading its initial device configuration, while user data is still being streamed. The controller on receiving an unknown identifier e.g. from a reader, will query this endpoint and attempt to access the user data directly. Implementation of this endpoint is optional, in which case the controller will deny access until the user data has been loaded via other means.

For more detailed instructions on using this feature see [Getting started with Unknown Token Download](..\GettingStarted\GettingStartedUnknownTokenDownload.md)

## URL

/grosvenor-oem/device/`<deviceSerialNumber>`/userentityrequest

### Method

`POST`

### Content

````json
{
    "tokenNumber": "1559635345"
}
````

### Required

`Authorization`

## Success Response

> **Code:** 200
>
> The requested user was found and a device specific record for the controller was returned.
>
> ````json
> {
>     "type": "AccessControl.User",
>     "id": "DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
>     "identifiers": [{
>         "type": "AccessControl.Token",
>         "id": "80213b75-eb3a-4376-8694-bf38d44b9772",
>         "data": "1559635345"
>     }],
>     "permissions": {
>         "Portal-1_Entry": [{
>             "type": "time",
>             "data": "Common.TimeTable:Mon-Friday-9-17"
>         }],
>         "Portal-1_Exit": [{
>             "type": "always"
>         }]
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
