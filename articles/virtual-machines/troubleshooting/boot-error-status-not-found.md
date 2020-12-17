---
title: 針對 Windows 開機管理程式錯誤進行疑難排解 - 0xC0000225「找不到狀態」
description: 解決在 Azure VM 中發生錯誤碼0xC0000225 問題的步驟。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: ff7d5a4e1181dccedc3584d958038a1d695c57ca
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657119"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>針對 Windows 開機管理程式錯誤進行疑難排解 - 0xC0000225「找不到狀態」
 
本文提供的步驟可解決在 Azure VM 中發生錯誤碼 0xC0000225 的問題。 此錯誤指出找不到狀態或物件。

## <a name="symptoms"></a>徵狀

當您使用[開機診斷](./boot-diagnostics.md)來檢視 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 無法啟動錯誤，且具有狀態碼「0xc0000225」。

與這個錯誤碼相關聯的檔案會通知您要採取哪些步驟才能解決問題。 找出 [檔案：] 區段的文字，以判斷適當的動作。

### <a name="drivers-os-related-or-third-party"></a>驅動程式、作業系統相關或第三方

如果檔案存在，但是所指的是驅動程式 (如同顯示)，或是作業系統相關或第三方，請依照[修復系統檔案](#repair-the-system-file)一節中的步驟進行。
 
在下圖中，Windows 開機管理程式顯示「Windows 無法啟動。 可能原因是最近的硬體或軟體變更。」 影像也顯示 [狀態] 為 "0xc0000225"、[檔案：] 為 `\windows\System32\drivers\atapi.sys`，以及 [資訊：] 為「無法載入作業系統，因為重要的系統驅動程式遺失或包含錯誤」。

![Windows 開機管理程式顯示「Windows 無法啟動。 可能原因是最近的硬體或軟體變更。」 也會顯示 [狀態] 為 “0xc0000225’、[檔案：] 為 ‘\windows\System32\drivers\atapi.sys’，以及在 [資訊] 區段顯示：「無法載入作業系統，因為重要的系統驅動程式遺失或包含錯誤。」](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>無檔案

如果顯示狀態碼，但是未顯示任何檔案，請依照[新增 OSDEVICE 變數](#add-the-osdevice-variable)一節底下的步驟進行。

在下圖中，Windows 開機管理程式顯示「Windows 無法啟動。 可能原因是最近的硬體或軟體變更。」 影像也會顯示 [狀態] 為 "0xc0000225"，以及 [資訊：] 為「開機選取失敗，因為無法存取必要的裝置。」

![Windows 開機管理程式顯示「Windows 無法啟動。 可能原因是最近的硬體或軟體變更。」 也會顯示 [狀態] 為 “0xc0000225’，以及在 [資訊] 區段中顯示：「開機選取失敗，因為無法存取必要的裝置。」](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>登錄檔案

如果所指的是任何登錄檔案，例如 \windows\system32\config\system，請依照[建立支援票證](#contact-support)一節底下的步驟進行。
 
在下圖中，Windows 開機管理程式顯示「Windows 無法啟動。 可能原因是最近的硬體或軟體變更。」 影像也顯示 [狀態] 為 "0xc0000225"、[檔案] 為 `\windows\System32\config\system`，以及 [資訊：] 為「無法載入作業系統，因為系統登錄檔案遺失或包含錯誤」。

![Windows 開機管理程式顯示「Windows 無法啟動。 可能原因是最近的硬體或軟體變更。」 也會顯示 [狀態] 為 “0xc0000225’、[檔案：] 為 ‘\windows\System32\config\system’，以及在 [資訊] 區段顯示：「無法載入作業系統，因為系統登錄檔案遺失或包含錯誤」。](./media/troubleshoot-boot-error-status-not-found/3.png)

在下圖中，復原畫面會顯示「您的電腦/裝置需要修復」。 無法載入作業系統，因為系統登錄檔案遺失或包含錯誤」。 影像也會顯示錯誤碼為 "0xc0000225"，而 [檔案] 為 `\windows\System32\config\system`。

![復原畫面會顯示「您的電腦/裝置需要修復。 無法載入作業系統，因為系統登錄檔案遺失或包含錯誤。 也會顯示錯誤碼為 “0xc0000225’，而 [檔案] 為 ‘\windows\System32\config\system’。](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>原因

### <a name="missing-binary"></a>遺失的二進位檔

您可能會遇到您的系統 **(.sys)** 檔案遺失或二進位檔損毀。

### <a name="bcd-corruption-or-improper-vhd-migration"></a>BCD 損毀或 VHD 移轉不正確

在此情況下，**開機設定資料 (BCD)** 已損毀，或 **虛擬硬碟 (VHD)** 已從內部部署遷移，但是未正確備妥。 結果是 **OSDEVICE** 變數遺失，而且必須新增。

### <a name="registry-hive-corruption"></a>登錄 Hive 損毀

登錄 Hive 損毀的原因可能是：

- Hive 失敗
- Hive 裝載，不過是空的
- Hive 未正確關閉
## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 建立和存取修復 VM。
1. 選取解決方案：
   - [修復系統檔案](#repair-the-system-file)
   - [新增 OSDevice 變數](#add-the-osdevice-variable)
   - [建立支援票證](#contact-support)
1. 啟用序列主控台和記憶體傾印集合。
1. 重建 VM。

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用 [VM 修復命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步驟 1-3 準備修復 VM。
1. 使用遠端桌面連線，連線至修復 VM。

### <a name="select-a-solution"></a>選取解決方案

1. 開啟提升權限的命令提示字元。
1. 根據您稍早識別的徵兆，遵循對應解決方案中的步驟。 您可以略過其他解決方案中的步驟，因為那些步驟不適用於您的問題：

- [修復系統檔案](#repair-the-system-file)
- [新增 OSDevice 變數](#add-the-osdevice-variable)
- [建立支援票證](#contact-support)

### <a name="repair-the-system-file"></a>修復系統檔案

1. 使用連結的 VHD，瀏覽至虛擬機器 (VM) 螢幕擷取畫面中顯示的二進位檔位置。
1. 在檔案上按一下滑鼠右鍵，選取 [屬性]，然後選取 [詳細資料] 索引標籤，以查看檔案的資訊。
   1. 請注意檔案的版本，如下圖所示：

      ![‘cng.sys’ 檔案的屬性視窗，檔案版本會醒目提示。](./media/troubleshoot-boot-error-status-not-found/5.png)

1. 將檔案重新命名為 **< BINARY.SYS >.old**，以檔案名稱取代 **< BINARY.SYS >** 。

   針對上述步驟中的影像，**cng.sys** 檔案會重新命名為 **cng.sys.old**

   > [!NOTE]
   > 如果您嘗試重新命名檔案並收到「檔案已損毀且無法讀取」的訊息，請[聯絡支援以取得協助](https://azure.microsoft.com/support/create-ticket/)，因為此解決方案將無法使用。

1. 既然損毀的檔案已重新命名，請從該檔案內部存放庫進行還原來修正檔案。
   1. 啟動 **CMD** 工作階段。
   1. 瀏覽至 **\windows\winsxs**。

   1. 使用下列命令，搜尋位於本節開頭的二進位檔：

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      此命令會列出電腦擁有檔案的所有版本，並提供該元件的路徑歷程記錄。

      例如，**dir cng.sys** 會重新命名 **dir cng.sys /s**

   1. 選擇清單上檔案的最新版本 (或是您偏好的版本)，並且使用先前的路徑和下列命令，將檔案複製到 **windows\system32** 資料夾：

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > 如果最新的二進位檔無法使用，請嘗試之前的版本，或是您知道是穩定檔案的其中一個版本，例如在修補程式之前的版本。

      例如，如果您尋找的二進位檔是 **cmimcext.sys**，則故障的磁碟機是磁碟機 **F:** ，若剛剛執行搜尋最新版本，您會看到下列影像，在 `dir cmim* /s` 命令提示字元中的查詢會找出 cmimcext.sys 檔案的最新版本。

      ![“dir cmim* /s’ 命令提示字元中的查詢會找出 cmimcext.sys 檔案的最新版本。](./media/troubleshoot-boot-error-status-not-found/6.png)

      在上述範例影像中，查詢是在 **C:** 上執行，其中磁碟機代號應該是故障的磁碟機 **F:** ，這是在修復 VM 上連結為資料磁碟的作業系統磁碟。

      用來複製檔案的結果命令是：`copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers`。

完成這項工作之後，請繼續[啟用序列主控台和記憶體傾印集合](#enable-the-serial-console-and-memory-dump-collection)。

### <a name="add-the-osdevice-variable"></a>新增 OSDevice 變數

收集目前的開機設定資訊，並記下作用中磁碟分割上的識別碼。 接著，您將使用這項資訊來新增 **OSDEVICE** 變數，並遵循產生 VM 的指示。

如果此資訊集合出現錯誤，指出沒有 **\boot\bcd** 檔案，請改為使用 [修復系統檔案](#repair-the-system-file) 中的指示。 

1. 針對第 1 代 VM，請以系統管理員身分開啟提高權限的命令提示字元，並輸入下列命令：

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   此圖顯示第 1 代 VM 中的 Windows 開機載入器，並醒目提示識別碼屬性。 醒目提示的識別碼屬性會顯示唯一的英數字元字串。

   ![第 1 代 VM 中的 Windows 開機載入器，已醒目提示識別碼屬性。 醒目提示的識別碼屬性會顯示唯一的英數字元字串。](./media/troubleshoot-boot-error-status-not-found/7.png)

   請記下 Windows 開機載入器的識別碼，其路徑是 **\windows\system32\winload.exe**。

1. 針對第 2 代 VM，請確認作業系統磁碟已上線，且已指派其磁碟分割磁碟代號。 當此驗證完成時，請收集開機設定資訊。
   1. 在 **Windows 搜尋** 中，輸入 **磁碟管理** 並且開啟磁碟管理主控台。 使用此主控台來識別您的修復 VM 上連結的磁碟號碼，以及保存 BCD 存放區的可延伸韌體介面 (EFI) 磁碟分割。

   在下圖中，「磁碟 2」是連結到修復 VM 的磁碟號碼。 影像也會顯示「磁碟 2」上的 EFI 系統磁碟分割，其大小為 100MB 且沒有指派的代號。

   ![「磁碟 2」顯示為連結至修復 VM 的磁碟號碼。 影像也會顯示「磁碟 2」上的 EFI 系統磁碟分割，其大小為 100MB 且沒有指派的代號。](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. 請以系統管理員身分開啟提高權限的命令提示字元，並輸入下列命令：
      1. 使用 `diskpart` 命令開啟 **DISKPART TOOL**。
      1. 列出所有磁碟，然後選取上一個步驟中所識別的連結磁碟：
      
         ```
         list disk
         sel disk <DISK #>
         ```

         下圖顯示列出和選取磁碟的結果。 磁片 0 (127 GB/線上) 、磁片 1 (32 GB/線上) 和磁片 2 (127 GB/線上) 會列出，並使用命令選取磁片 2 `sel disk 2` 。

         ![列出然後選取磁碟的結果。 列出磁碟 0 (127 GB | 線上)、磁碟 1 (32 GB | 線上)，以及磁碟 2 (127 GB | 線上)，選取「磁碟 2」。](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. 列出磁碟分割，然後選取上一個步驟中所識別的 EFI 系統磁碟分割：
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         下圖顯示列出和選取磁碟分割的結果。 分割區 1 (保留/16MB) 、資料分割 2 (系統/100MB) ，以及分割區 3 (主要/126 GB) 會列出，並使用命令選取分割區 2 `sel part 2` 。

         ![列出然後選取磁碟分割的結果。 列出磁碟分割 1 (保留 | 16MB)、磁碟分割 2 (系統 | 100MB)，以及磁碟分割 3 (主要 | 126 GB)，選取「磁碟分割 2」。](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. 使用 `assign` 命令，將代號指派給 EFI 磁碟分割。

         在下列影像中，`assign` 命令和新磁碟機 **SYSTEM (F:)** 都可在「檔案總管」中看見。

         ![指派命令和新磁碟機 SYSTEM (F:) 可在「檔案總管」中看見。](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. 使用下列命令來列出 BCD 存放區資料：
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         在下列影像中，Windows 開機載入器是在第 2 代 VM 中，並醒目提示識別碼屬性。 醒目提示的識別碼屬性具有 **{default}** 的值。

         ![第 2 代 VM 中的 Windows 開機載入器，已醒目提示識別碼屬性。 醒目提示識別碼屬性的值顯示為預設值。](./media/troubleshoot-boot-error-status-not-found/12.png)

         請記下 Windows 開機載入器的識別碼，其路徑是 **\windows\system32\winload.efi**。

1. 請注意，作用中磁碟分割上的 OSDEVICE 變數遺失：

   ![Windows 開機管理程式和 Windows 開機載入器的屬性會在命令提示字元中列出，但是作業系統裝置屬性遺失。](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   在此影像中，Windows 開機管理程式和 Windows 開機載入器的屬性會在命令提示字元中列出，但是 OSDEVICE 屬性遺失。

1. 根據下列資訊新增 OSDEVICE 變數：

   若為單一磁碟分割作業系統磁碟，請新增 `BOOT`。

   > [!NOTE]
   > 可開機資料夾將位於與 Windows 資料夾 **\windows 資料夾** 相同的磁碟分割上。
   > - 第 1 代 VM 的可開機資料夾是 **(\boot\bcd 資料夾)** 。
   > - 第 2 代 VM 的可開機資料夾是 **EFI\Microsoft\boot\bcd**。

   針對第 1 代 VM，請輸入下列命令：

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   針對第 2 代 VM，請輸入下列命令：

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   若為多個磁碟分割作業系統磁碟，請新增 `PARTITION=<LETTER OF WINDOWS FOLDER>:`。

   > [!NOTE]
   > 可開機資料夾最有可能位於與 Windows 資料夾 **\windows 資料夾** 不同的磁碟分割上。
   > - 第 1 代 VM 的可開機資料夾是 **(\boot\bcd 資料夾)** 。
   > - 第 2 代 VM 的可開機資料夾是 **EFI\Microsoft\boot\bcd**。

   針對第 1 代 VM，請輸入下列命令：

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   針對第 2 代 VM，請輸入下列命令：

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

完成這項工作之後，請繼續[啟用序列主控台和記憶體傾印集合](#enable-the-serial-console-and-memory-dump-collection)。

### <a name="contact-support"></a>請連絡支援人員

**登錄檔案** 錯誤有一個解決方案，但是您必須 [建立支援票證](https://azure.microsoft.com/support/create-ticket/)以取得進一步的協助。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>啟用序列主控台和記憶體傾印集合

**建議**：重建 VM 之前，請先藉由執行下列指令碼來啟用序列主控台和記憶體傾印集合：

1. 以系統管理員身分開啟提升權限的命令提示字元工作階段。
1. 執行下列命令：

   **啟用序列主控台**：
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. 確認作業系統磁碟上的可用空間大於 VM 上的記憶體大小 (RAM)。

   如果作業系統磁碟上沒有足夠的空間，請變更記憶體傾印檔案的建立位置，並將其指向任何已與具有足夠可用空間 VM 連結的資料磁碟位置。 若要變更位置，請使用下列命令，將 **%SystemRoot%** 取代為資料磁碟的磁碟機代號 (例如 **F:** )。

   啟用作業系統傾印的建議設定：

   **從中斷的作業系統磁碟載入登錄 Hive：**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **在 ControlSet001 上啟用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **在 ControlSet002 上啟用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **卸載中斷的作業系統磁碟：**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修復命令的步驟 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重建 VM。
