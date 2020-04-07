---
title: 與 Azure CLI 還原 Azure 檔案分享
description: 瞭解如何使用 Azure CLI 在回復服務保管庫中還原備份的 Azure 檔案分享
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757102"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>與 Azure CLI 還原 Azure 檔案分享

Azure CLI 提供了用於管理 Azure 資源的命令行體驗。 它是構建自定義自動化以使用 Azure 資源的絕佳工具。 本文介紹如何使用 Azure CLI 從[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)創建的還原點還原整個檔共用或特定檔。 您也可以透過 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

在本文結束時,您將瞭解如何使用 Azure CLI 執行以下操作:

* 查看備份的 Azure 檔共享的還原點。
* 還原完整的 Azure 文件共用。
* 還原單個檔或資料夾。

>[!NOTE]
> Azure 備份現在支援使用 Azure CLI 將多個檔案或資料夾還原到原始或備用位置。 請參閱[將多個檔或資料夾還原到本文檔的原始或備用位置](#restore-multiple-files-or-folders-to-original-or-alternate-location)部分以瞭解更多資訊。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>Prerequisites

本文假定您已經擁有由 Azure 備份備份的 Azure 檔共用。 如果沒有,請參閱[使用 CLI 備份 Azure 檔共用](backup-afs-cli.md),以配置檔共享的備份。 在本文中,您可以使用以下資源:

| 檔案共用  | 儲存體帳戶 | 區域 | 詳細資料                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azure 檔案*  | *阿夫斯帳戶*      | EastUS | 使用 Azure 備份備份原始來源                 |
| *azure 檔案1* | *帳戶1*      | EastUS | 將備用位置復原的目標來源 |

您可以為檔案共用使用類似的結構來嘗試本文中介紹的不同類型的還原。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>取得 Azure 檔案分享的回復點

使用[az 備份復原點清單](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list)cmdlet 列出備份檔案共用的所有復原點。

下面的範例獲取*afs帳戶*儲存帳戶中*azure檔*共用的復原點清單。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

還可以透過使用容器和項的友好名稱執行以前的 cmdlet,提供以下兩個附加參數:

* **--備份管理類型** *:azure 儲存*
* **--工作負載類型** *:azure 檔案共用*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

結果集是具有每個還原點時間和一致性詳細資訊的恢復點的清單。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

輸出中的**Name**屬性對應於復原點名稱,該名稱可用作復原操作中 **--rp-name**參數的值。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 完整回復分享

可以使用此還原選項在原始位置或備用位置還原完整的文件共用。

定義以下參數以執行回復操作:

* **--容器名稱**:承載備份的原始檔案共用的存儲帳戶的名稱。 若要檢索容器的名稱或友好名稱,請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**:要用於還原操作的備份原始檔共享的名稱。 若要檢索備份項的名稱或友好名稱,請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

### <a name="restore-a-full-share-to-the-original-location"></a>將完整分享回復到原始位置

還原到原始位置時,不需要指定與目標相關的參數。 只能提供 **「解決衝突**」。

下面的範例使用[az 備份還原還原-azure 檔共用](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)cmdlet,還原模式設置為*原始位置*,以還原原始位置中的*azure 檔*共用。 您可以使用回復點 932883129628959823,在[Azure 檔案共享的 Fetch 回復點](#fetch-recovery-points-for-the-azure-file-share)中取得該復原點:

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

輸出中的**Name**屬性對應於還原操作的備份服務創建的作業的名稱。 要追蹤作業的狀態,請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>將完整共享回復到備用位置

可以使用此選項將檔共享還原到備用位置,並保持原始檔共用原樣。 變更位置的參數:

* **--目標存儲帳戶**:還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目標檔案共用**:還原備份內容的目標存儲帳戶中的文件共用。
* **--目標資料夾**:還原資料的檔共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **--解決衝突**:如果與還原的數據發生衝突,則指示。 可接受的值為 **Overwrite** 或 **Skip**。

下面的範例使用[az 備份還原還原-azure 檔共享](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare)與還原模式作為*備用位置*,以將*afs 帳戶*儲存帳戶中的*azure 檔*共享還原到 afaccount1 儲存帳戶中的*azurefile1 檔*共用。 *afaccount1*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

輸出中的**Name**屬性對應於還原操作的備份服務創建的作業的名稱。 要追蹤作業的狀態,請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

## <a name="item-level-recovery"></a>項目層級復原

您可以使用此還原選項還原原始位置或備用位置中的單個檔案或資料夾。

定義以下參數以執行回復操作:

* **--容器名稱**:承載備份的原始檔案共用的存儲帳戶的名稱。 若要檢索容器的名稱或友好名稱,請使用[az 備份容器清單](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**:要用於還原操作的備份原始檔共享的名稱。 若要檢索備份項的名稱或友好名稱,請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。

指定指定以下參數:

* **SourceFilePath**:文件的絕對路徑,將在檔共用中作為字串還原。 此路徑與[az 儲存檔案下載](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download)中使用的路徑相同,[或 az 儲存檔案顯示](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show)CLI 命令。
* **源文件類型**:選擇是選擇目錄還是檔。 接受**目錄**或**檔案**。
* **解決衝突**:如果與還原的數據發生衝突,則說明。 可接受的值為 **Overwrite** 或 **Skip**。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>將單一檔案或資料夾回復到原始位置

使用[az 備份還原還原-azure 檔案](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)cmdlet,還原模式設定為*原始位置*,將特定檔或資料夾還原到其原始位置。

以下範例還原*還原還原測試.txt*檔的原始位置 *:azurefile*文件共用。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的**Name**屬性對應於還原操作的備份服務創建的作業的名稱。 要追蹤作業的狀態,請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>將單一檔案或資料夾回復到備用位置

要將特定檔案或資料夾還原到備用位置,請使用[az 備份還原還原-azure 檔案](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles)cmdlet,還原模式設定為*備用位置*,並指定以下目標相關參數:

* **--目標存儲帳戶**:還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **--目標檔案共用**:還原備份內容的目標存儲帳戶中的文件共用。
* **--目標資料夾**:還原資料的檔共用下的資料夾。 如果要將備份的內容還原到根資料夾,請將目標資料夾的值作為空字串。

以下範例將最初存在於*azure 檔案*共用中的*還原測試.txt*檔還原到備用位置:在*afaccount1*儲存帳戶中託管的*azurefile1*檔案共享中的*還原資料*資料夾。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

輸出中的**Name**屬性對應於還原操作的備份服務創建的作業的名稱。 要追蹤作業的狀態,請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>將多個檔案或資料夾回復到原始或備用位置

要對多個項目執行還原,請將**源檔路徑**參數的值作為要還原的所有檔案或資料夾**的空間分隔**路徑傳遞。

下面的範例還原*還原.txt*和*AFS 測試報告.docx*檔的原始位置。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

輸出將類似於：

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

輸出中的**Name**屬性對應於還原操作的備份服務創建的作業的名稱。 要追蹤作業的狀態,請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)cmdlet。

如果要將多個專案還原到備用位置,請使用上述命令,指定目標相關參數,如["將單個檔或資料夾還原到備用位置](#restore-individual-files-or-folders-to-an-alternate-location)部分"中所述。

## <a name="next-steps"></a>後續步驟

瞭解如何使用[Azure CLI 管理 Azure 檔案共享備份](manage-afs-backup-cli.md)。
