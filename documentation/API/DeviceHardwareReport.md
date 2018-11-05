# Device Hardware Report

When a controller starts up it will post it's detected hardware configuration to this endpoint. The configuration will be resent by default every 10 minutes. The URIs and update frequency can be altered in the platform configuration.

## URL

  /grosvenor-oem/device/\<DeviceSerialNumber\>/hardwarereport

## Method

  `POST`

## Header Params

### Required

  `Authorization`

## Data Parameters

````json
{
    "controllerHardware":
    {
        "controllerPartNumber": "TEST-OEM~00001234",
        "productCode": "ADV40",
        "firmwareVersion": "1.0.0",
        "firmwareBuild": "180601",
        "applicationName": "Virtual.OemAccess.UI",
        "applicationVersion": "3.0.6879.25747",
        "bladeCount": 4,
    },
    "bladeHardware": [
        {
            "slot": 1,
            "partNumber": "SAT-ADV-BL-2D~00004714",
            "vendorId": "15",
            "firmwareVersion": "3.0",
            "firmwareType": "ITAC-STD-DR",
            "hardwareRevision": "1V3"
        },
        {
            "slot": 2,
            "partNumber": "SAT-ADV-BL-IO16~00000157",
            "vendorId": "15",
            "firmwareVersion": "0.6",
            "firmwareType": "ITAC-IO-8-8",
            "hardwareRevision": "1V3"
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
> Authorised header not supplied.

OR

> ### **Code:** 403 FORBIDDEN
>
> Supplied authorisation credentials does not allow access to this resource.

OR

> ### **Code:** 404 NOT FOUND
>
> Requested resource does not exist, i.e. unknown device serial number.