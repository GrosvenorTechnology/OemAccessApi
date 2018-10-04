# Out Of Box Experience

When a controller is powered on for the first time, or booted after a factory reset the out of box experience will run.  The purpose of this application is to get the controller configured with the correct network settings, update the firmware and application to the versions specified in the platform config.  The controller will then reboot into the OemAccess application.

## Sequence of events

After power on the controllers LED will go through the following sequence

- Solid Blue - Boot-loader running
- Fast flash blue - OS booting and application starting
- Rainbow - The led wil cycle through red, yellow, green, blue, magenta, white to show the OOBE app is running.
- Slow flash white - Loading boot config, will check the usb drive for `bootconfig.json` or `{deviceSerial}.bootconfig.json` (with the ~ replaced with _) i.e. `SAT-ADV-C-MLT_00001234.bootconfig.json`.  This allows multiple config files to be prepared in advance.
- Slow flash blue - Updating network settings (this step is normally fast, blink and you'll miss it).
- Slow flash yellow - Download platform config based on settings in boot config file.
- Slow flash magenta - The application and firmware are updated based on settings in platform config file.  This step can take a while if the firmware has to be updated.
- Solid green for 10 seconds then 10 quick green flashes - Configuration complete and the controller will now reboot into the actual application.

## Errors

If at any point after the rainbow pattern something goes wrong, the current phase will go from slow flash to quick flash.  The failed action will be retried after 60 seconds.

For example, if the boot config file can not be found either on the controller or the USB memory stick the LED will flash fast white.

If an error occurs that can not be retried or suggests that something is wrong with the controller the LED will flash fast red for 10 seconds then the controller will reboot.
