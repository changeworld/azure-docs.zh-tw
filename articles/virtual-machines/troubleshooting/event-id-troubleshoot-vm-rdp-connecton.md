---
title: 依事件識別碼對 Azure VM 的 RDP 連線問題進行疑難排解 | Microsoft Docs
description: 使用事件識別解決阻止遠端桌面協定 (RDP) 連接到 Azure 虛擬機器 (VM) 的各種問題。
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
ms.openlocfilehash: 2073d5f91b26cd2ae53e3291a6d1dad4d711b66d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437069"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>依事件識別碼對 Azure VM 的 RDP 連線問題進行疑難排解 

本文說明如何使用事件識別碼對無法透過遠端桌面通訊協定 (RDP) 連線至 Azure 虛擬機器 (VM) 的問題進行疑難排解。

## <a name="symptoms"></a>徵狀

您嘗試使用遠端桌面通訊協定 (RDP) 工作階段連線至 Azure VM。 在您輸入認證後，連線失敗，並出現下列錯誤訊息：

**此電腦無法連接到遠端電腦。如果問題仍然存在,請嘗試再次連接,請與遠端電腦的所有者或網路管理員聯繫。**

若要對此問題進行疑難排解，請檢閱 VM 上的事件記錄，並參考下列案例。

## <a name="before-you-troubleshoot"></a>進行疑難排解之前

### <a name="create-a-backup-snapshot"></a>建立備份快照集

若要建立備份快照集，請依照[建立磁碟快照集](../windows/snapshot-copy-managed-disk.md)中的步驟進行操作。

### <a name="connect-to-the-vm-remotely"></a>從遠端連線到 VM

若要從遠端連線到 VM，請使用[如何使用遠端工具對 Azure VM 問題進行疑難排解](remote-tools-troubleshoot-azure-vm-issues.md)中的其中一個方法。

## <a name="scenario-1"></a>實例 1

### <a name="event-logs"></a>事件記錄

在 CMD 執行個體中執行下列命令，以檢查在過去 24 小時內是否有事件 1058 或事件 1057 記錄在系統記錄中：

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**紀錄名稱:**     系統 <br />
**來源：**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日期：**          *時間* <br />
**事件識別碼：**      1058 <br />
**工作類別:** 沒有 <br />
**等級:**        錯誤 <br />
**關鍵字:**     經典 <br />
**使用者:**         不要適用 <br />
**電腦：**      *電腦* <br />
**描述:** RD 工作階段主機伺服器未能替換用於 TLS 連接上的 RD 工作階段主機伺服器身份驗證的過期自簽名證書。 相關的狀態碼為「存取遭到拒絕」。

**紀錄名稱:**     系統 <br />
**來源：**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日期：**          *時間* <br />
**事件識別碼：**      1058 <br />
**工作類別:** 沒有 <br />
**等級:**        錯誤 <br />
**關鍵字:**     經典 <br />
**使用者:**         不要適用 <br />
**電腦：**      *電腦* <br />
**描述:** RD 會話主機伺服器未能創建新的自簽名證書,用於 TLS 連接上的 RD 會話主機伺服器身份驗證,相關狀態代碼已存在物件。

**紀錄名稱:**     系統 <br />
**來源：**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日期：**          *時間* <br />
**事件識別碼：**      1057 <br />
**工作類別:** 沒有 <br />
**等級:**        錯誤 <br />
**關鍵字:**     經典 <br />
**使用者:**         不要適用 <br />
**電腦：**      *電腦* <br />
**描述:** RD 工作階段主機伺服器未能建立新的自簽名證書,用於 TLS 連接上的 RD 工作階段主機伺服器身份驗證。 相關的狀態碼為「金鑰集不存在」

您也可以執行下列命令，以檢查是否有 SCHANNEL 錯誤事件 36872 和 36870：

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**紀錄名稱:**     系統 <br />
**來源：**        Schannel <br />
**日期：**          — <br />
**事件識別碼：**      36870 <br />
**工作類別:** 沒有 <br />
**等級:**        錯誤 <br />
**關鍵字:**       <br />
**使用者：**          SYSTEM <br />
**電腦：**      *電腦* <br />
**描述:** 嘗試造訪 TLS 伺服器認證的私密金鑰時發生致命錯誤。 從密碼編譯模組傳回的錯誤碼為 0x8009030D。  <br />
內部錯誤狀態為 10001。

### <a name="cause"></a>原因
發生此問題的原因是，無法存取 VM 上 MachineKeys 資料夾中的本機 RSA 加密金鑰。 發生此問題的可能原因包括：

1. Machinekeys 資料夾或 RSA 檔案的權限設定錯誤。

2. RSA 金鑰損毀或遺失。

### <a name="resolution"></a>解決方案

若要對此問題進行疑難排解，您必須依照下列步驟為 RDP 憑證設定正確的權限。

#### <a name="grant-permission-to-the-machinekeys-folder"></a>授與對 MachineKeys 資料夾的權限

1. 使用下列內容建立指令碼：

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  執行此指令碼以重設 MachineKey 資料夾的權限，並將 RSA 檔案重設為預設值。

3.  再次嘗試存取 VM。

執行指令碼之後，您可以檢查下列發生權限問題的檔案：

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>更新 RDP 自我簽署憑證

如果問題持續發生，請執行下列指令碼，以確定 RDP 自我簽署憑證已更新：

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

如果您無法更新憑證，請依照下列步驟嘗試刪除憑證：

1. 在相同 VNET 中的另一個 VM 上開啟 [執行]**** 方塊，並輸入 **mmc**，然後按 [確定]****。 

