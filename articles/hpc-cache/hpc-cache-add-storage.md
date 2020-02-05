---
title: 將儲存體新增至 Azure HPC 快取
description: 如何定義儲存體目標，讓您的 Azure HPC 快取可以使用您的內部部署 NFS 系統或 Azure Blob 容器進行長期檔案儲存
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: a68bf06bad995f71bedf6a5bdedcb676737a8c61
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990601"
---
# <a name="add-storage-targets"></a>新增儲存體目標

*儲存體目標*是透過 Azure HPC 快取實例存取之檔案的後端儲存體。 您可以新增 NFS 儲存體（例如內部部署硬體系統），或將資料儲存在 Azure Blob 中。

您最多可以為一個快取定義10個不同的儲存體目標。 快取會將所有儲存體目標呈現在一個匯總的命名空間中。

請記住，您必須能夠從快取的虛擬網路存取儲存體匯出。 針對內部部署硬體存放裝置，您可能需要設定可解析主機名稱以進行 NFS 儲存體存取的 DNS 伺服器。 如需詳細資訊，請參閱[DNS 存取](hpc-cache-prereqs.md#dns-access)。

建立快取之後，新增儲存體目標。 視您要新增 Azure Blob 儲存體或 NFS 匯出而定，此程式稍有不同。 每個的詳細資料如下。

## <a name="open-the-storage-targets-page"></a>開啟 [儲存體目標] 頁面

在 Azure 入口網站中，開啟您的快取實例，然後按一下左側邊欄上的 [**儲存體目標**]。 [儲存體目標] 頁面會列出所有現有的目標，並提供連結以加入新的目標。

![提要欄位上的 [儲存體目標] 連結的螢幕擷取畫面，位於 [設定] 標題底下，這是在類別標題設定和監視之間](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>新增 Azure Blob 儲存體目標

新的 Blob 儲存體目標需要空的 Blob 容器或以 Azure HPC 快取雲端檔案系統格式填入資料的容器。 深入瞭解在[將資料移至 Azure blob 儲存體](hpc-cache-ingest.md)中預先載入 Blob 容器。

您可以從這個頁面建立新的容器，然後再新增它。

若要定義 Azure Blob 容器，請輸入此資訊。

![[新增儲存體目標] 頁面的螢幕擷取畫面，其中填入了新的 Azure Blob 儲存體目標的資訊](media/hpc-cache-add-blob.png)

* **儲存體目標名稱**-在 Azure HPC 快取中設定用來識別此儲存體目標的名稱。
* **目標型別**-選擇 [ **Blob**]。
* **儲存體帳戶**-選取您要使用的帳戶。

  您將需要授權快取實例來存取儲存體帳戶，如[新增存取角色](#add-the-access-control-roles-to-your-account)中所述。

  如需您可以使用之儲存體帳戶類型的詳細資訊，請參閱[Blob 儲存體需求](hpc-cache-prereqs.md#blob-storage-requirements)。

* **儲存體容器**-選取此目標的 Blob 容器，**或按一下 [新建]** 。

  ![對話方塊的螢幕擷取畫面，用來指定新容器的名稱和存取層級（私用）](media/add-blob-new-container.png)

* **虛擬命名空間路徑**-設定此儲存體目標的面向用戶端檔案路徑。 請參閱[設定匯總命名空間](hpc-cache-namespace.md)，以深入瞭解虛擬命名空間功能。

完成後，按一下 **[確定]** 以新增儲存體目標。

> [!NOTE]
> 如果您的儲存體帳戶防火牆設定為僅限制「選取的網路」的存取，請使用在[解決 Blob 儲存體帳戶防火牆設定](hpc-cache-blob-firewall-fix.md)中所述的暫時因應措施。

### <a name="add-the-access-control-roles-to-your-account"></a>將存取控制角色新增至您的帳戶

Azure HPC 快取會使用[角色型存取控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/index)來授權快取服務，以存取您的 Azure Blob 儲存體目標儲存體帳戶。

儲存體帳戶擁有者必須明確地為使用者「HPC 快取資源提供者」新增角色[儲存體帳戶參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)和[儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)。

您可以提早執行此動作，或按一下頁面上的連結來新增 Blob 儲存體目標。 請記住，角色設定可能需要五分鐘的時間才能透過 Azure 環境傳播，因此在建立儲存體目標之前，您應該先等候幾分鐘後再新增角色。

新增 RBAC 角色的步驟：

1. 開啟儲存體帳戶的 [**存取控制（IAM）** ] 頁面。 （[**新增儲存體目標**] 頁面中的連結會自動為選取的帳戶開啟此頁面）。

1. 按一下頁面頂端的  **+** ，然後選擇 **新增角色指派**。

1. 從清單中選取 [儲存體帳戶參與者] 角色。

1. 在 [**指派存取權給**] 欄位中，保留選取預設值（「Azure AD 使用者、群組或服務主體」）。  

1. 在 [**選取**] 欄位中，搜尋 "hpc"。  此字串應符合一個名為「HPC 快取資源提供者」的服務主體。 按一下該主體來選取它。

   > [!NOTE]
   > 如果 "hpc" 的搜尋無法正常執行，請嘗試改為使用字串 "storagecache"。 加入預覽的使用者（在 GA 之前）可能需要使用較舊的服務主體名稱。

1. 按一下底部的 [**儲存**] 按鈕。

1. 重複此程式以指派「儲存體 Blob 資料參與者」角色。  

![新增角色指派 GUI 的螢幕擷取畫面](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>新增 NFS 儲存體目標

NFS 儲存體目標的欄位多於 Blob 儲存體目標。 這些欄位會指定如何到達儲存體匯出，以及如何有效率地快取其資料。 此外，如果 NFS 主機有一個以上的匯出可用，NFS 儲存體目標可讓您建立多個命名空間路徑。

![已定義 NFS 目標的 [新增儲存體目標] 頁面螢幕擷取畫面](media/hpc-cache-add-nfs-target.png)

為支援 NFS 的儲存體目標提供這種資訊：

* **儲存體目標名稱**-在 Azure HPC 快取中設定用來識別此儲存體目標的名稱。

* **目標型別**-選擇 [ **NFS**]。

* **主機名稱**-輸入 NFS 儲存體系統的 IP 位址或完整功能變數名稱。 （只有當您的快取可以存取可解析名稱的 DNS 伺服器時，才使用功能變數名稱）。

* **使用量模型**-根據您的工作流程選擇其中一個資料快取設定檔，如下所述：[選擇使用方式模型](#choose-a-usage-model)。

### <a name="nfs-namespace-paths"></a>NFS 命名空間路徑

NFS 儲存體目標可以有多個虛擬路徑，前提是每個路徑都代表相同儲存系統上的不同匯出或子目錄。

建立一個儲存體目標的所有路徑。

您可以隨時在儲存體目標上[新增和編輯命名空間路徑](hpc-cache-edit-storage.md)。

針對每個命名空間路徑填入這些值：

* **虛擬命名空間路徑**-設定此儲存體目標的面向用戶端檔案路徑。 請參閱[設定匯總命名空間](hpc-cache-namespace.md)，以深入瞭解虛擬命名空間功能。

<!--  The virtual path should start with a slash ``/``. -->

* **Nfs 匯出路徑**-輸入 nfs 匯出的路徑。

* **子目錄路徑**-如果您想要掛接匯出的特定子目錄，請在這裡輸入。 如果不是，請將此欄位保留空白。

完成後，按一下 **[確定]** 以新增儲存體目標。

### <a name="choose-a-usage-model"></a>選擇使用方式模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

當您建立指向 NFS 儲存體系統的儲存體目標時，您需要選擇該目標的*使用方式模型*。 此模型會決定資料的快取方式。

選項有三個：

* **讀取大量、不頻繁的寫入**-如果您想要加速對靜態或極少變更之檔案的讀取權限，請使用此選項。

  此選項會快取用戶端讀取的檔案，但會立即將寫入傳遞至後端儲存體。 儲存在快取中的檔案永遠不會與 NFS 存放磁片區上的檔案進行比較。

  如果有可能直接在儲存系統上修改檔案，而不需要先將檔案寫入快取，請不要使用此選項。 如果發生這種情況，檔案的快取版本永遠不會更新後端的變更，而且資料集可能會變得不一致。

* **大於15% 寫入**-這個選項可加速讀取和寫入效能。 使用此選項時，所有用戶端都必須透過 Azure HPC 快取存取檔案，而不是直接裝載後端存放裝置。 快取檔案將會有不會儲存在後端的最近變更。

  在此使用模型中，不會針對後端儲存體上的檔案檢查快取中的檔案。 檔案的快取版本被假設為較新的。 快取中已修改的檔案只會在快取中有一小時後寫入後端儲存系統，而不會進行任何額外的變更。

* **用戶端寫入 NFS 目標，略過**快取-如果工作流程中的任何用戶端直接將資料寫入儲存體系統，而未先寫入快取，請選擇此選項。 系統會快取用戶端要求的檔案，但從用戶端對這些檔案所做的任何變更，都會立即傳遞回後端儲存體系統。

  使用此使用模式時，會經常檢查快取中的檔案是否有更新的後端版本。 這項驗證可讓檔案在快取之外變更，同時維持資料一致性。

下表摘要說明使用方式模型的差異：

| 使用方式模型 | 快取模式 | 後端驗證 | 最大回寫延遲 |
| ---- | ---- | ---- | ---- |
| 讀取大量、不頻繁的寫入 | 讀取 | 永不 | 無 |
| 超過15% 寫入 | 讀取/寫入 | 永不 | 1 小時 |
| 用戶端略過快取 | 讀取 | 30 秒 | 無 |

## <a name="next-steps"></a>後續步驟

建立儲存體目標之後，請考慮下列其中一項工作：

* [掛接 Azure HPC Cache](hpc-cache-mount.md)
* [將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)

如果您需要更新任何設定，您可以[編輯儲存體目標](hpc-cache-edit-storage.md)。
