---
title: 因為 VM 開機到安全模式而無法從遠端連線到 Azure 虛擬機器 | Microsoft Docs
description: 了解如何針對因為 VM 開機到安全模式而無法透過 RDP 連線到 VM 的問題進行疑難排解 | Microsoft Docs
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
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918201"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>因為 VM 開機到安全模式而無法連線到 VM

此文章說明如何解決因為 VM 設定為開機到安全模式而無法連線到 Azure Windows 虛擬機器 (VM) 的問題進行疑難排解。


## <a name="symptoms"></a>徵兆

您因為 VM 設定為開機到安全模式而無法透過 RDP 連線或其他連線 (例如 HTTP) 連線到 Azure 中的 VM。 當您檢查 Azure 入口網站中[開機診斷](../troubleshooting/boot-diagnostics.md)的螢幕擷取畫面時，您可能會看到該 VM 正常開機，但網路介面無法使用：

![有關處於安全模式之網路介面的影像](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>原因

在安全模式中，RDP 服務無法使用。 當 VM 開機到安全模式時，只會載入基本系統程式與服務。 這適用於兩個不同版本的安全模式，亦即「安全模式 (基本)」與「安全模式 (含網路功能)」。


## <a name="solution"></a>解決方法

在遵循下列步驟之前，請擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要解決此問題，請使用序列主控台來設定 VM 以開機到安全模式或使用 VM 來[離線修復 VM](#repair-the-vm-offline)。

### <a name="use-serial-control"></a>使用序列主控台

1. 連線至[序列主控台並開啟 CMD 執行個體](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   )。 如果 VM 上未啟用序列主控台，請參閱[修復離線的 VM](#repair-the-vm-offline)一節。
2. 檢查開機設定資料：

        bcdedit /enum

    若 VM 已設定為開機到安全模式，您將會在 [Windows 開機載入器]**** 區段下看到稱為 [安全開機]**** 的額外旗標。 若您沒有看到 [安全開機]**** 旗標，表示 VM 並非處於安全模式。 此文章不適用於您的案例。

    [安全開機]**** 旗標會隨著下列值出現：
   - 最小
   - 網路

     在這兩個模式中的任一個模式中，RDP 將不會啟動。 因此，修正方式仍然相同。

     ![有關安全模式旗標的影像](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. 刪除 [安全開機]**** 旗標，這樣 VM 就會開機到標準模式：

        bcdedit /deletevalue {current} safeboot

4. 檢查開機設定資料以確定已移除 [安全開機]**** 旗標：

        bcdedit /enum

5. 重新啟動 VM，然後檢查問題是否已解決。

### <a name="repair-the-vm-offline"></a>修復離線的 VM

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. [將 OS 磁片連結至復原 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 啟動復原 VM 的遠端桌面連線。
3. 請確定磁片在 [磁片管理] 主控台中標示為 [**線上**]。 記下指派給已連結 OS 磁碟的磁碟機代號。

#### <a name="enable-dump-log-and-serial-console-optional"></a>啟用傾印記錄檔和序列主控台 (選擇性)

若使用此文章中所述的解決方式無法解決問題，傾印記錄檔和序列主控台可協助我們進行進一步的疑難排解。

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升許可權的命令提示字元會話（**以系統管理員身分執行**）。
2. 執行下列指令碼：

    在此指令碼中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將此磁碟機代號取代為 VM 的適當值。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>設定 Windows 開機到標準模式

1. 開啟提升許可權的命令提示字元會話（**以系統管理員身分執行**）。
2. 檢查開機設定資料。 在下列命令中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將此磁碟機代號取代為 VM 的適當值。

        bcdedit /store F:\boot\bcd /enum
    記下包含 **\windows** 資料夾的分割區識別碼名稱。 根據預設，識別碼名稱為 "Default"。

    若 VM 已設定為開機到安全模式，您將會在 [Windows 開機載入器]**** 區段下看到稱為 [安全開機]**** 的額外旗標。 如果沒有看到 [安全開機]**** 旗標，代表此文章不適用於您的案例。

    ![開機識別碼的相關影像](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. 移除 [安全開機]**** 旗標，VM 就會開機到標準模式：

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. 檢查開機設定資料以確定已移除 [安全開機]**** 旗標：

        bcdedit /store F:\boot\bcd /enum
5. [將 OS 磁碟中斷連結並建立 VM](../windows/troubleshoot-recovery-disks-portal.md)。 然後檢查問題是否已解決。
