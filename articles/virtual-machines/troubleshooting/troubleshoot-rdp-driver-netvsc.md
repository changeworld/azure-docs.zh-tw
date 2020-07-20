---
title: 當您從遠端連線到 Azure 中的 Windows 10 或 Windows Server 2016 VM 時針對 netvsc.sys 問題進行疑難排解 | Microsoft Docs
description: 了解如何針對當您連線到 Azure 中的 Windows 10 或 Windows Server 2016 VM 時針對 netsvc.sys 相關問題進行疑難排解。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "71057995"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>因為 netvsc.sys 而無法從遠端連線到 Azure 中的 Windows 10 或 Windows Server 2016 VM

此文章說明如何針對當您連線到 Hyper-V Server 2016 上的 Windows 10 或 Windows Server 2016 Datacenter 虛擬機器 (VM) 時沒有網路連線的問題進行疑難排解。

## <a name="symptoms"></a>徵狀

您無法使用遠端桌面通訊協定（RDP）來連接到 Azure Windows 10 或 Windows Server 2016 VM。 在 [開機診斷](boot-diagnostics.md) 中，畫面中的網路介面卡 (NIC) 上方顯示紅色打叉符號。 這表示 VM 在作業系統完全載入之後沒有連線。

一般而言，此問題發生在 Windows [組建 14393](https://support.microsoft.com/help/4093120/) 與[組建 15063](https://support.microsoft.com/help/4015583/) 中。 若您的作業系統版本比這些版本新，此文章不適用於您的案例。 若要檢查系統版本，請在[序列存取主控台](serial-console-windows.md) 中開啟 CMD 工作階段，然後執行 **Ver**。

## <a name="cause"></a>原因

若安裝之 netvsc.sys 系統檔案的版本是 **10.0.14393.594** 或 **10.0.15063.0**，則可能會發生此問題。 這些版本的 netvsc.sys 可能會使得系統無法與 Azure 平台互動。


## <a name="solution"></a>解決方案

在您遵循這些步驟之前，請將受影響 VM[的系統磁片快照](../windows/snapshot-copy-managed-disk.md)集作為備份。 若要針對此問題進行疑難排解，請使用「序列主控台」，或是將 VM 系統磁碟連結至復原 VM，藉以[修復離線的 VM](#repair-the-vm-offline)。


### <a name="use-the-serial-console"></a>使用序列主控台

連線到[序列主控台，然後開啟 PowerShell 執行個體](serial-console-windows.md)，並依照這些步驟執行。

> [!NOTE]
> 如果 VM 上未啟用序列主控台，請移至[修復離線的 VM](#repair-the-vm-offline) 一節。

1. 在 PowerShell 執行個體中執行命令，以取得檔案版本 (**c:\windows\system32\drivers\netvsc.sys**)：

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. 下載適當的更新到已連結到相同區域中工作中 VM 的新或現有資料磁碟：

   - **10.0.14393.594**：[KB4073562](https://support.microsoft.com/help/4073562) 或更新的更新
   - **10.0.15063.0**：[KB4016240](https://support.microsoft.com/help/4016240) 或更新的更新

3. 將公用程式磁碟從工作中 VM 中斷連結，然後將它連結到損壞的 VM。

4. 執行下列命令以在該 VM 上安裝更新：

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. 重新啟動 VM。

### <a name="repair-the-vm-offline"></a>修復離線的 VM

1. [將系統磁碟連結至復原 VM](../windows/troubleshoot-recovery-disks-portal.md)。

2. 啟動復原 VM 的遠端桌面連線。

3. 請確定磁片在 [磁片管理] 主控台中標示為 [**線上**]。 記下指派給已連結系統磁碟的磁碟機代號。

4. 建立 **\Windows\System32\config** 資料夾的複本，以便在需要回復變更時使用。

5. 在救援 VM 上，啟動 [登錄編輯程式] \(regedit.exe\)。

6. 選取 [ **HKEY_LOCAL_MACHINE** ] 索引鍵，然後**File**  >  從功能表中選取 [檔案] [**載入 Hive** ]。

7. 在 **\Windows\System32\config** 資料夾中尋找 SYSTEM 檔案。

8. 選取 [開啟]****、輸入 **BROKENSYSTEM** 作為名稱、展開 **HKEY_LOCAL_MACHINE** 機碼，然後尋找名為 **BROKENSYSTEM** 的另一個機碼。

9. 移至下列位置：

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. 在每個子機碼中 (例如 0000)，檢查顯示為 **Microsoft HYPER-V Network Adapter** 的 **DriverDesc** 值。

11. 在該子機碼中，檢查代表 VM 網路介面卡之驅動程式版本的 **DriverVersion** 值。

12. 下載適當的更新：

    - **10.0.14393.594**：[KB4073562](https://support.microsoft.com/help/4073562) 或更新的更新
    - **10.0.15063.0**：[KB4016240](https://support.microsoft.com/help/4016240) 或更新的更新

13. 將系統磁碟連結為您可以在其上下載更新之救援 VM 上的資料磁碟。

14. 執行下列命令以在該 VM 上安裝更新：

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. 執行下列命令以卸載登錄區：

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [中斷連結系統磁碟，並重新建立 VM](../windows/troubleshoot-recovery-disks-portal.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡 Azure 支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
