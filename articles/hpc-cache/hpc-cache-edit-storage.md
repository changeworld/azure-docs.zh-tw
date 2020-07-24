---
title: 更新 Azure HPC Cache 儲存體目標
description: 如何編輯 Azure HPC Cache 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092451"
---
# <a name="edit-storage-targets"></a>編輯儲存體目標

您可以從快取的**儲存體目標**入口網站頁面，或使用 Azure CLI，來移除或修改儲存體目標。

> [!TIP]
> [管理 AZURE HPC](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)快取影片顯示如何在 Azure 入口網站中編輯儲存體目標。

## <a name="remove-a-storage-target"></a>移除儲存體目標

### <a name="portal"></a>[入口網站](#tab/azure-portal)

若要移除儲存體目標，請在清單中選取它，然後按一下 [**刪除**] 按鈕。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-快取儲存體-[目標](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove)] [移除] 可從快取中刪除儲存體目標。

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

此動作會移除與此 Azure HPC 快取系統的儲存體目標關聯，但不會變更後端儲存體系統。 例如，如果您使用了 Azure Blob 儲存體容器，則在您從快取中刪除容器和其內容時，它仍會存在。 您可以將容器新增至不同的 Azure HPC 快取、將其重新加入至此快取，或使用 Azure 入口網站加以刪除。

儲存在快取中的任何檔案變更，會在儲存體目標移除之前寫入後端儲存體系統。 如果快取中有大量變更的資料，此程式可能需要一小時以上的時間。

## <a name="update-storage-targets"></a>更新儲存體目標

您可以編輯儲存體目標，以修改其部分屬性。 不同類型的儲存體可編輯不同的屬性：

* 針對 Blob 儲存體目標，您可以變更命名空間路徑。

* 針對 NFS 儲存體目標，您可以變更下列屬性：

  * 命名空間路徑
  * 使用方式模型
  * 匯出
  * 匯出子目錄

您無法編輯儲存體目標的名稱、類型或後端儲存系統（Blob 容器或 NFS 主機名稱/IP 位址）。 如果您需要變更這些屬性，請刪除儲存體目標，並建立新值的取代。

在 [Azure 入口網站中，您可以按一下儲存體目標名稱並開啟其 [詳細資料] 頁面，以查看可編輯的欄位。 您也可以使用 Azure CLI 來修改儲存體目標。

![NFS 儲存體目標之 [編輯] 頁面的螢幕擷取畫面](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>更新 NFS 儲存體目標

針對 NFS 儲存體目標，您可以更新數個屬性。 （如需範例 [編輯] 頁面，請參閱上面的螢幕擷取畫面）。

* **使用方式模型**-使用量模型會影響快取保留資料的方式。 若要深入瞭解，[請參閱選擇使用方式模型](hpc-cache-add-storage.md#choose-a-usage-model)。
* **虛擬命名空間路徑**-用戶端用來掛接此儲存體目標的路徑。 如需詳細資訊，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。
* **NFS 匯出路徑**-要用於此命名空間路徑的儲存體系統匯出。
* **子目錄路徑**-要與這個命名空間路徑相關聯的子目錄（在匯出下）。 如果您不需要指定子目錄，請將此欄位保留空白。

每個命名空間路徑都需要匯出和子目錄的唯一組合。 也就是說，您無法在後端儲存體系統上，將兩個不同的用戶端對應路徑放到完全相同的目錄。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

進行變更之後，請按一下 **[確定]** 以更新儲存體目標，或按一下 [**取消**] 來捨棄變更。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az nfs-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target)命令來變更儲存體目標的使用方式模型、虛擬命名空間路徑和 nfs 匯出或子目錄值。

* 若要變更使用方式模型，請使用 ``--nfs3-usage-model`` 選項。 範例： ``--nfs3-usage-model WRITE_WORKLOAD_15``

* 若要變更命名空間路徑、匯出或匯出子目錄，請使用 ``--junction`` 選項。

  ``--junction``參數會使用下列值：

  * ``namespace-path``-用戶端面向的虛擬檔案路徑
  * ``nfs-export``-要與用戶端對向路徑產生關聯的儲存體系統匯出
  * ``target-path``（選擇性）-匯出的子目錄（如有需要）

  範例： ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

所有更新命令中都需要快取名稱、儲存體目標名稱和資源群組。

命令範例︰ <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

如果快取已停止或狀態不良，則更新會在快取狀況良好之後套用。

---

## <a name="update-an-azure-blob-storage-target"></a>更新 Azure Blob 儲存體目標

針對 blob 儲存體目標，您可以修改虛擬命名空間路徑。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

Blob 儲存體目標的 [詳細資料] 頁面可讓您修改虛擬命名空間路徑。

![blob 儲存體目標之 [編輯] 頁面的螢幕擷取畫面](media/hpc-cache-edit-storage-blob.png)

完成後，按一下 **[確定]** 以更新儲存體目標，或按一下 [**取消**] 以捨棄變更。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update)來更新目標的命名空間路徑。

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

如果快取已停止或狀態不良，則會在快取狀況良好之後套用更新。

---

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解這些選項，請參閱[新增儲存體目標](hpc-cache-add-storage.md)。
* 如需使用虛擬路徑的更多秘訣，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。
