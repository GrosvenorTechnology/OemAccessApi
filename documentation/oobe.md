# Out Of Box Experience

When a controller is powered on for the first time, or booted after a factory reset the out of box experience will run.  The purpose of this application is to get the controller configured with the correct network settings, update the firmware and application to the versions specified in the platform config.  The controller will then reboot into the OemAccess application.

## Device Information Page

If you browse to the IP address of the controller, which you can find using our [Advance Discovery Tool](https://aka.gtl.biz/AdvanceBrowser) or looking the device up in your local DHCP server, you will get a summary of the controllers network and software & hardware versions.

### Changing the device IP via Web page

It is possible to change the controllers IP address via a web browser before it is configured, there is a link off the devices home page or you can browse directly to <http://---deviceip---/interactive/network>

## Configuring a controller

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

## OOBE API

The controller also has a HTTP API that can be used to configure the controller when it's in it's default state

### SystemIno

> GET /configuration/systeminfo

This request will return a JSON payload that contains the system information of the controller, an example response is:

```json
{
  "controller": {
    "platform": "Unix",
    "type": "ADV10",
    "firmwareVersion": "1.1.2+release",
    "firmwareBuild": "190405",
    "partNumber": "JC4-ADV-C2-SNG~00001702",
    "hardwareVersion": "1v4",
    "systemStatus": "New"
  },
  "application": {
    "name": "Itac.OemAccess.Oobe",
    "version": "4.0.23246+release"
  },
  "network": {
    "dnsName": "JC4-ADV-C2-SNG_00001702",
    "ipAddresses": [
      "172.16.66.14"
    ],
    "allIpAddresses": [
      "172.16.66.14",
      "fe80::201:ceff:fe01:a759%2"
    ],
    "macAddress": "00:01:ce:01:a7:59"
  }
}
```

### Enable SSH

> POST /configuration/enablessh

Sending a POST to this endpoint will enable SSH on the controller, no body is required.  The request will respond with a 202-Accepted response, the SSH will be enabled within 10 seconds of the request.

### Set Network Configuration

> POST /configuration/enablessh

To configure the IP of the controller, send a post to this endpoint, the body of the request should contain a boot configuration file, with only the [network section](../BootConfiguration/Overview.md#network), all other sections of the file will be ignored.

For example to set the controller onto a static IP address, send the following as the request body:

```json
{
    "boot": {
        "network": {
            "eth0": {
                "address": "192.168.43.101",
                "netmask": "255.255.255.0",
                "gateway": "192.168.43.254"
            }
        }
    }
}
```

### Upload Boot Configuration file

> POST /configuration/bootconfig

This function allows you to post a boot configuration file to the controller and start the provisioning process for the controller as described above.

The controller uses HTTP etags to detect changes to files requested from the server, it is suggested that if your server is using etags, that you supply the etag for the file via the `x-gtl-bootetag` header.  This will prevent the controller thinking the bootconfig file has changed after the first reboot into the OemAccess app, which will cause a second unnecessary restart.

## Discovery Protocol

While the device is running in out of box mode, it will listen for discovery requests on the IPv6 UDP multicast group FF:02::1 group on port 37757.  The controller will respond to requests with a UDP packet to the sender on port 2000 with a JSON payload that describes the controller.

The discovery protocol is designed to work over IPv6 link local addresses, so discovery is possible in the absence of any network infrastructure such as DHCP.

Shown below is some sample C# code to implement a discovery client for the Advance controllers, all error handling has been omitted for clarity.

The first step is to get a list of all active network adapters, the multicast must be bound to each adapter individually.

```c#
private IEnumerable<NetworkInterface> GetActiveNetworkInterfaces()
{
    return NetworkInterface.GetAllNetworkInterfaces()
        .Where(x =>
            x.SupportsMulticast && x.OperationalStatus == OperationalStatus.Up &&
            x.GetIPProperties().MulticastAddresses.Any())
        .ToList();
}
```

Then we can join each adapter into the multicast group

```c#
private void CreateBroadcasters()
{
    var nics = GetActiveNetworkInterfaces();

    foreach (var adapter in nics)
    {
        var prop = adapter.GetIPProperties();
        if (adapter.Supports(NetworkInterfaceComponent.IPv6))
        {
            var ip = prop.UnicastAddresses.FirstOrDefault(x => x.Address.AddressFamily == AddressFamily.InterNetworkV6 && x.Address.IsIPv6LinkLocal);
            if (ip != null)
            {
                var udpClient = new UdpClient(AddressFamily.InterNetworkV6);
                udpClient.Client.Bind(new IPEndPoint(ip.Address, 2000));
                var grpAddr = IPAddress.Parse("FF02::1");
                var ipv6MulticastOption = new IPv6MulticastOption(grpAddr);
                var group = ipv6MulticastOption.Group;
                udpClient.JoinMulticastGroup(group);
                _udpClients.Add(udpClient);
            }
        }
    }
}
```

Then for each UdpClient we have created above, we can attach a receive loop to listen to responses

```c#
private void CreateReceivers()
{
    foreach (var udpClient in _udpClients)
    {
        ReceiveLoop(udpClient);
    }
}

private async void ReceiveLoop(UdpClient udpClient)
{
    _logger.Trace($"Listening on adapter: {udpClient.Client.LocalEndPoint}");

    while (!_cancellationToken.IsCancellationRequested)
    {
        var reply = await udpClient.ReceiveAsync();
        var data = Encoding.UTF8.GetString(reply.Buffer);
        Device device;

        if (data.StartsWith("<details>") && data.EndsWith("</details>"))
        {
            device = CreateDeviceFromDetails(data, reply.RemoteEndPoint);
            //Do something with device data.
        }
    }
}
```

And finally we must send the hello request to the devices

```c#
private void SendPings()
{
    var grpAddr = IPAddress.Parse("FF02::1");
    var oemEndPoint = new IPEndPoint(grpAddr, 37757);
    var data = Encoding.UTF8.GetBytes("tell_me_something_about_yourself");

    foreach (var udpClient in _udpClients)
    {
        udpClient.Send(data, data.Length, oemEndPoint);
    }
}
```

The controllers will respond with a JSON payload (UTF8) as below

```json
{
  "controller": {
    "platform": "Unix",
    "type": "ADV10",
    "firmwareVersion": "1.1.2+release",
    "firmwareBuild": "190405",
    "partNumber": "JC4-ADV-C-SNG~00000057",
    "hardwareVersion": "1v2",
    "systemStatus": "New"
  },
  "application": {
    "name": "Itac.OemAccess.Oobe",
    "version": "4.0.23246+release"
  },
  "network": {
    "dnsName": "JC4-ADV-C-SNG_00000057",
    "ipAddresses": [
      "172.16.66.101"
    ],
    "allIpAddresses": [
      "172.16.66.101",
      "fe80::201:ceff:fe01:6a46%2"
    ],
    "macAddress": "00:01:ce:01:6a:46"
  }
}
```
