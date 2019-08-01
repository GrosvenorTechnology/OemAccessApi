# Permission Request

The permission request service is used by the `serverPermission` gate in an User entity permission and can also be used globally.

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

If the request is due to the global server permission being enabled, then the `requestId` will be "GlobalServerPermission". Otherwise it will be set by what is defined in the serverPermission gate.

## Response

The recommended data to use on coming to a decision is the `RequestId` and `UserId`.

If using for area APB the `RequestId` can be used to determine the area and whether entering or leaving.

### Code: 200

Request processed, result in response body.

#### Method 1

##### Allowed

````json
{
    "Allow": true
}
````

N.B. A reason can be supplied if required.

##### Not allowed

````json
{
    "Allow": false,
    "Reason":"FailedAreaCheck"
}
````

#### Method 2

##### Allow

````json
{
    "PermissionResponse ": "allow"
}
````

##### Deny

````json
{
    "PermissionResponse ": "deny",
    "Reason":"FailedAreaCheck"
}
````

##### FallBack

Fall back to defined user permissions in the controller.

````json
{
    "PermissionResponse ": "fallBack"
}
````

##### Error

When using global server permissions, if something goes wrong with the server processing of the request, returning `error` will get the controller to use its defined failure mode (Allow | Deny | FallBack).

````json
{
    "PermissionResponse ": "error",
    "Reason":"DatabaseError"
}
````

### Code other than success

Any other return code will be regarded as a request failure and the outcome will be determined by the `serverPermission` gate.

## No response

No response from the service will be regarded as a request failure and the outcome will be determined by the `serverPermission` gate.
