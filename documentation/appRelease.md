# Application Change log

Applications are release through two channels, the Stable channel and the beta channel.  The urls for each channel are:

- Stable : https://firmware.customexchange.net/adv
- Beta : https://firmware.customexchange.net/beta

The beta channel only contains the latest released firmware, and applications published to this channel may be removed at any time.

The Stable channel will contain all firmware versions as well as every application version published, no application will be removed from this channel

## Stable Channel Releases

## 5.2.10.0 (FW >= 2.2.0) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-5.2.10.0.zip)
 
 - [Enhancement] Support custom unknown token request timeout (max 60 seconds).
 - [Enhancement] Support custom unknown token user wait timeout (the time before the user access request invokes the fallback action).
 - [Enhancement] Support custom timeout for server permission request (max 60 seconds).
 - [Bug] Fix Inavid Value (value too big for unsigned int) logged when application starting.

## 5.1.0.55874 (FW >= 2.0.0) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-5.1.0.55874.zip)
 
 - [Feature] When builing bit  patterns for SmartIntego Whitelist, will now use multiple sequences and scope/multipliers.


## 5.0.46478 (FW >= 2.0.0) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-5.0.46478.zip)

 - [Feature] Support for v2 Door & OSDP blades (OEM-ADV-BL2-2D & OEM-ADV-OSDP2-2D)
 - [Feature] Support for GtConnect services
 - [Feature] Push configuration via GtConnect rather tha boot.config file
 - [Feature] Configuration upload support via GtConnect
 - [Feature] Device metrics now sent via GtConnect (CPU/Memory/Power/etc.)
 - [Enhancement] All frameworks and third party libaries updated to latest versions
 - [Bug] Logger changed from using LOG_DAEMON to LOG_USER
 

