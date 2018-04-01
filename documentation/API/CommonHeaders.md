# Common Headers

All headers used to exchange data related to cross cutting concerns such as
security and tracing will be provided in HTTP headers prefixed with x-gtl-oem

## Request headers

- If-None-Matched: Used in get requests to supply the ETag of the current copy of a resource held on the controller.

- x-gtl-oem-device-serial: This header must contain the serial number of the controller submitting the request to the service, it is main use is logging and tracing of HTTP requests

- x-gtl-oem-client-request-id: a client request ID. Service records this value. Allows the service to trace operation across services.

- x-gtl-oem-client-session-id: a client session ID. Service records this value. Allows the service to trace a group of related operations across services.

- x-gtl-oem-client-application-name: name of the application that generated this request. Service records this value.

## Required Response headers

- x-gtl-oem-request-id: server generated request ID. Can be used to investigate issues with a particular request and to correlate application and service trace logs.

## Optional response headers:

- ETag: When returning a resource, the response should include the ETag for the requested resource.
