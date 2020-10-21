---
title: 將儲存體新增至 Azure HPC Cache
description: 如何定義儲存體目標，讓您的 Azure HPC Cache 可以使用內部部署 NFS 系統或 Azure Blob 容器來進行長期檔案儲存
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: c1edbaf394a4abd36e47843a6f419eb9d62f08d7
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340303"
---
# <a name="add-storage-targets"></a>新增儲存體目標

*儲存體目標* 是透過 Azure HPC Cache 存取之檔案的後端儲存體。 您可以新增 NFS 儲存體 (例如內部部署硬體系統) ，或將資料儲存在 Azure Blob 中。

您最多可以為一個快取定義十個不同的儲存目標。 快取會以一個匯總的命名空間呈現所有的儲存體目標。

命名空間路徑會在您新增儲存體目標之後個別設定。 一般情況下，NFS 儲存體目標最多可以有十個命名空間路徑，或用於某些大型設定。 讀取 [NFS 命名空間路徑](add-namespace-paths.md#nfs-namespace-paths) 以取得詳細資料。

請記住，您必須能夠從快取的虛擬網路存取儲存體匯出。 針對內部部署硬體儲存體，您可能需要設定可解析主機名稱以進行 NFS 儲存體存取的 DNS 伺服器。 深入瞭解 [DNS 存取](hpc-cache-prerequisites.md#dns-access)。

建立快取之後，新增儲存體目標。 遵循此程式：

1. [建立快取](hpc-cache-create.md)
1. 請在本文中定義儲存體目標 (資訊) 
1. 建立[匯總命名空間](hpc-cache-namespace.md) ([的面向用戶端路徑](add-namespace-paths.md)) 

根據您要新增 Azure Blob 儲存體或 NFS 匯出，新增儲存體目標的程式會略有不同。 以下各項的詳細資料如下所示。

按一下下圖以觀看建立快取，以及從 Azure 入口網站新增儲存體目標的 [影片示範](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) 。

[![影片縮圖： Azure HPC Cache：設定 (按一下流覽影片頁面) ](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>新增 Azure Blob 儲存體目標

新的 Blob 儲存體目標需要空的 Blob 容器或使用 Azure HPC Cache 雲端檔案系統格式的資料填入的容器。 深入瞭解如何預先載入將 [資料移至 Azure blob 儲存體](hpc-cache-ingest.md)的 Blob 容器。

Azure 入口網站的 [ **新增儲存體目標** ] 頁面包含選項，可在您新增 Blob 容器之前，先建立新的 Blob 容器。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，開啟您的快取實例，然後按一下左側邊欄上的 [ **儲存目標** ]。

![[設定] > 儲存體目標] 頁面的螢幕擷取畫面，其中有兩個現有的儲存體目標在資料表中，並在資料表上方的 [+ 新增儲存體目標] 按鈕周圍醒目提示](media/add-storage-target-button.png)

[ **儲存體目標** ] 頁面會列出所有現有的目標，並提供連結來新增一個目標。

按一下 [ **新增儲存體目標** ] 按鈕。

![[新增儲存體目標] 頁面的螢幕擷取畫面，其中填入新 Azure Blob 儲存體目標的資訊](media/hpc-cache-add-blob.png)

若要定義 Azure Blob 容器，請輸入此資訊。

* **儲存體目標名稱** -設定在 Azure HPC Cache 中識別此儲存目標的名稱。
* **目標型別** -選擇 **Blob**。
* **儲存體帳戶** -選取您想要使用的帳戶。

  您將需要授權快取實例來存取儲存體帳戶，如 [新增存取角色](#add-the-access-control-roles-to-your-account)中所述。

  如需您可以使用之儲存體帳戶類型的詳細資訊，請參閱 [Blob 儲存體需求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* **儲存體容器** -選取此目標的 Blob 容器，或按一下 [ **建立新**的]。

  ![對話方塊的螢幕擷取畫面，用來指定新容器 (私人) 的名稱和存取層級](media/add-blob-new-container.png)

完成時，按一下 **[確定]** 以新增儲存體目標。

> [!NOTE]
> 如果您的儲存體帳戶防火牆設定為僅限「選取的網路」的存取權，請使用 [有關 Blob 儲存體帳戶防火牆設定](hpc-cache-blob-firewall-fix.md)的解決方法中所述的暫時因應措施。

### <a name="add-the-access-control-roles-to-your-account"></a>將存取控制角色新增至您的帳戶

Azure HPC Cache 使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/index.yml) 授權快取服務存取 Azure Blob 儲存體目標的儲存體帳戶。

儲存體帳戶擁有者必須明確地為使用者「HPC Cache 資源提供者」新增角色 [儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#storage-account-contributor) 和 [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 。

您可以事先完成這項作業，或按一下您新增 Blob 儲存體目標的頁面上的連結。 請記住，角色設定最多可能需要五分鐘的時間才會傳播到 Azure 環境，因此您應該在新增角色之後等候幾分鐘，再建立儲存體目標。

新增 Azure 角色的步驟：

1. 開啟儲存體帳戶 ** (IAM) 頁面的存取控制 ** 。  ([ **新增儲存體目標** ] 頁面中的連結，就會自動為選取的帳戶開啟此頁面。 ) 

1. 按一下 **+** 頁面頂端的，然後選擇 [ **新增角色指派**]。

1. 從清單中選取「儲存體帳戶參與者」角色。

1. 在 [ **將存取權指派給** ] 欄位中，保留選取的預設值 ( [Azure AD 使用者、群組或服務主體] ) 。  

1. 在 [ **選取** ] 欄位中，搜尋「hpc」。  此字串應該符合一個名為「HPC Cache 資源提供者」的服務主體。 按一下該主體以選取它。

   > [!NOTE]
   > 如果搜尋 "hpc" 無法運作，請嘗試改為使用字串 "storagecache"。 在 GA) 之前參與預覽版 (的使用者，可能需要使用較舊的服務主體名稱。

1. 按一下底部的 [ **儲存** ] 按鈕。

1. 重複此程式以指派「儲存體 Blob 資料參與者」角色。  

![新增角色指派 GUI 的螢幕擷取畫面](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>先決條件：儲存體帳戶存取

在您新增 blob 儲存體目標之前，請先檢查快取是否有正確的角色可存取儲存體帳戶，以及防火牆設定是否允許儲存體目標建立。

Azure HPC Cache 使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/index.yml) 授權快取服務存取 Azure Blob 儲存體目標的儲存體帳戶。

儲存體帳戶擁有者必須明確地為使用者「HPC Cache 資源提供者」新增角色 [儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#storage-account-contributor) 和 [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 。

如果快取沒有這些角色，建立儲存體目標將會失敗。

角色設定最多可能需要五分鐘的時間才能傳播至 Azure 環境，因此在建立儲存體目標之前，您應該等候幾分鐘的時間來新增角色。

如需詳細指示， [請參閱使用 Azure CLI 來新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-cli.md) 。

也請檢查您的儲存體帳戶的防火牆設定。 如果防火牆設定為僅限「選取的網路」的存取權，則儲存體目標建立可能會失敗。 使用 [針對 Blob 儲存體帳戶防火牆設定](hpc-cache-blob-firewall-fix.md)所記載的因應措施。

### <a name="add-a-blob-storage-target-with-azure-cli"></a>使用 Azure CLI 新增 blob 儲存體目標

使用 [az hpc cache blob-儲存體-目標新增](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) 介面來定義 Azure blob 儲存體目標。

> [!NOTE]
> Azure CLI 的命令目前會要求您在新增儲存體目標時建立命名空間路徑。 這與 Azure 入口網站介面所使用的進程不同。

除了標準資源群組和快取名稱參數之外，您還必須為儲存體目標提供下列選項：

* ``--name`` -設定在 Azure HPC Cache 中識別此儲存目標的名稱。

* ``--storage-account`` -下列格式的帳戶識別碼：/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  如需您可以使用之儲存體帳戶類型的詳細資訊，請參閱 [Blob 儲存體需求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* ``--container-name`` -指定要用於此儲存體目標的容器名稱。

* ``--virtual-namespace-path`` -為此儲存目標設定面向用戶端的檔案路徑。 以引號括住路徑。 若要深入瞭解虛擬命名空間功能，請閱讀 [匯總的命名空間](hpc-cache-namespace.md) 。

範例命令：

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>新增 NFS 儲存體目標

NFS 儲存體目標有不同于 Blob 儲存體目標的設定。 使用量模型設定可協助快取有效率地快取此儲存體系統的資料。

![已定義 NFS 目標的 [新增儲存體目標] 頁面螢幕擷取畫面](media/add-nfs-target.png)

> [!NOTE]
> 建立 NFS 儲存體目標之前，請確定您的儲存系統可從 Azure HPC Cache 存取，並符合許可權需求。 如果快取無法存取儲存體系統，則建立儲存體目標將會失敗。 如需詳細資訊，請參閱 [NFS 儲存體需求](hpc-cache-prerequisites.md#nfs-storage-requirements) 和 [疑難排解 NAS 設定和 NFS 儲存體的問題](troubleshoot-nas.md) 。

### <a name="choose-a-usage-model"></a>選擇使用模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

當您建立指向 NFS 儲存體系統的儲存體目標時，您需要選擇該目標的使用量模型。 此模型會決定資料的快取方式。

選項有三個：

* **讀取大量、不頻繁的寫入** -如果您想要加快對靜態或很少變更之檔案的讀取權限，請使用此選項。

  此選項會快取用戶端讀取的檔案，但會立即將寫入傳遞至後端儲存體。 儲存在快取中的檔案永遠不會與 NFS 存放磁片區上的檔案進行比較。

  如果沒有先將檔案寫入快取的風險，可能會在儲存系統上直接修改檔案，請不要使用此選項。 如果發生這種情況，檔案的快取版本永遠不會以後端的變更進行更新，而且資料集可能會不一致。

* **大於15% 的寫入** -此選項可加速讀取和寫入效能。 使用此選項時，所有用戶端都必須透過 Azure HPC Cache 存取檔案，而不是直接裝載後端儲存體。 快取的檔案將會有最近的變更，不會儲存在後端。

  在此使用模式中，不會針對後端儲存體上的檔案檢查快取中的檔案。 檔案的快取版本會被假設為較新的。 快取中已修改的檔案會在快取中的一小時後寫入後端儲存體系統，且不會進行任何額外的變更。

* **用戶端寫入至 NFS 目標，略過** 快取-如果工作流程中有任何用戶端直接將資料寫入儲存體系統，而未先寫入快取，請選擇此選項。 系統會快取用戶端要求的檔案，但從用戶端對這些檔案所做的任何變更都會立即傳回到後端儲存體系統。

  使用此使用模式時，會經常針對更新的後端版本檢查快取中的檔案。 此驗證可讓您在快取之外變更檔案，同時維持資料一致性。

下表摘要說明使用模型的差異：

| 使用量模型                   | 快取模式 | 後端驗證 | 最大回寫延遲 |
|-------------------------------|--------------|-----------------------|--------------------------|
| 大量讀取、不頻繁的寫入 | 讀取         | 永不                 | 無                     |
| 大於15% 寫入       | 讀取/寫入   | 永不                 | 1 小時                   |
| 用戶端略過快取      | 讀取         | 30 秒            | 無                     |

### <a name="create-an-nfs-storage-target"></a>建立 NFS 儲存體目標

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，開啟您的快取實例，然後按一下左側邊欄上的 [ **儲存目標** ]。

![[設定] > 儲存體目標] 頁面的螢幕擷取畫面，其中有兩個現有的儲存體目標在資料表中，並在資料表上方的 [+ 新增儲存體目標] 按鈕周圍醒目提示](media/add-storage-target-button.png)

[ **儲存體目標** ] 頁面會列出所有現有的目標，並提供連結來新增一個目標。

按一下 [ **新增儲存體目標** ] 按鈕。

![已定義 NFS 目標的 [新增儲存體目標] 頁面螢幕擷取畫面](media/add-nfs-target.png)

提供此資訊給支援 NFS 的儲存體目標：

* **儲存體目標名稱** -設定在 Azure HPC Cache 中識別此儲存目標的名稱。

* **目標型別** -選擇 **NFS**。

* **主機名稱** -輸入 NFS 儲存體系統的 IP 位址或完整功能變數名稱。 只有當您的快取可存取可解析名稱的 DNS 伺服器時， (才使用功能變數名稱。 ) 

* **使用量模型** -依據您的工作流程選擇其中一個資料快取設定檔，如上面的 [選擇使用方式模型](#choose-a-usage-model) 中所述。

完成時，按一下 **[確定]** 以新增儲存體目標。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 Azure CLI 命令 [az hpc-cache nfs-storage-目標新增](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) 來建立儲存體目標。

> [!NOTE]
> Azure CLI 的命令目前會要求您在新增儲存體目標時建立命名空間路徑。 這與 Azure 入口網站介面所使用的進程不同。

除了快取名稱和快取資源群組之外，還提供這些值：

* ``--name`` -設定在 Azure HPC Cache 中識別此儲存目標的名稱。
* ``--nfs3-target`` -NFS 儲存體系統的 IP 位址。  (如果您的快取可存取可解析名稱的 DNS 伺服器，您可以在此使用完整功能變數名稱。 ) 
* ``--nfs3-usage-model`` -資料快取設定檔的其中一個，如上面的 [選擇使用量模型](#choose-a-usage-model)中所述。

  使用命令 [az hpc-快取使用量-模型清單](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)來驗證使用模型的名稱。

* ``--junction`` -連接參數會將面向用戶端的虛擬檔案路徑與儲存系統上的匯出路徑連結。

  NFS 儲存體目標可以有多個虛擬路徑，只要每個路徑都代表相同儲存系統上的不同匯出或子目錄。 在一個儲存體目標上建立一個儲存系統的所有路徑。

  您可以隨時在儲存體目標上 [新增和編輯命名空間路徑](add-namespace-paths.md) 。

  ``--junction``參數會使用下列值：

  * ``namespace-path`` -面向用戶端的虛擬檔案路徑
  * ``nfs-export`` -要與面向用戶端路徑建立關聯的儲存體系統匯出
  * ``target-path`` (選擇性) -匯出的子目錄（如有需要）

  範例： ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  若要深入瞭解虛擬命名空間功能，請參閱 [設定匯總的命名空間](hpc-cache-namespace.md) 。

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

## <a name="view-storage-targets"></a>查看儲存體目標

您可以使用 Azure 入口網站或 Azure CLI 來顯示已為快取定義的儲存體目標。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，開啟您的快取實例，然後按一下 [ **儲存體目標**] （位於左側提要欄位的設定標題底下）。 [儲存目標] 頁面會列出新增或刪除所有現有的目標和控制項。

按一下儲存體目標的名稱，以開啟其詳細資料頁面。

若要深入瞭解，請參閱 [編輯儲存體目標](hpc-cache-edit-storage.md) 。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 [az hpc cache storage-target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) 選項來顯示快取的現有儲存目標。 提供快取名稱和資源群組 (，除非您已將它設定為全域) 。

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

使用 [az hpc cache storage-target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) 來查看特定儲存目標的詳細資料。  (依名稱指定儲存目標。 ) 

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

## <a name="next-steps"></a>後續步驟

建立儲存體目標之後，請繼續進行這些工作，以準備好使用您的快取：

* [設定匯總的命名空間](add-namespace-paths.md)
* [掛接 Azure HPC Cache](hpc-cache-mount.md)
* [將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)

如果您需要更新任何設定，可以 [編輯儲存體目標](hpc-cache-edit-storage.md)。