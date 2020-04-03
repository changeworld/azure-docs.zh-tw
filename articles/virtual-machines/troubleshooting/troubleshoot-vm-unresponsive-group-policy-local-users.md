---
title: 虛擬機器在應用程式「組策略本地使用者&組」策略時無回應
description: 本文提供了解決在 Azure 虛擬機器 (VM) 中啟動期間應用策略時負載螢幕卡住的問題的步驟。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620853"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>虛擬機器在應用程式「組策略本地使用者&組」策略時無回應

本文提供了解決在啟動期間在 Azure 虛擬機器 (VM) 中應用策略時負載螢幕卡住的問題的步驟。

## <a name="symptom"></a>徵狀

當您使用[開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)來檢視 VM 的螢幕截圖時,您將看到螢幕已卡住載入訊息:*套用群組原則的使用者和群組原則*。

![替換文字:顯示應用組策略本地使用者和組策略載入的螢幕(Windows Server 2012)。](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![替換文字:顯示應用組策略本地使用者和組策略載入(Windows Server 2012 R2)的螢幕。](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>原因

此凍結的癥狀是由 Windows 配置檔服務動態連結庫中的代碼缺陷 *(profsvc.dll*) 引起的。

> [!NOTE]
> 此缺陷僅適用於 Windows 伺服器 2012 和 Windows 伺服器 2012 R2。

### <a name="the-policy-in-question"></a>有關政策

正在應用的策略無法完成其流程,其策略是:

* *電腦設定_原則_管理樣本_系統/使用者設定檔\刪除在系統重新啟動時超過指定天數的使用者設定檔*

只當以下六個條件為 true 時,此策略才會掛起:

* 開啟 *「刪除使用者設定檔」,這些設定檔早於系統重新啟動策略上指定天數*。
* 您有滿足年齡要求的配置檔,需要清理。
* 您有已註冊用於刪除設定檔通知的元件。
* 元件發出需要從 Windows 的服務控制管理員 (SCM) 元件獲取數據的呼叫(直接或間接),例如有關服務的「開始」、停止或查詢資訊。
* 您已將服務設定為*自動啟動。*
* 此服務設置為在域帳戶的上下文中運行(而不是使用內置帳戶(如本地系統)。

### <a name="the-code-defect"></a>代碼錯誤

代碼缺陷是由於服務控制管理器 (SCM) 和配置檔服務嘗試同時對彼此應用鎖造成的。 存在鎖以防止多個服務同時對同一數據進行更改,這將導致損壞。 通常,多個鎖請求不會導致問題。 但是,由於這種情況在引導過程中發生,因此兩個服務都不能完成其進程,因為它們彼此處於等待階段。

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - 服務控制管理員死鎖,具有「在重新啟動時刪除使用者設定檔」策略

有兩個操作重疊,以便:

* 操作 1 獲取設定檔鎖,但尚未獲取 SCM 鎖。

  **和**

* 操作 2 獲取 SCM 鎖,但尚未獲取配置檔鎖。

發生此死鎖后,獲取第二個所需鎖的嘗試將掛起操作。

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>操作 1 - 舊設定檔移除通知(具有**設定檔案鎖**,需要**SCM 鎖定**)

1. 首先,刪除舊配置檔的策略集獲取內部設定檔服務鎖。

   * 此鎖用於防止兩個線程在*刪除操作*進行時與配置檔交互。

2. 策略查找已足夠舊到可以刪除的設定檔。
3. 作為設定檔刪除的一部份,已註冊設定檔刪除通知的元件嘗試**啟動服務**。
4. 在開始服務之前,服務控制管理員 (SCM) 需要取得**操作 2**中的線程持有的**內部 SCM 鎖**。

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>操作 2 - 使用者特定資料的設定檔載入/建立(具有**SCM 鎖定**,需要**設定檔案鎖定**)

1. 在啟動時,SCM需要按其組訂購所有*自動啟動*服務,以及這些服務所依賴的任何服務。

2. **SCM 獲取內部 SCM 鎖**,用於在服務訂購時控制對服務啟動、停止或配置的訪問。

3. 服務按順序進行,SCM 會迴圈訪問每個服務並啟動它。

4. 如果服務在域帳戶的上下文中運行,則需要為域帳戶載入或創建配置檔,以便它可以存儲使用者特定數據。

5. 此要求傳送**到 設定檔案服務**。

6. 設定檔案服務需要存**取操作 1****中取得的內部鎖**。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 建立及存取修復 VM
2. 開啟序列主控台和記憶體傾印集合
3. 重建 VM
4. 收集記憶體傾印檔案

   > [!NOTE]
   > 遇到此啟動錯誤時,來賓操作系統無法運行。 您將在離線模式下進行故障排除以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 使用[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)準備修復 VM。
2. 使用遠端桌面連接連接到修復 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>開啟序列主控台和記憶體傾印集合

要啟用記憶體轉儲集合和串列主控台,請運行以下文稿:

1. 打開提升的命令提示會話(以管理員身份運行)。
2. 執行下列命令：

   * 開啟串列主控台:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. 驗證 OS 磁碟上的可用空間是否與 VM 上的記憶體大小 (RAM) 相同。

   * 如果 OS 磁碟上沒有足夠的空間,則應更改將創建記憶體轉儲檔的位置,並將其引用到連接到具有足夠可用空間的 VM 的任何數據磁碟。 要更改位置,請用`%SystemRoot%`以下命令中數據磁碟的驅動器號(如"F:")替換。

#### <a name="suggested-configuration-to-enable-os-dump"></a>開啟 OS 轉印的建議設定

**載入損壞的 OS 磁碟:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**在 ControlSet001 上開啟:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**在 ControlSet002 上開啟:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>重建 VM

使用[VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新組裝 VM。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

要解決此問題,您需要首先收集崩潰的記憶體轉儲檔,並與記憶體轉儲檔聯繫支援。 要收集轉儲檔,請按照以下步驟操作:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將作業系統磁碟連線到新的修復 VM

1. 使用[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)準備新的修復 VM。

2. 使用遠端桌面連接連接到修復 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到傾印檔並提交支援票證

1. 在修復 VM 上,轉到連接的 OS 磁碟中的視窗資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。

2. 找到記憶體.dmp 檔案,然後提交包含記憶體轉印檔案[的支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

3. 如果在尋找記憶體.dmp 檔時遇到問題,您可能希望在[序列控制中使用不可阻擋的中斷 (NMI) 呼叫](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls)。 您可以按照本指南使用 NMI 調用[生成內核或完整的崩潰轉儲](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)檔。
