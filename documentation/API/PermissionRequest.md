# Permission Request

The permission request service is used by the `serverPermission` gate in an User entity permission.

## URL

/grosvenor-oem/device/`deviceSerialNumber`/permissionrequest

## Method

`POST`

## Header Params

### Required

`Authorization`

## Data Parameters

````json
{
    "UserId": "Person0002",
    "TokenId": "Token3107523291",
    "TokenNumber": "3107523291",
    "OriginatingEntity": "Osdp.Reader:Reader-2",
    "TargetEntity": "AccessControl.Portal:Portal-1",
    "CommandName": "RequestExit",
    "RequestId": "building-1_exit"
}
````

## Response

The recommended data to use on coming to a decision is the `RequestId` and `UserId`.

If using for area APB the `RequestId` can be used to determine the area and whether entering or leaving.

### Code: 200

Request processed, result in response body.

#### Allowed

````json
{
    "Allow":true
}
````

N.B. A reason can be supplied if required.

#### Not allowed

````json
{
    "Allow":false,
    "Reason":"FailedAreaCheck"
}
````

### Code other than success

Any other return code will be regarded as a request failure and the outcome will be determined by the `serverPermission` gate.

## No response

No response from the service will be regarded as a request failure and the outcome will be determined by the `serverPermission` gate.
