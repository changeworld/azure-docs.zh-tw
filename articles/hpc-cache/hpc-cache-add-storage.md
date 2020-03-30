---
title: 將存儲添加到 Azure HPC 緩存
description: 如何定義存儲目標，以便 Azure HPC 緩存可以使用本地 NFS 系統或 Azure Blob 容器進行長期檔存儲
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: a68bf06bad995f71bedf6a5bdedcb676737a8c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271885"
---
# <a name="add-storage-targets"></a>新增儲存體目標

*存儲目標是*通過 Azure HPC 緩存實例訪問的檔的後端存儲。 您可以添加 NFS 存儲（如本地硬體系統），或在 Azure Blob 中存儲資料。

您最多可以為一個緩存定義十個不同的存儲目標。 緩存在一個聚合命名空間中顯示所有存儲目標。

請記住，必須從緩存的虛擬網路訪問存儲匯出。 對於本地硬體存儲，您可能需要設置一個 DNS 伺服器，該伺服器可以解析主機名稱以進行 NFS 存儲訪問。 在[DNS 訪問](hpc-cache-prereqs.md#dns-access)中閱讀更多內容。

創建緩存後添加存儲目標。 該過程略有不同，具體取決於是添加 Azure Blob 存儲還是 NFS 匯出。 每個的詳細資訊如下。

## <a name="open-the-storage-targets-page"></a>打開存儲目標頁面

從 Azure 門戶打開緩存實例並按一下左側邊欄上的**存儲目標**。 存儲目標頁列出所有現有目標，並給出一個連結以添加新目標。

![側邊欄上的存儲目標連結的螢幕截圖，標題為"配置"，該標題介於類別標題"設置"和"監視"之間](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>添加新的 Azure Blob 存儲目標

新的 Blob 存儲目標需要一個空 Blob 容器或一個以 Azure HPC 緩存雲檔案系統格式填充資料的容器。 在[將資料移動到 Azure Blob 存儲](hpc-cache-ingest.md)中，閱讀有關預載入 Blob 容器的更多內容。

您可以在添加新容器之前從此頁面創建新容器。

要定義 Azure Blob 容器，請輸入此資訊。

![添加存儲目標頁的螢幕截圖，該頁面填充了新的 Azure Blob 存儲目標的資訊](media/hpc-cache-add-blob.png)

* **存儲目標名稱**- 設置在 Azure HPC 緩存中標識此存儲目標的名稱。
* **目標型別**- 選擇**Blob**。
* **存儲帳戶**- 選擇要使用的帳戶。

  您需要授權緩存實例訪問存儲帳戶，如[添加訪問角色](#add-the-access-control-roles-to-your-account)中所述。

  有關可以使用的存儲帳戶類型的資訊，請閱讀[Blob 存儲要求](hpc-cache-prereqs.md#blob-storage-requirements)。

* **存儲容器**- 為此目標選擇 Blob 容器，或按一下"**創建新**"。

  ![用於為新容器指定名稱和存取層級（私有）的對話方塊螢幕截圖](media/add-blob-new-container.png)

* **虛擬命名空間路徑**- 為此存儲目標設置面向用戶端的檔路徑。 閱讀[配置聚合命名空間](hpc-cache-namespace.md)以瞭解有關虛擬命名空間功能的更多內容。

完成後，按一下"**確定"** 以添加存儲目標。

> [!NOTE]
> 如果存儲帳戶防火牆設置為僅限制對"選定網路"的訪問，請使用"[解決 Blob 存儲帳戶防火牆設置"](hpc-cache-blob-firewall-fix.md)中記錄的臨時解決方法。

### <a name="add-the-access-control-roles-to-your-account"></a>將存取控制角色添加到您的帳戶

Azure HPC 緩存使用[基於角色的存取控制 （RBAC）](https://docs.microsoft.com/azure/role-based-access-control/index)授權快取服務訪問 Azure Blob 存儲目標的存儲帳戶。

存儲帳戶擁有者必須顯式添加使用者"HPC 緩存資來源提供者"的[存儲帳戶參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)和[存儲 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)的角色。

您可以提前執行此操作，或者按一下頁面上添加 Blob 存儲目標的連結。 請記住，角色設置最多可能需要五分鐘才能在 Azure 環境中傳播，因此在添加角色後應等待幾分鐘，然後再創建存儲目標。

添加 RBAC 角色的步驟：

1. 打開存儲帳戶**的存取控制 （IAM）** 頁面。 （**添加存儲目標**頁中的連結會自動打開所選帳戶的此頁面。

1. **+** 按一下頁面頂部的，然後選擇 **"添加角色指派**"。

1. 從清單中選擇"存儲帳戶參與者"角色。

1. 在"**分配對欄位的訪問**"中，保留預設值（"Azure AD 使用者、組或服務主體"）。  

1. 在 **"選擇"** 欄位中，搜索"hpc"。  此字串應匹配一個服務主體，稱為"HPC 緩存資來源提供者"。 按一下該主體以將其選中。

   > [!NOTE]
   > 如果搜索"hpc"不起作用，請嘗試改用字串"存儲緩存"。 加入預覽（在 GA 之前）的使用者可能需要使用服務主體的舊名稱。

1. 按一下底部的 **"保存**"按鈕。

1. 重複此過程以分配角色"存儲 Blob 資料參與者"。  

![添加角色指派 GUI 的螢幕截圖](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>添加新的 NFS 存儲目標

NFS 存儲目標具有比 Blob 存儲目標多的欄位。 這些欄位指定如何訪問存儲匯出以及如何高效地緩存其資料。 此外，如果 NFS 主機有多個匯出可用，則 NFS 存儲目標允許您創建多個命名空間路徑。

![定義 NFS 目標的添加存儲目標頁的螢幕截圖](media/hpc-cache-add-nfs-target.png)

為 NFS 支援的存儲目標提供此資訊：

* **存儲目標名稱**- 設置在 Azure HPC 緩存中標識此存儲目標的名稱。

* **目標型別**- 選擇**NFS**。

* **主機名稱**- 輸入 NFS 存儲系統的 IP 位址或完全限定的功能變數名稱。 （僅當緩存有權訪問可以解析該名稱的 DNS 伺服器時，才使用功能變數名稱。

* **使用方式模型**- 根據工作流選擇一個資料緩存設定檔，下面[在選擇使用模型](#choose-a-usage-model)中描述。

### <a name="nfs-namespace-paths"></a>NFS 命名空間路徑

NFS 存儲目標可以有多個虛擬路徑，只要每個路徑表示同一存儲系統上的不同匯出或子目錄。

從一個存儲目標創建所有路徑。

您可以隨時在存儲目標上[添加和編輯命名空間路徑](hpc-cache-edit-storage.md)。

為每個命名空間路徑填寫這些值：

* **虛擬命名空間路徑**- 為此存儲目標設置面向用戶端的檔路徑。 閱讀[配置聚合命名空間](hpc-cache-namespace.md)以瞭解有關虛擬命名空間功能的更多內容。

<!--  The virtual path should start with a slash ``/``. -->

* **NFS 匯出路徑**- 輸入 NFS 匯出的路徑。

* **子目錄路徑**- 如果要裝載匯出的特定子目錄，請在此處輸入它。 如果沒有，請將此欄位留空。

完成後，按一下"**確定"** 以添加存儲目標。

### <a name="choose-a-usage-model"></a>選擇使用模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

當您創建指向 NFS 存儲系統的存儲目標時，您需要為該目標選擇*使用模型*。 此模型確定資料的緩存方式。

選項有三個：

* **讀取重寫、不頻繁的寫入**- 如果要加快對靜態或很少更改的檔的讀取存取，請使用此選項。

  此選項快取用戶端讀取的檔，但立即將寫入傳遞到後端存儲。 存儲在緩存中的檔永遠不會與 NFS 存儲卷上的檔進行比較。

  如果存在檔可能直接在存儲系統上被修改而不先寫入緩存的風險，則不要使用此選項。 如果發生這種情況，該檔的緩存版本將永遠不會更新與從後端的更改，並且資料集可能會變得不一致。

* **大於 15% 寫入**- 此選項可加快讀取和寫入性能。 使用此選項時，所有用戶端都必須通過 Azure HPC 緩存訪問檔，而不是直接安裝後端存儲。 緩存的檔將具有未存儲在後端的最新更改。

  在此使用模型中，不會對照後端存儲上的檔檢查緩存中的檔。 假定檔的緩存版本更最新。 緩存中修改後的檔僅在緩存中一小時後寫入後端存儲系統，並且沒有其他更改。

* **用戶端將寫入 NFS 目標，繞過緩存**- 如果工作流中的任何用戶端在不首先寫入緩存的情況下將資料直接寫入存儲系統，則選擇此選項。 用戶端請求的檔將緩存，但從用戶端對這些檔的任何更改將立即傳回後端存儲系統。

  使用此使用模型，會經常對照後端版本檢查緩存中的檔以進行更新。 此驗證允許在緩存外部更改檔，同時保持資料一致性。

下表總結了使用模型差異：

| 使用模型 | 緩存模式 | 後端驗證 | 最大回寫延遲 |
| ---- | ---- | ---- | ---- |
| 閱讀重、不頻繁的寫入 | 讀取 | 永不 | None |
| 大於 15% 寫入 | 讀取/寫入 | 永不 | 1 小時 |
| 用戶端繞過緩存 | 讀取 | 30 秒 | None |

## <a name="next-steps"></a>後續步驟

創建存儲目標後，請考慮以下任務之一：

* [掛接 Azure HPC Cache](hpc-cache-mount.md)
* [將資料移動到 Azure Blob 存儲](hpc-cache-ingest.md)

如果需要更新任何設置，可以[編輯存儲目標](hpc-cache-edit-storage.md)。
