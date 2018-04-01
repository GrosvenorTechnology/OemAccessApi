# Area Movement Request

If a controller has *area-mode:remote* set in the controller section of the
application configuration then all checks relating to area enforcement and
anti-passback will be delegated to the remote service. In this case a POST will
be made the endpoint below requesting movement between areas. If there is only
one area involved, either from or to will be null.

## URL

/grosvenor-oem/areas/requestmovement

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
{
    "entity": "AccessControl.User:DAA01FA1-7CBA-401A-B98E-C3CC22D603B9",
    "from":[
        "AccessControl.Area:E5F3D144-8490-4D04-9761-18F018088A6A"
    ],
    "to": [
        "AccessControl.Area:71A3F764-4253-4186-AEF3-ECD74F05737C"
    ]
}
````

## Success Response

> **Code:** 200
>
> Request processed, result in response body
>
> ````json
> {
>     "result": "allowed"
> }
> ````

OR

> **Code:** 204
>
> User was not found

## Error Response

> ### **Code:** 400 BAD REQUEST
>
> Bad request. Badly formatted JSON or one of the areas is unknow, full
details are in the standard error response in the response body.

OR

> ### **Code:** 401 UNAUTHORIZED
>
> Authorised header not supplied

OR

> ### **Code:** 403 FORBIDDEN
>
> Supplied authorisation credentials does not allow access to this resource

OR

> ### **Code:** 501 FEATURE NOT IMPLEMENTED
>
> Feature not implemented by the server

## Notes
