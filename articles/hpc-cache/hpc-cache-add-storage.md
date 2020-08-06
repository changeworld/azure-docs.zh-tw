---
title: 將儲存體新增至 Azure HPC 快取
description: 如何定義儲存體目標，讓您的 Azure HPC 快取可以使用您的內部部署 NFS 系統或 Azure Blob 容器進行長期檔案儲存
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 585ea3b5ddd16acb9af83c1c1e0e4aa6ca9e631a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826699"
---
# <a name="add-storage-targets"></a>新增儲存體目標

*儲存體目標*是透過 Azure HPC 快取存取之檔案的後端儲存體。 您可以新增 NFS 儲存體 (，例如內部部署硬體系統) ，或將資料儲存在 Azure Blob 中。

您最多可以為一個快取定義10個不同的儲存體目標。 快取會將所有儲存體目標呈現在一個匯總的命名空間中。

請記住，您必須能夠從快取的虛擬網路存取儲存體匯出。 針對內部部署硬體存放裝置，您可能需要設定可解析主機名稱以進行 NFS 儲存體存取的 DNS 伺服器。 如需詳細資訊，請參閱[DNS 存取](hpc-cache-prerequisites.md#dns-access)。

建立快取之後，新增儲存體目標。 視您要新增 Azure Blob 儲存體或 NFS 匯出而定，此程式稍有不同。 每個的詳細資料如下。

按一下下方影像以觀看[示範](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)如何建立快取，以及如何從 Azure 入口網站新增儲存體目標。

[![影片縮圖： Azure HPC Cache：安裝 (按一下以流覽影片頁面) ](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="view-storage-targets"></a>查看儲存體目標

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，開啟您的快取實例，然後按一下左側邊欄上的 [**儲存體目標**]。 [儲存體目標] 頁面會列出所有現有的目標，並提供連結以加入新的目標。

![提要欄位上的 [儲存體目標] 連結的螢幕擷取畫面，位於 [設定] 標題底下，這是在類別標題設定和監視之間](media/hpc-cache-storage-targets-sidebar.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-cache storage-target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list)選項來顯示快取的現有儲存體目標。 提供快取名稱和資源群組 (，除非您已將它設為全域) 。

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

使用[az hpc-cache storage-target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list)查看特定儲存體目標的詳細資料。  (依名稱指定儲存體目標。 ) 

範例：

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="add-a-new-azure-blob-storage-target"></a>新增 Azure Blob 儲存體目標

新的 Blob 儲存體目標需要空的 Blob 容器或以 Azure HPC 快取雲端檔案系統格式填入資料的容器。 深入瞭解在[將資料移至 Azure blob 儲存體](hpc-cache-ingest.md)中預先載入 Blob 容器。

Azure 入口網站的 [**新增儲存體**] [目標] 頁面中，包含在新增 Blob 容器之前建立新的選項。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

![[新增儲存體目標] 頁面的螢幕擷取畫面，其中填入了新的 Azure Blob 儲存體目標的資訊](media/hpc-cache-add-blob.png)

若要定義 Azure Blob 容器，請輸入此資訊。

* **儲存體目標名稱**-在 Azure HPC 快取中設定用來識別此儲存體目標的名稱。
* **目標型別**-選擇 [ **Blob**]。
* **儲存體帳戶**-選取您要使用的帳戶。

  您將需要授權快取實例來存取儲存體帳戶，如[新增存取角色](#add-the-access-control-roles-to-your-account)中所述。

  如需您可以使用之儲存體帳戶類型的詳細資訊，請參閱[Blob 儲存體需求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* **儲存體容器**-選取此目標的 Blob 容器，**或按一下 [新建]**。

  ![對話方塊的螢幕擷取畫面，用來指定新容器 (私人) 的名稱和存取層級](media/add-blob-new-container.png)

* **虛擬命名空間路徑**-設定此儲存體目標的面向用戶端檔案路徑。 請參閱[設定匯總命名空間](hpc-cache-namespace.md)，以深入瞭解虛擬命名空間功能。

完成後，按一下 **[確定]** 以新增儲存體目標。

> [!NOTE]
> 如果您的儲存體帳戶防火牆設定為僅限制「選取的網路」的存取，請使用在[解決 Blob 儲存體帳戶防火牆設定](hpc-cache-blob-firewall-fix.md)中所述的暫時因應措施。

### <a name="add-the-access-control-roles-to-your-account"></a>將存取控制角色新增至您的帳戶

Azure HPC 快取會使用 azure[角色型存取控制 (AZURE RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/index)來授權快取服務存取 Azure Blob 儲存體目標的儲存體帳戶。

儲存體帳戶擁有者必須明確地為使用者「HPC 快取資源提供者」新增角色[儲存體帳戶參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)和[儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)。

您可以提早執行此動作，或按一下頁面上的連結來新增 Blob 儲存體目標。 請記住，角色設定可能需要五分鐘的時間才能透過 Azure 環境傳播，因此在建立儲存體目標之前，您應該先等候幾分鐘後再新增角色。

新增 Azure 角色的步驟：

1. 開啟儲存體帳戶的**存取控制 (IAM) **頁面。  ([**新增儲存體目標**] 頁面中的連結，會自動為選取的帳戶開啟此頁面。 ) 

1. 按一下 **+** 頁面頂端的，然後選擇 [**新增角色指派**]。

1. 從清單中選取 [儲存體帳戶參與者] 角色。

1. 在 [**指派存取權給**] 欄位中，保留選取的預設值 ( [Azure AD 使用者、群組或服務主體] ) 。  

1. 在 [**選取**] 欄位中，搜尋 "hpc"。  此字串應符合一個名為「HPC 快取資源提供者」的服務主體。 按一下該主體來選取它。

   > [!NOTE]
   > 如果 "hpc" 的搜尋無法正常執行，請嘗試改為使用字串 "storagecache"。 在 GA) 之前參與預覽 (的使用者，可能需要使用較舊的服務主體名稱。

1. 按一下底部的 [**儲存**] 按鈕。

1. 重複此程式以指派「儲存體 Blob 資料參與者」角色。  

![新增角色指派 GUI 的螢幕擷取畫面](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>先決條件：儲存體帳戶存取

在您新增 blob 儲存體目標之前，請先檢查快取是否具有正確的角色來存取儲存體帳戶，以及防火牆設定是否允許建立儲存體目標。

Azure HPC 快取會使用 azure[角色型存取控制 (AZURE RBAC) ](../role-based-access-control/index.yml)來授權快取服務存取 Azure Blob 儲存體目標的儲存體帳戶。

儲存體帳戶擁有者必須明確地為使用者「HPC 快取資源提供者」新增角色[儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#storage-account-contributor)和[儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)。

如果快取中沒有這些角色，則建立儲存體目標將會失敗。

角色設定可能需要五分鐘的時間才能透過 Azure 環境傳播，因此在建立儲存體目標之前，您應該先等候幾分鐘後再新增角色。

如需詳細指示，[請參閱使用 Azure CLI 來新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-cli.md)。

也請檢查您的儲存體帳戶的防火牆設定。 如果防火牆設定為僅限制「選取的網路」的存取，則儲存體目標建立可能會失敗。 使用[有關 Blob 儲存體帳戶防火牆設定](hpc-cache-blob-firewall-fix.md)中記載的因應措施。

### <a name="add-a-blob-storage-target-with-azure-cli"></a>新增具有 Azure CLI 的 blob 儲存體目標

使用[az hpc-cache blob-storage-target add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add)介面來定義 Azure blob 儲存體目標。

除了標準資源群組和快取名稱參數以外，您還必須提供儲存體目標的下列選項：

* ``--name``-設定在 Azure HPC 快取中識別此儲存體目標的名稱。

* ``--storage-account``-帳戶識別碼，格式為：/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  如需您可以使用之儲存體帳戶類型的詳細資訊，請參閱[Blob 儲存體需求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* ``--container-name``-指定要用於此儲存體目標的容器名稱。

* ``--virtual-namespace-path``-設定此儲存體目標的面向用戶端檔案路徑。 將路徑括在引號中。 請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)，以深入瞭解虛擬命名空間功能。

範例命令：

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>新增 NFS 儲存體目標

NFS 儲存體目標的欄位多於 Blob 儲存體目標。 這些欄位會指定如何到達儲存體匯出，以及如何有效率地快取其資料。 此外，如果 NFS 主機有一個以上的匯出可用，NFS 儲存體目標可讓您建立多個命名空間路徑。

![已定義 NFS 目標的 [新增儲存體目標] 頁面螢幕擷取畫面](media/add-nfs-target.png)

> [!NOTE]
> 建立 NFS 儲存體目標之前，請確定您的儲存體系統可從 Azure HPC 快取存取，並符合許可權需求。 如果快取無法存取儲存系統，儲存體目標的建立將會失敗。 如需詳細資訊，請參閱[NFS 存放裝置需求](hpc-cache-prerequisites.md#nfs-storage-requirements)和[疑難排解 NAS 設定和 NFS 儲存體目標問題](troubleshoot-nas.md)。

### <a name="choose-a-usage-model"></a>選擇使用方式模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

當您建立指向 NFS 儲存體系統的儲存體目標時，您需要選擇該目標的使用方式模型。 此模型會決定資料的快取方式。

選項有三個：

* **讀取大量、不頻繁的寫入**-如果您想要加速對靜態或極少變更之檔案的讀取權限，請使用此選項。

  此選項會快取用戶端讀取的檔案，但會立即將寫入傳遞至後端儲存體。 儲存在快取中的檔案永遠不會與 NFS 存放磁片區上的檔案進行比較。

  如果有可能直接在儲存系統上修改檔案，而不需要先將檔案寫入快取，請不要使用此選項。 如果發生這種情況，檔案的快取版本永遠不會更新後端的變更，而且資料集可能會變得不一致。

* **大於15% 寫入**-這個選項可加速讀取和寫入效能。 使用此選項時，所有用戶端都必須透過 Azure HPC 快取存取檔案，而不是直接裝載後端存放裝置。 快取檔案將會有不會儲存在後端的最近變更。

  在此使用模型中，不會針對後端儲存體上的檔案檢查快取中的檔案。 檔案的快取版本被假設為較新的。 快取中已修改的檔案會在快取中的一小時內寫入後端儲存體系統，而不會進行任何額外的變更。

* **用戶端寫入 NFS 目標，略過**快取-如果工作流程中的任何用戶端直接將資料寫入儲存體系統，而未先寫入快取，請選擇此選項。 系統會快取用戶端要求的檔案，但從用戶端對這些檔案所做的任何變更，都會立即傳遞回後端儲存體系統。

  使用此使用模式時，會經常檢查快取中的檔案是否有更新的後端版本。 這項驗證可讓檔案在快取之外變更，同時維持資料一致性。

下表摘要說明使用方式模型的差異：

| 使用方式模型                   | 快取模式 | 後端驗證 | 最大回寫延遲 |
|-------------------------------|--------------|-----------------------|--------------------------|
| 讀取大量、不頻繁的寫入 | 讀取         | 永不                 | 無                     |
| 超過15% 寫入       | 讀取/寫入   | 永不                 | 1 小時                   |
| 用戶端略過快取      | 讀取         | 30 秒            | 無                     |

### <a name="create-an-nfs-storage-target"></a>建立 NFS 儲存體目標

### <a name="portal"></a>[入口網站](#tab/azure-portal)

![已定義 NFS 目標的 [新增儲存體目標] 頁面螢幕擷取畫面](media/add-nfs-target.png)

為支援 NFS 的儲存體目標提供這種資訊：

* **儲存體目標名稱**-在 Azure HPC 快取中設定用來識別此儲存體目標的名稱。

* **目標型別**-選擇 [ **NFS**]。

* **主機名稱**-輸入 NFS 儲存體系統的 IP 位址或完整功能變數名稱。 只有當您的快取可以存取可解析名稱的 DNS 伺服器時， (才使用功能變數名稱。 ) 

* **使用方式模型**-根據您的工作流程選擇其中一個資料快取設定檔，如[選擇上述使用方式模型](#choose-a-usage-model)中所述。

### <a name="nfs-namespace-paths"></a>NFS 命名空間路徑

NFS 儲存體目標可以有多個虛擬路徑，前提是每個路徑都代表相同儲存系統上的不同匯出或子目錄。

建立一個儲存體目標的所有路徑。

您可以隨時在儲存體目標上[新增和編輯命名空間路徑](hpc-cache-edit-storage.md)。

針對每個命名空間路徑填入這些值：

* **虛擬命名空間路徑**-設定此儲存體目標的面向用戶端檔案路徑。 請參閱[設定匯總命名空間](hpc-cache-namespace.md)，以深入瞭解虛擬命名空間功能。

* **Nfs 匯出路徑**-輸入 nfs 匯出的路徑。

* **子目錄路徑**-如果您想要掛接匯出的特定子目錄，請在這裡輸入。 如果不是，請將此欄位保留空白。

完成後，按一下 **[確定]** 以新增儲存體目標。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 Azure CLI 命令[az hpc-cache nfs-儲存體-目標新增](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add)來建立儲存體目標。 除了快取名稱和快取資源群組之外，請提供這些值：

* ``--name``-設定在 Azure HPC 快取中識別此儲存體目標的名稱。
* ``--nfs3-target``-NFS 儲存體系統的 IP 位址。  (如果您的快取有權存取可解析名稱的 DNS 伺服器，您可以在這裡使用完整功能變數名稱。 ) 
* ``--nfs3-usage-model``-其中一個資料快取設定檔，如上方的[選擇使用方式模型](#choose-a-usage-model)中所述。

  使用命令[az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)來驗證使用模式的名稱。

* ``--junction``-聯結參數會將用戶端對向虛擬檔案路徑與儲存系統上的匯出路徑連結在一起。

  NFS 儲存體目標可以有多個虛擬路徑，前提是每個路徑都代表相同儲存系統上的不同匯出或子目錄。 在一個儲存體目標上建立一個儲存系統的所有路徑。

  您可以隨時在儲存體目標上[新增和編輯命名空間路徑](hpc-cache-edit-storage.md)。

  ``--junction``參數會使用下列值：

  * ``namespace-path``-用戶端面向的虛擬檔案路徑
  * ``nfs-export``-要與用戶端對向路徑產生關聯的儲存體系統匯出
  * ``target-path`` (選擇性) -匯出的子目錄（如有需要）

  範例： ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  請參閱[設定匯總命名空間](hpc-cache-namespace.md)，以深入瞭解虛擬命名空間功能。

範例命令：

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

輸出：
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="next-steps"></a>後續步驟

建立儲存體目標之後，請考慮下列其中一項工作：

* [掛接 Azure HPC Cache](hpc-cache-mount.md)
* [將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)

如果您需要更新任何設定，您可以[編輯儲存體目標](hpc-cache-edit-storage.md)。
