# Firmware Changelog

## v2.2.0 (Minimum supported application version v5.0, recommended v5.1)
 - Change GTConnect DPS protocol from `ampqs` to `https`, `amqps` used as fallback.
 - Add setting `it_gtconnect_iot_protocol` for GtConnect protocol selection. Possible protocols are `amqps` and `amqps-ws`.
 - Add settings for HTTP proxy (`proxy_host`, `proxy_port`, `proxy_username`, `proxy_password`)
 - Add setting `it_system_reset` for triggering factory reset.
 - Reboot terminal earlier on low memory condition via watchdog (10% of total memory).
 - Re-charge single controller battery only when below 80% (3.75V) when fully charged.
 - Enforce the date to be at least the year of the build date during boot-up.
 - Collect and send crash reports to GTConnect.
 - Collect and send terminal telemetry data to GTConnect.
 - Minor GTConnect enhancements:
   - Re-enrol after continuous failures to connect to IoT hub.
   - Handle minimum firmware requirement during enrollment.
   - Reply to `ping` direct command.
   - Supporting new log bundling setting `maxTimeToWait` in seconds to replace `maxTimeToWaitForEntry` (ms).
   - Report boot time in GtConnect properties.
   - Send events about USB and battery property changes.
   - Check for existence of `tblSettings` before accessing application configuration
   - Use correct HTTP `Accept` and `Content-Type` header for JSON files.
 - Fix issue where GTConnect device status updates could arrive out of order.
 - Fix issue in GTConnect timeout handling when waiting for log entries.
 - Fix HTTP header in `updateit` and remove cache control properties.
 - Update Openssl to v3.1.5
 - Update Curl to v8.6.0.
 - Update Connman to v1.42.
 - Update Dropbear to 2022.83.
 - UPdate Azure IoT SDK to `LTS_03_2024`.
 - Update firmware CAs:
   - Removed CAs:
     - Cybertrust, Inc - Cybertrust Global Root
     - Agencia Catalana de Certificacio (NIF Q-0801176-I) - EC-ACC
     - GlobalSign - GlobalSign
     - Hellenic Academic and Research Institutions Cert. Authority - Hellenic Academic and Research Institutions RootCA 2011
     - Network Solutions L.L.C. - Network Solutions Certificate Authority
     - Staat der Nederlanden - Staat der Nederlanden EV Root CA
     - TrustCor Systems S. de R.L. - TrustCor ECA-1
     - TrustCor Systems S. de R.L. - TrustCor RootCert CA-1
     - TrustCor Systems S. de R.L. - TrustCor RootCert CA-2
   - New CAs:
     - Autoridad de Certificacion Firmaprofesional CIF A62634068
     - Certainly - Certainly Root E1
     - Certainly - Certainly Root R1
     - D-Trust GmbH - D-TRUST BR Root CA 1 2020
     - D-Trust GmbH - D-TRUST EV Root CA 1 2020
     - DigiCert, Inc. - DigiCert TLS ECC P384 Root G5
     - DigiCert, Inc. - DigiCert TLS RSA4096 Root G5
     - E-Tugra EBG A.S. - E-Tugra Global Root CA ECC v3
     - E-Tugra EBG A.S. - E-Tugra Global Root CA RSA v3
     - Hellenic Academic and Research Institutions CA - HARICA TLS ECC Root CA 2021
     - Hellenic Academic and Research Institutions CA - HARICA TLS RSA Root CA 2021
     - Chunghwa Telecom Co., Ltd. - HiPKI Root CA - G1
     - Internet Security Research Group - ISRG Root X2
     - SECOM Trust Systems CO.,LTD. - Security Communication ECC RootCA1
     - SECOM Trust Systems CO.,LTD. - Security Communication RootCA3
     - Telia Finland Oyj - Telia Root CA v2
     - Agence Nationale de Certification Electronique - TunTrust Root CA
     - iTrusChina Co.,Ltd. - vTrus ECC Root CA
     - iTrusChina Co.,Ltd. - vTrus Root CA

