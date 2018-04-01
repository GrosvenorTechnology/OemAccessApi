# Application Configuration

The controller will poll this endpoint to pick up the static configuration of
the controller, this includes configuration for Readers, Doors, Areas, polling
rates, mainly configuration other than user data.

## URL

  /grosvenor-oem/device/\<DeviceSerialNumber\>/applicationconfiguration

## Method

  `GET`

## Header Params

### Required

  `Authorization`

### Optional

  `If-None-Match`

## Success Response

> **Code:** 200
>
> If-None-Match not provided or ETags do not match full JSON encoded device
configuration domain model returned with new ETag header.

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