## 4.6.31192 (FW >= 1.2.1) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.6.30851.zip)

 - [Feature] The reader tamper input on Hardware.Reader and now be configured as normally open or closed (#21675) [link](Entities/HardwareReader.md#ReaderTamperNormallyOpen)
 - [Feature] New operational modes added to reader 'isolated'.  This has been added primarily to disable OSDP readers without taking them offline. (#21924) [link1](Entities/OsdpReader.md#modes-of-operation) [link2](Entities/HardwareReader.md#modes-of-operation)
 - [Feature] Transfer reader and token states via reader interface.  New options are 'Denied By Reader', 'Low Battery' and 'Reader Tamper'  (#21546) [link](ApplicationConfiguration/TokenFormatDefinition.md#xml-document-definition)
 - [Feature] Added PortalSenseErrorState to portal. This allows to wach for error states at the portal, without the traffic overhead of open/close states (#20583) [link](Entities/AccessControlPortal.md#portalsenseerrorstate)
 - [Feature] Extended AccessGranted/Denied events to include a reason for the event, i.e. ServerDecision. (#21463) [link](Entities/AccessControlPortal.md#portalsenseerrorstate)
 - [Feature] Added new reader mode **TokenOnlyViaKeypad**.  These is only recommended for low security areas as the token number is generally isn't secret.  **TokenAndPinViaKeypad** is a safer option where practical. (#21464) [link](Entities/HardwareReader.md#modes-of-operation)
 - [Feature] Added new application to SDK to allow stand alone verification of config file (#21417)
 - [Enhancement] Hardware IDs for SimonsVoss devices increased form 4 to 8 chars (#21414)
 - [Bug] A significant change of time on the controller would trigger the watchdog to reboot the controller. (#21432)
 - [Bug] Sending a configuration file to controller with more than 8 readers per OSDP port would crash the blade requiring a controller restart. (#21888)
 - [Bug] Command Response messages do not include the device ID when used with a RS485 Line Header (#22047)
 - [Bug] Unable to control SSH via local actions (#20551)
 - [Bug] Local actions do not process the reset state of the configuration button correctly (#20547)
 - [Bug] Local actions do not process Area movement events correctly (#20545)
 - [Bug] Fixed application crash when overlapping reader Read events happen while requesting user record from server (#22647)

## 4.5.31191 (FW >= 1.1.2 Recommended 1.2.1)
 - [Bug] Fixed application crash when overlapping reader Read events happen while requesting user record from server (#22647)

## 4.5.30614 (FW >= 1.1.2 Recommended 1.2.1)
 - [Feature] The max OSDP reader limit per port was increased from 4 to 8.
 - [Bug] The max reader limit was incorrectly set to 16 instead of 36.
 - [Bug] Improved error handling around detection of offline event target detection.  This caused events to buffer on the controller. 
 - Note: No functional change compared to 4.5.30605, just rebuilt with release tagging.

## 4.5.29023 (FW >= 1.1.2 Recommended 1.2.1)
 - [Diagnostics] Extra logging added to reconcile messages.  IDs of messages deleted or missing are now logged to help in trouble shooting issues.
 - [Bug] The validation logic was wrong for the Portal fields NormalOpenTooLongPeriod & ExtendedOpenTooLongPeriod.  This was set to 10 minutes; for use cases such as roller shutter doors in loading areas this was to short.  Both have now been extended to 2 hours.
 - [Bug] Several inputs types would not correctly clear their error states when disabled.  For example, the portal egress switch can be set to 'Supervised'.  If this was done by mistake the switch would signal an error state.  When the switch was then returned to a disabled state, the error state would remain until the controller is restarted.  Affected states are:
   - Portal : LockDetectionState
   - Portal : EgressSwitchState
   - Portal : BreakglassState
   - Input (door & IO blades) : InputState
   - Reader : ReaderTamperState
 - [Bug] The performance of the event persistence system has been massively improved.  We noticed that as the number of events persisted to flash increased the time required to store and read an event would increase.  This is now resolved so that it is constant regardless of the number of events stored, and the controller should be able to send the events to the server at the same velocity as online events.
  

## 4.5.28895 (FW >= 1.1.2)
 - [Bug] In some cases, settings from application configuration were applied incorrectly (#20410)
 - Reduced the amount of network activity logged by default, can still be shown using the [verboseNetworkLogging](https://github.com/GrosvenorTechnology/OemAccessApi/blob/master/documentation/PlatformConfiguration/Overview.md#verbosenetworklogging-41) setting in the platform config file.

## 4.5.28894 (FW >= 1.1.2)
 - [Bug] Portals may fail to unlock when using UnlockOnMode (#20410)
 - [Bug] Actions sometimes skip a target when multiple targets are specified (#20410)

## 4.5.28794 (FW >= 1.1.2) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.5.28794.zip)

 - [Feature] Support for 1.2.0 Firmware
 - [Feature] Support for TLS 1.2 when running on 1.2.0 or later firmware (#19591)
 - [Feature] Batch Sending of States and Events (#20016)
 - [Feature] Added more content to PortalUsed, PortalEntryUsed, PortalExitUsed events about event source (#20382) 
 - [Feature] Controller history persisted to flash when controller is offline (#19636)
 - [Feature] Added new AuxOutputType property to allow the output to be defined as normal activeHigh/activeLow 
 - [Bug] Fixed bad casing of Enable/DisableSSH commands in Metadata 

### 4.4.27819 (FW Minimum >= 1.1.2) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.4.27819.zip)

- [Compatibility] Reverted minimum supported firmware version to 1.1.2
- [Bug] Fix for incorrect PSK used to sign Gateway HTTP requests (#20251)
- [Bug] Corrected validation values used for Blade V12 Current limits, raised from 2000 to 8000mA (#20256)

### 4.4.27675 (FW Minimum >= 1.1.4) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.4.27675.zip)

- [Feature] Added RejectedFormat to read event (#19702) [link](Entities/HardwareReader.md#read)
- [Feature] Now have TamperMode and Tamper property, allows selecting between Optical and mechanical tamper (#19465) [link](Entities/HardwareController.md#tampermode)
- [Feature] CPU temperature checked and reported every 10 minutes (#19858)
- [Feature] If OSDP device is online, only the `SetDeviceKey` is performed. (#20020)
- [Feature] Implemented Non-destructive read on sever queue. (#20015)
- [Feature] Time Table Adjustment only done when the seconds are zero. (#20021)
- [Feature] Added `ToggleMode` option to Operation Mode command set for all entities (#20167) [link](ApplicationConfiguration/ModeOverview.md#changemode)
- [Feature] OSDP Readers are now taken off poll when disabled (#20243)
- [Bug] OSDP readers reported online when blade has OSDP disabled (#20224)
- [Bug] Unable to set lock maximum voltage above 15V (#20226)
- [Bug] Software updates now delayed for 5 minutes after controller start to improve stability
- [Bug] Reliability of OSDP setup commands improved
- [Bug] System update commands improved to avoid triggering watchdog on slow networks
- [Bug] OSDP readers do not show correct portal state if portal is unlocked after restart
- [Bug] Reconcile no longer blocks access (#20017)
- [Bug] Updated Timezone files (#20054)
- [Bug] Support TLS 1.2 in OEM-1.2.0 firmware (#20114)

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

## 4.6.31192 (FW >= 1.2.1) 

 - [Bug] Fixed application crash when overlapping reader Read events happen while requesting user record from server (#22647)

## 4.6.30851 (FW >= 1.2.1) [SDK Download](https://firmware.customexchange.net/oemsdk/OemAccess-SDK-4.6.30851.zip)

 - [Feature] The reader tamper input on Hardware.Reader and now be configured as normally open or closed (#21675) [link](Entities/HardwareReader.md#ReaderTamperNormallyOpen)
 - [Feature] New operational modes added to reader 'isolated'.  This has been added primarily to disable OSDP readers without taking them offline. (#21924) [link1](Entities/OsdpReader.md#modes-of-operation) [link2](Entities/HardwareReader.md#modes-of-operation)
 - [Feature] Transfer reader and token states via reader interface.  New options are 'Denied By Reader', 'Low Battery' and 'Reader Tamper'  (#21546) [link](ApplicationConfiguration/TokenFormatDefinition.md#xml-document-definition)
 - [Feature] Added PortalSenseErrorState to portal. This allows to wach for error states at the portal, without the traffic overhead of open/close states (#20583) [link](Entities/AccessControlPortal.md#portalsenseerrorstate)
 - [Feature] Extended AccessGranted/Denied events to include a reason for the event, i.e. ServerDecision. (#21463) [link](Entities/AccessControlPortal.md#portalsenseerrorstate)
 - [Feature] Added new reader mode **TokenOnlyViaKeypad**.  These is only recommended for low security areas as the token number is generally isn't secret.  **TokenAndPinViaKeypad** is a safer option where practical. (#21464) [link](Entities/HardwareReader.md#modes-of-operation)
 - [Feature] Added new application to SDK to allow stand alone verification of config file (#21417)
 - [Enhancement] Hardware IDs for SimonsVoss devices increased form 4 to 8 chars (#21414)
 - [Bug] A significant change of time on the controller would trigger the watchdog to reboot the controller. (#21432)
 - [Bug] Sending a configuration file to controller with more than 8 readers per OSDP port would crash the blade requiring a controller restart. (#21888)
 - [Bug] Command Response messages do not include the device ID when used with a RS485 Line Header (#22047)
 - [Bug] Unable to control SSH via local actions (#20551)
 - [Bug] Local actions do not process the reset state of the configuration button correctly (#20547)
 - [Bug] Local actions do not process Area movement events correctly (#20545)
    