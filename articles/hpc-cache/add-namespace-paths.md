---
title: 設定 Azure HPC Cache 匯總的命名空間
description: 如何使用 Azure HPC Cache 建立後端儲存體的面向用戶端路徑
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 090e3f93d025fe87ad5b89a98193574595f3d632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614550"
---
# <a name="set-up-the-aggregated-namespace"></a>設定匯總的命名空間

建立儲存體目標之後，您也必須為其建立命名空間路徑。 用戶端電腦會使用這些虛擬路徑來透過快取存取檔案，而不是直接連線至後端儲存體。 此系統可讓快取系統管理員變更後端存放裝置系統，而不需要重寫用戶端指示。

若要深入瞭解這項功能，請閱讀 [匯總的命名空間](hpc-cache-namespace.md) 。

Azure 入口網站中的 [ **命名空間** ] 頁面會顯示用戶端用來透過快取存取資料的路徑。 使用此頁面來建立、移除或變更命名空間路徑。 您也可以使用 Azure CLI 來設定命名空間路徑。

所有現有的面向用戶端路徑都會列在 [ **命名空間** ] 頁面上。 如果儲存目標沒有任何路徑，它就不會出現在資料表中。

您可以按一下箭號來排序資料表資料行，並進一步瞭解快取的匯總命名空間。

![入口網站命名空間頁面的螢幕擷取畫面，其中包含資料表中的兩個路徑。 欄標題：命名空間路徑、儲存體目標、匯出路徑和匯出子目錄。 第一個資料行中的專案是可點選連結。 頂端按鈕：新增命名空間路徑、重新整理、刪除](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>新增或編輯面向用戶端的命名空間路徑

您必須先建立至少一個命名空間路徑，用戶端才能存取儲存體目標。  (讀取 [裝載 Azure HPC Cache](hpc-cache-mount.md) ，以深入瞭解用戶端存取。 ) 

### <a name="blob-namespace-paths"></a>Blob 命名空間路徑

Azure Blob 儲存體目標只能有一個命名空間路徑。

請遵循下列指示來設定或變更 Azure 入口網站或 Azure CLI 的路徑。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

從 Azure 入口網站載入 **命名空間** 設定頁面。 您可以從這個頁面加入、變更或刪除命名空間路徑。

* **加入新路徑：** 按一下頂端的 [ **+ 新增** ] 按鈕，並在 [編輯] 面板中填寫資訊。

  * 從下拉式清單中選取儲存體目標。  (在此螢幕擷取畫面中，無法選取 blob 儲存體目標，因為它已經有命名空間路徑。 ) 

    ![新的命名空間編輯欄位的螢幕擷取畫面，其中已公開儲存體目標選取器](media/namespace-select-storage-target.png)

  * 針對 Azure Blob 儲存體目標，匯出和子目錄路徑會自動設為 ``/`` 。

* **變更現有的路徑：** 按一下命名空間路徑。 [編輯] 面板隨即開啟，而您可以修改該路徑。

  ![按一下 Blob 命名空間路徑之後，命名空間頁面的螢幕擷取畫面-編輯欄位會出現在右側窗格中](media/edit-namespace-blob.png)

* **刪除命名空間路徑：** 選取路徑左側的核取方塊，然後按一下 [ **刪除** ] 按鈕。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 Azure CLI 時，您必須在建立儲存體目標時新增命名空間路徑。 如需詳細資料，請參閱 [新增 Azure Blob 儲存體目標](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) 。

若要更新目標的命名空間路徑，請使用 [az hpc cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) 命令。 Update 命令的引數類似于 create 命令中的引數，但您不會傳遞容器名稱或儲存體帳戶。

您無法使用 Azure CLI 從 blob 儲存體目標刪除命名空間路徑，但是可以用不同的值來覆寫路徑。

---

### <a name="nfs-namespace-paths"></a>NFS 命名空間路徑

NFS 儲存體目標可以有多個虛擬路徑，只要每個路徑都代表相同儲存系統上的不同匯出或子目錄。

規劃 NFS 儲存體目標的命名空間時，請記住，每個路徑都必須是唯一的，而且不可以是另一個命名空間路徑的子目錄。 例如，如果您有呼叫的命名空間路徑 ``/parent-a`` ，則您也無法建立命名空間路徑，例如 ``/parent-a/user1`` 和 ``/parent-a/user2`` 。 這些目錄路徑在命名空間中已可作為的子目錄來存取 ``/parent-a`` 。

NFS 儲存體系統的所有命名空間路徑都是在一個儲存體目標上建立。 大部分的快取設定最多可支援每個儲存目標10個命名空間路徑，但較大的設定最多可支援20個。

此清單會顯示每個設定的命名空間路徑數目上限。

* 最多 2 GB/秒的輸送量：

  * 3 TB 快取-10 命名空間路徑
  * 6 TB 快取-10 命名空間路徑
  * 23 TB 快取-20 命名空間路徑

* 最多 5 GB/秒的輸送量：

  * 6 TB 快取-10 命名空間路徑
  * 12 TB 快取-10 命名空間路徑
  * 24 TB 快取-20 命名空間路徑

* 最多 8 GB/秒的輸送量：

  * 12 TB 快取-10 命名空間路徑
  * 24 TB 快取-10 命名空間路徑
  * 48 TB 快取-20 命名空間路徑

針對每個 NFS 命名空間路徑，提供面向用戶端的路徑、儲存體系統匯出，以及選擇性的匯出子目錄。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

從 Azure 入口網站載入 **命名空間** 設定頁面。 您可以從這個頁面加入、編輯或刪除命名空間路徑。

* **若要加入新的路徑：** 按一下頂端的 [ **+ 新增** ] 按鈕，並在 [編輯] 面板中填寫資訊。
* **若要變更現有的路徑：** 按一下命名空間路徑。 [編輯] 面板隨即開啟，而您可以修改該路徑。
* **若要刪除命名空間路徑：** 選取路徑左側的核取方塊，然後按一下 [ **刪除** ] 按鈕。

針對每個命名空間路徑填入下列值：

* **命名空間路徑** -面向用戶端的檔案路徑。

* **儲存體目標** -如果建立新的命名空間路徑，請從下拉式功能表中選取儲存體目標。

* **匯出路徑** -輸入 NFS 匯出的路徑。 請務必正確輸入匯出名稱-入口網站會驗證此欄位的語法，但是在您提交變更之前，不會檢查匯出。

* **匯出子目錄** -如果您想要此路徑掛接匯出的特定子目錄，請在這裡輸入。 如果沒有，請將這個欄位保留空白。

![入口網站命名空間頁面的螢幕擷取畫面，其中的 [更新] 頁面在右側開啟](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 Azure CLI 時，您必須在建立儲存體目標時新增至少一個命名空間路徑。 如需詳細資料，請參閱 [新增 NFS 儲存體目標](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) 。

若要更新目標的命名空間路徑或新增其他路徑，請使用 [az hpc-cache nfs-storage-target-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) 命令。 使用 ``--junction`` 選項可指定您想要的所有命名空間路徑。

用於 update 命令的選項類似于 "create" 命令，不同之處在于您不會將儲存體系統資訊傳遞 (IP 位址或主機名稱) ，而且使用方式模型是選擇性的。 如需選項語法的詳細資訊，請參閱新增 [NFS 儲存體目標](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) ``--junction`` 。

---

## <a name="next-steps"></a>後續步驟

建立儲存體目標的匯總命名空間之後，您可以將用戶端掛接在快取上。 若要深入瞭解，請參閱這些文章。

* [掛接 Azure HPC Cache](hpc-cache-mount.md)
* [將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)
