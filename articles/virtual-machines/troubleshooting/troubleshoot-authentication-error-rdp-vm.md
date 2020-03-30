---
title: 針對使用 RDP 來連線至 Azure VM 時的驗證錯誤進行疑難排解 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: b7a561907e3f1968eb9adead3606822d7a1321c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266971"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>針對使用 RDP 來連線至 Azure VM 時的驗證錯誤進行疑難排解

本文可協助您針對使用「遠端桌面通訊協定」(RDP) 連線來連線至 Azure 虛擬機器 (VM) 時發生的驗證錯誤進行疑難排解。

## <a name="symptoms"></a>徵狀

您擷取一個顯示「歡迎」畫面並指出作業系統正在執行的 Azure VM 螢幕擷取畫面。 不過，當您嘗試使用「遠端桌面連線」來連線到該 VM 時，收到下列其中一個錯誤訊息。

### <a name="error-message-1"></a>錯誤訊息 1

**發生了身份驗證錯誤。無法連絡當地安全機構。**

### <a name="error-message-2"></a>錯誤訊息 2

**您嘗試連接到的遠端電腦需要網路級身份驗證 （NLA），但無法連絡 Windows 網域控制站以執行 NLA。如果您是遠端電腦上的管理員，則可以使用"系統屬性"對話方塊的"遠端"選項卡上的選項禁用 NLA。**

### <a name="error-message-3-generic-connection-error"></a>錯誤訊息 3 (一般連線錯誤)

**此電腦無法連接到遠端電腦。如果問題仍然存在，請嘗試再次連接，請與遠端電腦的擁有者或網路系統管理員聯繫。**

## <a name="cause"></a>原因

有多個原因可能導致 NLA 封鎖 RDP 對 VM 的存取。

### <a name="cause-1"></a>原因 1

VM 無法與網域控制站 (DC) 進行通訊。 此問題可能導致 RDP 無法使用網域認證來存取 VM。 不過，您仍然能夠使用「本機系統管理員」認證來登入。 此問題可能發生於下列情況：

1. 此 VM 與 DC 之間的「Active Directory 安全性通道」已中斷。

2. VM 的帳戶密碼是舊複本，DC 的帳戶密碼是新複本。

3. 此 VM 所連線的 DC 狀況不良。

### <a name="cause-2"></a>原因 2

VM 的加密層級高於用戶端電腦所使用的層級。

### <a name="cause-3"></a>原因 3

VM 上已停用 TLS 1.0、1.1 或 1.2 (伺服器) 通訊協定。

### <a name="cause-4"></a>原因 4

VM 已設定為將使用網域認證進行登入的功能停用，且未正確設定「本機安全性授權」(LSA)。

### <a name="cause-5"></a>原因 5

VM 已設定為只接受符合「美國聯邦資訊處理標準」(FIPS) 規範的演算法連線。 通常是使用 Active Directory 原則來完成此設定。 這是罕見的設定，但只能針對「遠端桌面」連線強制執行 FIPS。

## <a name="before-you-troubleshoot"></a>進行疑難排解之前

### <a name="create-a-backup-snapshot"></a>建立備份快照集

若要建立備份快照集，請依照[建立磁碟快照集](../windows/snapshot-copy-managed-disk.md)中的步驟進行操作。

### <a name="connect-to-the-vm-remotely"></a>從遠端連線到 VM

若要從遠端連線到 VM，請使用[如何使用遠端工具對 Azure VM 問題進行疑難排解](remote-tools-troubleshoot-azure-vm-issues.md)中的其中一個方法。

### <a name="group-policy-client-service"></a>群組原則用戶端服務

如果這是已加入網域的 VM，請先停止「群組原則」用戶端服務，以防止任何「Active Directory 原則」覆寫變更。 若要這樣做，請執行下列命令：

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

修正問題之後，請還原此 VM 聯繫網域的功能，以從網域擷取最新的 GPO。 若要這樣做，請執行下列命令：

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

如果變更被還原，即表示是 Active Directory 原則造成問題。 

### <a name="workaround"></a>因應措施

若要解決此問題，請在命令視窗中執行下列命令以停用 NLA：

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

接著，重新啟動 VM。

若要重新啟用 NLA，請執行下列命令，然後重新啟動 VM：

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>疑難排解

### <a name="for-domain-joined-vms"></a>針對已加入網域的 VM

若要針對此問題進行疑難排解，請先檢查 VM 是否能夠連線至 DC，以及 DC 的狀態是否為「良好」而能夠處理來自 VM 的要求。

