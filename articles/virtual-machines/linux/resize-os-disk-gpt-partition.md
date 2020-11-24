---
title: 調整具有 GPT 分割區的 OS 磁碟大小
description: 本文提供的指示說明如何調整在 Linux 中有 GUID 磁碟分割表格 (GPT) 磁碟分割的 OS 磁片大小。
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
ms.openlocfilehash: 3565b165c669af3566667d9bdfa401d15fcce101
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544151"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>調整具有 GPT 分割區的 OS 磁碟大小

> [!NOTE]
> 本文僅適用于具有 GUID 磁碟分割表格 (GPT) 磁碟分割的 OS 磁片。

本文說明如何在 Linux 中增加具有 GPT 分割區的 OS 磁碟大小。 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>識別 OS 磁碟是否有 MBR 或 GPT 磁碟分割

使用 `parted` 命令來識別磁碟分割是否已使用主開機記錄（ (MBR) 磁碟分割或 GPT 磁碟分割）建立。

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

若要增加 Ubuntu 16 中 OS 磁片的大小。*x* 和18。*x*：

1. 停止 VM。
1. 從入口網站增加 OS 磁碟的大小。
1. 重新開機 VM，然後以 **根** 使用者的身份登入 vm。
1. 確認 OS 磁碟現在顯示增加的檔案系統大小。

在下列範例中，OS 磁片已從入口網站調整為 100 GB。 / 上掛接的 **/dev/sda1** 檔案系統現在顯示 97 GB。

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

當 VM 重新開機時，請完成下列步驟：

1. 使用下列命令，以 **根** 使用者的身份存取您的 VM：

   ```
   # sudo -i
   ```

1. 使用下列命令來安裝 **growpart** 套件，您將使用此封裝來調整磁碟分割的大小：

   ```
   # zypper install growpart
   ```