## v2.1.0 (Minimum supported application version v5.0, recommended v5.1)
 - Update OemOobe-app to version 5.1.52832.
 - Log GTConnect log-parsing errors as debug instead of error.
 - Ignore log entries without content for GTConnect.

## v2.0.0 (Minimum supported application version v4.6, recommended v5.0)
 - Add GtConnect services (monitoring of logs, configuration, app restarts etc.)
 - Update OpenSSL to v1.1.1q.
 - Update Mono to 6.12.0.182.
 - Update OemOobe-app to version 5.0.46334.
   - Logging level reduced so not to log Errors or Warnings for USB check
   - Support added for SIGHUP based restarts
   - Update all framework components to latest versions.
 - Update firmware CAs:
 - Removed CA:
     - AC Camerfirma S.A. - Chambers of Commerce Root - 2008
     - GeoTrust Inc. - GeoTrust Primary Certification Authority - G2
     - AC Camerfirma S.A. - Global Chambersign Root - 2008
     - QuoVadis Limited - QuoVadis Root Certification Authority
     - Sonera - Sonera Class2 CA
     - Staat der Nederlanden - Staat der Nederlanden Root CA - G3
     - Trustis Limited - Trustis FPS Root CA
     - VeriSign, Inc. - VeriSign Universal Root Certification Authority
 - New CAs:
     - FNMT-RCM - AC RAIZ FNMT-RCM SERVIDORES SEGUROS
     - ANF Autoridad de Certificacion - ANF Secure Server Root CA
     - Asseco Data Systems S.A. - Certum EC-384 CA
     - Asseco Data Systems S.A. - Certum Trusted Root CA
     - e-commerce monitoring GmbH - GLOBALTRUST 2020
     - GlobalSign nv-sa - GlobalSign Root E46
     - GlobalSign nv-sa - GlobalSign Root R46
  - Log application output to stdout and stderr as error.
  - Remove Syslog-Forwarder, Cyrus/SASL library, QPID-proton library and related settings:
     - it_service_logging,
     - it_service_logging_url,
     - it_service_logging_debug,
     - it_service_syslog_server.
  - Remove over-current check resulting in a 12V cut-off in power driver.

## v1.3.0 (Minimum supported application version v4.4, recommended v4.6)
 - Updated setup application to make intitial setup more reliable.
 - Updated root CA store.

## v1.2.3 (Minimum supported application version v4.4, recommended v4.5)
 - Increase single controller battery temperature resolution.
 - Change die temperature threshold for starting battery charging from 70C to 80C.
 - Fix missing PCB revision labels.

## v1.2.1 (Minimum supported application version v4.4, recommended v4.5)

 - USB Network interface now sends OS descriptors when connected
   - Windows will now have more information to allow it to load the correct RNIDS driver, this will prevent the BSOD crashes seen with older firmware versions, and will also stop windows sending the wrong replies to the controller that was causing the controllers to restart when connected.
 - Fix byte alignment issue in application runtime with String comparison and BitConverter.
   - We found a bug in runtime that is used to run the app on the controller. This issue manifested its self in alignment issues when trying to read memory that was not aligned to a 32bit memory boundary.  The most common manifestation of this is that sometimes when comparing stings of specific length, the last character was ignored in the comparison.  This lead to an issue where device IDs 101 and 102 were seen as the same thing, but rdr101 and rdr102 were seen as different.  If you were using Guids as identifiers this issue would not have effected you due to the randomness in the ids. 
 - Watchdog timer now logs timeout messages as errors rather than info
   - This makes the app restarts triggered by the watch dog easier to see in the log files, mainly for use while the app is under development.
 - Out of box app has beed updated
   - This addresses the issue where a boot config file could bot be loaded from USB that contains the device serial number.

## ~~v1.2.0 (Minimum supported application version v4.4, recommended v4.5)~~

**This release is deprecated and not recommended for use due to the runtime bug resolved in 1.2.1**

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

