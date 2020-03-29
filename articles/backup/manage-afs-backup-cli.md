---
title: 使用 Azure CLI 管理 Azure 檔共用備份
description: 瞭解如何使用 Azure CLI 管理和監視 Azure 備份備份的 Azure 檔共用。
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934882"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>使用 Azure CLI 管理 Azure 檔共用備份

Azure CLI 提供了用於管理 Azure 資源的命令列體驗。 它是構建自訂自動化以使用 Azure 資源的絕佳工具。 本文介紹如何執行用於管理和監視 Azure[備份](https://docs.microsoft.com/azure/backup/backup-overview)備份的 Azure 檔共用的任務。 還可以使用[Azure 門戶](https://portal.azure.com/)執行這些步驟。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

要在本地安裝和使用 CLI，必須運行 Azure CLI 版本 2.0.18 或更高版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>Prerequisites

本文假定您已經擁有[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)備份的 Azure 檔共用。 如果沒有，請參閱[使用 CLI 備份 Azure 檔共用](backup-afs-cli.md)，以設定檔共用的備份。 在本文中，您可以使用以下資源：

* **資源組** *：azure 檔*
* **恢復服務Vault**： *azure 檔庫*
* **存儲帳戶** *：afs 帳戶*
* **檔共用**： *azure 檔*

## <a name="monitor-jobs"></a>監視工作

觸發備份或還原操作時，備份服務將創建用於跟蹤的作業。 要監視已完成或當前正在運行的作業，請使用[az 備份作業清單](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list)Cmdlet。 使用 CLI，您還可以[掛起當前正在運行的作業](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop)或[等待作業完成](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)。

下面的示例顯示*azurefilevault*恢復服務保存庫的備份作業的狀態：

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

您可以使用[az 備份項集策略](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)修改備份策略以更改備份頻率或保留範圍。

要更改策略，請定義以下參數：

* **--容器名稱**：承載檔共用的存儲帳戶的名稱。 若要檢索容器**的名稱**或**易記名稱**，請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--名稱**：要為其更改策略的檔共用的名稱。 若要檢索備份項**的名稱**或**易記名稱**，請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--策略名稱**：要為檔共用設置的備份策略的名稱。 您可以使用[az 備份策略清單](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list)查看保存庫的所有策略。

下面的示例設置*afs 帳戶*存儲帳戶中存在的*azure 檔*共用*的計畫2*備份策略。

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

還可以通過使用容器和項的易記名稱來運行上一個命令，提供以下兩個附加參數：

* **--備份管理類型** *：azure 存儲*
* **--工作負載類型** *：azure 檔共用*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

輸出中的**Name**屬性對應于由更改策略操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有將來的備份作業並*刪除*所有復原點。
* 停止所有將來的備份作業，但*離開*復原點。

將復原點留在存儲中可能會產生相關成本，因為 Azure 備份創建的基礎快照將保留。 離開復原點的好處是選擇以後恢復檔共用（如果需要）。 有關離開復原點的成本的資訊，請參閱[定價詳細資訊](https://azure.microsoft.com/pricing/details/storage/files)。 如果選擇刪除所有復原點，則無法還原檔共用。

要停止對檔共用的保護，請定義以下參數：

* **--容器名稱**：承載檔共用的存儲帳戶的名稱。 若要檢索容器**的名稱**或**易記名稱**，請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**：要停止保護的檔共用的名稱。 若要檢索備份項**的名稱**或**易記名稱**，請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="stop-protection-and-retain-recovery-points"></a>停止保護並保留復原點

要在保留資料時停止保護，請使用[az 備份保護禁用](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)Cmdlet。

以下示例停止保護 azure*檔*共用，但保留所有復原點。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

還可以通過提供以下兩個附加參數來運行前面的命令，使用容器和項的易記名稱：

* **--備份管理類型** *：azure 存儲*
* **--工作負載類型** *：azure 檔共用*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

輸出中的**Name**屬性對應于由停止保護操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

### <a name="stop-protection-without-retaining-recovery-points"></a>停止保護而不保留復原點

要停止保護而不保留復原點，請使用[az 備份保護禁用](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)Cmdlet，**刪除備份資料**選項設置為**true**。

以下示例停止保護 azure*檔*共用而不保留復原點。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

還可以通過提供以下兩個附加參數來運行前面的命令，使用容器和項的易記名稱：

* **--備份管理類型** *：azure 存儲*
* **--工作負載類型** *：azure 檔共用*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>繼續保護檔案共用

如果停止對 Azure 檔共用的保護，但保留了復原點，則可以稍後恢復保護。 如果不保留復原點，則無法恢復保護。

要恢復檔共用的保護，請定義以下參數：

* **--容器名稱**：承載檔共用的存儲帳戶的名稱。 若要檢索容器**的名稱**或**易記名稱**，請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**：要為其恢復保護的檔共用的名稱。 若要檢索備份項**的名稱**或**易記名稱**，請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--策略名稱**：要為其恢復檔共用保護的備份策略的名稱。

下面的示例使用[az 備份保護恢復](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume)Cmdlet 使用*計畫1*備份策略恢復*對 azure 檔*共用的保護。

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

還可以通過提供以下兩個附加參數來運行前面的命令，使用容器和項的易記名稱：

* **--備份管理類型** *：azure 存儲*
* **--工作負載類型** *：azure 檔共用*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

輸出中的**Name**屬性對應于備份服務為恢復保護操作創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

## <a name="unregister-a-storage-account"></a>取消註冊存儲帳戶

如果要使用不同的恢復服務保存庫來保護特定存儲帳戶中的檔共用，請首先停止保護該存儲帳戶[中的所有檔共用](#stop-protection-on-a-file-share)。 然後從當前用於保護的恢復服務保存庫中取消註冊帳戶。

您需要提供容器名稱以取消註冊存儲帳戶。 若要檢索容器**的名稱**或**易記名稱**，請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。

以下示例使用[az 備份容器取消註冊](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)Cmdlet 從*azure 檔庫*取消註冊*afs 帳戶*存儲帳戶。

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

還可以通過提供以下附加參數，使用容器的易記名稱運行以前的 Cmdlet：

* **--備份管理類型** *：azure 存儲*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱排除[Azure 檔共用備份的疑難排解](troubleshoot-azure-files.md)。
