---
title: Windows 停止錯誤 - 硬體故障
description: 本文提供的步驟可解決 Windows Server 2008 虛擬機器損毀的問題，並顯示錯誤訊息，指出發生硬體故障。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 18622d60f3a33658fadfd28c53c93a07b4b438a9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488641"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows 停止錯誤 - 硬體故障

本文提供的步驟可解決 Windows Server 2008 虛擬機器損毀的問題，並顯示錯誤訊息，指出發生硬體故障。

## <a name="symptoms"></a>徵狀

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示含有下列訊息的藍色畫面：

**\*\*\* 硬體故障**

**致電廠商尋求支援**

**\*\*\* 系統已停止 \*\*\***

#### <a name="blue-screen"></a>藍色畫面

![螢幕擷取畫面顯示藍色螢幕硬體故障損毀。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>序列主控台

![如果已啟用序列主控台，螢幕擷取畫面會顯示序列主控台功能的「硬體故障」訊息。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>原因

當虛擬作業系統未正確設定，而且傳送了非遮罩中斷 (NMI) 時，會出現此畫面。 此錯誤訊息表示核心模式程式產生例外狀況，但處理常式未攔截到此例外狀況。 您可以藉由收集記憶體傾印來識別所產生的例外狀況。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀 

1. 將非遮罩式插斷 (NMI) 登錄機碼 
2. 建立和存取修復 VM 
3. 啟用序列主控台和記憶體傾印收集 
4. 重建 VM 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>將非遮罩式插斷 (NMI) 登錄機碼

1. 使用 Azure 入口網站，重新開機 VM，讓來賓 OS 正常開機。 
2. 還原 VM 的部分存取權之後，請開啟提高許可權的命令提示字元 (以系統管理員身分執行) 。 
3. 使用下列命令來設定 NMI 登錄機碼：

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [查看 REG ADD 命令的進一步資訊](/windows-server/administration/windows-commands/reg-add)
4. *(選擇性)* 安裝程式記憶體傾印集合：

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(選擇性)* 設定序列主控台存取：

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [查看 BCDEDIT 命令的進一步資訊](/windows-server/administration/windows-commands/bcdedit)
6. 使用下列命令重新開機 VM：

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [查看關機命令的進一步資訊](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> 現在應該已修正此問題！

> [!NOTE]
> 重新開機之後，請測試您的 VM 以確定其正常運作。 如果您仍遇到問題，則可以繼續進行下一節，以取得進一步的指示。

> [!TIP]
> 建議您在上一節中設定非遮罩式插斷 (NMI) 登錄機碼，不過，如果您的 VM 在之後無法正常開機，則可能不會發生可能的虛擬 OS 登錄變更。 如果是這種情況，您可以依照下列指示手動新增登錄設定。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 使用 [VM 修復命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>啟用序列主控台和記憶體傾印收集

在重建 VM 之前，建議您啟用記憶體傾印收集和序列主控台。 若要這樣做，請執行下列腳本： 

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。 
2. 列出 BCD 存放區資料，並判斷您將在下一個步驟中使用的開機載入器識別碼。 
    1. 針對第1代 VM，請輸入下列命令，並記下列出的識別碼： 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        在命令中， `<BOOT PARTITON>` 將取代為連接磁片中包含開機資料夾之磁碟分割的字母。 

        ![螢幕擷取畫面會顯示在第1代 VM 中列出 BCD 存放區的輸出，這些 VM 會列示在 Windows 開機載入器的識別碼號碼。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. 針對第2代 VM，請輸入下列命令，並記下列出的識別碼：
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * 在命令中，以 `<LETTER OF THE EFI SYSTEM PARTITION>` EFI 系統磁碟分割的字母取代。
        * 啟動磁片管理主控台來識別標示為 *EFI 系統磁碟分割* 的適當系統磁碟分割可能很有説明。
        * 識別碼可以是唯一的 GUID，也可以是預設的 *bootmgr*。
3. 執行下列命令以啟用序列主控台：

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * 在命令中， `<VOLUME LETTER WHERE THE BCD FOLDER IS>` 將取代為 BCD 資料夾的字母。
    * 在命令中，以 `<BOOT LOADER IDENTIFIER>` 您在上一個步驟中找到的識別碼取代。
4. 確認 OS 磁片上的可用空間大於 VM 上 (RAM) 的記憶體大小。 
    1. 如果 OS 磁片上沒有足夠的空間，您應該變更將建立記憶體傾印檔案的位置。 除了在作業系統磁片上建立檔案，您也可以將它參考至連結至 VM 且有足夠可用空間的任何其他資料磁片。 若要變更位置，請將 **% SystemRoot%** 替換成磁碟機號 (例如，下列命令中的資料磁片的 **F：**) 。 
    2. 輸入下列命令， (建議的傾印設定) ：

    **從中斷的 OS 磁碟載入登錄區：**

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

    **卸載中斷的 OS 磁碟：**

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>重建虛擬機器

* 使用 [VM 修復命令的步驟 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)\(部分機器翻譯\)重建 VM。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [針對 Azure 虛擬機器開機錯誤進行疑難排解](./boot-error-troubleshoot.md)