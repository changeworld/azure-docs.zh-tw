---
title: 準備要上傳至 Azure 的 Windows VHD
description: 瞭解如何準備 Windows VHD 或 VHDX 以將它上傳至 Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 1f8f595fd0656a35d54012ece64e826357e03bb9
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098674"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>準備 Windows VHD 或 VHDX 以上傳至 Azure

將 Windows 虛擬機器（VM）從內部部署上傳至 Azure 之前，您必須準備虛擬硬碟（VHD 或 VHDX）。 Azure 支援具有 VHD 檔案格式且具有固定大小磁片的第1代和第2代 Vm。 允許的 VHD 大小上限為 2 TB。

在第1代 VM 中，您可以將 VHDX 檔案系統轉換為 VHD。 您也可以將動態擴充的磁片轉換成固定大小的磁片。 但您無法變更 VM 的世代。 如需詳細資訊，請參閱[應該在 hyper-v 中建立第1代或第2代 vm](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) ，並[Azure 支援第2代 vm （預覽）](generation-2.md)。

如需 Azure Vm 支援原則的相關資訊，請參閱[適用于 Azure vm 的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

> [!NOTE]
> 本文中的指示適用于：
>1. 64位版本的 Windows Server 2008 R2 和更新版本的 Windows Server 作業系統。 如需在 Azure 中執行32位作業系統的詳細資訊，請參閱[Azure vm 中的32位作業系統支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)。
>2. 如果將使用任何嚴重損壞修復工具來遷移工作負載（例如 Azure Site Recovery 或 Azure Migrate），仍必須執行此程式，並在虛擬作業系統上進行準備，然後再進行遷移。

## <a name="system-file-checker-sfc-command"></a>系統檔案檢查程式（SFC）命令

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>在建立客戶作業系統映射的一般化步驟之前，在 OS 上執行 Windows 系統檔案檢查工具公用程式（執行 sfc/scannow）

系統檔案檢查程式（SFC）命令是用來驗證和取代 Windows 系統檔案。

若要執行 SFC 命令：

1. 以系統管理員身分開啟提升許可權的命令提示字元。
1. 輸入`sfc /scannow` ，然後選取**Enter**。

    ![系統檔案檢查程式](media/prepare-for-upload-vhd-image/system-file-checker.png)


在 SFC 掃描完成之後，請嘗試安裝 Windows 更新並重新啟動電腦。

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>將虛擬磁片轉換成固定大小和 VHD

如果您需要將虛擬磁片轉換為 Azure 所需的格式，請使用本節中的其中一種方法：

1. 執行虛擬磁片轉換程式之前，請先備份 VM。

1. 請確定 Windows VHD 在本機伺服器上正常運作。 先解決 VM 本身的任何錯誤，然後嘗試轉換或上傳至 Azure。

1. 關於 VHD 的大小：

   1. Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 從上傳的 VHD 建立映射時，mb 的分數會導致錯誤。

   2. 作業系統 VHD 允許的大小上限為2TB。


轉換磁片之後，請建立使用該磁片的 VM。 啟動並登入 VM，完成準備以進行上傳。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>使用 Hyper-v 管理員轉換磁片 
1. 開啟 Hyper-V 管理員，然後在左側選取您的本機電腦。 在 [電腦] 清單上方的功能表中，選取 [**動作** > ] [**編輯磁片**]。
2. 在 [**尋找虛擬硬碟**] 頁面上，選取您的虛擬磁片。
3. 在 [**選擇動作**] 頁面上，選取 [**轉換** > **下一個]**。
4. 如果您需要從 VHDX 進行轉換，請選取 [ **VHD** > **] [下一步]**。
5. 如果您需要從動態擴充磁片進行轉換，請選取 [**固定大小** > **] [下一步]**。
6. 尋找並選取用以儲存新 VHD 檔案的路徑。
7. 選取 [完成]  。

> [!NOTE]
> 使用已提升許可權的 PowerShell 會話來執行本文中的命令。

### <a name="use-powershell-to-convert-the-disk"></a>使用 PowerShell 轉換磁片 
您可以在 Windows PowerShell 中使用 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 命令來轉換虛擬磁碟。 當您啟動 PowerShell 時，選取 [以系統管理員身分執行]****。 

下列範例命令會將磁片從 VHDX 轉換為 VHD。 此命令也會將磁片從動態擴充磁片轉換成固定大小的磁片。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

在此命令中，將的值`-Path`取代為您想要轉換之虛擬硬碟的路徑。 將的值`-DestinationPath`取代為已轉換磁片的新路徑和名稱。

### <a name="convert-from-vmware-vmdk-disk-format"></a>從 VMware VMDK 磁碟格式進行轉換
如果您的 Windows VM 映射是[VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)，請使用[Microsoft 虛擬機器轉換器](https://www.microsoft.com/download/details.aspx?id=42497)將它轉換成 VHD 格式。 如需詳細資訊，請參閱[如何將 VMWARE VMDK 轉換為 HYPER-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。

## <a name="set-windows-configurations-for-azure"></a>設定適用於 Azure 的 Windows 設定

> [!NOTE]
> Azure 平臺會在從一般化映射建立 Windows VM 時，將 ISO 檔案掛接至 DVD-ROM。
> 基於這個理由，您必須在通用映射的 OS 中啟用 DVD-ROM。 如果已停用，則 Windows VM 會停滯在 OOBE。

在您計畫上傳至 Azure 的 VM 上，從[提升許可權的命令提示字元視窗](https://technet.microsoft.com/library/cc947813.aspx)執行下列命令：

1. 在路由表上移除任何靜態持續路由：
   
   * 若要檢視路由表，在命令提示字元視窗上執行 `route print`。
   * 請檢查`Persistence Routes`各節。 如果有持續性的路由，請使用`route delete`命令將它移除。
2. 移除 WinHTTP Proxy：
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    如果 VM 需要使用特定的 proxy，請將 proxy 例外新增至 Azure IP 位址（[168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)），讓 vm 可以連線到 azure：
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 將磁片 SAN 原則設為[`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)：
   
    ```PowerShell
    diskpart 
    ```
    在 [開啟命令提示字元] 視窗中，輸入下列命令：

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. 設定 Windows 的國際標準時間（UTC）時間。 另請將 Windows 時間服務（`w32time`）的啟動類型設定為`Automatic`：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 將 [電源設定檔] 設定為 [高效能]：

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 請確定環境變數`TEMP`和`TMP`都設定為其預設值：

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>檢查 Windows 服務
確定以下的每個 Windows 服務都已設為 Windows 預設值。 這些服務是必須設定以確保 VM 連線能力的最小值。 若要重設啟動設定，請執行下列命令：
   
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
## <a name="update-remote-desktop-registry-settings"></a>更新遠端桌面登錄設定
請確定已正確設定遠端存取的下列設定：

>[!NOTE] 
>當您執行`Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`時，可能會收到錯誤訊息。 您可以放心忽略這個訊息。 這表示網域不會透過群組原則物件來推送該設定。

1. 遠端桌面通訊協定 (RDP) 已啟用：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. 已正確設定 RDP 埠。 預設通訊埠為3389：
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    當您部署 VM 時，會針對連接埠 3389 建立預設規則。 如果您想要變更埠號碼，請在 Azure 中部署 VM 之後執行此動作。

3. 接聽程式正在每個網路介面中進行接聽：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. 設定 RDP 連線的網路層級驗證（NLA）模式：
   
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
8. 移除系結至 RDP 接聽程式的任何自我簽署憑證：
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    這段程式碼可確保您可以在部署 VM 時，從一開始就連接。 如果您稍後需要進行檢查，您可以在 Azure 中部署 VM 之後執行此動作。

9. 如果 VM 將是網域的一部分，請檢查下列原則，以確定先前的設定不會還原。 
    
    | 目標                                     | 原則                                                                                                                                                       | 值                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 已啟用                           | 電腦設定\原則\Windows 設定\系統管理範本\元件\遠端桌面服務\遠端桌面工作階段主機\連線         | 允許使用者使用遠端桌面服務從遠端連線                                  |
    | NLA 群組原則                         | 設定\系統管理範本\元件\遠端桌面服務\遠端桌面工作階段主機\安全性                                                    | 需要使用 NLA 進行遠端存取的使用者驗證 |
    | Keep-alive 設定                      | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 設定 Keep-Alive 連線間隔                                                 |
    | 重新連線設定                       | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 自動重新連線                                                                   |
    | 連線設定數目有限 | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 限制連線數目                                                              |

## <a name="configure-windows-firewall-rules"></a>設定 Windows 防火牆規則
1. 在三個設定檔（網域、標準和公用）上開啟 Windows 防火牆：

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. 在 PowerShell 中執行下列命令，以允許 WinRM 透過三種防火牆設定檔（網域、私人和公用），並啟用 PowerShell 遠端服務：
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 啟用下列防火牆規則以允許 RDP 流量：

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. 啟用 [檔案及印表機共用] 規則，讓 VM 能夠回應虛擬網路內的 ping 命令：

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. 建立 Azure 平臺網路的規則：

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. 如果 VM 將是網域的一部分，請檢查下列 Azure AD 原則，以確定先前的設定不會還原。 

    | 目標                                 | 原則                                                                                                                                                  | 值                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | 啟用 Windows 防火牆設定檔 | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 保護所有網路連線         |
    | 啟用 RDP                           | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 允許輸入遠端桌面例外狀況 |
    |                                      | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\標準設定檔\Windows 防火牆 | 允許輸入遠端桌面例外狀況 |
    | 啟用 ICMP-V4                       | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 允許 ICMP 例外狀況                   |
    |                                      | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\標準設定檔\Windows 防火牆 | 允許 ICMP 例外狀況                   |

## <a name="verify-the-vm"></a>驗證 VM 

請確定 VM 的狀況良好、安全且可存取 RDP： 

1. 若要確定磁片狀況良好且一致，請在下一次 VM 重新開機時檢查磁片：

    ```PowerShell
    Chkdsk /f
    ```
    請確定報表顯示的是乾淨且狀況良好的磁片。

2. 設定開機組態資料 (BCD) 設定。 

    > [!NOTE]
    > 使用已提升許可權的 PowerShell 視窗來執行這些命令。
   
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
3. 傾印記錄檔有助於疑難排解 Windows 損毀問題。 啟用傾印記錄檔收集：

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
4. 確認 Windows Management Instrumentation （WMI）存放庫是一致的：

    ```PowerShell
    winmgmt /verifyrepository
    ```
    如果存放庫損毀，請參閱[WMI：存放庫損毀](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)。

5. 請確定沒有其他應用程式正在使用埠3389。 在 Azure 中，此連接埠是由 RDP 服務所使用。 若要查看 VM 上所使用的埠，請`netstat -anob`執行：

    ```PowerShell
    netstat -anob
    ```

6. 若要上傳網域控制站的 Windows VHD：

   * 遵循[這些額外的步驟](https://support.microsoft.com/kb/2904015)來準備磁碟。

   * 如果您在某個時間點必須以 DSRM 啟動 VM，請確定您知道目錄服務還原模式（DSRM）密碼。 如需詳細資訊，請參閱[設定 DSRM 密碼](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)。

7. 請確定您知道內建的系統管理員帳戶和密碼。 您可能會想要重設目前的本機系統管理員密碼，並確定您可以使用此帳戶，透過 RDP 連線來登入 Windows。 此存取權限是由「允許透過遠端桌面服務登入」群組原則物件所控制。 在 [本機群組原則編輯器] 中查看此物件：

    電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派

8. 請檢查下列 Azure AD 原則，以確定您不會透過 RDP 或從網路封鎖 RDP 存取：

    - 電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派\拒絕從網路存取此電腦

    - 電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派\拒絕從遠端桌面服務登入


9. 請檢查下列 Azure AD 原則，確定您未移除任何必要的存取帳戶：

   - 電腦配置 Assignment\Access 的設置 \ 許可權 \ （本機 \）-從網路將此電腦

   原則應會列出下列群組：

   - 系統管理員

   - 備份操作員

   - 所有人

   - 使用者

10. 重新開機 VM，以確保 Windows 仍然狀況良好，並可透過 RDP 連線來達到。 此時，您可能會想要在本機 Hyper-v 中建立 VM，以確保 VM 會完全啟動。 然後進行測試，以確定您可以透過 RDP 連線到 VM。

11. 移除任何額外的傳輸驅動程式介面（TDI）篩選器。 例如，移除分析 TCP 封包或額外防火牆的軟體。 如果您稍後需要進行檢查，您可以在 Azure 中部署 VM 之後執行此動作。

12. 卸載與實體元件或任何其他虛擬化技術相關的其他協力廠商軟體或驅動程式。

### <a name="install-windows-updates"></a>安裝 Windows 更新
在理想的情況下，您應該將電腦更新為*修補程式等級*。 如果無法這麼做，請確定已安裝下列更新。 若要取得最新的更新，請參閱 Windows update 歷程記錄頁面： [windows 10 和 Windows Server 2019](https://support.microsoft.com/help/4000825)、 [Windows 8.1 和 Windows server 2012 R2](https://support.microsoft.com/help/4009470)和[Windows 7 SP1 和 windows server 2008 R2 SP1](https://support.microsoft.com/help/4009469)。

| 元件               | Binary         | Windows 7 SP1、Windows Server 2008 R2 SP1 | Windows 8、Windows Server 2012               | Windows 8.1、Windows Server 2012 R2 | Windows 10 v1607、Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709、Windows Server 2016 v1709 | Windows 10 v1803、Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| 儲存體                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
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
> 為避免在 VM 布建期間意外重新開機，建議您確定所有 Windows Update 安裝都已完成，而且沒有任何更新正在擱置中。 執行此動作的其中一種方法是在執行 Sysprep 命令之前，先安裝所有可能的 Windows 更新並重新啟動一次。

### <a name="determine-when-to-use-sysprep"></a>判斷何時要使用 Sysprep<a id="step23"></a>    

系統準備工具（Sysprep）是您可以執行以重設 Windows 安裝的程式。 Sysprep 藉由移除所有個人資料並重設數個元件來提供「現成」體驗。 

您通常會執行 Sysprep 來建立範本，您可以在其中部署數個具有特定設定的其他 Vm。 此範本稱為*一般化映射*。

如果您只想要從一個磁片建立一個 VM，就不需要使用 Sysprep。 相反地，您可以從*特製化映射*建立 VM。 如需有關如何從特製化磁片建立 VM 的詳細資訊，請參閱：

- [從特殊化磁碟建立 VM](create-vm-specialized.md)
- [從特殊化 VHD 磁碟建立 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

如果您想要建立一般化映射，則必須執行 Sysprep。 如需詳細資訊，請參閱[如何使用 Sysprep：簡介](https://technet.microsoft.com/library/bb457073.aspx)。 

並非每個安裝在 Windows 電腦上的角色或應用程式都支援一般化映射。 因此，在執行此程式之前，請確定 Sysprep 支援電腦的角色。 如需詳細資訊，請參閱[伺服器角色的 Sysprep 支援](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

### <a name="generalize-a-vhd"></a>將 VHD 一般化

>[!NOTE]
> 在下列步驟`sysprep.exe`中執行之後，請關閉 VM。 請不要將它重新開啟，直到您在 Azure 中建立映射為止。

1. 登入 Windows VM。
1. 以系統管理員身分執行**命令提示字元**。 
1. 將目錄變更為`%windir%\system32\sysprep`。 然後執行 `sysprep.exe`。
1. 在 [系統準備工具]**** 對話方塊中，選取 [進入系統全新體驗 (OOBE)]****，並確認已勾選 [一般化]**** 核取方塊。

    ![系統準備工具](media/prepare-for-upload-vhd-image/syspre.png)
1. 在  **[關機選項]** 中，選取  **[關機]**。
1. 選取 [確定]  。
1. Sysprep 完成時，請關閉 VM。 請勿使用 [**重新開機**] 來關閉 VM。

現在已準備好上傳 VHD。 如需如何從一般化磁片建立 VM 的詳細資訊，請參閱[上傳一般化 VHD 並使用它在 Azure 中建立新的 vm](sa-upload-generalized.md)。


>[!NOTE]
> 不支援自訂的*unattend.xml*檔案。 雖然我們確實支援`additionalUnattendContent`屬性，但僅提供有限的支援，可將[microsoft windows shell 設定](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup)選項新增至 Azure 布建代理程式所使用的*unattend.xml*檔案。 例如，您可以使用[additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet)來新增 FirstLogonCommands 和 LogonCommands。 如需詳細資訊，請參閱[AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407)。


## <a name="complete-the-recommended-configurations"></a>完成建議的設定
下列設定不會影響 VHD 上傳。 不過，我們強烈建議您設定它們。

* 安裝[Azure 虛擬機器代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 然後您可以啟用 VM 擴充功能。 VM 延伸模組會執行您可能想要與 Vm 搭配使用的大部分重要功能。 例如，您將需要用來重設密碼或設定 RDP 的延伸模組。 如需詳細資訊，請參閱[Azure 虛擬機器代理程式總覽](../extensions/agent-windows.md)。
* 在 Azure 中建立 VM 之後，建議您將分頁檔放在*時態磁片磁碟機磁片*區上，以改善效能。 您可以設定檔案位置，如下所示：

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  如果資料磁片已連結至 VM，則時態磁片磁碟機的字母通常是*D*。視您的設定和可用的磁片磁碟機數目而定，這項指定可能會有所不同。
  * 建議您停用防毒軟體可能提供的腳本封鎖程式。 當您從映射部署新的 VM 時，它們可能會干擾並封鎖執行的 Windows 布建代理程式腳本。
  
## <a name="next-steps"></a>後續步驟
* [將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](upload-generalized-managed.md)
* [針對 Azure Windows VM 啟用問題進行疑難排解](troubleshoot-activation-problems.md)

