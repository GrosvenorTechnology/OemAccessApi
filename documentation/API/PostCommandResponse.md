# Command Response & Progress Submission

This endpoint is used by the controller to push command progress and response
messages back to the service.

## URL

~/device/`deviceSerialNumber`/commands

## Method

`POST`

## Header Params

### Required

`Authorization`

## URL Parameters

### Required

`deviceSerialNumber=[string]`

## Data Parameters

> ### Command Progress
> ````json
> {
>     "messageId": "BC6B64D2-14C7-465F-8177-54E2FC046C64",
>     "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
>     "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
>     "timeStamp": "2017-10-1T15:39:10Z",
>     "progress": "20",
> }
> ````

OR

> ### Command Response
> ````json
> {
>     "messageId": "4F57465D-4E05-4BA4-B464-E36FAB4C8903",
>     "correlationId": "6CF3B367-FA19-4356-8325-6BC2002408BD",
>     "previousMessageId": "E4C65FF0-2107-4B8E-AE29-082C360C457F",
>     "timestamp": "2017-10-1T15:39:30Z",
>     "result": "FailedOnPermissions",
>     "explaination": "NoEnabledAPGs"
> }
> ````

## Success Response

> **Code:** 204
>
> The response has been stored.

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

## Samples

The body of the call can be expressed in C# as

````c#
public class CommandResponse
{
    public Guid MessageId { get; } = Guid.NewGuid();
    public Guid CorrelationId { get; } 
    public Guid? PreviousMessageId { get; }
    public DateTimeOffset TimeStamp { get; } = DateTimeOffset.UtcNow;
    public CommandOutcomeCategory Result { get; }
    public string Explaination { get; }
}
````

````c#
public class CommandProgress
{
    public Guid MessageId { get; } = Guid.NewGuid();
    public Guid CorrelationId { get; } 
    public Guid? PreviousMessageId { get; }
    public DateTimeOffset TimeStamp { get; } = DateTimeOffset.UtcNow;
    public int Progress { get; }
}
````

## Notes

### Command Response Result codes

Valid values for the command response result property are

- Success,
- FailedOnPermissions,
- FailedBecauseOfError,
- UnknownCommand,
- TargetNotFound,
- CommandArgumentError,
- Accepted,
- InProgress
