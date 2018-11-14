# Application Change log

Applications are release through two channels, the Stable channel and the beta channel.  The urls for each channel are:

- Stable : https://firmware.customexchange.net/adv
- Beta : https://firmware.customexchange.net/beta

The beta channel only contains the latest released firmware, and applications published to this channel may be removed at any time.

The Stable channel will contain all firmware versions as well as every application version published, no application will be removed from this channel

## Stable Channel Releases

### 3.0.21413 (FW >= 1.0.3)

- OSDP Support 
- SmartIntego bug fixes and improvements (white lists)
- Bug fix to verification areas not being configured correctly
- Commands added for remotely enabling SSH
- Bug fix for bad HTTP headers
- Added delete all command for entities (e.g. Users)

### 2.0.20447 (FW >= 1.0.3)

- SmartIntego support added
- Bug Fixes


## Beta Channel Releases

## 3.0.21845 (FW >= 1.0.3)

- Permission Ids in user and portals are no longer case sensitive (#19199)
- Portal does not raise event when exit switch is used (#19201)
- Controller now reports hardware configuration to server (#19139) [link](API/DeviceHardwareReport.md)
- Fix for controller communication failing (#19138)
- Fix for null reference exception when presenting token to door (#18860)
- Fixed app crash when SmartIntego reader references no existent token format (#19025)
- Portals can now be unlocked based on a system mode (#19132, #19133)
- Support SmartIntego gateway nodes where the ID is not 0x100 (#19127)
- Firmware is not down graded to a lower patch level by default
- Stability improvements


### 3.0.21739 (FW >= 1.0.3)

- Changed actions to trigger off a state's state if the 'event' name matches a state.
- Added new compact timeTable format.​
- Outputs now support Command Stack as well as absolute On/Off modes of operation.​
- Controller main LED now flashes green when heatbeats successfully sent to server, reverts to yellow if connection fails.​
- Implemented fault detection in Input PIR mode.​
- Added option to sound reader beeper on valid read.​
- Corrected typo is setting name OpenTooLongSounderPeriod.​
- Added EnabledFrom/EnabledTo properties to user and user identifiers.​
- Fixed event order when door sensor is enabled/disabled​.
- Portal now relocks correctly when timeTable associated with door unlock is deleted​.
- Token format now changes when edited for SmartIntego, no longer requires a restart​.
- Can now create token specific PINs​.
- Added TimePeriod permission gate for access permissions.​
- OSDP improvements - Encryption support, diagnostic logging, line error count states, release version of door blade firmware​.
- System modes and Portal interlocks can now be defined in the application configuration.​
- Added server check permission gate, allows access checks to be delegated to the server.​
- Removed concept of local/Remote areas.  All areas are now local, for remote areas user the server check permission gate.​
- Improvements to user download speed.​
- Added support for network proxies.​
- Firmware will not downgrade when difference is only in patch level i.e. rolling back from 1.0.6 to 1.0.4.​
- Fixed error downloading boot configuration file.