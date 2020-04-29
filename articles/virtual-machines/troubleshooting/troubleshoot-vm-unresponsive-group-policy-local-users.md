---
title: 在套用「群組原則本機使用者 & 群組」原則時，虛擬機器沒有回應
description: 本文提供的步驟可解決在 Azure 虛擬機器（VM）開機期間，負載畫面停滯套用原則的問題。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620853"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>在套用「群組原則本機使用者 & 群組」原則時，虛擬機器沒有回應

本文提供的步驟可解決在 Azure 虛擬機器（VM）開機期間，負載畫面停滯套用原則的問題。

## <a name="symptom"></a>徵狀

當您使用 [[開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)] 來觀看 VM 的螢幕擷取畫面時，您會看到畫面已停滯在載入的訊息中： [套用*群組原則本機使用者和群組] 原則*。

![替代文字：顯示套用群組原則本機使用者和群組原則載入（Windows Server 2012）的畫面。](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![替代文字：顯示套用群組原則本機使用者和群組原則載入（Windows Server 2012 R2）的畫面。](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>原因

這項凍結的徵兆是由 Windows 設定檔服務動態連結程式庫（*profsvc*）中的程式碼瑕疵所造成。

> [!NOTE]
> 此瑕疵僅適用于 Windows Server 2012 和 Windows Server 2012 R2。

### <a name="the-policy-in-question"></a>有問題的原則

套用的原則將不會完成其進程，如下所示：

* *電腦設定管理範本 \ 系統/使用者 Profiles\Delete 使用者設定檔超過指定天數的系統重新開機次數*

只有在下列六個條件成立時，此原則才會停止回應：

* 在*系統重新開機原則上，已啟用 [刪除比指定天數還舊的使用者設定檔*]。
* 您有符合年齡需求的設定檔需要清除。
* 您有已針對設定檔註冊刪除通知的元件。
* 元件會呼叫（直接或間接），需要從 Windows 的服務控制管理員（SCM）元件取得資料，例如啟動、停止或查詢服務的相關資訊。
* 您的服務已設定為 [*自動*啟動]。
* 此服務設定為在網域帳戶的內容下執行（而不是使用內建帳戶，例如本機系統）。

### <a name="the-code-defect"></a>程式碼瑕疵

程式碼缺失是因為服務控制管理員（SCM）和設定檔服務嘗試將鎖定同時套用到另一個。 鎖定存在，以防止多個服務同時對相同的資料進行變更，這會導致損毀。 一般來說，多個鎖定要求不會造成問題。 不過，由於在開機期間會發生這種情況，因此這兩個服務都無法完成其進程，因為它們會停滯在等候彼此。

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648-服務控制管理員具有「重新開機時刪除使用者設定檔」原則的鎖死

有兩個重迭的動作，如下所示：

* 動作1會取得設定檔鎖定，但尚未取得 SCM 鎖定。

  **和**

* 動作2會取得 SCM 鎖定，但尚未取得設定檔鎖定。

一旦發生此鎖死，嘗試取得第二個必要的鎖定會導致動作停止回應。

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>動作 1-舊的設定檔刪除通知（具有**設定檔鎖定**，需要**SCM 鎖定**）

1. 首先，設定為 [刪除舊的設定檔] 的原則會取得內部設定檔服務鎖定。

   * 當*刪除*作業正在進行時，此鎖定可防止兩個執行緒與設定檔互動。

2. 原則會尋找夠舊的設定檔，以供刪除。
3. 在設定檔刪除過程中，已註冊的元件會嘗試**啟動服務**，以通知刪除設定檔。
4. 在啟動服務之前，服務控制管理員（SCM）必須取得**動作 2**中線程所持有的**內部 SCM 鎖定**。

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>動作 2-設定檔負載/建立使用者特定資料（具有**SCM 鎖定**，需要**設定檔鎖定**）

1. 在開機時，SCM 必須依其群組以及這些服務相依的任何服務來排列所有*自動啟動*服務。

2. **Scm 會取得內部 SCM 鎖定**，用來控制在排序服務時啟動、停止或設定服務的存取權。

3. 服務依序執行之後，SCM 會迴圈處理每個服務並啟動它。

4. 如果服務是在網域帳戶的內容下執行，則必須針對網域帳戶載入或建立設定檔，才能儲存使用者的特定資料。

5. 此要求會傳送至**設定檔服務**。

6. 設定檔服務需要存取在**動作 1**中取得的**內部鎖定**。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 建立和存取修復 VM
2. 啟用序列主控台和記憶體傾印集合
3. 重建 VM
4. 收集記憶體傾印檔案

   > [!NOTE]
   > 當遇到此開機錯誤時，虛擬作業系統無法運作。 您將會在離線模式中進行疑難排解，以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)來準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>啟用序列主控台和記憶體傾印集合

若要啟用記憶體傾印收集和序列主控台，請執行下列腳本：

1. 開啟提升許可權的命令提示字元會話（以系統管理員身分執行）。
2. 執行下列命令：

   * 啟用序列主控台：

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. 確認 OS 磁片上的可用空間，與 VM 上的記憶體大小（RAM）一樣大。

   * 如果 OS 磁片上沒有足夠的空間，您應該變更記憶體傾印檔案的建立位置，並將其指向任何連接至具有足夠可用空間之 VM 的資料磁片。 若要變更位置，請`%SystemRoot%`在下列命令中，將取代為數據磁片的磁碟機號（例如 "F："）。

#### <a name="suggested-configuration-to-enable-os-dump"></a>啟用 OS 轉儲的建議設定

**載入中斷的 OS 磁片：**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**在 ControlSet001 上啟用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**在 ControlSet002 上啟用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>重建 VM

使用[Vm 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)來重新組裝 vm。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

若要解決這個問題，您需要先使用記憶體傾印檔案來收集損毀和連線支援的記憶體傾印檔案。 若要收集傾印檔案，請遵循下列步驟：

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 使用[VM 修復命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)的步驟1-3 來準備新的修復 VM。

2. 使用遠端桌面連線連接到修復 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片中的 windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。

2. 找出記憶體 dmp 檔案，然後提交包含記憶體傾印檔案的[支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

3. 如果找不到記憶體 dmp 檔案，您可能會想要改為[在序列主控台中使用非遮罩式插斷（NMI）呼叫](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls)。 您可以遵循本指南，使用 NMI 呼叫來[產生核心或完成損毀](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。
