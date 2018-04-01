# Device Hardware Report

When a controller starts up (after checking the service location endpoint) it
will post it’s detected hardware configuration to this endpoint. The
configuration will be resent on any detected configuration changes (such as
adding or removing a blade).

## URL

  /grosvenor-oem/device/\<DeviceSerialNumber\>/hardware

## Method

  `POST`

## Header Params

### Required

  `Authorization`

## Success Response

> **Code:** 204
>
> The hardware report was successfully updated.

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