---
title: Azure Windows VM 中的 CMD 和 PowerShell | Microsoft Docs
description: 如何在 Azure Windows VM 中的 SAC 內使用 CMD 和 PowerShell 命令
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77167035"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows 命令 - CMD 和 PowerShell

本節包含範例命令，用以執行您在可能需要使用 SAC 來存取 Windows VM 的情況下 (例如，當您需要對 RDP 連線失敗進行疑難排解時) 常會進行的工作。

自 Windows Server 2003 起的所有 Windows 版本均已包含 SAC，但預設為停用。 SAC 須依賴 `sacdrv.sys` 核心驅動程式、`Special Administration Console Helper` 服務 (`sacsvr`) 和 `sacsess.exe` 程序。 如需詳細資訊，請參閱 [Emergency Management Services 工具和設定](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))。

SAC 可讓您透過序列埠連線至執行中的作業系統。 當您從 SAC 啟動 CMD 時，`sacsess.exe` 會在您執行中的作業系統內啟動 `cmd.exe`。 如果您在透過序列主控台功能連線至 SAC 的同時透過 RDP 連線至 VM，您可以在工作管理員中看到此情形。 您透過 SAC 存取的 CMD，即為您透過 RDP 進行連線時所使用的相同 `cmd.exe`。 可用的命令和工具完全相同，包括從該 CMD 執行個體啟動 PowerShell 的能力。 這是 SAC 與 Windows 修復環境 (WinRE) 之間的主要差異：SAC 可讓您管理執行中的作業系統，而 WinRE 則會開機並進入不同的最低作業系統。 雖然 Azure VM 不支援存取 WinRE 的功能，但在使用序列主控台功能時，可以透過 SAC 來管理 Azure VM。

由於 SAC 限用 80x24 的螢幕緩衝區，且無法往回捲動，請將 `| more` 新增至命令，以逐頁顯示輸出。 您可以使用 `<spacebar>` 檢視下一頁，或使用 `<enter>` 檢視下一行。

`SHIFT+INSERT` 是序列主控台視窗的貼上快速鍵。

由於 SAC 的螢幕緩衝區有所限制，因此，若命令較長，先在本機文字編輯器中輸入，然後再貼到 SAC 中，可能會較容易處理。

## <a name="view-and-edit-windows-registry-settings"></a>檢視和編輯 Windows 登錄設定
### <a name="verify-rdp-is-enabled"></a>確認 RDP 已啟用
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

只有已設定相關的群組原則設定時，才會有第二個機碼存在 (在 \Policies 下)。

### <a name="enable-rdp"></a>啟用 RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

只有已設定相關的群組原則設定時，才需要使用第二個機碼 (在 \Policies 下)。 值若已設定於群組原則中，將會在下一次群組原則重新整理時重寫。

## <a name="manage-windows-services"></a>管理 Windows 服務

### <a name="view-service-state"></a>檢視服務狀態
`sc query termservice`
###  <a name="view-service-logon-account"></a>檢視服務登入帳戶
`sc qc termservice`
### <a name="set-service-logon-account"></a>設定服務登入帳戶
`sc config termservice obj= "NT Authority\NetworkService"`

等號後面必須要有空格。
### <a name="set-service-start-type"></a>設定服務啟動類型
`sc config termservice start= demand`

等號後面必須要有空格。 可能的起始值包括 `boot`、`system`、`auto`、`demand`、`disabled`、`delayed-auto`。
### <a name="set-service-dependencies"></a>設定服務相依性
`sc config termservice depend= RPCSS`

等號後面必須要有空格。
### <a name="start-service"></a>啟動服務
`net start termservice`

或

`sc start termservice`
### <a name="stop-service"></a>停止服務
`net stop termservice`

或

`sc stop termservice`
## <a name="manage-networking-features"></a>管理網路功能
### <a name="show-nic-properties"></a>顯示 NIC 屬性
`netsh interface show interface`
### <a name="show-ip-properties"></a>顯示 IP 屬性
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>顯示 IPSec 組態
`netsh nap client show configuration`
### <a name="enable-nic"></a>啟用 NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>設定 NIC 以使用 DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

如需 `netsh` 的詳細資訊，請[按一下這裡](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) (機器翻譯)。

Azure VM 在客體作業系統中應一律設定為使用 DHCP 來取得 IP 位址。 Azure 靜態 IP 設定仍會使用 DHCP 將靜態 IP 提供給 VM。
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>連接埠 Ping
安裝 telnet 用戶端

`dism /online /Enable-Feature /FeatureName:TelnetClient`

測試連線能力

`telnet bing.com 80`

若要移除 telnet 用戶端

`dism /online /Disable-Feature /FeatureName:TelnetClient`

