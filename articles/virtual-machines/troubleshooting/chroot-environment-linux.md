---
title: Linux 修復 VM 中的 Chroot 環境。
description: 本文說明如何針對 Linux 中 (VM) 的 [修復虛擬機器] 中的 chroot 環境進行疑難排解。
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377933"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Linux 修復 VM 中的 Chroot 環境

本文說明如何針對 Linux 中 (VM) 的 [修復虛擬機器] 中的 chroot 環境進行疑難排解。

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 8.x && Ubuntu 18. x

1. 停止或解除配置受影響的 VM。
1. 使用受控磁片，在相同的資源群組中建立相同作業系統版本的修復 VM 映射 (RSG) 和位置。
1. 使用 Azure 入口網站取得受影響虛擬機器的 OS 磁片的快照集。
1. 從 OS 磁片的快照集建立磁片，並將它連接至修復 VM。
1. 建立磁片之後，請針對修復 VM 中的 chroot 環境進行疑難排解。

   1. 使用下列命令，以根使用者的身份存取 VM：

      `sudo su -`

   1. 使用 `dmesg` (您用來探索新磁片的方法，) 來找出磁片。 下列範例會使用 **dmesg** 來篩選 **SCSI** 磁片：

      `dmesg | grep SCSI`

      您的輸出會類似下列範例。 在此範例中，我們想要 **sdc** 磁片：

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 使用下列命令來存取 chroot 環境：

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. 針對 chroot 環境進行疑難排解。

   1. 使用下列命令來離開 chroot 環境：

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > 如果您收到錯誤 `unable to unmount /rescue` ，請將-l 選項新增至 umount 命令。
      >
      > 範例： `umount -l /rescue`

1. 從修復 VM 卸離磁片，並執行與原始 VM 的磁片交換。
1. 啟動原始 VM 並檢查其連線能力。

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/Centos/Oracle 6.x && Oracle 8.x && RHEL/Centos 7.x 與原始分割區

1. 停止或解除配置受影響的 VM。
1. 使用受控磁片，在相同的資源群組中建立相同作業系統版本的修復 VM 映射 (RSG) 和位置。
1. 使用 Azure 入口網站取得受影響虛擬機器的 OS 磁片的快照集。
1. 從 OS 磁片的快照集建立磁片，並將它連接至修復 VM。
1. 建立磁片之後，請針對修復 VM 中的 chroot 環境進行疑難排解。

   1. 使用下列命令，以根使用者的身份存取 VM：

      `sudo su -`

   1. 使用 `dmesg` (您用來探索新磁片的方法，) 來找出磁片。 下列範例會使用 **dmesg** 來篩選 **SCSI** 磁片：

      `dmesg | grep SCSI`

      您的輸出會類似下列範例。 在此範例中，我們想要 **sdc** 磁片：

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 使用下列命令來存取 chroot 環境：

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. 針對 chroot 環境進行疑難排解。

   1. 使用下列命令來離開 chroot 環境：

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > 如果您收到錯誤 `unable to unmount /rescue` ，請將-l 選項新增至 umount 命令。
      >
      > 範例： `umount -l /rescue`

1. 從修復 VM 卸離磁片，並執行與原始 VM 的磁片交換。
1. 啟動原始 VM 並檢查其連線能力。

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/Centos 7.x 與 LVM

   > [!NOTE]
   > 如果您的原始 VM 包含邏輯磁片區管理員 (LVM) 在 OS 磁片上，請在 OS 磁片上使用具有原始磁碟分割的映射來建立修復 VM。

1. 停止或解除配置受影響的 VM。
1. 使用受控磁片，在相同的資源群組中建立相同作業系統版本的修復 VM 映射 (RSG) 和位置。
1. 使用 Azure 入口網站取得受影響虛擬機器的 OS 磁片的快照集。
1. 從 OS 磁片的快照集建立磁片，並將它連接至修復 VM。
1. 建立磁片之後，請針對修復 VM 中的 chroot 環境進行疑難排解。

   1. 使用下列命令，以根使用者的身份存取 VM：

      `sudo su -`

   1. 使用 `dmesg` (您用來探索新磁片的方法，) 來找出磁片。 下列範例會使用 **dmesg** 來篩選 **SCSI** 磁片：

      `dmesg | grep SCSI`

      您的輸出會類似下列範例。 在此範例中，我們想要 **sdc** 磁片：

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 使用下列命令來啟用邏輯磁片區群組：

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. 使用 `lsblk` 命令來取出 lvm 名稱：

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. 使用下列命令來存取 chroot 環境：

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. 針對 chroot 環境進行疑難排解。

   1. 使用下列命令來離開 chroot 環境：

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > 如果您收到錯誤 `unable to unmount /rescue` ，請將-l 選項新增至 umount 命令。
      >
      > 範例： `umount -l /rescue`

