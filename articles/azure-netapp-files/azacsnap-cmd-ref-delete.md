---
title: 使用適用于 Azure NetApp Files Azure 應用程式一致的快照集工具來刪除 |Microsoft Docs
description: 提供執行 Azure 應用程式一致快照集工具（可搭配 Azure NetApp Files 使用）的 [刪除] 命令的指南。
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
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632622"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 應用程式一致的快照集工具刪除 (預覽) 

本文提供的指南說明如何執行 Azure 應用程式一致的快照集工具（可搭配 Azure NetApp Files 使用）的 [刪除] 命令。

## <a name="introduction"></a>簡介

您可以使用命令刪除磁片區快照集和資料庫目錄專案 `azacsnap -c delete` 。

> [!IMPORTANT]
> 在執行此命令之前，建立的快照集不到10分鐘，因此不應該刪除，因為可能會干擾快照式複寫。

## <a name="command-options"></a>命令選項

此 `-c delete` 命令有下列選項：

- `--delete hana` 搭配選項使用時 `--hanasid <SID>` ， `--hanabackupid <HANA backup id>` 會從符合準則的 SAP Hana 備份類別目錄中刪除專案。

- `--delete storage` 搭配選項使用時， `--snapshot <snapshot name>` 會從後端儲存體系統刪除快照集。

- `--delete sync` 與選項搭配使用， `--hanasid <SID>` 並 `--hanabackupid <HANA backup id>` 從的備份類別目錄取得儲存體快照集名稱時 `<HANA backup id>` ，然後從包含已命名快照集的任何磁片區中刪除備份類別目錄 _和_ 快照集的專案。

- `--delete sync` 搭配使用時 `--snapshot <snapshot name>` ，會檢查備份類別目錄中的任何專案 `<snapshot name>` ，並取得 SAP Hana 的備份識別碼，並從包含已命名快照集的任何磁片區刪除備份類別目錄 _和_ 快照集內的專案。

- `[--force]` (選用) *使用時請謹慎*。  這種作業會強制刪除，而不提示確認。

- `[--configfile <config filename>]` 是選擇性參數，允許自訂設定檔案名稱。

### <a name="delete-a-snapshot-using-sync-option"></a>使用選項刪除快照集 `sync`

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 檢查備份類別目錄中是否有 SAP Hana 備份識別碼157979797979的任何專案、取得儲存體快照集名稱，以及刪除備份類別目錄中的專案，以及從包含已命名快照集的所有磁片區刪除快照集。

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> 檢查備份類別目錄中名為 hana_hourly .2020-01-22 _2358 的任何專案，並取得 SAP Hana 備份識別碼，並從包含已命名快照集的任何磁片區刪除備份類別目錄和快照集內的專案。

### <a name="delete-a-snapshot-using-hana-option"></a>使用選項刪除快照集 `hana`

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 從 SID H80 的備份類別目錄中刪除 SAP Hana 備份識別碼157979797979。

### <a name="delete-a-snapshot-using-storage-option"></a>使用選項刪除快照集 `storage`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> 從任何包含名為 hana_hourly .2020-01-22 _2358 之快照集的磁片區刪除快照集。

**使用選項的輸出 `--delete storage`**

系統會要求使用者確認刪除。

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

使用選擇性參數可以避免使用者確認，如下所示 `--force` ：

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>下一步

- [取得快照詳細資料](azacsnap-cmd-ref-details.md)
- [進行備份](azacsnap-cmd-ref-backup.md)
