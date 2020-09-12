---
title: 針對 Linux VM 因為 fstab 錯誤而啟動的問題進行疑難排解 |Microsoft Docs
description: 說明為何無法啟動 Linux VM，以及如何解決問題。
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
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441658"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>針對 Linux VM 因為 fstab 錯誤而啟動的問題進行疑難排解

您無法使用安全殼層 (SSH) 連線來連接到 Azure Linux 虛擬機器 (VM) 。 當您在[Azure 入口網站](https://portal.azure.com/)上執行[開機診斷](./boot-diagnostics.md)功能時，您會看到類似下列範例的記錄專案：

## <a name="examples"></a>範例

以下是可能錯誤的範例。

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>範例1：磁片是由 SCSI 識別碼裝載，而非通用唯一識別碼 (UUID) 

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>範例2： CentOS 上缺少未連結的裝置

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>範例3： VM 無法啟動，因為 fstab 設定不正確或磁片已不再連接

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>範例4：序列記錄專案顯示不正確的 UUID

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

如果檔案系統資料表 (fstab) 語法不正確，或對應至 "/etc/fstab" 檔案中專案的必要資料磁片未附加至 VM，則可能會發生這個問題。

## <a name="resolution"></a>解決方案

若要解決此問題，請使用 Azure 虛擬機器的序列主控台，以緊急模式啟動 VM。 然後使用此工具來修復檔案系統。 如果您的 VM 上未啟用序列主控台，請移至 [ [修復 vm 離線](#repair-the-vm-offline) ] 區段。

## <a name="use-the-serial-console"></a>使用序列主控台

### <a name="using-single-user-mode"></a>使用單一使用者模式

1. 連接到 [序列主控台](./serial-console-linux.md)。
2. 使用序列主控台取得單一使用者模式的 [單一使用者模式](serial-console-grub-single-user-mode.md)
3. 一旦 vm 開機進入單一使用者模式。 使用您慣用的文字編輯器來開啟 fstab 檔案。 

   ```
   # nano /etc/fstab
   ```

4. 檢查列出的檔案系統。 Fstab 檔案中的每一行都會指出當 VM 啟動時所掛接的檔案系統。 如需 fstab 檔語法的詳細資訊，請執行 man fstab 命令。 若要針對啟動失敗進行疑難排解，請檢查每一行，以確定它在結構和內容中都是正確的。

   > [!Note]
   > * 每一行的欄位會以定位字元或空格分隔。 空白行會遭到忽略。 具有數位記號的行 ( # ) ，因為第一個字元是批註。 批註的行可以保留在 fstab 檔中，但是不會進行處理。 建議您針對不確定的 fstab 行加上批註，而不是移除這些程式程式碼。
   > * 若要讓 VM 復原並啟動，檔案系統分割區應該是唯一需要的磁碟分割。 VM 可能會遇到有關其他批註分割區的應用程式錯誤。 不過，VM 應該會啟動，而不需要額外的磁碟分割。 您稍後可以將任何已加上批註的行取消批註。
   > * 建議您在 Azure Vm 上使用檔案系統分割區的 UUID 來掛接資料磁片。 例如，執行下列命令：``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 若要判斷檔案系統的 UUID，請執行 blkid 命令。 如需語法的詳細資訊，請執行 man blkid 命令。
   > * Nofail 選項可協助確保即使檔案系統已損毀，或啟動時檔案系統不存在，VM 也會啟動。 建議您在 fstab 檔案中使用 nofail 選項，以便在 VM 啟動時不需要的資料分割中發生錯誤時，讓啟動繼續進行。

5. 變更或批註 fstab 檔中任何不正確或不必要的行，讓 VM 能夠正確啟動。

6. 將變更儲存至 fstab 檔案。

7. 使用下列命令重新開機 vm。
   
   ```
   # reboot -f
   ```
> [!Note]
   > 您也可以使用 "ctrl + x" 命令，這也會重新開機 vm。


8. 如果專案批註或修正成功，系統應該會在入口網站中到達 bash 提示字元。 檢查您是否可以連線至 VM。

### <a name="using-root-password"></a>使用根密碼

1. 連接到 [序列主控台](./serial-console-linux.md)。
2. 使用本機使用者和密碼登入系統。

   > [!Note]
   > 您無法在序列主控台中使用 SSH 金鑰來登入系統。

3. 尋找指出磁片未裝載的錯誤。 在下列範例中，系統嘗試連接的磁片已不存在：

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

4. 使用根密碼 (Red Hat 型 Vm) 來連線至 VM。

5. 使用您慣用的文字編輯器來開啟 fstab 檔案。 掛接磁片之後，請執行下列適用于 Nano 的命令：

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 檢查列出的檔案系統。 Fstab 檔案中的每一行都會指出當 VM 啟動時所掛接的檔案系統。 如需 fstab 檔語法的詳細資訊，請執行 man fstab 命令。 若要針對啟動失敗進行疑難排解，請檢查每一行，以確定它在結構和內容中都是正確的。

   > [!Note]
   > * 每一行的欄位會以定位字元或空格分隔。 空白行會遭到忽略。 具有數位記號的行 ( # ) ，因為第一個字元是批註。 批註的行可以保留在 fstab 檔中，但是不會進行處理。 建議您針對不確定的 fstab 行加上批註，而不是移除這些程式程式碼。
   > * 若要讓 VM 復原並啟動，檔案系統分割區應該是唯一需要的磁碟分割。 VM 可能會遇到有關其他批註分割區的應用程式錯誤。 不過，VM 應該會啟動，而不需要額外的磁碟分割。 您稍後可以將任何已加上批註的行取消批註。
   > * 建議您在 Azure Vm 上使用檔案系統分割區的 UUID 來掛接資料磁片。 例如，執行下列命令：``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 若要判斷檔案系統的 UUID，請執行 blkid 命令。 如需語法的詳細資訊，請執行 man blkid 命令。
   > * Nofail 選項可協助確保即使檔案系統已損毀，或啟動時檔案系統不存在，VM 也會啟動。 建議您在 fstab 檔案中使用 nofail 選項，以便在 VM 啟動時不需要的資料分割中發生錯誤時，讓啟動繼續進行。

7. 變更或批註 fstab 檔中任何不正確或不必要的行，讓 VM 能夠正確啟動。

8. 將變更儲存至 fstab 檔案。

9. 重新啟動虛擬機器。

10. 如果專案批註或修正成功，系統應該會在入口網站中到達 bash 提示字元。 檢查您是否可以連線至 VM。

11. 當您執行 mount – a 命令來測試任何 fstab 變更時，請檢查您的掛接點。 如果沒有任何錯誤，則掛接點應該是良好的。

## <a name="repair-the-vm-offline"></a>修復離線的 VM

1. 將 VM 的系統磁片以資料磁片的形式連結至復原 VM (任何正常運作的 Linux VM) 。 若要這樣做，您可以使用 [CLI 命令](./troubleshoot-recovery-disks-linux.md) ，也可以使用 [VM repair 命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)自動設定復原 VM。

2. 將系統磁片掛接為復原 VM 上的資料磁片之後，請在進行變更之前先備份 fstab 檔案，然後依照後續步驟來更正 fstab 檔案。

3. 尋找指出磁片未裝載的錯誤。 在下列範例中，系統嘗試連接的磁片已不存在：

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. 使用根密碼 (Red Hat 型 Vm) 來連線至 VM。

5. 使用您慣用的文字編輯器來開啟 fstab 檔案。 掛接磁片之後，請執行下列適用于 Nano 的命令。 確定您正在處理的 fstab 檔案位於裝載的磁片上，而不是在修復 VM 上的 fstab 檔案。

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 檢查列出的檔案系統。 Fstab 檔案中的每一行都會指出當 VM 啟動時所掛接的檔案系統。 如需 fstab 檔語法的詳細資訊，請執行 man fstab 命令。 若要針對啟動失敗進行疑難排解，請檢查每一行，以確定它在結構和內容中都是正確的。

   > [!Note]
   > * 每一行的欄位會以定位字元或空格分隔。 空白行會遭到忽略。 具有數位記號的行 ( # ) ，因為第一個字元是批註。 批註的行可以保留在 fstab 檔中，但是不會進行處理。 建議您針對不確定的 fstab 行加上批註，而不是移除這些程式程式碼。
   > * 若要讓 VM 復原並啟動，檔案系統分割區應該是唯一需要的磁碟分割。 VM 可能會遇到有關其他批註分割區的應用程式錯誤。 不過，VM 應該會啟動，而不需要額外的磁碟分割。 您稍後可以將任何已加上批註的行取消批註。
   > * 建議您在 Azure Vm 上使用檔案系統分割區的 UUID 來掛接資料磁片。 例如，執行下列命令：``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 若要判斷檔案系統的 UUID，請執行 blkid 命令。 如需語法的詳細資訊，請執行 man blkid 命令。 請注意，您想要復原的磁片現在已掛接在新的 VM 上。 雖然 Uuid 應該是一致的，但裝置磁碟分割識別碼 (例如，此 VM 上的 "/dev/sda1" ) 不同。 [使用 CLI 命令](./troubleshoot-recovery-disks-linux.md)的復原 VM 無法使用位於非系統 VHD 上的原始失敗 VM 檔案系統分割區。
   > * Nofail 選項可協助確保即使檔案系統已損毀，或啟動時檔案系統不存在，VM 也會啟動。 建議您在 fstab 檔案中使用 nofail 選項，以便在 VM 啟動時不需要的資料分割中發生錯誤時，讓啟動繼續進行。

7. 變更或批註 fstab 檔中任何不正確或不必要的行，讓 VM 能夠正確啟動。

8. 將變更儲存至 fstab 檔案。

9. 重新開機虛擬機器，或重建原始 VM。

10. 如果專案批註或修正成功，系統應該會在入口網站中到達 bash 提示字元。 檢查您是否可以連線至 VM。

11. 當您執行 mount – a 命令來測試任何 fstab 變更時，請檢查您的掛接點。 如果沒有任何錯誤，則掛接點應該是良好的。

12. 卸載並卸離原始虛擬硬碟，然後從原始系統磁片建立 VM。 若要這樣做，您可以使用 [CLI 命令](troubleshoot-recovery-disks-linux.md) 或 [VM 修復命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 來建立復原 VM。

13. 再次建立 VM，而且您可以透過 SSH 連線到 VM 之後，請採取下列動作：
    * 檢查復原期間變更或批註的任何 fstab 行。
    * 請確定您使用的是 UUID 和 nofail 選項。
    * 重新開機 VM 之前，請先測試任何 fstab 變更。 若要這樣做，請使用下列命令： ``$ sudo mount -a``
    * 建立已更正 fstab 檔案的額外複本，以用於未來的復原案例。

## <a name="next-steps"></a>接下來的步驟

* [使用 Azure CLI 2.0 將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解](./troubleshoot-recovery-disks-linux.md)
* [使用 Azure 入口網站將 OS 磁碟連結至復原 VM，以針對 Linux VM 進行疑難排解](./troubleshoot-recovery-disks-portal-linux.md)
