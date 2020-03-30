---
title: 準備要上傳至 Azure 的 Windows VHD
description: 瞭解如何準備 Windows VHD 或 VHDX 將其上載到 Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250188"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>準備 Windows VHD 或 VHDX 以上傳至 Azure

在將 Windows 虛擬機器 （VM） 從本地上載到 Azure 之前，必須準備虛擬硬碟 （VHD 或 VHDX）。 Azure 支援 VHD 檔案格式且具有固定大小的磁片的 1 代和第 2 代 VM。 允許的 VHD 大小上限為 1023 GB。 

在第 1 代 VM 中，您可以將 VHDX 檔案系統轉換為 VHD。 您還可以將動態擴展的磁片轉換為固定大小的磁片。 但您無法變更 VM 的世代。 有關詳細資訊，請參閱[是否應在 Hyper-V 中創建第 1 代或 2 代 VM？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)和[Azure 支援第 2 代 VM（預覽）。](generation-2.md)

有關 Azure VM 的支援策略的資訊，請參閱[Azure VM 的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

> [!NOTE]
> 本文中的說明適用于：
>1. 64 位版本的 Windows Server 2008 R2 和更高版本的 Windows Server 作業系統。 有關在 Azure 中運行 32 位作業系統的資訊，請參閱[Azure VM 中 32 位作業系統的支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)。
>2. 如果任何災害復原工具將用於遷移工作負荷（如 Azure 網站恢復或 Azure 遷移），則仍需要在客體作業系統上完成此過程並遵循此過程，才能在遷移之前準備映射。

## <a name="system-file-checker-sfc-command"></a>系統檔檢查器 （SFC） 命令

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>在創建客戶作業系統映射的通用步驟之前，在作業系統上運行 Windows 系統檔檢查器實用程式（運行 sfc /scannow）

系統檔檢查器 （SFC） 命令用於驗證和替換 Windows 系統檔。

要運行 SFC 命令：

1. 以管理員身份打開提升的 CMD 提示。
1. 鍵入`sfc /scannow`並選擇**Enter**。

    ![系統檔案檢查程式](media/prepare-for-upload-vhd-image/system-file-checker.png)


SFC 掃描完成後，請嘗試安裝 Windows 更新並重新啟動電腦。

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>將虛擬磁片轉換為固定大小和 VHD

如果需要將虛擬磁片轉換為 Azure 所需的格式，請使用本節中的方法之一：

1. 在運行虛擬磁片轉換過程之前備份 VM。

1. 確保 Windows VHD 在本機伺服器上正常工作。 先解決 VM 本身的任何錯誤，然後嘗試轉換或上傳至 Azure。

1. 關於 VHD 的大小：

   1. Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 從上傳的 VHD 創建圖像時，MB 的分數將導致錯誤。

   2. OS VHD 允許的最大大小為 2TB。


轉換磁片後，創建使用該磁片的 VM。 啟動並登錄到 VM 以完成其上傳準備。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>使用超 V 管理器轉換磁片 
1. 開啟 Hyper-V 管理員，然後在左側選取您的本機電腦。 在電腦清單上方的功能表中，選擇 **"操作** > **編輯磁片**"。
2. 在 **"查找虛擬硬碟"** 頁上，選擇虛擬磁片。
3. 在 **"選擇操作"** 頁上，**選擇"下** > **一步**轉換"。
4. 如果需要從 VHDX 進行轉換，請選擇 **"VHD** > **下一個**"。
5. 如果需要從動態擴展的磁片進行轉換，請選擇 **"下** > **一個**固定大小"。
6. 尋找並選取用以儲存新 VHD 檔案的路徑。
7. 選取 [完成]****。

> [!NOTE]
> 使用提升的 PowerShell 會話運行本文中的命令。

### <a name="use-powershell-to-convert-the-disk"></a>使用 PowerShell 轉換磁片 
您可以在 Windows PowerShell 中使用 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 命令來轉換虛擬磁碟。 當您啟動 PowerShell 時，選取 [以系統管理員身分執行]****。 

以下示例命令將磁片從 VHDX 轉換為 VHD。 該命令還將磁片從動態擴展的磁片轉換為固定大小的磁片。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

在此命令中，將 的值`-Path`替換為要轉換的虛擬硬碟的路徑。 將 的值`-DestinationPath`替換為轉換磁片的新路徑和名稱。

### <a name="convert-from-vmware-vmdk-disk-format"></a>從 VMware VMDK 磁碟格式進行轉換
如果[VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)包含 Windows VM 映射，請使用[Microsoft 虛擬機器轉換器](https://www.microsoft.com/download/details.aspx?id=42497)將其轉換為 VHD 格式。 有關詳細資訊，請參閱[如何將 VMware VMDK 轉換為超 VVHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。

## <a name="set-windows-configurations-for-azure"></a>設定適用於 Azure 的 Windows 設定

> [!NOTE]
> 從通用映射創建 Windows VM 時，Azure 平臺會將 ISO 檔裝載到 DVD-ROM。
> 因此，必須在通用映射中的作業系統中啟用 DVD-ROM。 如果禁用，Windows VM 將卡在 OOBE。

在計畫上載到 Azure 的 VM 上，請從[提升的命令提示視窗](https://technet.microsoft.com/library/cc947813.aspx)運行以下命令：

1. 在路由表上移除任何靜態持續路由：
   
   * 若要檢視路由表，在命令提示字元視窗上執行 `route print`。
   * 檢查部分`Persistence Routes`。 如果存在持久路由，請使用 命令`route delete`將其刪除。
2. 移除 WinHTTP Proxy：
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    如果 VM 需要使用特定代理，請向 Azure IP 位址[（168.63.129.16）](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)添加代理異常，以便 VM 可以連接到 Azure：
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 將磁片 SAN 策略[`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)設置為 ：
   
    ```PowerShell
    diskpart 
    ```
    在打開的命令提示視窗中，鍵入以下命令：

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. 為 Windows 設置協調的通用時間 （UTC） 時間。 還將 Windows 時間服務的啟動類型 （`w32time`）`Automatic`設置為 ：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 將電源設定檔設置為高性能：

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 確保環境變數`TEMP`並`TMP`設置為其預設值：

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>檢查 Windows 服務
確定以下的每個 Windows 服務都已設為 Windows 預設值。 這些服務是確保 VM 連接而必須設置的最小值。 若要重設啟動設定，請執行下列命令：
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>更新遠端桌面註冊表設置
確保正確配置了以下設置以進行遠端存取：

>[!NOTE] 
>運行`Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`時可能會收到錯誤訊息。 您可以放心忽略這個訊息。 它只意味著域沒有通過群組原則物件推送該配置。

1. 遠端桌面通訊協定 (RDP) 已啟用：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. RDP 埠設置正確。 預設埠為 3389：
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    當您部署 VM 時，會針對連接埠 3389 建立預設規則。 如果要更改埠號，在 Azure 中部署 VM 後執行此操作。

3. 接聽程式正在每個網路介面中進行接聽：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. 為 RDP 連接配置網路級身份驗證 （NLA） 模式：
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. 設定 Keep-Alive 值：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. 重新連線：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. 並行連線數目的限制：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. 刪除綁定到 RDP 攔截器的任何自簽章憑證：
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    此代碼可確保在部署 VM 時可以在開始時進行連接。 如果需要稍後查看此情況，可以在在 Azure 中部署 VM 後執行此操作。

9. 如果 VM 將成為域的一部分，請檢查以下策略以確保未還原以前的設置。 
    
    | 目標                                     | 原則                                                                                                                                                       | 值                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 已啟用                           | 電腦設定\原則\Windows 設定\系統管理範本\元件\遠端桌面服務\遠端桌面工作階段主機\連線         | 允許使用者使用遠端桌面服務從遠端連線                                  |
    | NLA 群組原則                         | 設定\系統管理範本\元件\遠端桌面服務\遠端桌面工作階段主機\安全性                                                    | 使用 NLA 需要使用者身份驗證進行遠端存取 |
    | 保持活動設置                      | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 設定 Keep-Alive 連線間隔                                                 |
    | 重新連線設定                       | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 自動重新連接                                                                   |
    | 連接設置數量有限 | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 限制連線數目                                                              |

## <a name="configure-windows-firewall-rules"></a>設定 Windows 防火牆規則
1. 在三個設定檔（域、標準和公共設定檔）上打開 Windows 防火牆：

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. 在 PowerShell 中運行以下命令，允許 WinRM 通過三個防火牆設定檔（域、私有和公共），並啟用 PowerShell 遠端服務：
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 啟用下列防火牆規則以允許 RDP 流量：

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. 啟用檔和印表機共用規則，以便 VM 可以回應虛擬網路中的 ping 命令：

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. 為 Azure 平臺網路創建規則：

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. 如果 VM 將是域的一部分，請檢查以下 Azure AD 策略，以確保未還原以前的設置。 

    | 目標                                 | 原則                                                                                                                                                  | 值                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | 啟用 Windows 防火牆設定檔 | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 保護所有網路連線         |
    | 啟用 RDP                           | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 允許輸入遠端桌面例外狀況 |
    |                                      | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\標準設定檔\Windows 防火牆 | 允許輸入遠端桌面例外狀況 |
    | 啟用 ICMP-V4                       | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 允許 ICMP 例外狀況                   |
    |                                      | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\標準設定檔\Windows 防火牆 | 允許 ICMP 例外狀況                   |

## <a name="verify-the-vm"></a>驗證 VM 

確保 VM 是正常運行、安全的且可訪問 RDP： 

1. 要確保磁片是正常且一致的，請在下次 VM 重新開機時檢查磁片：

    ```PowerShell
    Chkdsk /f
    ```
    確保報表顯示乾淨且健康的磁片。

2. 設定開機組態資料 (BCD) 設定。 

    > [!NOTE]
    > 使用提升的 PowerShell 視窗運行這些命令。
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. 轉儲日誌有助於解決 Windows 崩潰問題。 啟用轉儲日誌集合：

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. 驗證 Windows 管理檢測 （WMI） 存儲庫是否一致：

    ```PowerShell
    winmgmt /verifyrepository
    ```
    如果存儲庫已損壞，請參閱[WMI：存儲庫損壞與否](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)。

5. 確保沒有其他應用程式正在使用埠 3389。 在 Azure 中，此連接埠是由 RDP 服務所使用。 要查看 VM 上使用哪些埠，運行`netstat -anob`：

    ```PowerShell
    netstat -anob
    ```

6. 要上載屬於網域控制站的 Windows VHD，請執行以下操作：

   * 遵循[這些額外的步驟](https://support.microsoft.com/kb/2904015)來準備磁碟。

   * 請確保您知道目錄服務還原模式 （DSRM） 密碼，以防某個時候必須啟動 DSRM 中的 VM。 有關詳細資訊，請參閱設置[DSRM 密碼](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)。

7. 請確保您知道內置管理員帳戶和密碼。 您可能希望重置當前本地管理員密碼，並確保使用此帳戶通過 RDP 連接登錄到 Windows。 此存取權限由"通過遠端桌面服務允許登錄"群組原則物件控制。 在此處的本機群組策略編輯器中查看此物件：

    電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派

8. 檢查以下 Azure AD 策略，以確保不會阻止通過 RDP 或網路訪問 RDP：

    - 電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派\拒絕從網路存取此電腦

    - 電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派\拒絕從遠端桌面服務登入


9. 檢查以下 Azure AD 策略，以確保不會刪除任何必需的存取帳戶：

   - 電腦配置_Windows 設置\安全設置\本地策略\使用者許可權分配\從網路訪問此電腦

   策略應列出以下組：

   - 系統管理員

   - 備份操作員

   - 所有人

   - 使用者

10. 重新開機 VM 以確保 Windows 仍然正常，並且可以通過 RDP 連接進行訪問。 此時，您可能希望在本地 Hyper-V 中創建 VM，以確保 VM 完全啟動。 然後進行測試，以確保您可以通過 RDP 到達 VM。

11. 刪除任何額外的傳輸驅動程式介面 （TDI） 篩選器。 例如，刪除分析 TCP 資料包或額外防火牆的軟體。 如果需要稍後查看此情況，可以在在 Azure 中部署 VM 後執行此操作。

12. 卸載與物理元件或任何其他虛擬化技術相關的任何其他協力廠商軟體或驅動程式。

### <a name="install-windows-updates"></a>安裝 Windows 更新
理想情況下，應使電腦在*修補程式級別*保持更新。 如果無法這樣做，請確保安裝了以下更新。 要獲取最新更新，請參閱 Windows 更新歷史記錄頁[：Windows 10 和 Windows 伺服器 2019、Windows](https://support.microsoft.com/help/4000825) [8.1 和 Windows 伺服器 2012 R2](https://support.microsoft.com/help/4009470)和[Windows 7 SP1 和 Windows 伺服器 2008 R2 SP1](https://support.microsoft.com/help/4009469)。

| 元件               | Binary         | Windows 7 SP1， Windows 伺服器 2008 R2 SP1 | 視窗 8， 視窗伺服器 2012               | Windows 8.1， Windows 伺服器 2012 R2 | Windows 10 v1607， Windows 伺服器 2016 v1607 | Windows 10 v1703    | Windows 10 v1709， Windows 伺服器 2016 v1709 | Windows 10 v1803， Windows 伺服器 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| 存放裝置                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| 網路                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| 核心                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| 遠端桌面服務問題 | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| 安全性                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> 為了避免在 VM 預配期間意外重新開機，我們建議確保所有 Windows 更新安裝都已完成，並且沒有掛起任何更新。 執行此操作的一種方法是在運行 Sysprep 命令之前安裝所有可能的 Windows 更新並重新啟動一次。

### <a name="determine-when-to-use-sysprep"></a>確定何時使用 Sysprep<a id="step23"></a>    

系統準備工具 （Sysprep） 是一個可以運行以重置 Windows 安裝的過程。 Sysprep 通過刪除所有個人資料並重置多個元件，提供"開箱即用"體驗。 

通常運行 Sysprep 以創建一個範本，從中可以部署具有特定配置的其他幾個 VM。 該範本稱為*通用圖像*。

如果只想從一個磁片創建一個 VM，則不必使用 Sysprep。 相反，可以從*專用映射*創建 VM。 有關如何從專用磁片創建 VM 的資訊，請參閱：

- [從特殊化磁碟建立 VM](create-vm-specialized.md)
- [從特殊化 VHD 磁碟建立 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

如果要創建通用映射，則需要運行 Sysprep。 有關詳細資訊，請參閱如何使用[Sysprep：簡介](https://technet.microsoft.com/library/bb457073.aspx)。 

並非安裝在基於 Windows 的電腦上的每個角色或應用程式都支援通用映射。 因此，在運行此過程之前，請確保 Sysprep 支援電腦的角色。 有關詳細資訊，請參閱[對伺服器角色的 Sysprep 支援](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

### <a name="generalize-a-vhd"></a>概括 VHD

>[!NOTE]
> 執行以下步驟後`sysprep.exe`，關閉 VM。 在 Azure 中從映射創建映射之前，不要將其重新打開。

1. 登入 Windows VM。
1. 以系統管理員身分執行**命令提示字元**。 
1. 將目錄更改為`%windir%\system32\sysprep`。 然後執行 `sysprep.exe`。
1. 在 [系統準備工具]**** 對話方塊中，選取 [進入系統全新體驗 (OOBE)]****，並確認已勾選 [一般化]**** 核取方塊。

    ![系統準備工具](media/prepare-for-upload-vhd-image/syspre.png)
1. 在  **[關機選項]** 中，選取  **[關機]**。
1. 選取 [確定]****。
1. Sysprep 完成後，關閉 VM。 不要使用 **"重新開機"** 關閉 VM。

現在已準備好上傳 VHD。 有關如何從通用磁片創建 VM 的詳細資訊，請參閱[上載通用 VHD 並使用它在 Azure 中創建新 VM。](sa-upload-generalized.md)


>[!NOTE]
> 不支援自訂*unattend.xml*檔。 儘管我們確實支援該`additionalUnattendContent`屬性，但該屬性僅提供有限支援，用於將[Microsoft-windows-shell 設置](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup)選項添加到 Azure 預配代理使用的*unattend.xml*檔中。 例如，您可以使用[其他"未參加內容"](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet)來添加第一日誌命令和登錄命令。 有關詳細資訊，請參閱[其他"未參加內容第一Logon命令"示例](https://github.com/Azure/azure-quickstart-templates/issues/1407)。


## <a name="complete-the-recommended-configurations"></a>完成建議的配置
以下設置不會影響 VHD 上載。 不過，我們強烈建議您設定它們。

* 安裝[Azure 虛擬機器代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 然後您可以啟用 VM 擴充功能。 VM 擴展實現了您可能希望與 VM 一起使用的大多數關鍵功能。 例如，您需要擴展來重置密碼或配置 RDP。 有關詳細資訊，請參閱[Azure 虛擬機器代理概述](../extensions/agent-windows.md)。
* 在 Azure 中創建 VM 後，我們建議您將分頁檔放在*臨時磁碟機卷*上以提高性能。 您可以按照如下方式設置檔放置：

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  如果資料磁片附加到 VM，則時態磁碟機卷的字母通常是*D*。此指定可能不同，具體取決於您的設置和可用磁碟機的數量。
  * 我們建議禁用防毒軟體可能提供的腳本阻止程式。 它們可能會干擾和阻止從映射部署新 VM 時執行的 Windows 預配代理腳本。
  
## <a name="next-steps"></a>後續步驟
* [將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](upload-generalized-managed.md)
* [解決 Azure Windows VM 啟動問題](troubleshoot-activation-problems.md)

