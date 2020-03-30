---
title: 更新 Azure HPC 緩存存儲目標
description: 如何編輯 Azure HPC 緩存存儲目標
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866993"
---
# <a name="edit-storage-targets"></a>編輯儲存體目標

可以從緩存的**存儲目標**頁中刪除或修改存儲目標。

## <a name="remove-a-storage-target"></a>刪除存儲目標

要刪除存儲目標，請在清單中選擇它，然後按一下"**刪除**"按鈕。

此操作將刪除存儲目標與此 Azure HPC 緩存系統的聯繫，但不會更改後端存儲系統。 例如，如果使用 Azure Blob 存儲容器，則從緩存中刪除容器及其內容後仍然存在。 您可以將容器添加到其他 Azure HPC 緩存，將其重新添加到此緩存，或使用 Azure 門戶將其刪除。

在刪除存儲目標之前，緩存中存儲的任何檔更改都將寫入後端存儲系統。 如果緩存中有很多更改的資料，此過程可能需要一個小時或更長時間。

## <a name="update-storage-targets"></a>更新存儲目標

您可以編輯存儲目標以修改其某些屬性。 不同類型的存儲可編輯不同的屬性：

* 對於 Blob 存儲目標，可以更改命名空間路徑。

* 對於 NFS 存儲目標，您可以更改以下屬性：

  * 命名空間路徑
  * 使用模型
  * 匯出
  * 匯出子目錄

不能編輯存儲目標的名稱、類型或後端存儲系統（Blob 容器或 NFS 主機名稱/IP 位址）。 如果需要更改這些屬性，請刪除存儲目標，然後使用新值創建替換。

要修改存儲目標，請按一下存儲目標名稱以打開其詳細資訊頁。 頁面中的某些欄位是可編輯的。

![NFS 存儲目標編輯頁面的螢幕截圖](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>更新 NFS 存儲目標

對於 NFS 存儲目標，可以更新多個屬性。 （有關示例編輯頁面，請參閱上面的螢幕截圖。

* **使用方式模型**- 使用模型影響緩存保留資料的方式。 閱讀[選擇使用模型](hpc-cache-add-storage.md#choose-a-usage-model)以瞭解更多資訊。
* **虛擬命名空間路徑**- 用戶端用於裝載此存儲目標的路徑。 閱讀[規劃聚合的命名空間](hpc-cache-namespace.md)以瞭解詳細資訊。
* **NFS 匯出路徑**- 用於此命名空間路徑的存儲系統匯出。
* **子目錄路徑**- 要與此命名空間路徑關聯的子目錄（在匯出下）。 如果不需要指定子目錄，請將此欄位留空。

每個命名空間路徑都需要匯出和子目錄的唯一組合。 也就是說，您無法使兩個不同的面向用戶端的路徑到後端存儲系統上完全相同的目錄。

進行更改後，按一下"**確定"** 以更新存儲目標，或按一下"**取消"** 以丟棄更改。

## <a name="update-an-azure-blob-storage-target"></a>更新 Azure Blob 存儲目標

Blob 存儲目標的詳細資訊頁允許您修改虛擬命名空間路徑。

![Blob 存儲目標編輯頁面的螢幕截圖](media/hpc-cache-edit-storage-blob.png)

完成後，按一下"**確定"** 以更新存儲目標，或按一下"**取消"** 以丟棄更改。

## <a name="next-steps"></a>後續步驟

* 閱讀[添加存儲目標](hpc-cache-add-storage.md)以瞭解有關這些選項的詳細資訊。
* 閱讀[規劃聚合命名空間](hpc-cache-namespace.md)，瞭解有關使用虛擬路徑的更多提示。
