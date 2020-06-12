---
title: 對 Windows 停止錯誤進行疑難排解 – 目錄服務初始化失敗
description: 解決 Azure 中的 Active Directory 網域控制站虛擬機器 (VM) 卡在迴圈 (指出需要重新啟動) 中的問題。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 118c81dd52951729bfbbb97a510e693861666ee6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663936"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>對 Windows 停止錯誤進行疑難排解 – 目錄服務初始化失敗

本文提供的步驟可解決 Azure 中 Active Directory 網域控制站虛擬機器 (VM) 卡在迴圈中，並指出需要重新啟動的問題。

## <a name="symptom"></a>徵狀

當您使用[開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)檢視 VM 的螢幕擷取畫面時，螢幕擷取畫面顯示 VM 因錯誤而需要重新啟動，並顯示停止代碼 **0xC00002E1** (Windows Server 2008 R2) 或 **0xC00002E2** (Windows Server 2012 或更新版本)。

![Windows Server 2012 啟動螢幕指出「您的電腦發生問題，需要重新啟動。 我們只會收集一些錯誤資訊，然後會為您重新啟動。」](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>原因

錯誤碼 **0xC00002E2** 代表 **STATUS_DS_INIT_FAILURE**，錯誤碼 **0xC00002E1** 代表 **STATUS_DS_CANT_START**。 當目錄服務有問題時，就會發生這兩個錯誤。

作業系統開機時，負責驗證使用者登入的本機安全性驗證服務器 (**LSASS.EXE**) 會強制系統自動重新啟動。 如果 VM 上的作業系統是網域控制站，且沒有其本機 Active Directory 資料庫的讀取/寫入權限，就不會進行驗證。 由於不具 **Active Directory (AD)** 的存取權，LSASS.EXE 無法進行驗證，且會強制重新啟動作業系統。

下列任何情況均可能導致此錯誤：

- 無法存取存放本機 AD 資料庫 (**NTDS.DIT**) 的磁碟。
- 存放本機 AD 資料庫 (NTDS.DIT) 的磁碟已用盡可用空間。
- 遺漏本機 AD 資料庫 (NTDS.DIT) 檔案。
- VM 有多個磁碟，且存放區域網路 (SAN) 原則未正確設定。 SAN 原則未設定為 **ONLINEALL**，磁碟管理員以離線模式連結了非作業系統磁碟。
- 本機 AD 資料庫 (NTDS.DIT) 檔案已損毀。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀：

1. 建立和存取修復 VM。
1. 磁碟上的可用空間。
1. 檢查是否包含 AD 資料庫的磁碟機是否已連結。
1. 啟用目錄服務還原模式。
1. **建議**：重建 VM 之前，請先啟用序列主控台和記憶體傾印集合。
1. 重建 VM。
1. 重新設定 SAN 原則。

> [!NOTE]
> 發生此錯誤時，客體作業系統將無法運作。 您將在離線模式下進行疑難排解，以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用 [VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 準備修復 VM。
1. 使用遠端桌面連線連線至修復 VM。

### <a name="free-up-space-on-disk"></a>釋放磁碟上的空間

磁碟現在已連結至修復 VM，請確認存放 Active Directory 內部資料庫的磁碟有足夠的空間可正確執行。

1. 在磁碟機上按一下滑鼠右鍵並選取 [屬性]，以檢查磁碟是否已滿。
1. 如果磁碟的可用空間低於 300 MB，請[使用 PowerShell 將其擴充為最大值 1 TB](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)。
1. 如果磁碟已使用的空間已達到 1 TB，請執行磁碟清理。

   1. 使用 PowerShell 從中斷的 VM [將資料磁碟中斷連結](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)。
   1. 將中斷的 VM 中斷連結後，請[將資料磁碟連結至](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)正常運作的 VM。
   1. 使用[磁碟清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)釋放額外的空間。

1. **選擇性** - 如果需要更多空間，請開啟 CMD 執行個體，並輸入 `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` 命令，以在磁碟機上執行磁碟重組：

  * 在此命令中，請將 `<LETTER ASSIGNED TO THE OS DISK>` 取代為作業系統磁碟的代號。 例如，如果磁碟代號為 `F:`，則命令將是 `defrag F: /u /x /g`。

  * 磁碟重組可能需要數小時的時間，視片段的層級而定。

如果磁碟上有足夠的空間，請繼續進行下一個工作。

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>檢查是否包含 Active Directory 資料庫的磁碟機是否已連結

1. 開啟提升權限的 CMD 執行個體，然後執行下列命令：

   1. 載入登錄檔案：

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      指定 `f:` 會假設磁碟為磁碟機 `F:`。 請使用包含作業系統磁碟的磁碟機所對應的磁碟機代號。

   1. 確認 **NTDS.DIT** 的磁碟機代號和資料夾：

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. 卸載登錄檔案：

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. 使用 Azure 入口網站，確認 NTDS.DIT 設定所在的磁碟機已新增至 VM。
1. 在客體作業系統上使用磁碟管理主控台，確認包含 NTDS.DIT 的磁碟已上線。
   1. 您可以在 [系統管理工具 > 電腦管理 > 儲存體] 中找到磁碟管理工具，或在 CMD 執行個體中使用 `diskmgmt.msc` 命令加以存取。
1. 如果磁碟未連結至 VM，請重新連結資料磁碟以修正問題。

   如果磁碟已正常連結，請繼續進行下一個工作。

### <a name="enable-directory-services-restore-mode"></a>啟用目錄服務還原模式

將 VM 設定為以 [目錄服務還原模式 (DSRM)] 模式開機，以在開機期間略過檢查 NTDS.DIT 檔案是否存在的作業。

1. 繼續作業前，請先確認您已完成先前將磁碟連結至修復 VM 的工作，並已判定 NTDS.DIT 檔案所在的磁碟。
1. 使用提升權限的 CMD 執行個體列出該存放區上的開機分割區資訊，以從作用中的分割區尋找識別碼：

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   請將 `< Drive Letter >` 取代為在先前的步驟中確認的代號。

   ![此螢幕擷取畫面顯示提升權限的 CMD 執行個體在輸入 'bcdedit /store <Drive Letter>:\boot\bcd /enum' 命令之後的情形，其中會顯示附有識別碼的 Windows 開機管理程式。](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. 在開機分割區上啟用 `safeboot DsRepair` 旗標：

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   請將 `< Drive Letter >` 和 `< Identifier >` 取代為在先前的步驟中確認的值。

1. 重新查詢開機選項，以確定您的變更已正確設定。

   ![此螢幕擷取畫面顯示提升權限的 CMD 執行個體在啟用 safeboot DsRepair 旗標之後的情形。](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建議事項：重建 VM 之前，請先啟用序列主控台和記憶體傾印集合

若要啟用記憶體傾印收集和序列主控台，請開啟提升權限的命令提示字元工作階段以執行下列指令碼 (以管理員身分執行)，然後執行下列命令。

1. 啟用序列主控台：

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. 確認作業系統磁碟上的可用空間至少等於 VM 上的記憶體大小 (RAM)。

  1. 如果作業系統磁碟上沒有足夠的空間，請變更記憶體傾印檔案的建立位置，並將其指向任何已與具有足夠可用空間的 VM 連結的資料磁碟。

     若要變更位置，請使用下列命令，並將 `%SystemRoot%` 取代為資料磁碟的磁碟機代號 (例如 `F:`)。

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>建議使用下列設定來啟用作業系統傾印：

  **載入中斷的作業系統磁碟**：

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **在 ControlSet001 上啟用**：

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **在 ControlSet002 上啟用**：

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **卸載中斷的作業系統磁碟**：

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>重建 VM

1. 使用 [VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 重新組裝 VM。

### <a name="reconfigure-the-storage-area-network-policy"></a>重新設定存放區域網路原則

1. 以 DSRM 模式開機時，唯一可登入的使用者是復原管理員，這是在 VM 升階為網域控制站時所使用的管理員。 所有其他使用者都會導致驗證錯誤。

   1. 如果沒有其他可用的 DC，則必須使用 `.\administrator` 或 `machinename\administrator` 和 DSRM 密碼在本機登入。

1. 設定 SAN 原則，讓所有磁碟都處於線上狀態。

   1. 開啟提升權限的 CMD 執行個體，並輸入`DISKPART`。
   1. 查詢磁碟清單。

      `DISKPART> list disk`

   1. 輸入下列命令以選取需要上線的磁碟，並變更 SAN 原則：

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. 問題修正後，請確實移除旗標 `DsRepair safeboot`：

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. 重新啟動您的 VM。

   > [!NOTE]
   > 如果您的 VM 剛從內部部署遷移過來，且您想要將更多網域控制站從內部部署遷移至 Azure，則應考慮執行下列文章中的步驟，以避免在日後遷移時發生此問題：
   >
   > [如何使用 Azure PowerShell 將現有的內部部署 Hyper-V 網域控制站上傳到 Azure](https://support.microsoft.com/help/2904015)
