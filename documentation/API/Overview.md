# OEM controller Protocol

## Overview

This document aims to provide a high-level overview of the Grosvenor OEM
controller protocol, with basic protocol and domain models examples to
illustrate the operational flow of a controller interaction with the service.
Other documents will cover the complete details of the protocol and domain
models involved.

## HTTP vs HTTPS

This document will always reference HTTP when discussing protocol request. At
all times those requests are expected to be made over a TLS tunnel in the form
of a HTTPS request.

## Versioning

New entries may be added to message schemas in future version, therefor it is best practice to ignore unknown properties rather than cause an exception on the unknown data.  Removal of properties will be deemed a braking change.

## Protocol Basics

1. [Functional Levels](FunctionalLevels.md)
2. [CommonHeaders](CommonHeaders.md)
3. [Authentication](Authentication.md)
4. [Server Queues](ServerQueues.md)
5. [Device Heartbeats](DeviceHeartbeat.md)

## Device Configuration

1. [Platform Configuration Request](PlatformConfigurationRequest.md)
2. [Application Configuration Requests](ApplicationConfigurationRequest.md)
3. [Device Hardware Report](DeviceHardwareReport.md)
4. [Entity Types](EntityTypes.md)
5. [Entity Changes](EntityChanges.md)
6. [Entity Requests](EntityRequests.md)
7. [Entity Resync](EntityResync.md)
8. [User lookup (by identifier)](LookUpUserByIdentifier.md)


## Real Time Events

1. [Event Submission](PostEvent.md)
2. [State Notification](PostStateNotification.md)
3. [State Requests Queue](StateRequests.md)

## Commanding

1. [Command Queue](Commands.md)
2. [Command Responses](PostCommandResponse.md)

## Areas

1. [Area Movement Requests](AreaMovementRequest.md)