1. 您 `lsblk` 可以使用命令來尋找裝載于檔案系統根目錄的磁碟分割 (**/**) 。 在此情況下，我們會看到裝置 **sda** 的磁碟分割4掛接在 **/** ：

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. 使用 `growpart` 上一個步驟中所決定的命令和資料分割編號，調整所需的磁碟分割大小：

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. 重新執行 `lsblk` 命令，以檢查是否已增加磁碟分割。

   下列輸出顯示 **/dev/sda4** 分割區已調整大小為 46.5 GB：
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. 使用命令搭配旗標來識別 OS 磁片上的檔案系統類型 `lsblk` `-f` ：

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. 根據檔案系統類型，使用適當的命令來調整檔案系統的大小。
   
   若為 **xfs**，請使用此命令：
   
   ```
   #xfs_growfs /
   ```
   
   範例輸出︰
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   若為 **ext4**，請使用此命令：
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. 使用下列命令，確認已增加 **df** 的檔案系統大小：
   
   ```
   #df -Thl
   ```
   
   範例輸出︰
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   在上述範例中，我們可以看到 OS 磁碟的檔案系統大小已增加。

### <a name="rhel-with-lvm"></a>RHEL 與 LVM

1. 使用下列命令，以 **根** 使用者的身份存取您的 VM：

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. 使用 `lsblk` 命令來判斷 (LV) 裝載在檔案系統 () 的根目錄上的邏輯磁片區 **/** 。 在此情況下，我們會看到 **rootvg-rootlv** 已掛接在上 **/** 。 如果您想要其他檔案系統，請在本文中取代 LV 和掛接點。

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. 檢查 LVM 磁片區群組中是否有可用空間 (VG 包含根磁碟分割的) 。 如果有可用空間，請跳至步驟12。

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   在此範例中，line **FREE PE/Size** 顯示磁片區群組中有 38.02 GB 可用。 在磁片區群組中新增空間之前，您不需要調整磁片的大小。

1. 在 RHEL 7 中增加作業系統磁片的大小。*x* 與 LVM：

   1. 停止 VM。
   1. 從入口網站增加 OS 磁碟的大小。
   1. 啟動 VM。

1. 當 VM 重新開機時，請安裝 **雲端公用程式-growpart** 套件以取得 `growpart` 命令，您需要增加 OS 磁片的大小。

      此套件已預先安裝在大部分的 Azure Marketplace 映射上。

      ```bash
      [root@dd-rhel7vm ~]# yum install cloud-utils-growpart
      ```

1. 使用命令，判斷哪個磁片和磁碟分割保存 LVM 實體磁片區或磁片區 (PV) 在名為 **rootvg** 的磁片區群組中 `pvscan` 。 請注意括弧 (**[** and **]**) 之間所列的大小和可用空間。

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. 使用確認磁碟分割的大小 `lsblk` 。 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 使用 `growpart` 、裝置名稱和資料分割編號，展開包含這個 PV 的資料分割。 這麼做會擴充指定的磁碟分割，以使用裝置上的所有可用連續空間。

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. 再次使用命令，確認磁碟分割的大小已調整為預期的大小 `lsblk` 。 請注意，在範例 **sda4** 中，已從 63 gb 變更為 95 gb。

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 展開 PV 以使用新展開之資料分割的其餘部分：

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. 確認 PV 的新大小是預期的大小，並將它與原始 **[大小/免費]** 值進行比較：

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. 依您想要的數量，展開 (LV) 所需的邏輯磁片區。 數量不需要是磁片區群組中的所有可用空間。 在下列範例中， **/dev/mapper/rootvg-rootlv** 會調整大小為 2 gb 到 12 gb (增加 10 gb 的) 。 此命令也會調整檔案系統的大小。

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   範例輸出︰

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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

1. 此 `lvresize` 命令會自動針對 LV 呼叫檔案系統的適當調整大小命令。 使用下列命令，檢查裝載于的 **/dev/mapper/rootvg-rootlv** 是否 **/** 具有增加的檔案系統大小：

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   範例輸出︰

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> 若要使用相同的程式來調整任何其他邏輯磁片區的大小，請變更步驟12中的 LV 名稱。

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>請一律先建立 VM 的快照集，再增加 OS 磁片大小。

若要增加 RHEL 原始磁碟分割中的 OS 磁片大小：

1. 停止 VM。
1. 從入口網站增加 OS 磁碟的大小。
1. 啟動 VM。

當 VM 重新開機時，請完成下列步驟：

1. 使用下列命令，以 **根** 使用者的身份存取您的 VM：
 
   ```
   sudo su
   ```

1. 安裝 **gptfdisk** 套件，您需要增加作業系統磁片的大小：

   ```
   yum install gdisk -y
   ```

1.  若要查看磁片上的所有可用磁區，請執行下列命令：
    ```
    gdisk -l /dev/sda
    ```

1. 您將會看到通知資料分割類型的詳細資料。 請確定它是 GPT。 識別根磁碟分割。 請勿變更或刪除開機磁碟分割 (BIOS 開機磁碟分割) 或系統磁碟分割 (EFI 系統磁碟分割) 。

1. 您可以使用此命令來啟動第一次的資料分割： 
    ```
    gdisk /dev/sda
    ```

1. 您會看到一則訊息，提示您輸入下一個命令： `Command: ? for help` 。 選取 **w** 鍵：

   ```
   w
   ```

1. 您將會收到下列訊息： `Warning! Secondary header is placed too early on the disk! Do you want to
correct this problem? (Y/N)` 。 選取 **Y** 鍵： 

   ```
   Y
   ```

1. 您應該會看到一則訊息，指出最後的檢查已完成，並提示確認。 選取 **Y** 鍵：

   ```
   Y
   ```

1. 使用 `partprobe` 命令來檢查所有專案是否都正確發生：

   ```
   partprobe
   ```

1. 您已完成先前的步驟，以確保次要 GPT 標頭放置於結尾。 接下來，使用工具再次開始調整大小的程式 `gdisk` 。 使用下列命令：

   ```
   gdisk /dev/sda
   ```
1. 在 [命令] 功能表中，選取 **p** 按鍵以查看分割區清單。 識別根磁碟分割。  (在這些步驟中， **sda2** 會被視為根磁碟分割。 ) 識別開機磁碟分割。  (在這些步驟中， **sda3** 會被視為開機分割區。 )  

   ```
   p
   ```
    ![顯示根磁碟分割和開機磁碟分割的螢幕擷取畫面。](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. 選取 **d** 鍵以刪除分割區。 然後選取指派給開機磁碟分割的資料分割編號。  (在此範例中為 **3**. ) 
   ```
   d
   3
   ```
1. 選取 **d** 鍵以刪除分割區。 選取指派給開機磁碟分割的資料分割編號。  (在此範例中為 **2**. ) 
   ```
   d
   2
   ```
    ![顯示刪除根和開機磁碟分割之步驟的螢幕擷取畫面。](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. 若要重新建立具有增加大小的根磁碟分割，請選取 [ **n** ] 機碼，然後輸入您先前為此範例中的根 (**2** 刪除的分割區編號) 。 選擇 `Default Value` 第一個磁區。 選擇 `Last sector value -  boot size sector` 此案例中的最後一個磁區 (`4096` ，對應至 2 MB 開機) 。 選擇 `8300` 十六進位碼。
   ```
   n
   2
   (Enter default)
   (Calculated value of Last sector value - 4096)
   8300
   ```
1. 若要重新建立開機磁碟分割，請選取 [ **n** ] 機碼，然後輸入您先前在此範例中針對開機 (**3** 所刪除的磁碟分割編號) 。 選擇 `Default Value` 第一個磁區和最後一個磁區。 選擇 `EF02` 十六進位碼。
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. 使用命令寫入變更 `w` ，然後選取 `Y` 以確認變更：
   ```
   w
   Y
   ```
1. 執行 `partprobe` 命令以檢查磁片穩定性：
   ```
   partprobe
   ```
1. 重新啟動 VM。 應增加根磁碟分割的大小。
   ```
   reboot
   ```

   ![顯示重新建立開機磁碟分割之步驟的螢幕擷取畫面。](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. `xfs_growfs`在磁碟分割上執行命令以調整其大小：
   ```
   xfs_growfs /dev/sda2
   ```

   ![顯示執行 xfs_growfs 之結果的螢幕擷取畫面。](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>後續步驟

- [調整磁碟大小](expand-disks.md)
