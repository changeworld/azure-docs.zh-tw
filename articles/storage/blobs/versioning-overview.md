---
title: Blob 版本設定（預覽）
titleSuffix: Azure Storage
description: Blob 儲存體版本設定（預覽）會自動維護舊版的物件，並使用時間戳來識別它們。 您可以還原先前版本的 blob，以在錯誤地修改或刪除資料時加以復原。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 77af321b74a47306a7202c1fddf6e81edc0ee02a
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926063"
---
# <a name="blob-versioning-preview"></a>Blob 版本設定（預覽）

您可以啟用 Blob 儲存體版本設定（預覽），自動維護舊版的物件。  啟用 blob 版本設定時，您可以還原較舊版本的 blob，以在錯誤地修改或刪除資料時加以復原。

儲存體帳戶上會啟用 Blob 版本設定，並套用至儲存體帳戶中的所有 blob。 為儲存體帳戶啟用 blob 版本設定之後，Azure 儲存體會自動維護儲存體帳戶中每個 blob 的版本。

Microsoft 建議使用 blob 版本設定來維護舊版的 blob，以提供更佳的資料保護。 可能的話，請使用 blob 版本設定而不是 blob 快照集來維護先前的版本。 Blob 快照集提供類似的功能，因為它們會維護舊版的 blob，但您的應用程式必須手動維護快照集。

