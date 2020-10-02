---
title: 備份與還原適用於 PostgreSQL 超大規模資料庫的 Azure 資料庫伺服器群組
description: 備份與還原適用於 PostgreSQL 超大規模資料庫的 Azure 資料庫伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4fb64a2ea55744d66b203ef4d901f22ae4695e1a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630418"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 啟用的于 postgresql 超大規模伺服器群組備份和還原

您可以對已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組進行完整備份/還原。 當您這樣做時，您的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組之所有節點上的整組資料庫都會進行備份及/或還原。
若要進行備份並還原，您必須確定已針對您的伺服器群組設定備份儲存類別。 目前，您必須在建立伺服器群組時指出備份儲存類別。 您還無法設定您的伺服器群組在建立之後使用備份儲存類別。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> Preview 不支援 Postgres 引擎11版的備份/還原。 它只支援 Postgres 12 版的備份/還原。

## <a name="verify-configuration"></a>驗證組態

首先，確認您現有的伺服器群組是否已設定為使用備份儲存類別。

設定伺服器群組的名稱之後，請執行下列命令：
```console
 azdata arc postgres server show -n postgres01
```
查看輸出的儲存體區段：
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
如果您在該命令的輸出的「備份」一節中看到儲存類別的名稱，表示您的伺服器群組已設定為使用備份儲存類別，並已準備好讓您進行備份並進行還原。 如果您沒有看到「備份」一節，您必須刪除並重新建立您的伺服器群組，以設定備份儲存類別。 此時，在建立伺服器群組之後，還無法設定備份儲存類別。

>[!IMPORTANT]
>如果您的伺服器群組已設定為使用備份儲存類別，請略過下一個步驟，並直接移至「進行手動完整備份」步驟。

## <a name="create-a-server-group"></a>建立伺服器群組 

接著，建立針對備份/還原設定的伺服器群組。

為了能夠進行備份和還原，您必須建立使用儲存類別設定的伺服器。

若要取得 Kubernetes 叢集中可用的儲存體類別清單，請執行下列命令：

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

例如，如果您已建立以 kubeadm 為基礎的簡單環境：
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>進行手動完整備份

接下來，進行手動完整備份。

若要完整備份您伺服器群組的整個資料和記錄檔資料夾，請執行下列命令：

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
其中：
- __名稱__ 表示備份的名稱
- __伺服器名稱__ 表示伺服器群組
- [__否-等候__] 表示命令列不會等候備份完成，讓您可以繼續使用此命令列視窗

此命令會在組成 Azure Arc enabled 于 postgresql 超大規模伺服器群組的所有節點之間協調分散式完整備份。 換句話說，它會備份您的協調器與背景工作節點中的所有資料。

例如：
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

當備份完成時，將會傳回備份的識別碼、名稱和狀態。 例如：
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> 尚未可能：
> - 排程自動備份
> - 顯示執行中的備份進度

## <a name="list-backups"></a>列出備份

列出可用來還原的備份。

若要列出可用來還原的備份，請執行下列命令：

```console
azdata arc postgres backup list --server-name <servergroup name>
```

例如：
```console
azdata arc postgres backup list --server-name postgres01
```

它會傳回輸出，如下所示：
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

時間戳記表示取得備份的時間點（UTC 時間點）。

## <a name="restore-a-backup"></a>還原備份

若要還原整個伺服器群組的備份，請執行下列命令：

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

其中：
- __備份識別碼__ 是清單備份命令中所顯示的備份識別碼 (請參閱步驟 3) 。
這會協調所有組成 Azure Arc enabled 于 postgresql 超大規模伺服器群組的節點之間的分散式完整還原。 換句話說，它會還原您的協調器和背景工作節點中的所有資料。

例如：
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

當還原作業完成時，它會傳回類似以下的輸出到命令列：
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> 尚未可能：
> - 藉由指示備份的名稱來還原備份
> - 以不同的名稱或不同的伺服器群組還原伺服器群組
> - 顯示還原作業的進度

## <a name="delete-backups"></a>刪除備份
無法在預覽中設定備份保留期。 不過，您可以手動刪除不需要的備份。
刪除備份的一般命令為：
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
其中：
- `--server-name` 這是使用者想要刪除備份的伺服器組名
- `--name` 這是要刪除的備份名稱
- `-id`這是要刪除之備份的識別碼

> [!NOTE]
> `--name` 和 `-id` 都是互斥的。

您可以執行 [列出備份] 命令（如前一段所述），以取出備份的名稱和識別碼。

例如，假設您已列出下列備份：
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
而您想要刪除第一個，請執行下列命令：
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
如果您要在該時間點列出備份，您會得到下列輸出：
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

如需刪除命令的詳細資訊，請執行：
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>後續步驟
- 瞭解如何相應放大 (在您的伺服器群組[) 新增背景工作節點](scale-out-postgresql-hyperscale-server-group.md)
- 深入瞭解向上 [或向下延展 (增加/減少記憶體/虛擬核心) ](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 伺服器群組
