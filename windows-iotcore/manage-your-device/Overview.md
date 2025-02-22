---
title: Debugging overview
ms.date: 05/10/2019
ms.topic: article
ms.prod: windows-iot
ms.technology: iot
description: Learn about the different ways that you can debug Windows 10 IoT Core.
keywords: windows iot, debugging, Debug, PowerShell, SSH
---

# Debugging on Windows IoT Core
Once you have your IoT Core image setup with running application, it is important that you can debug the application, or the system as needed. The best time to debug and test the system is while the test image state. Once IoT Core based systems are out in the wild, debugging can become challenging. That is not to say it cannot be done, but with additional layers of difficulties added to debug, compared to a testing phase. You can use the following to debug your application or image while in test mode:

## Device Portal
Windows Device Portal (WDP) allows for you to configure and manage your IoT Device remotely over local network. WDP can be reachable via local IP of the IoT Device. Additional information on WDP on IoT can be found [here](./deviceportal.md).

### Collecting ETW / WPP Logs
-----

### File Sharing
The App File Explorer can allow access to the directories that your apps can access
*vCameraRoll is shared among all apps
* Documents are shared among all apps
* LocalAppData contains folders specific to each app. This folder will be the same name as your app and other apps cannot access it.
See above link for additional information.

### Kernel Debug
You can download live Kernel dumps via WDP as well. Any system crashes will automatically be logged and available for download. See above link for additional information.

### Enable Crash Dump
You can download crash dumps of applications on IoT Device via WDP. See above link for additional information.

## SSH/PowerShell/TShell
PowerShell is a task-based command-line shell and scripting language, designed especially for system administration. Details on debugging and setting up PowerShell can be found [here](../connect-your-device/powershell.md).

## Debug through Visual Studio Deployment
Deploying and debugging your application is straightforward with Visual Studio. Remote Debugging feature can be used to deploy and debug the app to your locally connected Windows 10 IoT Core device. Details on deployment and debugging can be found [here](../develop-your-app/RemoteDebugging.md).

-----
## Live App Debug
In Visual Studio (2015 and later), you can analyze performance and diagnose issues in your ASP.NET web app both in debugging and in production, using telemetry from Azure Application Insights. The feature is later extended to include desktop and UWP applications in Visual Studio 2017 and via Azure portal. Additional information on debugging your project can be found [here](/azure/azure-monitor/app/visual-studio) and monitoring usage, and performance in desktop or UWP applications can be found [here](/azure/azure-monitor/app/windows-desktop).