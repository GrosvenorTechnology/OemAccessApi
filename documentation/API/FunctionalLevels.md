# Protocol Functional Level

The protocol is provided in three levels, the overall functionality of the
protocol remains the same across all three levels, but the performance increases in the higher levels at the cost of more complicated implementations.

## Level 1

Controller initiates all connections to the service API over HTTP using JSON
payloads. All updates to be sent to the controller are queued on the service and
picked up by the controller polling the queue endpoint.

## Level 2

This level implements all the functions of Level 1 with the addition of a long
polling/websockets endpoint which pushes ‘attention’ messages to the controller
requesting that a specific endpoint is polled as soon as possible.

## Level 3

At this level the HTTP+Websockets implementation is replaced with a high
performance full duplex protocol (AMQP) which provides the maximum performance
for dispatching commands (i.e. door unlock) to the controller.
