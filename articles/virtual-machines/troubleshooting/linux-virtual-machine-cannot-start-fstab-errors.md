---
title: 排除 Linux VM 啟動問題，由於 fstab 錯誤 |微軟文檔
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351142"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>排除 Linux VM 啟動問題，由於 fstab 錯誤

無法使用安全外殼 （SSH） 連接連接到 Azure Linux 虛擬機器 （VM）。 在[Azure 門戶](https://portal.azure.com/)上運行[啟動診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)功能時，您將看到類似于以下示例的日誌條目：

## <a name="examples"></a>範例

以下是可能錯誤的示例。

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>示例 1：磁片由 SCSI ID 而不是通用唯一識別碼 （UUID） 裝載

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>示例 2：CentOS 上缺少未連接的設備

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>示例 3：由於 fstab 配置錯誤或磁片不再連接，VM 無法啟動

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>示例 4：串列日誌條目顯示不正確的 UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

如果檔案系統表 （fstab） 語法不正確，或者映射到"/etc/fstab"檔中的條目所需的資料磁片未附加到 VM，則可能會出現此問題。

## <a name="resolution"></a>解決方案

要解決此問題，請使用 Azure 虛擬機器的串列主控台啟動緊急模式的 VM。 然後使用該工具修復檔案系統。 如果您的 VM 上未啟用串列主控台，請轉到["修復 VM 離線](#repair-the-vm-offline)"部分。

## <a name="use-the-serial-console"></a>使用序列主控台

### <a name="using-single-user-mode"></a>使用單一使用者模式

1. 連接到[串列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)。
2. 使用串列主控台採用單使用者模式[單使用者模式](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. 一旦 vm 引導到單一使用者模式。 使用您最喜愛的文字編輯器打開 fstab 檔。 

   ```
   # nano /etc/fstab
   ```

4. 查看列出的檔案系統。 fstab 檔中的每一行都表示 VM 啟動時裝載的檔案系統。 有關 fstab 檔的語法的詳細資訊，運行 man fstab 命令。 要排除啟動故障，請查看每行，以確保其在結構和內容上都是正確的。

   > [!Note]
   > * 每行上的欄位由定位字元或空格分隔。 空白行會遭到忽略。 具有數位記號 （#） 作為第一個字元的行是注釋。 注釋的行可以保留在 fstab 檔中，但不會處理它們。 我們建議您注釋您不確定要的 fstab 行，而不是刪除這些行。
   > * 要恢復和啟動 VM，檔案系統分區應是唯一必需的分區。 VM 可能會遇到有關其他注釋分區的應用程式錯誤。 但是，VM 應該在沒有附加分區的情況下啟動。 以後可以取消注釋任何注釋行。
   > * 我們建議您使用檔案系統分區的 UUID 在 Azure VM 上裝載資料磁片。 例如，運行以下命令：``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 要確定檔案系統的 UUID，請運行 blkid 命令。 有關語法的詳細資訊，運行 man blkid 命令。
   > * nofail 選項有助於確保 VM 啟動，即使檔案系統已損壞或啟動時檔案系統不存在也是如此。 我們建議您使用 fstab 檔中的 nofail 選項，以便在 VM 啟動的分區中發生錯誤後繼續啟動。

5. 更改或注釋 fstab 檔中的任何不正確或不必要行，以使 VM 能夠正確啟動。

6. 保存對 fstab 檔的更改。

7. 使用以下命令重新開機 vm。
   
   ```
   # reboot -f
   ```
> [!Note]
   > 您還可以使用"ctrl_x"命令，該命令也會重新開機 vm。


8. 如果條目注釋或修復成功，系統應在門戶中達到 bash 提示。 檢查是否可以連接到 VM。

### <a name="using-root-password"></a>使用根密碼

1. 連接到[串列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)。
2. 使用本地使用者和密碼登錄系統。

   > [!Note]
   > 不能使用 SSH 金鑰在串列主控台中登錄到系統。

3. 查找指示磁片未裝載的錯誤。 在下面的示例中，系統嘗試連接不再存在的磁片：

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. 使用根密碼（基於紅帽的 VM）連接到 VM。

5. 使用您最喜愛的文字編輯器打開 fstab 檔。 安裝磁片後，運行 Nano 的以下命令：

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 查看列出的檔案系統。 fstab 檔中的每一行都表示 VM 啟動時裝載的檔案系統。 有關 fstab 檔的語法的詳細資訊，運行 man fstab 命令。 要排除啟動故障，請查看每行，以確保其在結構和內容上都是正確的。

   > [!Note]
   > * 每行上的欄位由定位字元或空格分隔。 空白行會遭到忽略。 具有數位記號 （#） 作為第一個字元的行是注釋。 注釋的行可以保留在 fstab 檔中，但不會處理它們。 我們建議您注釋您不確定要的 fstab 行，而不是刪除這些行。
   > * 要恢復和啟動 VM，檔案系統分區應是唯一必需的分區。 VM 可能會遇到有關其他注釋分區的應用程式錯誤。 但是，VM 應該在沒有附加分區的情況下啟動。 以後可以取消注釋任何注釋行。
   > * 我們建議您使用檔案系統分區的 UUID 在 Azure VM 上裝載資料磁片。 例如，運行以下命令：``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 要確定檔案系統的 UUID，請運行 blkid 命令。 有關語法的詳細資訊，運行 man blkid 命令。
   > * nofail 選項有助於確保 VM 啟動，即使檔案系統已損壞或啟動時檔案系統不存在也是如此。 我們建議您使用 fstab 檔中的 nofail 選項，以便在 VM 啟動的分區中發生錯誤後繼續啟動。

7. 更改或注釋 fstab 檔中的任何不正確或不必要行，以使 VM 能夠正確啟動。

8. 保存對 fstab 檔的更改。

9. 重新啟動虛擬機器。

10. 如果條目注釋或修復成功，系統應在門戶中達到 bash 提示。 檢查是否可以連接到 VM。

11. 通過運行裝載 +命令來測試任何 fstab 更改時，請檢查您的裝載點。 如果沒有錯誤，您的裝載點應該很好。

## <a name="repair-the-vm-offline"></a>修復離線的 VM

1. 將 VM 的系統磁片作為資料磁片附加到恢復 VM（任何正常工作的 Linux VM）。 為此，可以使用[CLI 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)，也可以使用[VM 修復命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)自動設置恢復 VM。

2. 將系統磁片作為資料磁片裝載到恢復 VM 上後，在進行更改之前備份 fstab 檔，然後按照後續步驟更正 fstab 檔。

3.    查找指示磁片未裝載的錯誤。 在下面的示例中，系統嘗試連接不再存在的磁片：

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. 使用根密碼（基於紅帽的 VM）連接到 VM。

5. 使用您最喜愛的文字編輯器打開 fstab 檔。 安裝磁片後，運行 Nano 的以下命令。 請確保正在處理位於裝載磁片上的 fstab 檔，而不是救援 VM 上的 fstab 檔。

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 查看列出的檔案系統。 fstab 檔中的每一行都表示 VM 啟動時裝載的檔案系統。 有關 fstab 檔的語法的詳細資訊，運行 man fstab 命令。 要排除啟動故障，請查看每行，以確保其在結構和內容上都是正確的。

   > [!Note]
   > * 每行上的欄位由定位字元或空格分隔。 空白行會遭到忽略。 具有數位記號 （#） 作為第一個字元的行是注釋。 注釋的行可以保留在 fstab 檔中，但不會處理它們。 我們建議您注釋您不確定要的 fstab 行，而不是刪除這些行。
   > * 要恢復和啟動 VM，檔案系統分區應是唯一必需的分區。 VM 可能會遇到有關其他注釋分區的應用程式錯誤。 但是，VM 應該在沒有附加分區的情況下啟動。 以後可以取消注釋任何注釋行。
   > * 我們建議您使用檔案系統分區的 UUID 在 Azure VM 上裝載資料磁片。 例如，運行以下命令：``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 要確定檔案系統的 UUID，請運行 blkid 命令。 有關語法的詳細資訊，運行 man blkid 命令。 請注意，要恢復的磁片現在安裝在新的 VM 上。 儘管 UUD 應該一致，但設備分區 ID（例如，"/dev/sda1"）在此 VM 上是不同的。 位於非系統 VHD 上的原始故障 VM 的檔案系統分區[無法使用 CLI 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)的恢復 VM。
   > * nofail 選項有助於確保 VM 啟動，即使檔案系統已損壞或啟動時檔案系統不存在也是如此。 我們建議您使用 fstab 檔中的 nofail 選項，以便在 VM 啟動的分區中發生錯誤後繼續啟動。

7. 更改或注釋 fstab 檔中的任何不正確或不必要行，以使 VM 能夠正確啟動。

8. 保存對 fstab 檔的更改。

9. 重新開機虛擬機器或重建原始 VM。

10. 如果條目注釋或修復成功，系統應在門戶中達到 bash 提示。 檢查是否可以連接到 VM。

11. 通過運行裝載 +命令來測試任何 fstab 更改時，請檢查您的裝載點。 如果沒有錯誤，您的裝載點應該很好。

12. 卸載並分離原始虛擬硬碟，然後從原始系統磁片創建 VM。 為此，可以使用[CLI 命令](troubleshoot-recovery-disks-linux.md)或 VM[修復命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)（如果使用這些命令創建恢復 VM）。

13. 再次創建 VM 並且可以通過 SSH 連接到 VM 後，請執行以下操作：
    * 查看在恢復期間更改或注釋掉的任何 fstab 行。
    * 確保您使用的 UUID 和 nofail 選項正確。
    * 在重新開機 VM 之前測試任何 fstab 更改。 為此，請使用以下命令：``$ sudo mount -a``
    * 創建已更正的 fstab 檔的其他副本，以便將來恢復方案使用。

## <a name="next-steps"></a>後續步驟

* [使用 Azure CLI 2.0 將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [使用 Azure 入口網站將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

