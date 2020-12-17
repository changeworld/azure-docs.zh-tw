---
title: 使用適用于 Azure NetApp Files Azure 應用程式一致快照集工具的災難復原 |Microsoft Docs
description: 說明使用可搭配 Azure NetApp Files 使用 Azure 應用程式一致快照集工具時，如何執行嚴重損壞修復。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: c34ca08ae2ede9430804f6b8bb33f2bfcb0b39ab
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632610"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 應用程式一致的快照集工具 (預覽) 的嚴重損壞修復

本文說明如何在使用可搭配 Azure NetApp Files 使用 Azure 應用程式一致快照集工具時執行嚴重損壞修復。

> [!IMPORTANT]
> 此作業僅適用于 **Azure 大型實例** 。

## <a name="introduction"></a>簡介

Azure 大型實例平臺也可以設定災難復原網站，以便將存放磁片區快照集複寫至該網站。  如果已使用這類設定正確地設定快照集，則可以在此網站執行嚴重損壞修復。  本檔的目的是為此設定執行嚴重損壞修復的指南。

## <a name="prerequisites-for-disaster-recovery-setup"></a>嚴重損壞修復設定的必要條件

規劃嚴重損壞修復容錯移轉之前，必須先符合下列必要條件。

- 您有在 DR 網站上布建的 DR 節點。 DR 有兩個選項。 其中一個是一般 DR，另一個則是多用途 DR。
- 您有可用的儲存體複寫。 Microsoft 營運團隊會在 DR 布建時，自動執行儲存體複寫設定。 您可以使用 DR 網站上的命令來監視儲存體複寫 `azacsnap -c details --details replication` 。
- 您已在主要位置設定和設定儲存體快照集。
- 您已在 DR 網站上安裝 HANA 實例，其具有與主要實例相同的 SID。
- 您已閱讀並瞭解在[Azure 上 SAP Hana 大型實例高可用性和嚴重損壞修復](/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure)中所述的 DR 容錯移轉程式
- 您已在 DR 位置設定和設定儲存體快照集。
- 設定檔 (例如，已 `DR.json` 使用 dr 儲存體磁片區和 dr 伺服器上的相關資訊建立) 。
- 您已完成 DR 網站上的步驟，以：
  - 啟用與儲存體的通訊。
  - 啟用與 SAP Hana 的通訊。

## <a name="set-up-disaster-recovery"></a>設定災害復原

Microsoft 支援 DR 復原的儲存體層級複寫。 有兩種方式可以設定 DR。

其中一個是 **正常** 的，另一個則是多 **用途**。 在 **正常** 的 dr 中，您會在 dr 位置上有一個用於容錯移轉的專用實例。 在多 **用途** DR 案例中，您會在 dr 網站上的 hana 大型實例單位上執行另一個 QA 或開發 hana 實例。 但是，您也安裝了一個預先安裝的 HANA 實例，該實例會處於休眠狀態，且與您想要容錯移轉至該 HANA 大型實例單位的 HANA 實例具有相同的 SID。 Microsoft 作業會為您設定環境，包括以服務要求表單中提供的輸入為基礎的儲存體複寫， (SRF) 上線時。

> [!IMPORTANT]
> 確定已符合 DR 設定的所有必要條件。

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>監視從主要複本到 DR 網站的資料複寫

Microsoft 作業小組已管理並監視從主要網站到 DR 網站的 DR 連結。
您可以使用 snapshot 命令，監視從主伺服器到 DR 伺服器的資料複寫 `azacsnap -c details --details replication` 。

## <a name="perform-a-failover-to-dr-site"></a>執行容錯移轉至 DR 網站

在 DR 網站上執行容錯移轉命令 (`azacsnap -c restore --restore revertvolume`) 。

> [!IMPORTANT]
> 此 `azacsnap -c restore --restore revertvolume` 命令會中斷從生產網站到 DR 網站的儲存體複寫。 您必須與 Microsoft 作業聯繫，才能再次設定複寫。 重新啟用複寫之後，此 SID 的 DR 儲存體上的所有資料都會被初始化。 執行容錯移轉的命令會提供最近複寫的儲存體快照集。 如果您需要還原回較舊的快照集，請開啟支援要求，讓作業可以協助提供先前在 DR 網站中還原的快照集。

概括而言，以下是 DR 容錯移轉所需遵循的步驟：

