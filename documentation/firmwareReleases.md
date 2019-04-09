# Firmware Changelog

## v1.1.2 **CRITICAL**
- Update OemOobe-app to 4.0.23246
   - Adds support for setting device IP address via web interface
   - Web server will rebind to new ip addresses if ethernet connected after controller boot
   - Date is forced to 2019-01-01 if before this date
- Changes to device power management
  - Increased current available to RAM to fix stability issues under high load on single blade controllers
  - Disable unused charger and configure 4P2 as only input for DC-DC (multi only)
- Changes to Ethernet clock initialization improves startup reliability
- Added logStream app, allows Syslog messages to be forwarded to Custom Exchange
- Check for non-zero RTC calibration register
- Increase boot loader version to force update, necessary for detecting new RAM size

## v1.0.8
- Updated OemOobe-app to 3.3.22583 - Improves setup times when bootconfig posted to device over HTTP

## v1.0.7

- Update OemOobe-app to 3.0.21739.

## v1.0.6

- Detect PCB revision 1V4 (single) and 1V5 (multi) (#687).
- Reset USB controllers in Linux kernel if left running from boot ROM (#686).
- Prompt tester to hold RESET button for 3 seconds during production testing (#686).
- Lowered 5AMP current thresholds for production testsi (#688).

## v1.0.5

- Adjust production test thresholds for newer low current USB sticks.

## v1.0.4

- Update OemOobe-app to 2.0.21391.

## v1.0.3

- Update OemOobe-app to 2.0.21323.

## v1.0.2

- Allow for new part numbers (OEM-ADV-C2-MLT, OEM-ADV-C2-SNG).

## v1.0.1

- Update OemOobe-app from 2.0.19900 to 2.0.20447.

## v1.0.0

- Based on Sateon Advance v1.2.0
- Disable logging of statistics in thttpd in dev-build.
- Add driver for reading PCB revision.
- Add root certificate store.
- Enable certificate checking in updateit.
- Update OpenSSL version from 1.0.2h to 1.0.2o.
- Update Mono to version 5.12.0.226 (and Monolite 1051500007).
- Remove FTP daemon from dev-build.
- Next application start is delayed if application runs for less than 30s.
- New OEM default application (OemOobe-app-2.0.19900+release.zip).
- Reduce connman NTP logging.