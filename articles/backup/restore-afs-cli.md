---
title: 使用 Azure CLI 還原 Azure 檔案共用
description: 瞭解如何使用 Azure CLI 在復原服務保存庫中還原已備份的 Azure 檔案共用
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931041"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>使用 Azure CLI 還原 Azure 檔案共用

Azure CLI 提供管理 Azure 資源的命令列體驗。 這是建立自訂自動化以使用 Azure 資源的絕佳工具。 本文說明如何使用 Azure CLI，從[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)所建立的還原點還原整個檔案共用或特定檔案。 您也可以透過 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

在本文結尾，您將瞭解如何使用 Azure CLI 執行下列作業：

* 查看已備份 Azure 檔案共用的還原點。
* 還原完整的 Azure 檔案共用。
* 還原個別的檔案或資料夾。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>必要條件

本文假設您已經有 Azure 備份備份的 Azure 檔案共用。 如果您沒有帳戶，請參閱[使用 CLI 來備份 Azure 檔案共用](backup-afs-cli.md)，以設定檔案共用的備份。 在本文中，您會使用下列資源：

| 檔案共用  | 儲存體帳戶 | 地區 | 詳細資料                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | 使用 Azure 備份備份的原始來源                 |
| *azurefiles1* | *afaccount1*      | EastUS | 用於替代位置復原的目的地來源 |

您可以使用類似的檔案共用結構來嘗試本文中所述的不同類型還原。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>提取 Azure 檔案共用的復原點

使用[az backup new-recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) Cmdlet 列出備份檔案共用的所有復原點。

下列範例會在*afsaccount*儲存體帳戶中，提取*azurefiles*檔案共用的復原點清單。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

您也可以藉由提供下列兩個額外的參數，藉由使用容器和專案的易記名稱來執行上一個 Cmdlet：

* **--備份管理-類型**： *azurestorage*
* **--工作負載-類型**： *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

結果集是復原點的清單，其中包含每個還原點的時間和一致性詳細資料。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

輸出中的**名稱**屬性會對應至復原點名稱，可在復原作業中用來做為 **--rp-Name**參數的值。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 的完整共用復原

您可以使用此還原選項，在原始或替代位置中還原完整的檔案共用。

定義下列參數以執行還原作業：

* **--container-name**：主控已備份之原始檔案共用的儲存體帳戶名稱。 若要取得容器的名稱或易記名稱，請使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--專案名稱**：您要用於還原作業的已備份原始檔案共用名稱。 若要取出已備份專案的名稱或易記名稱，請使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="restore-a-full-share-to-the-original-location"></a>將完整共用還原至原始位置

當您還原至原始位置時，您不需要指定與目標相關的參數。 必須提供 [僅**解決衝突**]。

下列範例會使用[az backup restore azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) Cmdlet，並將還原模式設定為*originallocation* ，以在原始位置還原*azurefiles*檔案共用。 您會使用復原點932883129628959823，這是您在[Azure 檔案共用的提取復原點](#fetch-recovery-points-for-the-azure-file-share)中取得的：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

輸出中的**name**屬性會對應至備份服務針對您的還原作業所建立的工作名稱。 若要追蹤作業的狀態，請使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>將完整共用還原至替代位置

您可以使用此選項將檔案共用還原至替代位置，並保留原始的檔案共用。 針對替代位置復原指定下列參數：

* **--target-儲存體帳戶**：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目標-** 檔案共用：目標儲存體帳戶內的檔案共用，備份的內容會還原到該檔案中。
* **--target 資料夾**：要還原資料之檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **--resolve-衝突**：如果與還原的資料發生衝突，則為指令。 可接受的值為 **Overwrite** 或 **Skip**。

下列範例會使用[az backup restore azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) with 還原模式作為*alternatelocation* ，將*afsaccount*儲存體帳戶中的*azurefiles*檔案共用還原至*azurefiles1*儲存體帳戶中的*afaccount1 "* 檔案共用。

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

輸出中的**name**屬性會對應至備份服務針對您的還原作業所建立的工作名稱。 若要追蹤作業的狀態，請使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

## <a name="item-level-recovery"></a>項目層級復原

您可以使用此還原選項來還原原始或替代位置中的個別檔案或資料夾。

定義下列參數以執行還原作業：

* **--container-name**：主控已備份之原始檔案共用的儲存體帳戶名稱。 若要取得容器的名稱或易記名稱，請使用[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--專案名稱**：您要用於還原作業的已備份原始檔案共用名稱。 若要取出已備份專案的名稱或易記名稱，請使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

針對您要復原的專案指定下列參數：

* **SourceFilePath**：檔案共用中要還原之檔案的絕對路徑，以字串表示。 此路徑與[az storage file 下載](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download)或[az storage file show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI 命令中使用的路徑相同。
* **SourceFileType**：選擇是否選取目錄或檔案。 可接受的值為 **Directory** 或 **File**。
* **ResolveConflict**：如果與還原的資料發生衝突，則為指令。 可接受的值為 **Overwrite** 或 **Skip**。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>將個別檔案或資料夾還原至原始位置

使用[az backup restore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) Cmdlet 搭配還原模式設定為*originallocation* ，將特定檔案或資料夾還原至其原始位置。

下列範例會還原其原始位置中的*RestoreTest* ： *azurefiles*檔案共用。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的**name**屬性會對應至備份服務針對您的還原作業所建立的工作名稱。 若要追蹤作業的狀態，請使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>將個別檔案或資料夾還原至替代位置

若要將特定檔案或資料夾還原至替代位置，請使用[az backup restore restore-azurefiles 指令程式](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)，並將還原模式設定為*alternatelocation* ，並指定下列與目標相關的參數：

* **--target-儲存體帳戶**：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目標-** 檔案共用：目標儲存體帳戶內的檔案共用，備份的內容會還原到該檔案中。
* **--target 資料夾**：要還原資料之檔案共用下的資料夾。 如果備份的內容要還原至根資料夾，請將目的檔案夾的值指定為空字串。

下列範例會將*azurefiles*檔案共用中最初出現的*RestoreTest*還原至替代位置： *azurefiles1*檔案共用中裝載于*afaccount1*儲存體帳戶的*restoredata*資料夾。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的**name**屬性會對應至備份服務針對您的還原作業所建立的工作名稱。 若要追蹤作業的狀態，請使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 Azure CLI 來管理 Azure 檔案共用備份](manage-afs-backup-cli.md)。