依預設限用 Windows 中的可用方法時，可能較適合使用 PowerShell 來測試連接埠連線。 請參閱以下 PowerShell 一節中的範例。
### <a name="test-dns-name-resolution"></a>測試 DNS 名稱解析
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>顯示 Windows 防火牆規則
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>停用 Windows 防火牆
`netsh advfirewall set allprofiles state off`

您可以在進行疑難排解時使用此命令，以暫時排除 Windows 防火牆。 下次重新開機時，或當您使用下列命令啟用它時，它就會啟用。 請勿以停止 Windows 防火牆服務 (MPSSVC) 或基礎篩選引擎 (BFE) 服務的方式來排除 Windows 防火牆。 停止 MPSSVC 或 BFE 將會導致所有連線遭到封鎖。
### <a name="enable-windows-firewall"></a>啟用 Windows 防火牆
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>管理使用者和群組
### <a name="create-local-user-account"></a>建立本機使用者帳戶
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>將本機使用者新增至本機群組
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>確認使用者帳戶已啟用
`net user <username> | find /i "active"`

從一般化映像建立的 Azure VM 將會使本機系統管理員帳戶重新命名為在 VM 佈建期間指定的名稱。 因此，該名稱通常不是 `Administrator`。
### <a name="enable-user-account"></a>啟用使用者帳戶
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>檢視使用者帳戶屬性
`net user <username>`

本機管理員帳戶中的相關指令行範例：

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>檢視本機群組
`net localgroup`
## <a name="manage-the-windows-event-log"></a>管理 Windows 事件記錄檔
### <a name="query-event-log-errors"></a>查詢事件記錄檔錯誤
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

將 `/c:10` 變更為所要傳回的事件數目，或加以移動以傳回所有符合篩選條件的事件。
### <a name="query-event-log-by-event-id"></a>依事件識別碼查詢事件記錄檔
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>依事件識別碼和提供者查詢事件記錄檔
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>依事件識別碼和提供者查詢過去 24 小時的事件記錄檔
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

使用 `604800000` 回溯查看 7 天，而非 24 小時。
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>依事件識別碼、提供者和 EventData 查詢過去 7 天的事件記錄檔
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>檢視或移除已安裝的應用程式
### <a name="list-installed-applications"></a>列出已安裝的應用程式
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` 會依安裝日期遞減排序，以便檢視哪些是最近安裝的項目。 使用 `<spacebar>` 前進至輸出的下一頁，或使用 `<enter>` 前進至下一行。
### <a name="uninstall-an-application"></a>解除安裝應用程式
`wmic path win32_product where name="<name>" call uninstall`

針對您要移除的應用程式，將 `<name>` 取代為上述命令傳回的名稱。

## <a name="file-system-management"></a>檔案系統管理
### <a name="get-file-version"></a>取得檔案版本
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

此範例會傳回虛擬 NIC 驅動程式的檔案版本，也就是 netvsc.sys、netvsc63.sys 或 netvsc60.sys，視 Windows 版本而定。
### <a name="scan-for-system-file-corruption"></a>掃描系統檔案損毀
`sfc /scannow`

另請參閱[修復 Windows 映像](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)。
### <a name="scan-for-system-file-corruption"></a>掃描系統檔案損毀
`dism /online /cleanup-image /scanhealth`

另請參閱[修復 Windows 映像](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)。
### <a name="export-file-permissions-to-text-file"></a>將檔案權限匯出至文字檔
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>將檔案權限儲存至 ACL 檔案
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>從 ACL 檔案還原檔案權限
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

使用 `/restore` 時的路徑必須是您在使用 `/save` 時所指定之資料夾的上層資料夾。 在此範例中，`\RSA` 是上述 `/save` 範例中，指定的 `\MachineKeys` 資料夾的上層資料夾。
### <a name="take-ntfs-ownership-of-a-folder"></a>取得資料夾的 NTFS 擁有權
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>以遞迴方式授與資料夾的 NTFS 權限
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>管理裝置
### <a name="remove-non-present-pnp-devices"></a>移除不存在的 PNP 裝置
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>管理群組原則
### <a name="force-group-policy-update"></a>強制更新群組原則
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>其他工作
### <a name="show-os-version"></a>顯示作業系統版本
`ver`

或

`wmic os get caption,version,buildnumber /format:list`

或

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>檢視作業系統安裝日期
`systeminfo | find /i "original"`

或

`wmic os get installdate`
### <a name="view-last-boot-time"></a>檢視上次開機時間
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>檢視時區
`systeminfo | find /i "time zone"`

或

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>重新啟動 Windows
`shutdown /r /t 0`

新增 `/f` 將會強制執行中的應用程式關閉，且不會事先警告使用者。
### <a name="detect-safe-mode-boot"></a>偵測安全模式開機
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Windows 命令 - PowerShell

若要在 SAC 中執行 PowerShell，在出現 CMD 命令提示字元時，請輸入：

`powershell <enter>`

> [!CAUTION]
> 在執行任何其他 PowerShell 命令之前，請先從 PowerShell 工作階段移除 PSReadLine 模組。 目前已知的問題是，在 SAC 中以 PowerShell 工作階段執行 PSReadLine 時，從剪貼簿貼上的文字中可能會插入多餘的字元。

請先確認 PSReadLine 已載入。 在 Windows Server 2016、Windows 10 和 Windows 的更新版本中，會依預設加以載入。 如果它是以手動方式安裝的，則只會出現在舊版的 Windows 上。

如果此命令傳回提示而沒有輸出，表示模組並未載入，而您可以繼續在 SAC 中正常使用 PowerShell 工作階段。

`get-module psreadline`

如果上述命令傳回 PSReadLine 模組版本，請執行下列命令加以卸載。 此命令並不會刪除或解除安裝模組，而只會從目前的 PowerShell 工作階段加以卸載。

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>檢視和編輯 Windows 登錄設定
### <a name="verify-rdp-is-enabled"></a>確認 RDP 已啟用
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

只有已設定相關的群組原則設定時，才會有第二個機碼存在 (在 \Policies 下)。
### <a name="enable-rdp"></a>啟用 RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

只有已設定相關的群組原則設定時，才需要使用第二個機碼 (在 \Policies 下)。 值若已設定於群組原則中，將會在下一次群組原則重新整理時重寫。
## <a name="manage-windows-services"></a>管理 Windows 服務
### <a name="view-service-details"></a>檢視服務詳細資料
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` 可供使用，但未包含服務登入帳戶。 `Get-WmiObject win32-service` 則包含。
### <a name="set-service-logon-account"></a>設定服務登入帳戶
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

