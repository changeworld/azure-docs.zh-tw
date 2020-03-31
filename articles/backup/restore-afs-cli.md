---
title: 與 Azure CLI 還原 Azure 檔共用
description: 瞭解如何使用 Azure CLI 在恢復服務保存庫中還原備份的 Azure 檔共用
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931041"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>與 Azure CLI 還原 Azure 檔共用

Azure CLI 提供了用於管理 Azure 資源的命令列體驗。 它是構建自訂自動化以使用 Azure 資源的絕佳工具。 本文介紹如何使用 Azure CLI 從[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)創建的還原點還原整個檔共用或特定檔。 您也可以透過 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

在本文結束時，您將瞭解如何使用 Azure CLI 執行以下操作：

* 查看備份的 Azure 檔共用的還原點。
* 還原完整的 Azure 檔共用。
* 還原單個檔或資料夾。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>Prerequisites

本文假定您已經擁有由 Azure 備份備份的 Azure 檔共用。 如果沒有，請參閱[使用 CLI 備份 Azure 檔共用](backup-afs-cli.md)，以設定檔共用的備份。 在本文中，您可以使用以下資源：

| 檔案共用  | 儲存體帳戶 | 區域 | 詳細資料                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azure 檔*  | *阿夫斯帳戶*      | EastUS | 使用 Azure 備份備份的原始源                 |
| *azure 檔1* | *帳戶1*      | EastUS | 用於備用位置恢復的目標源 |

您可以為檔共用使用類似的結構來嘗試本文仲介紹的不同類型的還原。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>獲取 Azure 檔共用的復原點

使用[az 備份復原點清單](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list)Cmdlet 列出備份檔案共用的所有復原點。

下面的示例獲取*afs 帳戶*存儲帳戶中*azure 檔*共用的復原點清單。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

還可以通過使用容器和項的易記名稱運行以前的 Cmdlet，提供以下兩個附加參數：

* **--備份管理類型** *：azure 存儲*
* **--工作負載類型** *：azure 檔共用*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

結果集是具有每個還原點時間和一致性詳細資訊的復原點的清單。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

輸出中的**Name**屬性對應于復原點名稱，該名稱可用作恢復操作中 **--rp-name**參數的值。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 完全恢復共用

可以使用此還原選項在原始位置或備用位置還原完整的檔共用。

定義以下參數以執行還原操作：

* **--容器名稱**：承載備份的原始檔案共用的存儲帳戶的名稱。 若要檢索容器的名稱或易記名稱，請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**：要用於還原操作的備份原始檔案共用的名稱。 若要檢索備份項的名稱或易記名稱，請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="restore-a-full-share-to-the-original-location"></a>將完整共用還原到原始位置

還原到原始位置時，不需要指定與目標相關的參數。 只能提供 **"解決衝突**"。

下面的示例使用[az 備份還原還原-azure 檔共用](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)Cmdlet，還原模式設置為*原始位置*，以還原原始位置中的*azure 檔*共用。 您可以使用復原點 932883129628959823，在[Azure 檔共用的 Fetch 復原點](#fetch-recovery-points-for-the-azure-file-share)中獲取該復原點：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

輸出中的**Name**屬性對應于還原操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>將完整共用還原到備用位置

可以使用此選項將檔共用還原到備用位置，並保持原始檔案共用原樣。 為備用位置恢復指定以下參數：

* **--目標存儲帳戶**：還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目的檔案共用**：還原備份內容的目標存儲帳戶中的檔共用。
* **--目的檔案夾**：還原資料的檔共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **--解決衝突**：如果與還原的資料發生衝突，則指示。 可接受的值為 **Overwrite** 或 **Skip**。

下面的示例使用[az 備份還原還原-azure 檔共用](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)與還原模式作為*備用位置*，以將*afs 帳戶*存儲帳戶中的*azure 檔*共用還原到 afaccount1 存儲帳戶中的*azurefile1"* 檔共用。 *afaccount1*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

輸出中的**Name**屬性對應于還原操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

## <a name="item-level-recovery"></a>項目層級復原

您可以使用此還原選項還原原始位置或備用位置中的單個檔或資料夾。

定義以下參數以執行還原操作：

* **--容器名稱**：承載備份的原始檔案共用的存儲帳戶的名稱。 若要檢索容器的名稱或易記名稱，請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**：要用於還原操作的備份原始檔案共用的名稱。 若要檢索備份項的名稱或易記名稱，請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

為要恢復的項指定以下參數：

* **SourceFilePath**：檔的絕對路徑，將在檔共用中作為字串還原。 此路徑與[az 存儲檔下載](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download)中使用的路徑相同，[或 az 存儲檔顯示](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show)CLI 命令。
* **源檔案類型**：選擇是選擇目錄還是檔。 接受**目錄**或**檔**。
* **解決衝突**：如果與還原的資料發生衝突，則說明。 可接受的值為 **Overwrite** 或 **Skip**。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>將單個檔或資料夾還原到原始位置

使用[az 備份還原還原-azure 檔](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)Cmdlet，還原模式設置為*原始位置*，將特定檔或資料夾還原到其原始位置。

以下示例還原*還原還原測試.txt*檔的原始位置 *：azurefile*檔共用。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的**Name**屬性對應于還原操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>將單個檔或資料夾還原到備用位置

要將特定檔或資料夾還原到備用位置，請使用[az 備份還原還原-azure 檔](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)Cmdlet，還原模式設置為*備用位置*，並指定以下目標相關參數：

* **--目標存儲帳戶**：還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目的檔案共用**：還原備份內容的目標存儲帳戶中的檔共用。
* **--目的檔案夾**：還原資料的檔共用下的資料夾。 如果要將備份的內容還原到根資料夾，請將目的檔案夾的值作為空字串。

以下示例將最初存在於*azure 檔*共用中的*還原測試.txt*檔案還原到備用位置：在*afaccount1*存儲帳戶中託管的*azurefile1*檔共用中的*還原資料*資料夾。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的**Name**屬性對應于還原操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

## <a name="next-steps"></a>後續步驟

瞭解如何使用[Azure CLI 管理 Azure 檔共用備份](manage-afs-backup-cli.md)。
