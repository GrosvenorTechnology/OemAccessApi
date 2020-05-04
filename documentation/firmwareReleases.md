# Firmware Changelog

## v1.2.0 (Minimum supported application version v4.4, recommended v4.5)

 - Mount filesystem in sync-mode to minimise file system corruption if power fails.
 - Synchronise filesystem after application installation.
 - Enable USB FTDI serial driver support in Linux kernel.
 - Fix PID-file creation of log stream service (#18).
 - Update Linux (from 4.1 to 4.14) and U-Boot (from 2009.08 to 2016.07).
 - Update packages
   - busybox from 1.25.0 to 1.30.1,
   - connman 1.33 to 1.36,
   - dropbear from 2016.74 to 2019.78,
   - libcurl from 7.50.1 to 7.64.1,
   - openssl from 1.0.2o to 1.0.2s,
   - zlib from 1.2.8 to 1.2.11.
   - mono from 5.12.0.226 to 6.8.0.105.	
 - Enable support for TLS 1.2 in Mono.
 - Add ntptest command line tool.
 - Add RS-485 unit test.
 - Fix RS-485 interrupt latency issue by using FIFO for higher braudrates.
 - Configure getaddrinfo to prefer IPv4 over IPv6.
 - Update OemOobe-app to version 4.4.27735.
 - Fix netcfg to properly configure NTP if set to enabled.

## V1.1.4
 - Fix Linux UART driver to disable UART loopback mode (LBE) in DE-check timer.

## v1.1.2 **CRITICAL**

- Update OemOobe-app to 4.0.23246
  - Adds support for setting device IP address via web interface
  - Web server will rebind to new ip addresses if ethernet connected after controller boot
  - Date is forced to 2019-01-01 if before this date
- Changes to device power management
  - Increased current available to RAM to fix stability issues under high load on single blade controllers
  - Disable unused charger and configure 4P2 as only input for DC-DC (multi only)
- Changes to Ethernet clock initialization improves start-up reliability
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


## Beta Channel Releases

