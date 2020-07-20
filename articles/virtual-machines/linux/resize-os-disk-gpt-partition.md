---
title: 調整具有 GPT 分割區的 OS 磁碟大小 |Microsoft Docs
description: 本文提供調整具有 GPT 分割區的 OS 磁碟大小的指示。
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 7c408e8e29b3f9ac423a6104c40242f11f93a171
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651083"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>調整具有 GPT 分割區的 OS 磁碟大小

> [!NOTE]
> 此案例僅適用於具有 GUID 磁碟分割表格 (GPT) 分割區的 OS 磁碟。

本文說明如何在 Linux 中增加具有 GPT 分割區的 OS 磁碟大小。 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>識別 OS 磁碟是否有 MBR 或 GPT 磁碟分割

使用 **parted** 命令來識別磁碟分割區是使用主開機記錄 (MBR) 磁碟分割或 GPT 磁碟分割而建立。

### <a name="mbr-partition"></a>MBR 磁碟分割

在下列輸出中，**Partition Table** 顯示 **msdos.sys** 值。 此值表示 MBR 磁碟分割。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT 磁碟分割

在下列輸出中，**Partition Table** 顯示 **gpt** 值。 此值表示 GPT 磁碟分割。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

如果您的虛擬機器 (VM) 具有 OS 磁碟上的 GPT 分割區，請增加 OS 磁碟的大小。

## <a name="increase-the-size-of-the-os-disk"></a>增加 OS 磁碟的大小

下列指示適用於 Linux 背書的發行版本。

> [!NOTE]
> 在繼續之前，請先建立 VM 的備份複本，或製作 OS 磁碟的快照。

### <a name="ubuntu"></a>Ubuntu

增加 Ubuntu 16. x 和 18. x 中的 OS 磁碟大小：

1. 停止 VM。
1. 從入口網站增加 OS 磁碟的大小。
1. 重新開機 VM，然後以**根**使用者的身分登入 VM。
1. 確認 OS 磁碟現在顯示增加的檔案系統大小。

如下列範例所示，已從入口網站將 OS 磁碟調整為 100 GB。 / 上掛接的 **/dev/sda1** 檔案系統現在顯示 97 GB。

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

增加 SUSE 12 SP4、SUSE SLES 12 for SAP、SUSE SLES 15、SUSE SLES 15 for SAP 中的 OS 磁碟大小：

1. 停止 VM。
1. 從入口網站增加 OS 磁碟的大小。
1. 重新啟動 VM。

當 VM 重新開機後，執行下列步驟：

   1. 使用下列命令，以**根**使用者身分存取您的 VM：
   
      `#sudo su`

   1. 使用下列命令安裝 **gptfdisk** 套件，這是增加 OS 磁碟大小所需的套件：

      `#zypper install gptfdisk -y`

   1. 若要檢視磁碟上可用的最大磁區，請執行下列命令：

      `#sgdisk -e /dev/sda`

   1. 使用下列命令來調整分割區的大小，而不將其刪除。 **parted** 命令有一個 **resizepart** 選項，可在不刪除分割區的情況下調整其大小。 **resizepart** 後的數字 4 表示調整第四個分割區的大小。

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. 執行 **#lsblk** 命令，檢查分割區是否已增加。

      下列輸出顯示 **/dev/sda4** 分割區的大小已調整為 98.5 GB。

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. 使用下列命令，識別 OS 磁碟上的檔案系統類型：

      `blkid`

      範例輸出︰

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. 根據檔案系統類型，使用適當的命令來調整檔案系統的大小。

      針對 **xfs**，使用下列命令：

      ` #xfs_growfs /`

      範例輸出︰

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      針對 **ext4**，使用下列命令：

      ```#resize2fs /dev/sda4```

   1. 使用下列命令，確認 **df -Th** 增加的檔案系統大小：

      `#df -Th`

      範例輸出︰

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

在上述範例中，我們可以看到 OS 磁碟的檔案系統大小已增加。

### <a name="rhel"></a>RHEL

使用 LVM 增加 RHEL 7.x 中的 OS 磁碟大小：

1. 停止 VM。
1. 從入口網站增加 OS 磁碟的大小。
1. 啟動 VM。

當 VM 重新開機後，執行下列步驟：

   1. 使用下列命令，以**根**使用者身分存取您的 VM：
   
      `#sudo su`

   1. 安裝 **gptfdisk** 套件，這是增加 OS 磁碟大小所需的套件。

      `#yum install gdisk -y`

   1. 若要查看磁碟上可用的最大磁區，請執行下列命令：

      `#sgdisk -e /dev/sda`

   1. 使用下列命令來調整分割區的大小，而不將其刪除。 **parted** 命令有一個 **resizepart** 選項，可在不刪除分割區的情況下調整其大小。 **resizepart** 後的數字 4 表示調整第四個分割區的大小。

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. 執行下列命令，以確認分割區大小是否已增加：

      `#lsblk`

      下列輸出顯示 **/dev/sda4** 分割區的大小已調整為 99 GB。

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. 使用下列命令來調整實體磁碟區 (PV) 的大小：

      `#pvresize /dev/sda4`

      下列輸出顯示 PV 大小已調整為 99.02 GB。

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. 在下列範例中，會使用下列命令將 **/dev/mapper/rootvg-rootlv** 的大小從 2 GB 調整到 12 GB (增加 10 GB)。 此命令也會調整檔案系統的大小。

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      範例輸出︰

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
      Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
      Logical volume rootvg/rootlv successfully resized.
      meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=0 spinodes=0
      data     =                       bsize=4096   blocks=524288, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 524288 to 3145728
      ```
         
   1. 使用下列命令，確認 **/dev/mapper/rootvg-rootlv** 是否具有增加的檔案系統大小：

      `#df -Th /`

      範例輸出︰

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

   > [!NOTE]
   > 若要使用相同程序來調整任何其他邏輯磁碟區的大小，請變更步驟 7 中的 **lv** 名稱。

## <a name="next-steps"></a>後續步驟

- [調整磁碟大小](expand-disks.md)