2. 在 [檔案]**** 功能表上，選取 [新增/移除嵌入式管理單元]****。

3. 在 [可用的嵌入式管理單元]**** 清單中選取 [憑證]****，然後選取 [新增]****。

4. 選取 [電腦帳戶]****，然後選取 [下一步]****。

5. 選取 [其他電腦]****，然後針對有問題 VM 新增 IP 位址。
   >[!Note]
   >請嘗試使用內部網路，以避免使用虛擬 IP 位址。

6. 選取 [完成]****，然後選取 [確定]****。

   ![選取電腦](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. 展開憑證，移至 Remote Desktop\Certificates 資料夾，以滑鼠右鍵按一下憑證，然後選取 [刪除]****。

8. 重新啟動遠端桌面組態服務：

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >此時，如果您從 mmc 重新整理存放區，憑證將會再次出現。 

再次嘗試使用 RDP 存取 VM。

#### <a name="update-tlsssl-certificate"></a>更新 TLS/SSL 憑證

如果將 VM 設置為使用 TLS/SSL 憑證,則運行以下命令以獲取指紋。 然後，檢查該指紋是否與憑證的指紋相同：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

如果不同，請變更指紋：

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

您也可以嘗試刪除金鑰，讓 RDP 使用 RDP 的自我簽署憑證：

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>案例 2

### <a name="event-log"></a>事件記錄檔

在 CMD 執行個體中執行下列命令，以檢查在過去 24 小時內是否有 SCHANNEL 錯誤事件 36871 記錄在系統記錄中：

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**紀錄名稱:**     系統 <br />
**來源：**        Schannel <br />
**日期：**          — <br />
**事件識別碼：**      36871 <br />
**工作類別:** 沒有 <br />
**等級:**        錯誤 <br />
**關鍵字:**       <br />
**使用者：**          SYSTEM <br />
**電腦：**      *電腦* <br />
**描述：** 建立 TLS 伺服器認證時發生嚴重錯誤。 內部錯誤狀態為 10013。
 
### <a name="cause"></a>原因

此問題由安全性原則所導致。 當較舊版本的 TLS (例如 1.0) 停用時，RDP 存取會失敗。

### <a name="resolution"></a>解決方案

RDP 使用 TLS 1.0 作為預設通訊協定。 不過，您可以將此通訊協定變更為已成為新標準的 TLS 1.1。

若要對此問題進行疑難排解，請參閱[對使用 RDP 連線至 Azure VM 時的驗證錯誤進行疑難排解](troubleshoot-authentication-error-rdp-vm.md#tls-version)。

## <a name="scenario-3"></a>案例 3

如果您已在 VM 上安裝**遠端桌面連線代理人**角色，請檢查在過去 24 小時內是否有事件 2056 或事件 1296。 在 CMD 執行個體中執行下列命令： 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**記錄名稱：**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**來源：**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**日期：**          *時間* <br />
**事件識別碼：**      2056 <br />
**工作類別：**(109) <br />
**等級:**        錯誤 <br />
**關鍵字:**       <br />
**使用者：**          NETWORK SERVICE <br />
**電腦：**      *電腦 FQDN* <br />
**描述：** 找不到來自 Microsoft-Windows-TerminalServices-SessionBroker 的事件識別碼 2056 的描述。 可能是引發此事件的元件未安裝在您的本機電腦上，或安裝已損毀。 您可以在本機電腦上安裝或修復該元件。 <br />
如果該事件源自於另一部電腦，則必須將顯示資訊連同事件一起儲存。 <br />
該事件隨附了下列資訊： <br />
NULL <br />
NULL <br />
更新資料庫失敗。

**記錄名稱：**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**來源：**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**日期：**          *時間* <br />
**事件識別碼：**      1296 <br />
**工作類別：**(104) <br />
**等級:**        錯誤 <br />
**關鍵字:**       <br />
**使用者：**          NETWORK SERVICE <br />
**電腦：**      *電腦 FQDN* <br />
**描述：** 找不到來自 Microsoft-Windows-TerminalServices-SessionBroker-Client 的事件識別碼 1296 的描述。 可能是引發此事件的元件未安裝在您的本機電腦上，或安裝已損毀。 您可以在本機電腦上安裝或修復該元件。
如果該事件源自於另一部電腦，則必須將顯示資訊連同事件一起儲存。
該事件隨附了下列資訊：  <br />
*文字* <br />
*文字* <br />
遠端桌面連線代理人尚未就緒，無法用於 RPC 通訊。

### <a name="cause"></a>原因

之所以發生此問題，是因為變更遠端桌面連線代理人伺服器的主機名稱有所變更，但此變更不受支援。 

主機名稱具有 Windows 內部資料庫的項目和相依性，而這是遠端桌面服務伺服器陣列的正常運作所不可或缺的。 在伺服器陣列建置之後變更主機名稱將造成許多錯誤，並且可能導致代理人伺服器停止運作。

### <a name="resolution"></a>解決方案 

若要修正此問題，必須重新安裝遠端桌面連線代理人角色與 Windows 內部資料庫。

## <a name="next-steps"></a>後續步驟

[Schannel 事件](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[SChannel SSP 技術概觀](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[遠端桌面工作階段主機憑證與 SSL 通訊的 RDP 失敗，並出現事件識別碼 1058 和事件 36870](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[網域控制站上的 Schannel 36872 或 Schannel 36870](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[事件識別碼 1058 - 遠端桌面服務驗證和加密](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