使用 `NT AUTHORITY\LocalService`、`NT AUTHORITY\NetworkService` 或 `LocalSystem` 以外的服務帳戶時，請將帳戶密碼指定為帳戶名稱後面的最後一個 (第八個) 引數。
### <a name="set-service-startup-type"></a>設定服務啟動類型
`set-service termservice -startuptype Manual`

`Set-service` 接受以 `Automatic`、`Manual` 或 `Disabled` 作為啟動類型。
### <a name="set-service-dependencies"></a>設定服務相依性
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>啟動服務
`start-service termservice`
### <a name="stop-service"></a>停止服務
`stop-service termservice`
## <a name="manage-networking-features"></a>管理網路功能
### <a name="show-nic-properties"></a>顯示 NIC 屬性
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

或

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` 適用於 2012+，在 2008R2 中請使用 `Get-WmiObject`。
### <a name="show-ip-properties"></a>顯示 IP 屬性
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>啟用 NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

或

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` 適用於 2012+，在 2008R2 中請使用 `Get-WmiObject`。
### <a name="set-nic-to-use-dhcp"></a>設定 NIC 以使用 DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` 適用於 2012+。 在 2008R2 中請使用 `Get-WmiObject`。 Azure VM 在客體作業系統中應一律設定為使用 DHCP 來取得 IP 位址。 Azure 靜態 IP 設定仍會使用 DHCP 將 IP 提供給 VM。
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> 寫入進度 Cmdlet 可能無法與此命令搭配使用。 作為緩和措施，您可以 `$ProgressPreference = "SilentlyContinue"` 在 PowerShell 中執行來停用進度列。

或

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

沒有任何參數的 `Test-Netconnection` 將會嘗試 Ping `internetbeacon.msedge.net`。 這適用於 2012+。 在 2008R2 中，請比照第二個範例使用 `Get-WmiObject`。
### <a name="port-ping"></a>連接埠 Ping
`test-netconnection -ComputerName bing.com -Port 80`

或

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` 適用於 2012+。 在 2008R2 中請使用 `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>測試 DNS 名稱解析
`resolve-dnsname bing.com`

或

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` 適用於 2012+。 在 2008R2 中請使用 `System.Net.DNS`。
### <a name="show-windows-firewall-rule-by-name"></a>依名稱顯示 Windows 防火牆規則
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>依連接埠顯示 Windows 防火牆規則
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

或

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` 適用於 2012+。 在 2008R2 中請使用 `hnetcfg.fwpolicy2` COM 物件。
### <a name="disable-windows-firewall"></a>停用 Windows 防火牆
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` 適用於 2012+。 在 2008R2 中請使用 `netsh advfirewall`，如先前的 CMD 一節中所示。
## <a name="manage-users-and-groups"></a>管理使用者和群組
### <a name="create-local-user-account"></a>建立本機使用者帳戶
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>確認使用者帳戶已啟用
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

或

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` 適用於 2012+。 在 2008R2 中請使用 `Get-WmiObject`。 此範例說明內建的本機系統管理員帳戶，其 SID 一律為 `S-1-5-21-*-500`。 從一般化映像建立的 Azure VM 將會使本機系統管理員帳戶重新命名為在 VM 佈建期間指定的名稱。 因此，該名稱通常不是 `Administrator`。
### <a name="add-local-user-to-local-group"></a>將本機使用者新增至本機群組
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>啟用本機使用者帳戶
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

