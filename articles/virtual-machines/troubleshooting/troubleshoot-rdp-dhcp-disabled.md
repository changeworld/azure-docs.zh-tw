---
title: 因 DHCP 停用而無法遠端連線到 Azure 虛擬機器 | Microsoft Docs
description: 了解如何針對 Microsoft Azure 中因停用 DHCP 用戶端服務所造成的 RDP 問題進行疑難排解。| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749886"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>因為停用 DHCP 用戶端服務，而無法 RDP 連線至 Azure 虛擬機器

本文描述在 VM 中停用 DHCP 用戶端服務之後，您無法將遠端桌面連線至 Azure Windows 虛擬機器 (VM) 的問題。


## <a name="symptoms"></a>徵狀
由於 VM 的 DHCP 用戶端服務已停用，因此您無法 RDP 連線至 Azure 中的 VM。 當您檢查 Azure 入口網站中[開機診斷](../troubleshooting/boot-diagnostics.md)的螢幕擷取畫面時，您會在登入畫面中看到 VM 正常開機並等候認證。 您使用事件檢視器從遠端檢視 VM 中的事件記錄。 您會看到 DHCP 用戶端服務未啟動，或無法啟動。 下列為記錄範例：

**日誌名稱**： 系統 </br>
**源**： 服務控制管理員 </br>
**日期**：12/16/2015 11:19:36 AM </br>
**事件 ID**： 7022 </br>
**工作分類**：無 </br>
**級別**： 錯誤 </br>
**關鍵字**： 經典</br>
**使用者**：不適用 </br>
**電腦**：myvm.cosotos.com</br>
**描述**：DHCP 用戶端服務啟動時無反應。</br>

針對 Resource Manager VM，您可以使用 [序列存取主控台] 功能並透過下列命令來查詢事件記錄 7022：

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

針對傳統 VM，您必須在 [離線] 模式中作業，並以手動方式收集記錄。

## <a name="cause"></a>原因

VM 上的 DHCP 用戶端服務並未執行。

> [!NOTE]
> 本文僅適用於 DHCP 用戶端服務而非 DHCP 伺服器。

## <a name="solution"></a>解決方法

在遵循下列步驟之前，請擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要解決此問題，請使用序列控制來為 VM 啟用 DHCP 或[重設網路介面](reset-network-interface.md)。

### <a name="use-serial-control"></a>使用序列主控台

1. 連線至[序列主控台並開啟 CMD 執行個體](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。
). 如果未在 VM 上啟用序列主控台，請參閱[重設網路介面](reset-network-interface.md)。
2. 請檢查是否已在網路介面上停用 DHCP：

        sc query DHCP
3. 如果 DHCP 已停止，請嘗試啟動服務

        sc start DHCP

4. 再次查詢服務，以確定服務已成功啟動。

        sc query DHCP

    嘗試連線至 VM 並查看問題是否已解決。
5. 如果服務未啟動，請根據您收到的錯誤訊息，使用下列適當的解決方法：

    | 錯誤  |  解決方法 |
    |---|---|
    | 5- 拒絕存取  | 請參閱 [DHCP 用戶端服務因為拒絕存取錯誤而停止](#dhcp-client-service-is-stopped-because-of-an-access-denied-error)。  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | 請參閱 [DHCP 用戶端服務當機或停止回應](#dhcp-client-service-crashes-or-hangs)。  |
    | 1058 - ERROR_SERVICE_DISABLED  | 請參閱 [DHCP 用戶端已停用](#dhcp-client-service-is-disabled)。  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。   |
    | 1067 - ERROR_PROCESS_ABORTED |請參閱 [DHCP 用戶端服務當機或停止回應](#dhcp-client-service-crashes-or-hangs)。   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | 請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  請參閱 [DHCP 用戶端服務因為無法登入而失敗](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | 請參閱 [DHCP 用戶端服務當機或停止回應](#dhcp-client-service-crashes-or-hangs)。  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | 請參閱 [DHCP 用戶端已停用](#dhcp-client-service-is-disabled)。  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | 請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。  |
    |1053 | 請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP 用戶端服務因為拒絕存取錯誤而停止

1. 連線至[序列主控台](serial-console-windows.md)並開啟 PowerShell 執行個體。
2. 執行下列指令碼來下載處理程序監視工具：

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. 現在開始**一個長子**跟蹤：

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. 啟動產生**拒絕存取**訊息的服務，以重現問題：

   ```
   sc start DHCP
   ```

   當它失敗時，終止處理序監視追蹤：

   ```
   procmon /Terminate
   ```
5. 收集 **c:\temp\ProcMonTrace.PML** 檔案：

    1. [將資料磁碟連結至 VM](../windows/attach-managed-disk-portal.md
)。
    2. 您可以使用序列主控台，將檔案複製到新的磁碟機。 例如： `copy C:\temp\ProcMonTrace.PML F:\` 。 在此命令中，F 是所連結之資料磁碟的磁碟機代號。 將字母取代為適當的正確值。
    3. 將資料磁碟機中斷連結，並連結到作用中且已安裝處理序監視的 VM。

6. 使用作用中 VM 的處理序監視開啟 **ProcMonTrace.PML**。 然後依 [結果] 是 [拒絕存取]來篩選，如下列螢幕擷取畫面中所示 **：**

    ![在處理程序監視中依結果篩選](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. 修正輸出上的登錄機碼、資料夾或檔案。 當用於服務上的登入帳戶沒有存取這些物件的 ACL 權限時，通常會發生此問題。 若要判斷登入帳戶的正確 ACL 權限，您可以在狀況良好的 VM 上查看。

#### <a name="dhcp-client-service-is-disabled"></a>DHCP 用戶端已停用

1. 將服務還原為其預設的啟動值：

   ```
   sc config DHCP start= auto
   ```

2. 啟動服務：

   ```
   sc start DHCP
   ```

3. 再次查詢服務狀態，以確定服務正在執行：

   ```
   sc query DHCP
   ```

4. 嘗試使用遠端桌面來連線至 VM。

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP 用戶端服務因為無法登入而失敗

1. 如果此服務的啟動帳戶有所變更，就會發生這個問題，請將帳戶還原為其預設狀態：

        sc config DHCP obj= 'NT Authority\Localservice'
2. 啟動服務：

        sc start DHCP
3. 嘗試使用遠端桌面來連線至 VM。

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP 用戶端服務當機或停止回應

1. 如果服務狀態停滯在 [起始]**** 或 [停止]**** 狀態，請嘗試停止服務：

        sc stop DHCP
2. 將服務隔離在其自己的 'svchost' 容器上：

        sc config DHCP type= own
3. 啟動服務：

        sc start DHCP
4. 如果服務仍然未啟動，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="repair-the-vm-offline"></a>修復離線的 VM

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. [將作業系統磁片附加到恢復 VM。](../windows/troubleshoot-recovery-disks-portal.md)
2. 啟動復原 VM 的遠端桌面連線。 確定連結的磁碟在磁碟管理主控台中標示為 [線上]****。 記下指派給所連結 OS 磁碟的磁碟機代號。
3.  打開一個提升的命令提示實例 （**以管理員身份運行**）。 然後執行下列指令碼。 此腳本假定分配給附加的 OS 磁片的磁碟機號為**F**。將字母替換為 VM 中的值。

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [將 OS 磁碟中斷連結並建立 VM](../windows/troubleshoot-recovery-disks-portal.md)。 然後檢查問題是否已解決。

## <a name="next-steps"></a>後續步驟

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以解決您的問題。