- 您必須關閉 **主要** 網站上的 HANA 實例。 只有當您真正執行容錯移轉至 DR 網站，而不會有資料不一致時，才需要執行此動作。
- 關閉 DR 節點上生產 SID 的 HANA 實例。
- `azacsnap -c restore --restore revertvolume`在 DR 節點上執行包含要復原之 SID 的命令
  - 此命令會中斷從主要複本到 DR 網站的儲存體複寫連結
  - 此命令只會還原/data sources 和/logbackups 磁片區，/shared 磁片區不會復原，而是會在 DR 位置使用 SID 的現有/shared。
  - 裝載/data sources 和/logbackups 磁片區–確定將它新增至 fstab 檔案
- 還原 HANA SYSTEMDB 快照。 HANA studio 只會顯示在執行命令時還原的儲存體快照集下可用的最新 HANA 快照集 `azacsnap -c restore --restore revertvolume` 。
- 復原租使用者資料庫。
- 在 DR 網站上啟動生產 SID 的 HANA 實例， (範例：在此案例中為 H80) 。
- 執行測試。

### <a name="example-performing-disaster-recovery"></a>執行嚴重損壞修復的範例

本小節描述容錯移轉至嚴重損壞修復網站的詳細步驟。

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>步驟1：取得 DR 節點的磁片區詳細資料

執行命令 `df –h` 以列出容錯移轉之後要參考的檔案系統和相關聯的磁片區。

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>步驟2：關閉主要網站上的 HANA

如果執行生產工作負載的完整容錯移轉，而且可以連線到主要生產網站，然後關閉 SAP Hana 實例 (s) 容錯移轉至 DR。

例如，如果以 root 的身分登入，則下列範例會顯示如何關閉 SAP Hana。  <sid>以您的 SAP HANA SID 取代。

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>步驟3：關閉 DR 網站上的 HANA

在復原磁碟區之前，請務必先關閉 DR 網站上的 SAP Hana。

例如，如果以 root 的身分登入，則下列範例會顯示如何關閉 SAP Hana。  <sid>以您的 SAP HANA SID 取代。

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> 在還原任何磁片區之前，請確定 DR 網站上的 HANA 實例已離線。

#### <a name="step-4-restore-the-volumes"></a>步驟4：復原磁碟區

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_DR 容錯移轉命令的輸出_**。

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> 主控台顯示結尾的步驟需要採取，才能完成 DR 容錯移轉的儲存體準備。

#### <a name="step-5-unmount-unnecessary-filesystems"></a>步驟5：卸載不必要的檔案系統

執行命令 `umount` 以卸載不需要的檔案系統/磁片區。

```bash
umount <Mount point>
```

卸載資料和記錄備份掛接點。 您在相應放大案例中可能會有多個資料掛接點。

#### <a name="step-6-configure-the-mount-points"></a>步驟6：設定掛接點

修改檔案 `/etc/fstab` ，將主要 SID 的資料和記錄備份專案標記為批註 (在此範例中，SID = H80) 並新增從主要網站 DR 磁片區建立的新掛接點專案。 命令輸出中會提供新的掛接點專案。

- 使用下列字元將 DR 網站上執行的現有掛接點標記為批註 `#` ：

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- 將下列幾行新增至 `/etc/fstab`
  > 這應該是來自命令的相同輸出

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>步驟7：掛接復原磁片區

執行命令 `mount –a` 以掛接所有掛接點。

```bash
mount -a
```

現在，如果您執行， `df –h` 您應該會看到 `*_dp` 裝載的磁片區。

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>步驟8：復原 SYSTEMDB

在 HANA Studio 中，以滑鼠右鍵按一下 [SYSTEMDB 實例]，然後選擇 [備份與復原]，再選擇 [復原系統資料庫]

請參閱從快照集復原資料庫（尤其是 SYSTEMDB）的指南。

#### <a name="step-9-recover-the-tenant-database"></a>步驟9：復原租使用者資料庫

在 HANA Studio 中，以滑鼠右鍵按一下 [SYSTEMDB 實例]，然後選擇 [備份與復原]，再選擇 [復原租使用者資料庫]。

請參閱從快照集復原資料庫的指南，特別是 (s 的租使用者資料庫) 。

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>`azacsnap -c backup`在 DR 網站執行

如果您在 DR 網站執行以快照集為基礎的備份，則在 DR 網站的設定檔中設定的 HANA 伺服器名稱 `azacsnap` 應該與實際執行伺服器名稱相同。

> [!IMPORTANT]
> 執行 `azacsnap -c backup` 時，可以在 DR 網站上建立儲存體快照集，而不會自動將這些快照集複寫到另一個網站。  使用 Microsoft 作業，更清楚地瞭解將任何檔案或資料傳回原始生產網站。

## <a name="next-steps"></a>下一步

- [取得快照詳細資料](azacsnap-cmd-ref-details.md)
- [進行備份](azacsnap-cmd-ref-backup.md)
