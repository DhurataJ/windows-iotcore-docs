---
title: Using PowerShell for Windows IoT
author: paulmon
ms.author: riameser
ms.date: 08/28/2017
ms.topic: article
ms.prod: windows-iot
ms.technology: iot
description: Learn how to use PowerShell to connect to your device as well as manage your device.
keywords: windows iot, PowerShell, Windows PowerShell, command line, command-line shell
---

# Using PowerShell for Windows IoT
> [!NOTE]
> Add the [open source powershell](https://github.com/PowerShell/PowerShell/releases) version using [Import-PSCoreRelease (importps)](https://github.com/ms-iot/iot-adk-addonkit/blob/master/Tools/IoTCoreImaging/Docs/Import-PSCoreRelease.md#Import-PSCoreRelease). You will still require IOT_POWERSHELL feature to include WinRM binaries

Remotely configure and manage any Windows 10 IoT Core device by using Windows PowerShell.
PowerShell is a task-based command-line shell and scripting language, designed especially for system administration.

Make sure to follow these steps to correctly configure your device running Windows 10 IoT Core to work well with Visual Studio 2017.

## Initiating a PowerShell session
1. To start a PowerShell session with your Windows 10 IoT Core device, you'll first need to create a trust relationship between your host PC and your device. After starting your Windows IoT Core device, an IP address will be shown on the screen attached to the device.

    ![DefaultApp on Windows 10 IoT Core](../media/PowerShell/DefaultApp.png)

   You can find the same information on the Windows 10 IoT Core Dashboard.

2. Open an administrator PowerShell console on your local PC. Type **powershell** in the **Search the web and Windows** box near the Windows Start menu. Windows will find PowerShell on your PC.

    ![Find PowerShell](../media/PowerShell/start-ps.png)

3. To start PowerShell as an administrator, right-click **Windows PowerShell**, and then select **Run as administrator**.

    ![Run PowerShell as administrator](../media/PowerShell/start-ps2.png)

   Now you should see the PowerShell console.

    ![PS console](../media/PowerShell/ps.PNG)

4. You may need to start the WinRM service on your desktop to enable remote connections. To do so, from the PowerShell console, type the following command:
```
        net start WinRM
```
5. From the PowerShell console, type the following, substituting `<machine-name or IP address>` with the appropriate value (using your **machine-name** is the easiest, but if your device is not uniquely named on your network, try the IP address):
```
        Set-Item WSMan:\localhost\Client\TrustedHosts -Value <machine-name or IP Address>
```
6. Enter `Y` to confirm the change.
```
        Set-Item WSMan:\localhost\Client\TrustedHosts -Value "<machine1-name or IP Address>,<machine2-name or IP Address>"
```
> [!NOTE]
> If you want to connect multiple devices, you can use commas and quotation marks to separate each device.

7. Now you can start a session with your Windows IoT Core device. From your administrator PowerShell console, type:
```
        Enter-PSSession -ComputerName <machine-name or IP Address> -Credential <machine-name or IP Address or localhost>\Administrator
```
8. In the credential dialog, enter the following default password: `p@ssw0rd`

    <div class="alert alert-note">
      <h5><span class="win-icon win-icon-Page"></span>
        NOTE
      </h5>
      <p>The connection process is not immediate and can take up to 30 seconds.</p>
    </div>    

    If you successfully connected to the device, you should see the IP address of your device before the prompt.

    ![PowerShell console](../media/PowerShell/ps_device.png)

9. Update your account password. We *highly recommend* that you update the default password for the Administrator account. To do this, issue the following commands in your PowerShell connection:

	a. Replace `[new password]` with a strong password:
```
	        net user Administrator [new password]
```
b. Next, establish a new PowerShell session using `Exit-PSSession` and `Enter-PSSession` with the new credentials.
```
	        Exit-PSSession

	        Enter-PSSession -ComputerName <machine-name or IP Address> -Credential <machine-name or IP Address or localhost>\Administrator
```
## Commonly used PowerShell commands

### Troubleshooting with Visual Studio Remote Debugger

To be able to deploy applications from Visual Studio 2017, you will need to make sure that the Visual Studio Remote Debugger is running on your Windows IoT Core device. The remote debugger should open automatically when you start your computer. To double check, use the `tlist` command to list all the running processes from PowerShell. There should be two instances of msvsmon.exe running on the device.

It is possible for the Visual Studio Remote Debugger to time out after long periods of inactivity. If Visual Studio cannot connect to your Windows IoT Core device, try restarting the device.

### Configure your Windows IoT Core device

If you want, you can rename your device.

1. To change the computer name, use the `setcomputername` utility:
```
        setcomputername <new-name>
```
2. Restart the device for the change to take effect. You can use the `shutdown` command as follows:
```
        shutdown /r /t 0
```
3. Because the computer name was changed, after you restart you will need to rerun this command to connect to your device using the new name:
```
        Set-Item WSMan:\localhost\Client\TrustedHosts -Value <new-name>
```
Your Windows IoT Core device should now be properly configured and ready to use!

### Commonly used utilities

For a list of commands and utilities that you can use with PowerShell, see the [Command Line Utils](../manage-your-device/CommandLineUtils.md) page.

## Known issues and workarounds

**ISSUE**: A known bug in PowerShell security policies causes the following issues to manifest within the remote session:
* Get-Help returns unexpected matches.
* Get-Command on a specified module returns an empty command list.
* Running a cmdlet from any of these modules throws CommandNotFoundException: Appx, NetAdapter, NetSecurity, NetTCPIP, PnpDevice.
* Import-Module on any of the above modules throws PSSecurityException exception with UnauthorizedAccess. Module auto loading does not seem to work either.

**Workaround**: Modify the execution policy within the remote PowerShell session to **RemoteSigned**. For more information on the different execution policies, see [Using the Set-ExecutionPolicy Cmdlet](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)).

**ISSUE**: Cmdlets from some modules such as NetAdapter are sometimes not visible. For example, Get-Module NetAdapter returns an empty list.

**Workaround**: Use the -Force parameter with Import-Module. For example, `Import-Module NetAdapter -Force`.

**ISSUE**: Setting execution policy to "AllSigned" breaks PowerShell remoting. Subsequent attempts to create a remote session fail with a SecurityException loading Typesv3.ps1xml.

**Workaround**: Use winrs.exe to restore PowerShell's execution policy:
* Change console code page `Chcp 65001`
* Log on to a remote cmd.exe shell `Winrs.exe -r:<target> -u:<username> -p:<password> cmd.exe`
* Within remote cmd.exe, modify the appropriate registry key `reg add HKLM\Software\Microsoft\PowerShell\1\ShellIds\Microsoft.PowerShell /v ExecutionPolicy /d RemoteSigned /f`
* Exit remote cmd.exe session `exit`

### Other known issues

- In PowerShell scripts, attributes to PowerShell class or enumeration do not work. Adding attributed results in the following exception thrown: *Type must be a runtime Type object*.

- Outbound CIM and PowerShell remoting are not supported. Relevant functionality in relying cmdlets will not work. These include  Enter-PSSession, Get-Job, Receive-Job, Import-Module, Invoke-Command, and Copy-Item.

- SecureString commands ConvertFrom-SecureString and ConvertTo-SecureString do not work unless the session is created using CredSSP authentication. Otherwise, the -Key parameter must be specified. For details on configuring CredSSP authentication, see [Enable PowerShell “Second-Hop” Functionality with CredSSP](https://devblogs.microsoft.com/scripting/enable-powershell-second-hop-functionality-with-credssp/).