> [!IMPORTANT]
> Blob 版本控制無法協助您復原意外刪除的儲存體帳戶或容器。 若要防止意外刪除儲存體帳戶，請在儲存體帳戶資源上設定**CannotDelete**鎖定。 如需有關鎖定 Azure 資源的詳細資訊，請參閱[鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

## <a name="how-blob-versioning-works"></a>Blob 版本控制的運作方式

版本會在指定的時間點，捕捉 blob 的狀態。 針對儲存體帳戶啟用 blob 版本設定時，Azure 儲存體會在每次修改或刪除 blob 時，自動建立新版本的 blob。

當您建立已啟用版本設定的 blob 時，新的 blob 會是 blob （或基底 blob）的目前版本。 如果您後續修改該 blob，Azure 儲存體會建立一個版本，以在修改之前先捕獲 blob 的狀態。 修改後的 blob 會變成新的目前版本。 每次修改 blob 時，就會建立新的版本。

當您刪除已啟用版本設定的 blob 時，Azure 儲存體會建立一個版本，它會在刪除 blob 之前，先捕獲該 blob 的狀態。 然後會刪除 blob 的目前版本，但會保存 blob 的版本，以便在需要時重新建立。 

Blob 版本是不可變的。 您無法修改現有 blob 版本的內容或中繼資料。

### <a name="version-id"></a>版本識別碼

每個 blob 版本都是以版本識別碼來識別。 版本識別碼的值是寫入或更新 blob 的時間戳記。 建立版本時，會指派版本識別碼。

您可以藉由提供版本識別碼，對特定版本的 blob 執行讀取或刪除作業。 如果您省略版本識別碼，作業會針對目前的版本（基底 blob）進行操作。

當您呼叫寫入作業來建立或修改 blob 時，Azure 儲存體會在回應中傳回*x-ms 版本識別碼*標頭。 此標頭包含寫入作業所建立之目前 blob 版本的版本識別碼。

版本識別碼在版本的存留期間保持不變。

### <a name="versioning-on-write-operations"></a>寫入作業的版本控制

當 blob 版本設定開啟時，對 blob 進行的每個寫入作業都會建立新的版本。 寫入作業包括[放置 blob](/rest/api/storageservices/put-blob)、[放置區塊清單](/rest/api/storageservices/put-block-list)、[複製 Blob](/rest/api/storageservices/copy-blob)和[設定 blob 中繼資料](/rest/api/storageservices/set-blob-metadata)。

如果寫入作業會建立新的 blob，則產生的 blob 會是 blob 的目前版本。 如果寫入作業修改現有的 blob，則會在更新的 blob （也就是目前的版本）中捕捉新的資料，而 Azure 儲存體會建立儲存 blob 先前狀態的版本。

為了簡單起見，本文中顯示的圖表會將版本識別碼顯示為簡單的整數值。 實際上，版本識別碼是時間戳記。 目前的版本以藍色顯示，而先前的版本則以灰色顯示。

下圖顯示寫入作業對 blob 版本的影響。 建立 blob 時，該 blob 就是目前的版本。 修改相同的 blob 時，會建立新的版本來儲存 blob 先前的狀態，而更新的 blob 會變成目前的版本。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="顯示寫入作業如何影響已設定版本之 blob 的圖表":::

> [!NOTE]
> 在為儲存體帳戶啟用版本設定之前建立的 blob 沒有版本識別碼。 修改該 blob 時，修改後的 blob 會變成目前的版本，並建立一個版本，以便在更新之前儲存 blob 的狀態。 版本會指派為其建立時間的版本識別碼。

### <a name="versioning-on-delete-operations"></a>刪除作業的版本控制

當您刪除 blob 時，目前的 blob 版本會變成先前的版本，並刪除基底 blob。 刪除 blob 時，會保留所有現有舊版的 blob。

呼叫不含版本識別碼的[刪除 blob](/rest/api/storageservices/delete-blob)作業會刪除基底 Blob。 若要刪除特定版本，請在刪除操作上提供該版本的識別碼。

下圖顯示在已建立版本的 blob 上進行刪除作業的效果：

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="此圖顯示已建立版本的 blob 刪除":::

將新資料寫入 blob 會建立新版本的 blob。 任何現有的版本都不受影響，如下圖所示。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="顯示刪除後重新建立版本 blob 的圖表":::

### <a name="blob-types"></a>Blob 類型

針對儲存體帳戶啟用 blob 版本設定時，區塊 blob 上的所有寫入和刪除作業都會觸發新版本的建立，但[Put block](/rest/api/storageservices/put-block)操作除外。

針對分頁 blob 和附加 blob，只有寫入和刪除作業的子集會觸發版本的建立。 這些作業包括：

- [Put Blob](/rest/api/storageservices/put-blob) \(英文\)
- [Put Block List](/rest/api/storageservices/put-block-list) \(英文\)
- [刪除 Blob](/rest/api/storageservices/delete-blob)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [複製 Blob](/rest/api/storageservices/copy-blob)

下列作業不會觸發新版本的建立。 若要從這些作業中捕捉變更，請手動建立快照集：

- [Put 頁面](/rest/api/storageservices/put-page)（分頁 blob）
- [附加區塊](/rest/api/storageservices/append-block)（附加 blob）

Blob 的所有版本都必須是相同的 blob 類型。 如果 blob 有先前的版本，除非您先刪除 blob 及其所有版本，否則無法使用另一種類型覆寫其中一個類型的 blob。

### <a name="access-tiers"></a>存取層級

藉由呼叫「[設定 Blob 層](/rest/api/storageservices/set-blob-tier)」作業，您可以將任何版本的區塊 blob （包括目前版本）移至不同的 blob 存取層。 您可以藉由將較舊版本的 blob 移至非經常性或封存層，以利用較低的容量定價。 如需詳細資訊，請參閱[Azure Blob 儲存體：經常性、非經常性和封存存取層](storage-blob-storage-tiers.md)。

若要將區塊 blob 移至適當層的程式自動化，請使用 blob 生命週期管理。 如需生命週期管理的詳細資訊，請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。

## <a name="enable-or-disable-blob-versioning"></a>啟用或停用 blob 版本設定

若要瞭解如何啟用或停用 blob 版本設定，請參閱[啟用或停用 blob 版本](versioning-enable.md)設定。

停用 blob 版本設定並不會刪除現有的 blob、版本或快照集。 當您關閉 blob 版本設定時，任何現有的版本仍可在您的儲存體帳戶中存取。 後續不會建立任何新版本。

如果在儲存體帳戶上停用版本設定後建立或修改 blob，則覆寫 blob 會建立新的版本。 更新的 blob 已不再是目前的版本，而且沒有版本識別碼。 Blob 的所有後續更新都會覆寫其資料，而不會儲存先前的狀態。

您可以在停用版本設定後，使用版本識別碼來讀取或刪除版本。 您也可以在停用版本設定後，列出 blob 的版本。

下圖顯示在停用版本控制功能之後修改 blob，如何建立未設定版本的 blob。 與 blob 相關聯的任何現有版本都會保存。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="顯示停用版本設定後，基底 blob 已修改的圖表":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 版本設定和虛刪除

Blob 版本設定和 blob 虛刪除會一起工作，以提供最佳的資料保護。 當您啟用虛刪除時，您會指定 Azure 儲存體應保留虛刪除 blob 的時間長度。 任何虛刪除的 blob 版本都會保留在系統中，而且可以在虛刪除保留期限內取消刪除。 如需有關 blob 虛刪除的詳細資訊，請參閱[Azure 儲存體 blob 的虛刪除](storage-blob-soft-delete.md)。

### <a name="deleting-a-blob-or-version"></a>刪除 blob 或版本

虛刪除提供刪除 blob 版本的額外保護。 如果已在儲存體帳戶上啟用版本控制和虛刪除，則當您刪除 blob 時，Azure 儲存體會建立新的版本，以在刪除之前立即儲存 blob 的狀態，並刪除目前的版本。 新版本不會虛刪除，而且不會在虛刪除保留期限到期時移除。

當您刪除舊版的 blob 時，會虛刪除版本。 虛刪除的版本會保留在儲存體帳戶的虛刪除設定中指定的保留期間內，而且在虛刪除保留期限到期時，會永久刪除。

若要移除舊版的 blob，請藉由指定版本識別碼來明確刪除它。

下圖顯示當您刪除 blob 或 blob 版本時，會發生什麼事。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="此圖顯示刪除已啟用虛刪除的版本":::

如果在儲存體帳戶上啟用版本控制和虛刪除，則在修改或刪除 blob 或 blob 版本時，不會建立虛刪除的快照集。

### <a name="restoring-a-soft-deleted-version"></a>還原虛刪除的版本

當虛刪除保留期限生效時，您可以藉由在版本上呼叫取消[刪除 blob](/rest/api/storageservices/undelete-blob)作業來還原已虛刪除的 blob 版本。 取消**刪除 blob**作業會還原 Blob 的所有虛刪除版本。

使用取消**刪除 Blob**作業來還原已虛刪除的版本，並不會將任何版本升級為目前的版本。 若要還原目前的版本，請先還原所有已虛刪除的版本，然後使用[複製 blob](/rest/api/storageservices/copy-blob)作業來複製先前的版本，以還原 Blob。

下圖顯示如何使用「取消**刪除 blob** 」作業來還原已虛刪除的 blob 版本，以及如何使用「**複製 blob** 」作業來還原 blob 的目前版本。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="此圖顯示如何還原已虛刪除的版本":::

經過虛刪除保留期限之後，所有虛刪除的 blob 版本都會永久刪除。

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 版本設定和 blob 快照集

Blob 快照集是特定時間點所取得之 blob 的唯讀複本。 Blob 快照集和 blob 版本類似，但您或您的應用程式會手動建立快照集，而當您的儲存體帳戶啟用 blob 版本設定時，會在寫入或刪除作業上自動建立 blob 版本。

> [!IMPORTANT]
> Microsoft 建議您在啟用 blob 版本設定之後，也會更新應用程式，以停止取得區塊 blob 的快照集。 如果您的儲存體帳戶已啟用版本設定，所有區塊 blob 更新和刪除都會依版本來加以捕捉和保留。 如果已啟用 blob 版本設定，則製作快照集不會針對您的區塊 blob 資料提供任何額外的保護，而且可能會增加成本和應用程式的複雜性。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>啟用版本控制時建立 blob 的快照集

雖然不建議這麼做，但您可以建立也是版本設定之 blob 的快照集。 如果您在啟用版本控制時無法更新應用程式來停止製作 blob 的快照集，您的應用程式可以同時支援快照集和版本。

當您建立已建立版本之 blob 的快照集時，會在建立快照集時建立新的版本。 建立快照集時，也會建立新的目前版本。

下圖顯示當您建立已設定版本之 blob 的快照集時，會發生什麼事。 在圖表中，版本識別碼為2和3的 blob 版本和快照集包含相同的資料。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="顯示已建立版本之 blob 快照集的圖表":::

## <a name="authorize-operations-on-blob-versions"></a>授權 blob 版本的作業

您可以使用下列其中一種方法來授權 blob 版本的存取權：

- 藉由使用角色型存取控制（RBAC）將許可權授與 Azure Active Directory （Azure AD）安全性主體。 Microsoft 建議使用 Azure AD 以獲得更佳的安全性和易用性。 如需有關使用 Azure AD 搭配 blob 作業的詳細資訊，請參閱[使用 Azure Active Directory 授權存取 blob 和佇列](../common/storage-auth-aad.md)。
- 藉由使用共用存取簽章（SAS）來委派 blob 版本的存取權。 指定已簽署資源類型`bv`的版本識別碼（代表 blob 版本），以針對特定版本的作業建立 SAS 權杖。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。
- 藉由使用帳戶存取金鑰，以共用金鑰組 blob 版本的作業進行授權。 如需詳細資訊，請參閱[使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key)。

Blob 版本設定是為了保護您的資料免于遭到意外或惡意刪除所設計。 若要加強保護，刪除 blob 版本需要特殊許可權。 下列各節說明刪除 blob 版本所需的許可權。

### <a name="rbac-action-to-delete-a-blob-version"></a>用來刪除 blob 版本的 RBAC 動作

下表顯示哪些 RBAC 動作支援刪除 blob 或 blob 版本。

| 描述 | Blob 服務作業 | 需要 RBAC 資料動作 | RBAC 內建角色支援 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 刪除 blob 的目前版本 | 刪除 Blob | **Microsoft. Storage/storageAccounts/blobServices/container/blob/delete/actionDeleting** | 儲存體 Blob 資料參與者 |
| 刪除版本 | 刪除 Blob | **Microsoft. Storage/storageAccounts/blobServices/container/blob/deleteBlobVersion/** | 儲存體 Blob 資料擁有者 |

### <a name="shared-access-signature-sas-parameters"></a>共用存取簽章（SAS）參數

Blob 版本的已簽署資源是`bv`。 如需詳細資訊，請參閱[建立服務 SAS](/rest/api/storageservices/create-service-sas)或[建立使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下表顯示在 SAS 上刪除 blob 版本所需的許可權。

| [權限]**** | **URI 符號** | **允許的作業** |
|----------------|----------------|------------------------|
| 刪除         | x              | 刪除 blob 版本。 |

## <a name="about-the-preview"></a>關於預覽

Blob 版本設定在下欄區域提供預覽：

- 法國南部
- 法國中部
- 加拿大東部
- 加拿大中部

預覽僅適用於非生產環境。

2019-10-10 版和更新版本的 Azure 儲存體 REST API 支援 blob 版本設定。

### <a name="storage-account-support"></a>儲存體帳戶支援

Blob 版本設定適用于下列類型的儲存體帳戶：

- 一般用途 v2 儲存體帳戶
- 區塊 blob 儲存體帳戶
- Blob 儲存體帳戶

如果您的儲存體帳戶是一般用途 v1 帳戶，請使用 Azure 入口網站升級至一般用途 v2 帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

目前不支援已啟用階層命名空間可與 Azure Data Lake Storage Gen2 搭配使用的儲存體帳戶。

### <a name="register-for-the-preview"></a>註冊預覽

若要註冊 blob 版本設定預覽，請使用 PowerShell 或 Azure CLI 來提交要求，以向您的訂用帳戶註冊此功能。 核准您的要求之後，您可以使用任何新的或現有的一般用途 v2、Blob 儲存體或高階區塊 blob 儲存體帳戶來啟用 blob 版本設定。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 註冊，請呼叫[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
    
# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向 Azure CLI 註冊，請呼叫[az feature register](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name Versioning
```

---

### <a name="check-the-status-of-your-registration"></a>檢查註冊的狀態

若要檢查註冊的狀態，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查註冊的狀態，請呼叫[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 檢查註冊的狀態，請呼叫[az feature](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name Versioning
```

---

## <a name="pricing-and-billing"></a>價格和計費

啟用 blob 版本設定可能會導致您的帳戶產生額外的資料儲存體費用。 設計您的應用程式時，請務必留意產生這些費用的可能方式，以便將成本降至最低。

Blob 版本（例如 blob 快照集）的計費方式與使用中資料的費率相同。 如果版本與基底 blob 共用區塊或分頁，則您只需支付版本與基底 blob 之間未共用的任何額外區塊或頁面。

> [!NOTE]
> 針對經常覆寫的資料啟用版本設定，可能會導致儲存體容量費用增加，並在列出作業期間增加延遲。 若要減輕這些問題，請在停用版本設定的個別儲存體帳戶中儲存經常覆寫的資料。

### <a name="important-billing-considerations"></a>重要的計費考量

啟用 blob 版本設定時，請務必考慮下列幾點：

- 無論唯一的區塊或分頁是在 blob 或舊版的 blob 中，您的儲存體帳戶都會產生費用。 您的帳戶在您更新其所依據的 blob 之前，不會產生與 blob 相關聯之版本的額外費用。 在您更新 blob 之後，它會從先前的版本分歧。 發生這種情況時，您需支付每個 blob 或版本中唯一區塊或頁面的費用。
- 當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。 即使區塊具有與版本中相同的區塊識別碼和相同的資料，也是如此。 再次認可該區塊之後，它會在任何版本中從其對應的分歧，而且會向您收取其資料的費用。 這同樣適用分頁 Blob 中以相同資料更新的頁面。
- Blob 儲存體沒有判斷兩個區塊是否包含相同資料的方法。 已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。 因為對唯一區塊會產生費用，請務必考慮在啟用版本設定時更新 blob 會導致額外的唯一區塊及額外費用。
- 啟用 blob 版本控制時，請在區塊 blob 上設計更新作業，使其更新最少可能的區塊數目。 允許對區塊進行細微控制的寫入作業，會[放入區塊](/rest/api/storageservices/put-block)和[放置區塊清單](/rest/api/storageservices/put-block-list)。 另一方面， [Put blob](/rest/api/storageservices/put-blob)作業會取代 Blob 的整個內容，因此可能會導致額外的費用。

### <a name="versioning-billing-scenarios"></a>版本控制計費案例

下列案例示範如何針對區塊 blob 和其版本產生費用。

#### <a name="scenario-1"></a>實例 1

在案例1中，blob 具有先前的版本。 建立版本後，blob 尚未更新，因此只會針對唯一的區塊1、2和3產生費用。

![Azure 儲存體資源](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>案例 2

在案例2中，blob 中的一個區塊（圖中的區塊3）已更新。 雖然更新的區塊包含相同的資料和相同的識別碼，但它與先前版本中的區塊3並不相同。 因此，此帳戶必須支付四個區塊的費用。

![Azure 儲存體資源](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>案例 3

在案例3中，blob 已更新，但版本沒有。 區塊3已取代為基底 blob 中的區塊4，但先前版本仍然反映區塊3。 因此，此帳戶必須支付四個區塊的費用。

![Azure 儲存體資源](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>案例 4

在案例 4 中，基底 Blob 已完全更新，且未包含它的任何原始區塊。 因此，帳戶會針對基底 blob 中的所有八個&mdash;唯一區塊和前一版中的四個區塊收費。 如果您使用 Put Blob 作業寫入 blob，就會發生此情況，因為它會取代基底 blob 的完整內容。

![Azure 儲存體資源](./media/versioning-overview/versions-billing-scenario-4.png)

## <a name="see-also"></a>另請參閱

- [啟用 Blob 版本設定](versioning-enable.md)
- [建立 blob 的快照集](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure 儲存體 Blob 的虛刪除](storage-blob-soft-delete.md)
