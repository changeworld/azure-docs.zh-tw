---
title: 使用適用于 Azure NetApp Files Azure 應用程式一致的快照集工具進行還原 |Microsoft Docs
description: 提供執行 Azure 應用程式一致快照集工具（可搭配 Azure NetApp Files 使用）的 [還原] 命令的指南。
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632620"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 應用程式一致的快照集工具來還原 (預覽) 

本文提供的指南說明如何執行 Azure 應用程式一致的快照集工具（可搭配 Azure NetApp Files 使用）的 [還原] 命令。

## <a name="introduction"></a>簡介

從快照集執行磁片區還原是使用命令完成的 `azacsnap -c restore` 。

> [!IMPORTANT]
> 這不會執行資料庫復原，只會復原磁碟區 (s) ，如下列每個選項所述。

## <a name="command-options"></a>命令選項

此 `-c restore` 命令有下列選項：

- `--restore snaptovol` 根據目標磁片區上的最新快照集建立新的磁片區。 此命令會根據設定的目標磁片區，建立新的「複製」磁片區，並使用最新的磁片區快照集作為建立新磁片區的基底。  此命令不會中斷從主要到次要的儲存體複寫。 相反地，會在 DR 網站上建立最新可用的快照集，並顯示所複製磁片區的建議檔案系統掛接點。 此命令應該在 **DR 區域中** 的 Azure 大型實例系統上執行 (也就是目標容錯移轉系統) 。

- `--restore revertvolume` 根據最新的快照集，將目標磁片區還原為先前的狀態。  在 DR 容錯移轉的過程中，使用此命令作為成對的 DR 區域。 此命令會 **停止** 從主要網站複寫至次要網站，並將目標 dr 磁片區 (s) 還原至 dr 磁片區上的最新可用快照集，以及還原的 dr 磁片區建議的檔案系統掛接點。 此命令應該在 **DR 區域中** 的 Azure 大型實例系統上執行 (也就是目標容錯移轉系統) 。
    > [!NOTE]
    > 子命令 (`--restore revertvolume`) 僅適用于 Azure 大型實例，不適用於 Azure NetApp Files。
- `--hanasid <SAP HANA SID>` 是要從設定檔中選取的 SAP Hana SID，以套用磁片區還原命令。

- `[--configfile <config filename>]` 是選擇性參數，允許自訂設定檔案名稱。

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>執行測試 DR 容錯移轉 `azacsnap -c restore --restore snaptovol`

此命令就像是「完整」 DR 容錯移轉命令 (`--restore restorevolume`) ，而不是中斷主要網站與嚴重損壞修復網站之間的複寫，而是從嚴重損壞修復磁片區建立複製磁片區，以允許在 DR 網站中還原最新的快照集。 然後，客戶可以使用這些複製的磁片區來測試嚴重損壞修復，而不需要執行其 HANA 環境的完整容錯移轉，以中斷主要網站與嚴重損壞修復網站之間的複寫協定。

- 您可以用這種方式測試多個不同的還原點，每個還原點都有自己的還原點。
- 複製是由命令執行時的時間戳記所指定，並代表執行時最新的資料和其他可用的快照集。

> [!IMPORTANT]
> 此操作只適用于 Azure 大型實例。
>
> - 執行此命令時，必須先使用連絡人電子郵件來進行 liaise，然後再于4周後刪除複製。
> - 每次執行此命令時，將會建立新的複製品，當測試結束時，Microsoft 作業必須刪除此複本。
> - 系統會在4周後自動刪除任何已建立的複製磁片區。

例如，設定檔 (例如， `DR.json`) 只應包含 DR 磁片區，而非生產磁片區，否則生產磁片區可能會建立複製。

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol`Single-Node 案例的命令 (輸出) 

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> 各種案例的「依磁片區顯示掛接點」輸出不同。

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>執行完整的 DR 容錯移轉 `azacsnap -c restore --restore revertvolume`

此命令會 **停止** 從主要網站到次要網站的儲存體複寫，還原 dr 磁片區上的最新快照集，並提供 dr 磁片區的掛接點。

您必須在 DR 伺服器上使用設定檔來執行此命令 (例如， `DR.json` 使用 dr 磁片區) 。

藉由執行命令來執行容錯移轉至 DR 網站 `azacsnap -c restore --restore revertvolume` 。  此命令需要將 SID 新增為參數。 這是 HANA 實例的 SID，必須在 DR 網站上復原。

> [!IMPORTANT]
> 只有在您打算執行 DR 練習或測試時，才執行此命令。 此命令會中斷複寫。 您必須聯絡 Microsoft 作業，才能重新啟用複寫。

概括而言，以下是執行 DR 容錯移轉的步驟：

- 您必須關閉 **主要** 網站上的 HANA 實例。 只有當您真正執行容錯移轉至 DR 網站以避免資料不一致時，才需要執行此動作。
- 關閉 DR 節點上生產 SID 的 HANA 實例。
- 在 `azacsnap -c restore --restore revertvolume` 具有要復原之 SID 的 DR 節點上執行命令。
  - 此命令會中斷從主要複本到 DR 網站的儲存體複寫連結
  - 此命令會還原已設定的「資料」和「其他」磁片區。  通常，這項作業適用于 `/hana/data` 和檔案系統的磁片區 `/hana/logbackups` 。  `/hana/shared`系統不會復原檔案系統，而是會 `/hana/shared` 在 DR 位置使用現有的 for SID。
  - 掛接 `/hana/data` 和 `/hana/logbackups` 磁片區–確定已將其新增至 `/etc/fstab` 檔案
- 還原 HANA SYSTEMDB 快照。 HANA studio 只會顯示在快照命令執行期間還原的儲存體快照集下可用的最新 HANA 快照集 `azacsnap -c restore --restore revertvolume` 。
- 復原租使用者資料庫。
- 在 DR 網站上啟動生產 SID 的 HANA 實例， (範例：在此案例中為 H80) 。
- 執行任何資料庫測試。

## <a name="next-steps"></a>下一步

- [進行備份](azacsnap-cmd-ref-backup.md)
- [取得快照詳細資料](azacsnap-cmd-ref-details.md)