此範例會啟用內建的本機系統管理員帳戶，其 SID 一律為 `S-1-5-21-*-500`。 從一般化映像建立的 Azure VM 將會使本機系統管理員帳戶重新命名為在 VM 佈建期間指定的名稱。 因此，該名稱通常不是 `Administrator`。
### <a name="view-user-account-properties"></a>檢視使用者帳戶屬性
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

或

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` 適用於 2012+。 在 2008R2 中請使用 `Get-WmiObject`。 此範例說明內建的本機系統管理員帳戶，其 SID 一律為 `S-1-5-21-*-500`。
### <a name="view-local-groups"></a>檢視本機群組
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` 適用於 2012+。 在 2008R2 中請使用 `Get-WmiObject`。
## <a name="manage-the-windows-event-log"></a>管理 Windows 事件記錄檔
### <a name="query-event-log-errors"></a>查詢事件記錄檔錯誤
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

將 `/c:10` 變更為所要傳回的事件數目，或加以移動以傳回所有符合篩選條件的事件。
### <a name="query-event-log-by-event-id"></a>依事件識別碼查詢事件記錄檔
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>依事件識別碼和提供者查詢事件記錄檔
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>依事件識別碼和提供者查詢過去 24 小時的事件記錄檔
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

使用 `604800000` 回溯查看 7 天，而非 24 小時。 |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>依事件識別碼、提供者和 EventData 查詢過去 7 天的事件記錄檔
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>檢視或移除已安裝的應用程式
### <a name="list-installed-software"></a>列出已安裝的軟體
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>解除安裝軟體
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>檔案系統管理
### <a name="get-file-version"></a>取得檔案版本
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

此範例會傳回虛擬 NIC 驅動程式的檔案版本，其名稱為 netvsc.sys、netvsc63.sys 或 netvsc60.sys，視 Windows 版本而定。
### <a name="download-and-extract-file"></a>下載並解壓縮檔案
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

此範例會建立 `c:\bin` 資料夾，然後將 Sysinternals 工具套件下載並解壓縮至 `c:\bin`。
## <a name="miscellaneous-tasks"></a>其他工作
### <a name="show-os-version"></a>顯示作業系統版本
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>檢視作業系統安裝日期
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>檢視上次開機時間
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>檢視 Windows 執行時間
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

以 `<days>:<hours>:<minutes>:<seconds>:<milliseconds>` 的格式傳回執行時間，例如 `49:16:48:00.00`。
### <a name="restart-windows"></a>重新啟動 Windows
`restart-computer`

新增 `-force` 將會強制執行中的應用程式關閉，且不會事先警告使用者。
## <a name="instance-metadata"></a>執行個體中繼資料

您可以從 Azure VM 內查詢 Azure 執行個體中繼資料，以檢視各項詳細資料，例如 osType、位置、vmSize、vmId、名稱、resourceGroupName、subscriptionId、privateIpAddress 和 publicIpAddress。

要查詢執行個體中繼資料必須要有狀況良好的客體網路連線，因為此作業會透過 Azure 主機對執行個體中繼資料服務發出 REST 呼叫。 因此，如果您能夠查詢執行個體中繼資料，即表示客體能夠透過網路與 Azure 託管的服務進行通訊。

如需詳細資訊，請參閱 [Azure 執行個體中繼資料服務](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)。

### <a name="instance-metadata"></a>執行個體中繼資料
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>作業系統類型 (執行個體中繼資料)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>位置 (執行個體中繼資料)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>大小 (執行個體中繼資料)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM 識別碼 (執行個體中繼資料)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM 名稱 (執行個體中繼資料)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>資源群組名稱 (執行個體中繼資料)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>訂用帳戶識別碼 (執行個體中繼資料)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>標記 (執行個體中繼資料)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>放置群組識別碼 (執行個體中繼資料)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>平台容錯網域 (執行個體中繼資料)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>平台更新網域 (執行個體中繼資料)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 私人 IP 位址 (執行個體中繼資料)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 公用 IP 位址 (執行個體中繼資料)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 子網路位址/前置詞 (執行個體中繼資料)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP 位址 (執行個體中繼資料)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Mac 位址 (執行個體中繼資料)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>後續步驟
* 主要序列主控台 Windows 文件頁面位於[這裡](serial-console-windows.md)。
* 序列主控台也適用於 [Linux](serial-console-linux.md) VM。
* 深入了解[開機診斷](boot-diagnostics.md)。