1. 從修復 VM 卸離磁片，並執行與原始 VM 的磁片交換。
1. 啟動原始 VM 並檢查其連線能力。

## <a name="rhel-8x-with-lvm"></a>RHEL 8.x 與 LVM

   > [!NOTE]
   > 如果您的原始 VM 包含邏輯磁片區管理員 (LVM) 在 OS 磁片上，請在 OS 磁片上使用具有原始磁碟分割的映射來建立修復 VM。

1. 停止或解除配置受影響的 VM。
1. 使用受控磁片，在相同的資源群組中建立相同作業系統版本的修復 VM 映射 (RSG) 和位置。
1. 使用 Azure 入口網站取得受影響虛擬機器的 OS 磁片的快照集。
1. 從 OS 磁片的快照集建立磁片，並將它連接至修復 VM。
1. 建立磁片之後，請針對修復 VM 中的 chroot 環境進行疑難排解。

   1. 使用下列命令，以根使用者的身份存取 VM：

      `sudo su -`

   1. 使用 `dmesg` (您用來探索新磁片的方法，) 來找出磁片。 下列範例會使用 **dmesg** 來篩選 **SCSI** 磁片：

      `dmesg | grep SCSI`

      您的輸出會類似下列範例。 在此範例中，我們想要 **sdc** 磁片：

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 使用下列命令來啟用邏輯磁片區群組：

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. 使用 `lsblk` 命令來取出 lvm 名稱：

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. 使用下列命令來存取 chroot 環境：

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. 針對 chroot 環境進行疑難排解。

   1. 使用下列命令來離開 chroot 環境：

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > 如果您收到錯誤 `unable to unmount /rescue` ，請將-l 選項新增至 umount 命令。
      >
      > 範例： `umount -l /rescue`

1. 從修復 VM 卸離磁片，並執行與原始 VM 的磁片交換。
1. 啟動原始 VM 並檢查其連線能力。

## <a name="oracle-7x"></a>Oracle 7。x

1. 停止或解除配置受影響的 VM。
1. 使用受控磁片，在相同的資源群組中建立相同作業系統版本的修復 VM 映射 (RSG) 和位置。
1. 使用 Azure 入口網站取得受影響虛擬機器的 OS 磁片的快照集。
1. 從 OS 磁片的快照集建立磁片，並將它連接至修復 VM。
1. 建立磁片之後，請針對修復 VM 中的 chroot 環境進行疑難排解。

   1. 使用下列命令，以根使用者的身份存取 VM：

      `sudo su -`

   1. 使用 `dmesg` (您用來探索新磁片的方法，) 來找出磁片。 下列範例會使用 **dmesg** 來篩選 **SCSI** 磁片：

      `dmesg | grep SCSI`

      您的輸出會類似下列範例。 在此範例中，我們想要 **sdc** 磁片：

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 使用下列命令來存取 chroot 環境：

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. 針對 chroot 環境進行疑難排解。

   1. 使用下列命令來離開 chroot 環境：

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > 如果您收到錯誤 `unable to unmount /rescue` ，請將-l 選項新增至 umount 命令。
      >
      > 範例： `umount -l /rescue`

1. 從修復 VM 卸離磁片，並執行與原始 VM 的磁片交換。
1. 啟動原始 VM 並檢查其連線能力。

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4、SUSE-SLES 12 SP4 For SAP &&  # # SUSE-SLES 15 SP1、SUSE-SLES 15 SP1 For SAP

1. 停止或解除配置受影響的 VM。
1. 使用受控磁片，在相同的資源群組中建立相同作業系統版本的修復 VM 映射 (RSG) 和位置。
1. 使用 Azure 入口網站取得受影響虛擬機器的 OS 磁片的快照集。
1. 從 OS 磁片的快照集建立磁片，並將它連接至修復 VM。
1. 建立磁片之後，請針對修復 VM 中的 chroot 環境進行疑難排解。

   1. 使用下列命令，以根使用者的身份存取 VM：

      `sudo su -`

   1. 使用 `dmesg` (您用來探索新磁片的方法，) 來找出磁片。 下列範例會使用 **dmesg** 來篩選 **SCSI** 磁片：

      `dmesg | grep SCSI`

      您的輸出會類似下列範例。 在此範例中，我們想要 **sdc** 磁片：

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 使用下列命令來存取 chroot 環境：

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. 針對 chroot 環境進行疑難排解。

   1. 使用下列命令來離開 chroot 環境：

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > 如果您收到錯誤 `unable to unmount /rescue` ，請將-l 選項新增至 umount 命令。
      >
      > 範例： `umount -l /rescue`

1. 從修復 VM 卸離磁片，並執行與原始 VM 的磁片交換。
1. 啟動原始 VM 並檢查其連線能力。

## <a name="next-steps"></a>後續步驟

- [針對 ssh 連線進行疑難排解](troubleshoot-ssh-connection.md)
