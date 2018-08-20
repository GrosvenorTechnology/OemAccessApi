# Device Heartbeat

The device is required to send a heartbeat to the service on an interval defined
in the device configuration. This heartbeat is used to allow the service to
detect the controller has gone offline. If the service is capable, the response
payload will return the id of any endpoints or queues that requires an immediate
poll.

## URL

/grosvenor-oem/device/\<DeviceSerialNumber\>/heartbeat

## Method

`POST`

## Header Params

### Required

`Authorization`

## Success Response

> **Code:** 200
>
> The body contains the ids of the queues that have pending data. It is
allowable to return an empty activity array.
>
> ````json
> {
>     "activity": [
>         "platformConfig",
>         "changes"
>     ]
> }
>````

OR

> **Code:** 204
>
> Heartbeat accepted, and either smart heartbeats are disabled, or no data is
queued on the service.

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

Valid Activities

- bootConfig
- platformConfig
- applicationConfig
- changes
- commands
- states