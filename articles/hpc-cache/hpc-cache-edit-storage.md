---
title: 更新 Azure HPC Cache 儲存體目標
description: 如何編輯 Azure HPC Cache 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 1/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0fa8be58db9754c652d6e1ee5349c950a1c19109
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513842"
---
# <a name="edit-storage-targets"></a>編輯儲存體目標

您可以從快取的 [**儲存體目標**] 頁面中移除或修改儲存體目標。

> [!TIP]
> [管理 AZURE HPC](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)快取影片顯示如何編輯儲存體目標。

## <a name="remove-a-storage-target"></a>移除儲存體目標

若要移除儲存體目標，請在清單中選取它，然後按一下 [**刪除**] 按鈕。

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

若要修改儲存體目標，請按一下儲存體目標名稱，以開啟其詳細資料頁面。 頁面中的某些欄位是可編輯的。

![NFS 儲存體目標之 [編輯] 頁面的螢幕擷取畫面](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>更新 NFS 儲存體目標

針對 NFS 儲存體目標，您可以更新數個屬性。 （如需範例 [編輯] 頁面，請參閱上面的螢幕擷取畫面）。

* **使用方式模型**-使用量模型會影響快取保留資料的方式。 若要深入瞭解，[請參閱選擇使用方式模型](hpc-cache-add-storage.md#choose-a-usage-model)。
* **虛擬命名空間路徑**-用戶端用來掛接此儲存體目標的路徑。 如需詳細資訊，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。
* **NFS 匯出路徑**-要用於此命名空間路徑的儲存體系統匯出。
* **子目錄路徑**-要與這個命名空間路徑相關聯的子目錄（在匯出下）。 如果您不需要指定子目錄，請將此欄位保留空白。

每個命名空間路徑都需要匯出和子目錄的唯一組合。 也就是說，您無法在後端儲存體系統上，將兩個不同的用戶端對應路徑放到完全相同的目錄。

進行變更之後，請按一下 **[確定]** 以更新儲存體目標，或按一下 [**取消**] 來捨棄變更。

## <a name="update-an-azure-blob-storage-target"></a>更新 Azure Blob 儲存體目標

Blob 儲存體目標的 [詳細資料] 頁面可讓您修改虛擬命名空間路徑。

![blob 儲存體目標之 [編輯] 頁面的螢幕擷取畫面](media/hpc-cache-edit-storage-blob.png)

完成後，按一下 **[確定]** 以更新儲存體目標，或按一下 [**取消**] 以捨棄變更。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解這些選項，請參閱[新增儲存體目標](hpc-cache-add-storage.md)。
* 如需使用虛擬路徑的更多秘訣，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。
