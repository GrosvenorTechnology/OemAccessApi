# Boot Configuration

The controller will poll this endpoint to pick up the boot configuration of
the controller.

## URL

  /grosvenor-oem/device/\<DeviceSerialNumber\>/bootconfiguration

## Method

  `GET`

## Header Params

### Required

  `Authorization`

### Optional

  `If-None-Match`

## Success Response

> **Code:** 200
> **Content:** [Boot Configuration](../BootConfiguration/Overview.md)  
> If-None-Match not provided or ETags do not match, Boot Configuration returned
with new ETag header.

OR

> **Code:** 304
>
> If-None-Match matches current ETag, no body provided

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
> Requested resource does not exist, i.e. unknown device serial number

## Notes