>[!Note] 
>若要測試 DC 健康情況，您可以使用在相同 VNET 和子網路上、共用相同登入伺服器的另一個 VM。

根據＜從遠端連線到 VM＞一節中的步驟，使用序列主控台、遠端 CMD 或遠端 PowerShell 來連線到有問題的 VM。

若要判斷 VM 所連線的是哪個 DC，請在主控台中執行下列命令： 

```cmd
set | find /i "LOGONSERVER"
```

接著，檢查 VM 與 DC 之間安全通道的健康情況。 若要這樣做，請在已提升權限的 PowerShell 執行個體中執行下列命令。 此命令會傳回一個指出安全通道是否正常運作的布林值旗標：

```powershell
Test-ComputerSecureChannel -verbose
```

如果該通道已中斷，請執行下列命令來修復它：

```powershell
Test-ComputerSecureChannel -repair
```

確定 VM 和 DC 上已更新 Active Directory 中的電腦帳戶密碼：

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

如果 DC 與 VM 之間的通訊良好，但 DC 狀況未良好到足以開啟 RDP 工作階段，則您可以嘗試重新啟動 DC。

如果上述命令無法修正與網域的通訊問題，您可以將 VM 重新加入網域。 若要這樣做，請遵循下列步驟：

1. 使用下列內容來建立名為 Unjoin.ps1 的指令碼，然後在 Azure 入口網站上將該指令碼部署為「自訂指令碼延伸模組」：

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    此指令碼會將 VM 從網域中強制取出並在 10 秒後重新啟動。 接著，您必須清除網域端的電腦物件。

2.  清除完成之後，請將此 VM 重新加入網域。 若要這樣做，請使用下列內容來建立名為 JoinDomain.ps1 的指令碼，然後在 Azure 入口網站上將該指令碼部署為「自訂指令碼延伸模組」： 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >這會使用指定的認證將 VM 加入網域。

如果 Active Directory 通道狀況良好、電腦密碼已更新，且網域控制站如預期般運作，請嘗試進行下列步驟。

如果問題持續發生，請檢查是否已停用網域認證。 若要這樣做，請開啟已提升權限的命令提示字元視窗，然後執行下列命令以判斷是否已將 VM 設定為停用用於登入 VM 的網域帳戶：

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

如果機碼設定為 **1**，即表示已將伺服器設定為不允許使用網域認證。 請將此機碼變更為 **0**。

### <a name="for-standalone-vms"></a>針對獨立 VM

#### <a name="check-minencryptionlevel"></a>檢查 MinEncryptionLevel

在 CMD 執行個體中，執行下列命令以查詢 **MinEncryptionLevel** 登錄值：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

根據此登錄值，依照下列步驟進行操作：

* 4 (FIPS)：前往[檢查符合 FIPS 規範的演算法連線](#fips-compliant)。

* 3 (128 位元加密)：執行下列命令以將嚴重性設定為 **2**：

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (可能的最高加密層級，依據用戶端規定)：您可以執行下列命令來嘗試將加密設定為最小值 **1**：

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
重新啟動 VM 以讓登錄變更生效。

#### <a name="tls-version"></a>TLS 版本

視系統而定，RDP 會使用 TLS 1.0、1.1 或 1.2 (伺服器) 通訊協定。 若要查詢 VM 上如何設定這些通訊協定，請開啟 CMD 執行個體，然後執行下列命令：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

如果傳回的值並非全部為 **1**，即表示已停用通訊協定。 若要啟用這些通訊協定，請執行下列命令：

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

針對其他通訊協定版本，您可以執行下列命令：

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> 請從 SCHANNEL 錯誤相關「客體 OS 記錄」取得 SSH/TLS 版本 x.x。

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a> 檢查符合 FIPS 規範的演算法連線

您可以強制遠端桌面只使用符合 FIPS 規範的演算法連線。 使用登錄機碼即可進行此設定。 若要這樣做，請開啟已提升權限的命令提示字元視窗，然後查詢下列機碼：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

如果命令傳回 **1**，請將登錄值變更為 **0**。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

檢查 VM 上目前的 MinEncryptionLevel：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

如果命令傳回 **4**，請將登錄值變更為 **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

重新啟動 VM 以讓登錄變更生效。

## <a name="next-steps"></a>後續步驟

[Win32_TSGeneralSetting 類別的 SetEncryptionLevel 方法](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel) \(英文\)

[設定伺服器驗證和加密層級](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11)) \(英文\)

[Win32_TSGeneralSetting 類別](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
