# Device Hardware Report

When a controller starts up it will post it’s detected hardware configuration to this endpoint. The configuration will be resent on any detected configuration changes (such as adding or removing a blade).

## URL

  /grosvenor-oem/device/\<DeviceSerialNumber\>/hardware

## Method

  `POST`

## Header Params

### Required

  `Authorization`

## Data Parameters

````json
{
    "serialNumber": "MLT_123456",
    "controllerType": "ADV-MULTI",
    "hardwareVersion": "1.0",
    "firmwareVersion": "1.2.0+release",
    "applicationVersion": "3.4.0",
    "maxBladeCount": 4,
    "blades": [
        {
            "slot": 1,
            "type": "BLADE-IO"
        },
        {
            "slot": 2,
            "type": "BLADE-2DR",
            "dynamicDevices": [
                {
                    "id": 2-1-1,
                    "type": "osdp",
                    "port": 1,
                    "vendorCode": 74375839,
                    "modelNumber": 54,
                    "version": 2,
                    "serialNumber": 654735673,
                    "firmware": "2.8.7",
                    "capabilities": {
                        "001": {"compliance": 3, "numberOf": 8},
                        "002": {"compliance": 1, "numberOf": 8}
                    }
                }
            ]
        }
    ]
}

````


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