---
title: 針對因為檔案系統錯誤而啟動的 Linux VM 問題進行疑難排解 |Microsoft Docs
description: 瞭解如何針對 Linux VM 因為檔案系統錯誤而啟動問題進行疑難排解
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
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325941"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>針對因為檔案系統錯誤而啟動的 Linux VM 問題進行疑難排解

您無法使用安全殼層 (SSH) ，連接到 Azure Linux 虛擬機器 (VM) 。 當您在 [Azure 入口網站](https://portal.azure.com/)上執行開機診斷功能時，您會看到類似下列範例的記錄專案。

## <a name="examples"></a>範例

以下是可能錯誤的範例。

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

這個範例是由乾淨的 fsck 所造成。 在此情況下，也會有額外的資料磁片連接至 VM (/dev/sdc1 和/dev/sde1) 。

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

如果檔案系統未完全關閉或儲存體相關問題，就可能會發生此問題。 這些問題包括硬體或軟體錯誤、驅動程式或程式問題、寫入錯誤等。備份重要資料永遠是很重要的。 本文中描述的工具可能有助於復原檔案系統，但仍可能發生資料遺失。

Linux 有數個可用的檔案系統檢查程式。 在 Azure 中最常見的散發套件是： [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific)、 [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)和 [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)。

## <a name="resolution"></a>解決方案

若要解決此問題，請使用 [序列主控台](./serial-console-linux.md) 將 VM 開機進入緊急模式，然後使用該工具修復檔案系統。 如果您的 VM 上未啟用序列主控台，或其無法運作，請參閱本文的「 [修復 VM 離線](#repair-the-vm-offline) 」一節。

## <a name="use-the-serial-console"></a>使用序列主控台

1. 連線到序列主控台。

   > [!Note]
   > 如需使用適用于 Linux 的序列主控台的詳細資訊，請參閱：
   > * [使用序列主控台來存取 GRUB 和單一使用者模式](serial-console-grub-single-user-mode.md)
   > * [使用序列主控台進行 SysRq 和 NMI 呼叫](./serial-console-nmi-sysrq.md)

2. 選取電源圖示按鈕，然後選取 [重新開機 VM]。  (如果序列主控台未啟用或未成功連接，您將不會看到該按鈕。 ) 

   ![影像](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. 將 VM 開機到緊急模式。

4. 輸入根帳號的密碼以登入緊急模式。

5. 使用 xfs_repair 搭配-n 選項來偵測檔案系統中的錯誤。 在下列範例中，我們假設系統磁碟分割是/dev/sda1。 將它取代為 VM 的適當值：

   ```
   xfs_repair -n /dev/sda1
   ```

6. 執行下列命令以修復檔案系統：

   ```
   xfs_repair /dev/sda1
   ```

7. 如果您收到錯誤訊息：「錯誤：檔案系統在需要重新執行的記錄檔中有重要的中繼資料變更」，請建立臨時目錄並掛接檔案系統：

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. 如果磁片無法掛接，請使用-L 選項執行 xfs_repair 命令， (強制進行記錄檔清空) ：

   ```
   xfs_repair /dev/sda1 -L
   ```

9. 接下來，嘗試掛接檔案系統。 如果成功掛接磁片，您將會收到下列輸出：
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. 重新開機 VM，然後檢查問題是否已解決。

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>修復離線的 VM

1. 將 VM 的系統磁片以資料磁片的形式連結至復原 VM (任何正常運作的 Linux VM) 。 若要這樣做，您可以使用 [CLI 命令](./troubleshoot-recovery-disks-linux.md) ，也可以使用 [VM repair 命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)自動設定復原 VM。

2. 找出您所連接之系統磁片的磁片磁碟機標籤。 在此情況下，我們假設您附加的系統磁片標籤為/dev/sdc1。 將它取代為 VM 的適當值。

3. 使用 xfs_repair 搭配-n 選項來偵測檔案系統中的錯誤。

   ```
   xfs_repair -n /dev/sdc1
   ```

4. 執行下列命令以修復檔案系統：

   ```
   xfs_repair /dev/sdc1
   ```

5. 如果您收到錯誤訊息：「錯誤：檔案系統在需要重新執行的記錄檔中有重要的中繼資料變更」，請建立臨時目錄並掛接檔案系統：

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   如果磁片無法掛接，請使用-L 選項執行 xfs_repair 命令， (強制進行記錄檔清空) ：

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. 接下來，嘗試掛接檔案系統。 如果成功掛接磁片，您將會收到下列輸出：

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. 卸載並卸離原始虛擬硬碟，然後從原始系統磁片建立 VM。 若要這樣做，您可以使用 [CLI 命令](troubleshoot-recovery-disks-linux.md) 或 [VM 修復命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 來建立復原 VM。

8. 檢查問題是否已解決。

## <a name="next-steps"></a>後續步驟

* [使用 Azure CLI 2.0 將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解](./troubleshoot-recovery-disks-linux.md)
* [使用入口網站將資料磁碟附加至 Linux VM](../linux/attach-disk-portal.md)
