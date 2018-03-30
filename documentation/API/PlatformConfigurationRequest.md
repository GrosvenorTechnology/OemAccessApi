# Platform Configuration

The controller will poll this endpoint to get the static platform configuration of the controller

## URL

  /grosvenor-oem/device/\<DeviceSerialNumber\>/platformconfiguration

## Method

  `GET`

## Header Params

### Required

  `Authorization`

### Optional

  `If-None-Match`

## Success Response

* **Code:** 200 <br />
  **Content:** `{ id : 12 }`

## Error Response

* **Code:** 401 UNAUTHORIZED - Authorised header not supplied

  OR

* **Code:** 403 FORBIDDEN - Supplied authorisation credentials does not allow access to this resource

  OR

* **Code:** 404 NOT FOUND - Requested resource does not exist, i.e. unknown device serial number


## Notes

