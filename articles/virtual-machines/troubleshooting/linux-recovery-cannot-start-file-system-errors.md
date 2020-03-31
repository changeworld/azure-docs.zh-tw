---
title: 解決由於檔案系統錯誤而導致的 Linux VM 啟動問題 |微軟文檔
description: 解釋為什麼 Linux VM 無法啟動以及如何解決問題。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842396"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>解決由於檔案系統錯誤而導致的 Linux VM 啟動問題

無法使用安全外殼 （SSH） 連接到 Azure Linux 虛擬機器 （VM）。 在[Azure 門戶](https://portal.azure.com/)上運行啟動診斷功能時，您將看到類似于以下示例的日誌條目。

## <a name="examples"></a>範例

以下是可能錯誤的示例。

### <a name="example-1"></a>範例 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>範例 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>範例 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>範例 4 

此示例由乾淨的 fsck 引起。 在這種情況下，還有附加到 VM（/dev/sdc1 和 /dev/sde1） 的其他資料磁片。

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

如果檔案系統未正常關機或與存儲相關的問題，則可能會出現此問題。 這些問題包括硬體或軟體錯誤、驅動程式或程式問題、寫入錯誤等。備份關鍵資料始終很重要。 本文中描述的工具可能有助於恢復檔案系統，但仍可能發生資料丟失。

Linux 有幾個檔案系統檢查器可用。 Azure 中分佈最常見的是[：FSCK、E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific)和[Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)。 [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)

## <a name="resolution"></a>解決方案

要解決此問題，請使用[串列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)將 VM 引導到緊急模式，並使用該工具修復檔案系統。 如果 VM 上未啟用串列主控台或不起作用，請參閱本文[的"修復 VM 離線](#repair-the-vm-offline)"部分。

## <a name="use-the-serial-console"></a>使用序列主控台

1. 連線到序列主控台。

   > [!Note]
   > 有關為 Linux 使用串列主控台的詳細資訊，請參閱：
   > * [使用串列主控台訪問 GRUB 和單使用者模式](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [使用串列主控台進行 SysRq 和 NMI 調用](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. 選擇"電源"圖示按鈕，然後選擇"重新開機 VM"。 （如果串列主控台未啟用或未成功連接，您將看不到該按鈕。

   ![IMAGE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. 將 VM 引導到緊急模式。

4. 輸入根帳號的密碼以登錄到緊急模式。

5. 將xfs_repair與 -n 選項一起檢測檔案系統中的錯誤。 在下面的示例中，我們假定系統磁碟分割是 /dev/sda1。 將其替換為 VM 的相應值：

   ```
   xfs_repair -n /dev/sda1
   ```

6. 運行以下命令以修復檔案系統：

   ```
   xfs_repair /dev/sda1
   ```

7. 如果您收到錯誤訊息"ERROR：檔案系統在日誌中有需要重播的寶貴中繼資料更改"，請創建一個臨時目錄並裝載檔案系統：

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. 如果磁片無法裝載，請使用 -L 選項運行xfs_repair命令（強制日誌歸零）：

   ```
   xfs_repair /dev/sda1 -L
   ```

9. 接下來，嘗試裝載檔案系統。 如果磁片安裝成功，您將收到以下輸出：
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. 重新開機 VM，然後檢查問題是否得到解決。

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>修復離線的 VM

1. 將 VM 的系統磁片作為資料磁片附加到恢復 VM（任何正常工作的 Linux VM）。 為此，可以使用[CLI 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)，也可以使用[VM 修復命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)自動設置恢復 VM。

2. 找到您連接的系統磁片的磁碟機標籤。 在這種情況下，我們假定您附加的系統磁片的標籤是 /dev/sdc1。 將其替換為 VM 的相應值。

3. 將xfs_repair與 -n 選項一起檢測檔案系統中的錯誤。

   ```
   xfs_repair -n /dev/sdc1
   ```

4. 運行以下命令以修復檔案系統：

   ```
   xfs_repair /dev/sdc1
   ```

5. 如果您收到錯誤訊息"ERROR：檔案系統在日誌中有需要重播的寶貴中繼資料更改"，請創建一個臨時目錄並裝載檔案系統：

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   如果磁片無法裝載，請使用 -L 選項運行xfs_repair命令（強制日誌歸零）：

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. 接下來，嘗試裝載檔案系統。 如果磁片安裝成功，您將收到以下輸出：

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. 卸載並分離原始虛擬硬碟，然後從原始系統磁片創建 VM。 為此，可以使用[CLI 命令](troubleshoot-recovery-disks-linux.md)或 VM[修復命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)（如果使用這些命令創建恢復 VM）。

8. 檢查問題是否已解決。

## <a name="next-steps"></a>後續步驟

* [使用 Azure CLI 2.0 將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [使用入口網站將資料磁碟附加至 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

