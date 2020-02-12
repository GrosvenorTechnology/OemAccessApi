# Application Change log

Applications are release through two channels, the Stable channel and the beta channel.  The urls for each channel are:

- Stable : https://firmware.customexchange.net/adv
- Beta : https://firmware.customexchange.net/beta

The beta channel only contains the latest released firmware, and applications published to this channel may be removed at any time.

The Stable channel will contain all firmware versions as well as every application version published, no application will be removed from this channel

## Stable Channel Releases

### 4.3.25685 (FW Minimum >= 1.1.2) (Critical Update) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.3.25685.zip)

- [Feature] Shared access permissions (#19327) [link](Entities/CommonSharedPermissionSet.md)
- [Feature] Added support for Custom input levels and sense times #19616
- [Feature] Improved OSDP reader setup to support setting up OSDP Reader encryption #19544
- [Feature] For OSDP part coded blades, OSDP will be enabled by default at 9600, no termination using a default master key #19857
- [Feature] Added extra vendor IDs for detecting Third Millennium OSDP readers #19815
- [Feature] Minimum lift output activation time reduced to 500ms from 2 seconds #19849
- [Feature] New state added for egress (REX) state (Normal/Tamper) #19845
- [Bug] This release resolves a critical issue where a controller would occasionally 'fall asleep' and stop communicating with server and responding to reads (#20013)
- [Bug] Performance improvement for Simple Lifts, outputs now activate faster #19850
- [Bug] Unknown feature did not allow access on first read of card, access granted on second read #19854
- [Bug] Message ID was set to empty guid on state notifications #19812
- [Bug] OSDP Reader LED now defaults to constant not pulsed #19811
- [Bug] Inputs not working after upgrading from older application versions #19838
- [Bug] Fixes for multiple fast reads on controller #19787
- [Bug] Pins sent to controller with leading zeros do not work (i.e. 0012) #19759
- [Bug] 485 Controller-to-Controller stability fixes #19725

### 4.1.25118 (FW Minimum >= 1.1.2) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.1.25118.zip)

- [Bug] Multi-digit keypad data not recognized #19759

### 4.1.25068 (FW Minimum >= 1.1.2) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.1.25068.zip)

- [Feature] Added support for simple lift support #19395 [link](Entities/HardwareLift.md)
- [Feature] Global support for Server Permissions on readers #19724 [link](Entities/HardwareController.md#globalserverpermissionenabled--v41)
- [Feature] Verbose network logging can now be enabled via platform config file (allows enabling without app restart) [link](PlatformConfiguration/Overview.md#verbosenetworklogging--v41)
- [Feature] Batched heartbeat support for 485 Line Headers #19546 [link](Entities/HardwareController.md#configurationdownload)
- [Feature] Configuration Event (Success) now sent when config is applied, fail event contains validation failures #19711
- [Feature] Configuration app now writes entry and exit permissions IDs in doors
- [Feature] Configuration app has 'development use only' banner removed
- [Bug] Fixed invalid validation of device addresses #19741
- [Bug] Log stream on controller not correctly enabled via boot config file #19726
- [Bug] DHCP status incorrectly reported #19690

### 4.0.24757 (FW Minimum >= 1.0.8 : Recommended >= 1.1.2) - Released 12/7/2019 [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.0.24757.zip)

- Download of unknown tokens [link](API/UserEntityRequest.md)
- Preview release of downstream 485 support - details from support on request
- Log forwarding can be enabled without controller restart (#19650)
- Nuget package now published on nuget.org is signed (GT.OemAccess.Configuration)
- Updated simulator UI
- TTL timeouts in commands now enforced at the controller, commands will complete with status TtlExceeded (#19641)
- EnabledFrom/EnableTo are now nullable inside controller rather than defaulting to Min/Max values for DateTime which causes issues when changing timezones (#19640)
- Main LED States are no longer overwritten on heartbeat (#19551)
- Controller now caches last downloaded application config file in db directory for diagnostics (#19586)
- Suppression of controller app crash when bad data detected in TimeTable (#19526)
- Device MAC and active IP (if any) not included in 485 device hardware report (#19535)
- Controller now has much stronger validation of invalid application config file (#19586)
- Controller now has stronger validation of invalid boot configuration file (#19548)
- Controller writes system uptime to diagnostic log every minute (#19539)
- Added 'Clear operational Mode' (ClearMode) command (#19564)
- 485 Line headers now implement back pressure to prevent download of changes from server at a much faster rate than the can be sent to the target device (#19545)
- Bug fix: Null reference error reported on first card swipe after controller start (#19515)
- Improved stability for OSDP readers when using encryption
- Added support for controlling logStream app through commands and configuration (requires FW >= 1.1.2) [link1](Entities/HardwareController.md#enablelogging-4000--fw-110) [link2](BootConfiguration/Overview.md#optional-settings-logging)
- Fix for dueDate error in logs when using Holidays (#19270)
- Added support to receive commands from multiple servers
- Added support to receive state requests from multiple servers
- Fix for null reference on card swipe reported in logs (#19433)
- Http stability improvements when server is intermittently available.
  - Http default time-out reduced to 10 seconds from 100
- Support for 485 communication between controllers (restrictions apply)
  - New gateway application acts as network proxy to downstream 485 controllers
- Command argument checks improved when receiving malformed commands
- Hardware reports now sent when changes detected
- Reboot and Restart commands added to controller [link](Entities/HardwareController.md#restart-4000)
- Controller Firmware and application can be updated via USB drive [link](Entities/HardwareController.md#enableusbupdate-4000)

### 3.3.22855 (FW >= 1.0.3) - Released 21/2/2019 - [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-3.3.22855.zip)

- Optimise XML sent to blade, was causing issues with OSDP when encryption was enabled.

### 3.3.2267 (FW >= 1.0.3) - Released 5/2/2019 - [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-3.3.22667.zip)

- Heartbeat can now contain a timestamp from server, controller will correct if more than 2 minutes adrift.  For use when no NTP available.
- Removed unneeded reboots when updating the application only or changes to boot config that do not require restarts.
- Made changes to how states are queued and sent, it was possible that the most recent state was not sent and a stale version sent instead.
- Fixed reader decode type 'both' so that it works for Wiegand and clock and data reads.  (#19335)
- Portal does not update portalState in some cases when changing operational mode. (#19334)
- General clean up for trace message levels
- Added set person state command to controller object for working with anti passback areas. (#19257)
- Added new state to controller for battery health state (#19242)
- Added support for holiday periods in permission gates (#19241)
- Added disable mode for TimeTable entities (#19215)

### 3.2.21863 (FW >= 1.0.3) - Released 21/11/2018

- Fixed issue where incorrect pin would result in a 'string format' error (#19203)
- Implemented Holiday record type and permission gate (#19204) [link](Entities/CommonHoliday.md)
- Server can now choose unique Blade IDs (#19200) [link](Entities/HardwareBlade.md)
- Minor version number changed.
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
- Changed actions to trigger off a state's state if the 'event' name matches a state.
- Added new compact timeTable format.​
- Outputs now support Command Stack as well as absolute On/Off modes of operation.​
- Controller main LED now flashes green when heartbeats successfully sent to server, reverts to yellow if connection fails.​
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

### 4.4.27032 (FW Minimum >= 1.1.4)

- [Feature] Added RejectedFormat to read event (#19702) [link](Entities/HardwareReader.md#read)
- [Feature] Now have TamperMode and Tamper property, allows selecting between Optical and mechanical tamper (#19465) [link](Entities/HardwareController.md#tampermode)
- [Feature] CPU temperature checked and reported every 10 minutes (#19858)
- [Feature] If OSDP device is online, only the `SetDeviceKey` is performed. (#20020)
- [Feature] Implemented Non-destructive read on sever queue. (#20015)
- [Feature] Time Table Adjustment only done when the seconds are zero. (#20021)
- [Feature] Added `ToggleMode` option to Operation Mode command set for all entities (#20167) [link](ApplicationConfiguration/ModeOverview.md#changemode)
- [Bug] Reconcile no longer blocks access (#20017)
- [Bug] Updated Timezone files (#20054)
- [Bug] Support TLS 1.2 in OEM-1.2.0 firmware (#20114)
