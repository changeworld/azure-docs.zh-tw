---
title: 使用 Azure CLI 來還原 Azure 檔案共用
description: 瞭解如何使用 Azure CLI 在復原服務保存庫中還原已備份的 Azure 檔案共用
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562316"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>使用 Azure CLI 來還原 Azure 檔案共用

Azure CLI 提供用來管理 Azure 資源的命令列體驗。 它是建立自訂自動化以使用 Azure 資源的絕佳工具。 本文說明如何使用 Azure CLI，從 [Azure 備份](./backup-overview.md) 所建立的還原點還原整個檔案共用或特定檔案。 您也可以透過 [Azure PowerShell](./backup-azure-afs-automation.md) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

在本文結束時，您將瞭解如何使用 Azure CLI 來執行下列作業：

* 查看已備份 Azure 檔案共用的還原點。
* 還原完整的 Azure 檔案共用。
* 還原個別的檔案或資料夾。

>[!NOTE]
> Azure 備份現在支援使用 Azure CLI 將多個檔案或資料夾還原到原始位置或替代位置。 若要深入瞭解，請參閱本檔的「將 [多個檔案或資料夾還原到原始或替代位置](#restore-multiple-files-or-folders-to-original-or-alternate-location) 」一節。

## <a name="prerequisites"></a>必要條件

本文假設您已經有 Azure 備份所備份的 Azure 檔案共用。 如果您沒有帳戶，請參閱 [使用 CLI 備份 Azure 檔案共用](backup-afs-cli.md) ，以設定檔案共用的備份。 在本文中，您將使用下列資源：

| 檔案共用 | 儲存體帳戶 | 區域 | 詳細資料 |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | 使用 Azure 備份備份的原始來源 |
| *azurefiles1* | *afaccount1* | EastUS | 用於替代位置復原的目的地來源 |

您可以針對檔案共用使用類似的結構，以試用本文中所述的不同類型的還原。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - 本教學課程需要 Azure CLI 的版本2.0.18 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>提取 Azure 檔案共用的復原點

使用 [az backup new-recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) Cmdlet 來列出已備份檔案共用的所有復原點。

下列範例會針對 *afsaccount* 儲存體帳戶中的 *azurefiles* 檔案共用，提取復原點的清單。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

您也可以提供下列兩個額外的參數，藉由使用容器和專案的易記名稱來執行上一個 Cmdlet：

* **--備份-管理-類型** ： *azurestorage*
* **--工作負載類型** ： *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

結果集是包含每個還原點的時間與一致性詳細資料的復原點清單。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

輸出中的 **Name** 屬性會對應至復原點名稱，此名稱可作為復原操作中 **--rp-Name** 參數的值。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 的完整共用復原

您可以使用此還原選項來還原原始或替代位置中的完整檔案共用。

定義下列參數以執行還原作業：

* **--容器-名稱** ：裝載已備份之原始檔案共用的儲存體帳戶名稱。 若要取出容器的名稱或易記名稱，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--專案名稱** ：您要用於還原作業的已備份原始檔案共用名稱。 若要取出備份專案的名稱或易記名稱，請使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

### <a name="restore-a-full-share-to-the-original-location"></a>將完整共用還原至原始位置

當您還原到原始位置時，您不需要指定目標相關的參數。 必須只提供 **解決衝突** 。

下列範例會使用 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) Cmdlet，並將還原模式設定為 *originallocation* ，以還原原始位置中的 *azurefiles* 檔案共用。 您可以使用在 [Azure 檔案共用的提取復原點](#fetch-recovery-points-for-the-azure-file-share)中取得的復原點932883129628959823：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的還原作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>將完整共用還原至替代位置

您可以使用此選項將檔案共用還原至替代位置，並保留原始的檔案共用。 指定下列參數進行替代位置復原：

* **--目標-儲存體-帳戶** ：還原備份內容的儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目標-** 檔案共用：要還原備份內容之目標儲存體帳戶內的檔案共用。
* **--目的檔案夾** ：要在其中還原資料之檔案共用底下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **--resolve-衝突** ：如果與還原的資料發生衝突，則為指令。 可接受的值為 **Overwrite** 或 **Skip** 。

下列範例會使用 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) with restore mode as *alternatelocation* ，將 *afsaccount* 儲存體帳戶中的 *azurefiles* 檔案共用還原至 *azurefiles1* 儲存體帳戶中的 *afaccount1 "* 檔案共用。

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的還原作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

## <a name="item-level-recovery"></a>項目層級復原

您可以使用此還原選項來還原原始或替代位置中的個別檔案或資料夾。

定義下列參數以執行還原作業：

* **--容器-名稱** ：裝載已備份之原始檔案共用的儲存體帳戶名稱。 若要取出容器的名稱或易記名稱，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--專案名稱** ：您要用於還原作業的已備份原始檔案共用名稱。 若要取出備份專案的名稱或易記名稱，請使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

針對您想要復原的專案指定下列參數：

* **SourceFilePath** ：檔案的絕對路徑，會在檔案共用內以字串的形式還原。 此路徑與 [az storage file 下載](/cli/azure/storage/file#az-storage-file-download) 或 [az storage file show](/cli/azure/storage/file#az-storage-file-show) CLI 命令中使用的路徑相同。
* **SourceFileType** ：選擇是否選取目錄或檔案。 接受 **目錄** 或 **檔案** 。
* **ResolveConflict** ：如果與還原的資料發生衝突，則為指令。 可接受的值為 **Overwrite** 或 **Skip** 。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>將個別檔案或資料夾還原到原始位置

使用 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) Cmdlet，並將還原模式設定為 *originallocation* ，以將特定檔案或資料夾還原至其原始位置。

下列範例會在其原始位置還原 *RestoreTest.txt* 檔案： *azurefiles* 檔案共用。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的還原作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>將個別檔案或資料夾還原到替代位置

若要將特定檔案或資料夾還原到替代位置，請使用 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) Cmdlet，並將 restore 模式設定為 *alternatelocation* ，並指定下列目標相關參數：

* **--目標-儲存體-帳戶** ：還原備份內容的儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目標-** 檔案共用：要還原備份內容之目標儲存體帳戶內的檔案共用。
* **--目的檔案夾** ：要在其中還原資料之檔案共用底下的資料夾。 如果備份的內容要還原至根資料夾，請將目的檔案夾的值指定為空字串。

下列範例會將最初存在於 *azurefiles* 檔案共用中的 *RestoreTest.txt* 檔案還原至替代位置： *afaccount1* 儲存體帳戶所裝載 *azurefiles1* 檔案共用中的 *restoredata* 資料夾。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的還原作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>將多個檔案或資料夾還原到原始位置或替代位置

若要執行多個專案的還原，請將 **來源檔案路徑** 參數的值傳遞為您想要還原之所有檔案或資料夾的 **空格分隔** 路徑。

下列範例會在其原始位置中 Report.docx檔案還原 *Restore.txt* 和 *AFS 測試* 。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

輸出將類似於：

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的還原作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

如果您想要將多個專案還原到替代位置，請使用上述命令，方法是指定目標相關參數，如將 [個別檔案或資料夾還原到替代位置](#restore-individual-files-or-folders-to-an-alternate-location) 一節中所述。

## <a name="next-steps"></a>後續步驟

瞭解如何 [使用 Azure CLI 管理 Azure 檔案共用備份](manage-afs-backup-cli.md)。
