# Device Hardware Addresses

All devices within the controller can be addressed by a logical address of the
form

`blade`-`bus`-`device`

e.g. 1-0-5

The controller itself is blade 0

Blade addresses 0-99 are reserved for internal use. Addresses 100+ are available
for virtual devices such as SimonsVoss IP locks.

The native devices on a blade (locks/inputs/outputs) are on bus 0

The OSDP buses on a door blade are bus 1 and 2
