---
title: 使用 Azure CLI 管理 Azure 檔案共用備份
description: 瞭解如何使用 Azure CLI 來管理及監視由 Azure 備份備份的 Azure 檔案共用。
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44c095d58e2da5a74985ce216268aab15922ed1e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332741"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>使用 Azure CLI 管理 Azure 檔案共用備份

Azure CLI 提供用來管理 Azure 資源的命令列體驗。 它是建立自訂自動化以使用 Azure 資源的絕佳工具。 本文說明如何執行工作，以管理及監視 [Azure 備份](./backup-overview.md)所備份的 Azure 檔案共用。 您也可以使用 [Azure 入口網站](https://portal.azure.com/)來執行這些步驟。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版版或更新版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>Prerequisites

本文假設您已有 [Azure 備份](./backup-overview.md)所備份的 Azure 檔案共用。 如果您沒有帳戶，請參閱 [使用 CLI 備份 Azure 檔案共用](backup-afs-cli.md) ，以設定檔案共用的備份。 在本文中，您將使用下列資源：

* **資源群組**： *azurefiles*
* **RecoveryServicesVault**： *azurefilesvault*
* **儲存體帳戶**： *afsaccount*
* 檔案**共用**： *azurefiles*

## <a name="monitor-jobs"></a>監視工作

當您觸發備份或還原作業時，備份服務會建立追蹤的作業。 若要監視已完成或目前正在執行的作業，請使用 [az backup job list](/cli/azure/backup/job#az-backup-job-list) Cmdlet。 使用 CLI，您也可以 [暫停目前正在執行的工作](/cli/azure/backup/job#az-backup-job-stop) ，或 [等候直到作業完成為止](/cli/azure/backup/job#az-backup-job-wait)。

下列範例會顯示 *azurefilesvault* 復原服務保存庫的備份作業狀態：

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>修改原則

您可以使用 [az backup item set-policy](/cli/azure/backup/item#az-backup-item-set-policy)來修改備份原則，以變更備份頻率或保留範圍。

若要變更原則，請定義下列參數：

* **--容器-名稱**：裝載檔案共用的儲存體帳戶名稱。 若要取出容器的 **名稱** 或 **易記名稱** ，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--name**：您要變更原則的檔案共用名稱。 若要取出備份專案的 **名稱** 或 **易記名稱** ，請使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。
* **--policy-name**：您要為檔案共用設定的備份原則名稱。 您可以使用 [az 備份原則清單](/cli/azure/backup/policy#az-backup-policy-list) 來查看您保存庫的所有原則。

下列範例會針對*afsaccount*儲存體帳戶中存在的*azurefiles*檔案共用設定*schedule2*備份原則。

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

您也可以提供下列兩個額外的參數，藉由使用容器和專案的易記名稱來執行上一個命令：

* **--備份-管理-類型**： *azurestorage*
* **--工作負載類型**： *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務為您的變更原則作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有未來的備份作業並 *刪除* 所有復原點。
* 停止所有未來的備份作業，但 *保留* 復原點。

在儲存體中保留復原點可能會有相關聯的成本，因為 Azure 備份所建立的基礎快照將會保留。 保留復原點的好處是稍後視需要還原檔案共用的選項。 如需保留復原點成本的相關資訊，請參閱 [定價詳細資料](https://azure.microsoft.com/pricing/details/storage/files)。 如果您選擇刪除所有復原點，則無法還原檔案共用。

若要停止保護檔案共用，請定義下列參數：

* **--容器-名稱**：裝載檔案共用的儲存體帳戶名稱。 若要取出容器的 **名稱** 或 **易記名稱** ，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--專案名稱**：您要停止保護的檔案共用名稱。 若要取出備份專案的 **名稱** 或 **易記名稱** ，請使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

### <a name="stop-protection-and-retain-recovery-points"></a>停止保護並保留復原點

若要在保留資料時停止保護，請使用 [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) Cmdlet。

下列範例會停止 *azurefiles* 檔案共用的保護，但會保留所有復原點。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

您也可以提供下列兩個額外的參數，藉由使用容器和專案的易記名稱來執行上一個命令：

* **--備份-管理-類型**： *azurestorage*
* **--工作負載類型**： *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的停止保護作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

### <a name="stop-protection-without-retaining-recovery-points"></a>停止保護而不保留復原點

若要在不保留復原點的情況下停止保護，請在 [**刪除-備份-資料**] 選項設定為 [ **true**] 時，使用[az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) Cmdlet。

下列範例會停止保護 *azurefiles* 檔案共用，而不會保留復原點。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

您也可以提供下列兩個額外的參數，藉由使用容器和專案的易記名稱來執行上一個命令：

* **--備份-管理-類型**： *azurestorage*
* **--工作負載類型**： *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>繼續保護檔案共用

如果您停止保護 Azure 檔案共用，但保留復原點，您可以稍後再繼續保護。 如果您沒有保留復原點，就無法繼續保護。

若要繼續保護檔案共用，請定義下列參數：

* **--容器-名稱**：裝載檔案共用的儲存體帳戶名稱。 若要取出容器的 **名稱** 或 **易記名稱** ，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--專案名稱**：您要繼續保護的檔案共用名稱。 若要取出備份專案的 **名稱** 或 **易記名稱** ，請使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。
* **--policy-name**：您要為其繼續保護檔案共用的備份原則名稱。

下列範例會使用[az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume) Cmdlet，以使用*schedule1*備份原則來繼續保護*azurefiles*檔案共用。

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

您也可以提供下列兩個額外的參數，藉由使用容器和專案的易記名稱來執行上一個命令：

* **--備份-管理-類型**： *azurestorage*
* **--工作負載類型**： *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務為您的 resume 保護作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

## <a name="unregister-a-storage-account"></a>取消註冊儲存體帳戶

如果您想要使用不同的復原服務保存庫來保護特定儲存體帳戶中的檔案共用，請先 [停止保護](#stop-protection-on-a-file-share) 該儲存體帳戶中的所有檔案共用。 然後從目前用於保護的復原服務保存庫取消註冊帳戶。

您需要提供容器名稱以取消註冊儲存體帳戶。 若要取出容器的 **名稱** 或 **易記名稱** ，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。

下列範例會使用[az backup container 取消註冊](/cli/azure/backup/container#az-backup-container-unregister)Cmdlet，從*azurefilesvault*中取消註冊*afsaccount*儲存體帳戶。

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

您也可以藉由提供下列額外參數，來執行上一個 Cmdlet，方法是使用容器的易記名稱：

* **--備份-管理-類型**： *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱針對 [Azure 檔案共用備份進行疑難排解](troubleshoot-azure-files.md)。
