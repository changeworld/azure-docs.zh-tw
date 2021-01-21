---
title: Azure VM 的 Windows 重新開機迴圈 | Microsoft Docs
description: 了解如何對 Windows 重新開機迴圈進行疑難排解 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: ad0ed7e9619f0b789bf8949fe398aa27bc36b9e0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629635"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Azure VM 的 Windows 重新開機迴圈
本文說明您在 Microsoft Azure 中的 Windows 虛擬機器 (VM) 上可能會遇到的重新開機迴圈。

## <a name="symptom"></a>徵狀

當您使用[開機診斷](./boot-diagnostics.md)取得 VM 的螢幕擷取畫面時，您發現虛擬機器正在開機，但開機程序中斷，並從頭開始。

![起始畫面 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>原因

發生重新開機迴圈的可能原因如下：

### <a name="cause-1"></a>原因 1

標示為重要的第三方服務無法啟動。 這會導致作業系統重新開機。

### <a name="cause-2"></a>原因 2

作業系統進行了某些變更。 這些變更通常與更新安裝、應用程式安裝或新增原則有關。 您可能需要查看下列記錄以取得詳細資訊：

- 事件記錄
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>原因 3

這可能是檔案系統損毀所致。 不過，我們很難診斷並找出導致作業系統損毀的變更。

## <a name="solution"></a>解決方案

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

若要解決此問題，請[備份 OS 磁碟](../windows/snapshot-copy-managed-disk.md)，並[將 OS 磁碟連結至救援 VM](./troubleshoot-recovery-disks-portal-windows.md)，然後據以執行解決方案選項，或逐一嘗試每個解決方案。

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

1. OS 磁碟連結至運作中的 VM 後，請確定該磁碟在磁碟管理主控台中標示為 [線上]，並記下 **\Windows** 資料夾所在分割區的磁碟機代號。

2. 如果該磁碟設為 [離線]，請將其設為 [線上]。

3. 建立 **\Windows\System32\config** 的複本，以便在需要回復變更時使用。

4. 在救援 VM 上，開啟 Windows 登錄編輯程式 (regedit)。

5. 選取 **HKEY_LOCAL_MACHINE** 機碼，然後從功能表中選取 [檔案] > [載入登錄區]。

6. 瀏覽至 **\Windows\System32\config** 資料夾中的 SYSTEM 檔案。

7. 選取 [開啟]、輸入 **BROKENSYSTEM** 作為名稱、展開 **HKEY_LOCAL_MACHINE** 機碼，您就會看到名為 **BROKENSYSTEM** 的另一個機碼。

8. 查看電腦是從哪個 ControlSet 開機的。 您會在下列登錄機碼中看到其機碼編號。

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. 透過下列登錄機碼查看 VM 代理程式服務的重要性。

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. 如果登錄機碼的值未設為 **2**，請移至下一個緩和措施。

11. 如果登錄機碼的值設為 **2**，請將值從 **2** 變更為 **1**。

12. 如果有下列任何機碼存在，且其值為 **2** 或 **3**，請將這些值據以設為 **1**：

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. 選取 **BROKENSYSTEM** 機碼，然後  >  從功能表中選取 [檔案卸載 **Hive** ]。

14. 從疑難排解中的 VM 卸離 OS 磁碟。

15. 從疑難排解中的 VM 移除磁碟，並等待大約 2 分鐘，讓 Azure 釋放此磁碟。

16. [從 OS 磁碟建立新的 VM](../windows/create-vm-specialized.md)。

17. 如果問題獲得修正，您可能需要重新安裝 [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe)。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

將 VM 還原到最後一個已知的正確組態，並依照[如何使用最後一個已知的正確組態啟動 Azure Windows VM](https://support.microsoft.com/help/4016731/) 中的步驟操作。

### <a name="solution-for-cause-3"></a>原因 3 的解決方案
>[!NOTE]
>下列程序應視為最後的手段。 雖然從 regback 還原後可恢復對機器的存取，但 OS 實際上並不穩定，因為在登錄區的時間戳記與目前的日期之間，登錄中有資料遺失。 您必須建立新的 VM，並擬定移轉資料的計畫。

1. 磁碟連結至疑難排解中的 VM 後，請確定該磁碟在磁碟管理主控台中標示為 [線上]。

2. 建立 **\Windows\System32\config** 的複本，以便在需要回復變更時使用。

3. 複製 **\Windows\System32\config\regback** 資料夾中的檔案，並取代 **\Windows\System32\config** 資料夾中的檔案。

4. 從疑難排解中的 VM 移除磁碟，並等待大約 2 分鐘，讓 Azure 釋放此磁碟。

5. [從 OS 磁碟建立新的 VM](../windows/create-vm-specialized.md)。
