# Entity Resync Request

Calling this endpoint instructs the service to clear the user data queue for
this device and requeue all users for download to the controller.

## URL

~/device/`deviceSerialNumber`/changes/resync/`namespace`/`typeName`

## Method

`POST`

## URL Parameters

### Required

`deviceSerialNumber=[string]`
`namespace=[string]`
`typeName=[string]`

## Header Params

### Required

`Authorization`

## Success Response

> **Code:** 204
>
> Resync request accepted

## Error Response

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
> Requested resource does not exist, i.e. unknown device serial number or unknown type.

## Notes
