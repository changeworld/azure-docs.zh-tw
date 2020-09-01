---
title: Blob 版本設定
titleSuffix: Azure Storage
description: Blob 儲存體版本設定會自動維護舊版的物件，並使用時間戳來識別它們。 您可以還原舊版的 blob 來復原您的資料（如果資料錯誤地修改或刪除）。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 72597d445be41ede47d043d11653df139bc52d0d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226257"
---
# <a name="blob-versioning"></a>Blob 版本設定

您可以啟用 Blob 儲存體版本設定，以自動維護舊版的物件。  啟用 blob 版本設定時，您可以還原舊版的 blob 來復原您的資料（如果錯誤遭到修改或刪除）。

儲存體帳戶上會啟用 blob 版本設定，並套用至儲存體帳戶中的所有 blob。 啟用儲存體帳戶的 blob 版本設定之後，Azure 儲存體會自動維護儲存體帳戶中每個 blob 的版本。

Microsoft 建議使用 blob 版本設定來維護舊版的 blob，以獲得較佳的資料保護。 可能的話，請使用 blob 版本設定，而不是 blob 快照集來維護先前的版本。 Blob 快照集提供類似的功能，因為它們會維護較早版本的 blob，但您的應用程式必須手動維護快照集。

若要瞭解如何啟用 blob 版本設定，請參閱 [啟用和管理 blob 版本](versioning-enable.md)設定。

> [!IMPORTANT]
> Blob 版本設定無法協助您從意外刪除的儲存體帳戶或容器復原。 若要防止意外刪除儲存體帳戶，請在儲存體帳戶資源上設定 **CannotDelete** 鎖定。 如需有關鎖定 Azure 資源的詳細資訊，請參閱 [鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Blob 版本設定的運作方式

版本會在指定的時間點捕捉 blob 的狀態。 針對儲存體帳戶啟用 blob 版本設定時，Azure 儲存體會在每次修改或刪除 blob 時，自動建立新版本的 blob。

當您建立已啟用版本設定的 blob 時，新的 blob 會是目前的 blob 版本 (或基底 blob) 。 如果您之後修改該 blob，Azure 儲存體會在修改之前建立一個可捕獲 blob 狀態的版本。 修改過的 blob 會成為新的目前版本。 每次修改 blob 時，就會建立新的版本。

當您刪除已啟用版本設定的 blob 時，Azure 儲存體會建立一個版本，以在其被刪除之前，先捕捉 blob 的狀態。 Blob 的目前版本會被刪除，但 blob 的版本會保存，因此可以視需要重新建立。 

Blob 版本是不可變的。 您無法修改現有 blob 版本的內容或中繼資料。

Blob 版本設定適用于一般用途 v2、區塊 blob 和 Blob 儲存體帳戶。 目前不支援已啟用階層命名空間的儲存體帳戶搭配 Azure Data Lake Storage Gen2 使用。

版本2019-10-10 和更高版本的 Azure 儲存體 REST API 支援 blob 版本設定。

### <a name="version-id"></a>版本識別碼

每個 blob 版本都是以版本識別碼來識別。 版本識別碼的值是寫入或更新 blob 的時間戳記。 版本識別碼會在建立版本時指派。

您可以藉由提供版本識別碼，對特定版本的 blob 執行讀取或刪除作業。 如果您省略版本識別碼，則作業會針對基底 blob)  (的最新版本運作。

當您呼叫寫入作業來建立或修改 blob 時，Azure 儲存體會在回應中傳回 *x-ms 版本識別碼* 標頭。 此標頭包含寫入作業所建立之 blob 目前版本的版本識別碼。

版本識別碼在版本的存留期內保持不變。

### <a name="versioning-on-write-operations"></a>寫入作業的版本控制

開啟 blob 版本控制時，對 blob 的每個寫入作業都會建立新的版本。 寫入作業包括 [Put blob](/rest/api/storageservices/put-blob)、 [put Block List](/rest/api/storageservices/put-block-list)、 [Copy blob](/rest/api/storageservices/copy-blob)以及 [Set blob Metadata](/rest/api/storageservices/set-blob-metadata)。

如果寫入作業建立新的 blob，則產生的 blob 是 blob 的最新版本。 如果寫入作業修改現有的 blob，則會在更新的 blob 中（也就是目前的版本）中捕捉新的資料，Azure 儲存體建立可儲存 blob 先前狀態的版本。

為了簡單起見，本文中所顯示的圖表會以簡單的整數值顯示版本識別碼。 實際上，版本識別碼是時間戳記。 目前的版本會以藍色顯示，舊版則會以灰色顯示。

下圖顯示寫入作業如何影響 blob 版本。 建立 blob 時，該 blob 會是目前的版本。 修改相同的 blob 時，會建立新的版本來儲存 blob 的先前狀態，而更新的 blob 會成為目前的版本。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="顯示寫入作業如何影響已建立版本之 blob 的圖表":::

> [!NOTE]
> 在針對儲存體帳戶啟用版本設定之前建立的 blob 沒有版本識別碼。 修改該 blob 時，修改過的 blob 會變成目前的版本，並建立一個版本，以在更新之前儲存 blob 的狀態。 版本會指派為其建立時間的版本識別碼。

### <a name="versioning-on-delete-operations"></a>刪除作業的版本控制

當您刪除 blob 時，blob 的目前版本會變成先前的版本，並刪除基底 blob。 刪除 blob 時，會保留所有現有舊版的 blob。

呼叫不含版本識別碼的「 [刪除 blob](/rest/api/storageservices/delete-blob) 」作業會刪除基底 Blob。 若要刪除特定版本，請在刪除操作上提供該版本的識別碼。

下圖顯示針對已建立版本之 blob 的刪除作業效果：

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="顯示已建立版本 blob 之刪除的圖表":::

將新資料寫入 blob 會建立新版本的 blob。 任何現有版本都不會受到影響，如下圖所示。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="顯示刪除後重新建立已建立版本之 blob 的圖表":::

### <a name="blob-types"></a>Blob 類型

針對儲存體帳戶啟用 blob 版本設定時，區塊 blob 上的所有寫入和刪除作業都會觸發新版本的建立作業，但 [Put block](/rest/api/storageservices/put-block) 作業除外。

針對分頁 blob 和附加 blob，只有一小部分的寫入和刪除作業會觸發版本的建立。 這些作業包括：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置區塊清單](/rest/api/storageservices/put-block-list)
- [刪除 Blob](/rest/api/storageservices/delete-blob)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [複製 Blob](/rest/api/storageservices/copy-blob)

下列作業不會觸發新版本的建立。 若要從這些作業中捕獲變更，請手動建立快照集：

- [將頁面放置](/rest/api/storageservices/put-page) (分頁 blob) 
- [附加 blob) 附加區塊](/rest/api/storageservices/append-block) (

Blob 的所有版本都必須是相同的 blob 類型。 如果 blob 有舊版，除非您先刪除 blob 及其所有版本，否則無法以其他類型覆寫某個類型的 blob。

### <a name="access-tiers"></a>存取層級

您可以藉由呼叫「 [設定 Blob 層](/rest/api/storageservices/set-blob-tier) 」作業，將任何版本的區塊 blob （包括目前的版本）移至不同的 blob 存取層。 您可以藉由將較舊版本的 blob 移至非經常性或封存層，來利用較低的容量定價。 如需詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取層和封存存取層](storage-blob-storage-tiers.md)。

若要將區塊 blob 移至適當層的程式自動化，請使用 blob 生命週期管理。 如需生命週期管理的詳細資訊，請參閱 [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。

## <a name="enable-or-disable-blob-versioning"></a>啟用或停用 blob 版本設定

若要瞭解如何啟用或停用 blob 版本設定，請參閱 [啟用和管理 blob 版本](versioning-enable.md)設定。

停用 blob 版本設定並不會刪除現有的 blob、版本或快照集。 當您關閉 blob 版本設定時，您的儲存體帳戶中仍可存取任何現有的版本。 後續不會建立任何新版本。

如果 blob 是在儲存體帳戶上停用版本設定之後建立或修改，則覆寫 blob 會建立新的版本。 更新的 blob 不再是目前的版本，而且沒有版本識別碼。 Blob 的所有後續更新都將覆寫其資料，而不會儲存先前的狀態。

停用版本設定之後，您可以使用版本識別碼來讀取或刪除版本。 停用版本設定之後，您也可以列出 blob 的版本。

下圖顯示停用版本設定之後修改 blob 的方式如何建立未建立版本的 blob。 任何與 blob 相關聯的現有版本都會保存。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="顯示停用版本設定之後修改基底 blob 的圖表":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 版本設定和虛刪除

Blob 版本設定和 blob 虛刪除會一起運作，以提供您最佳的資料保護。 當您啟用虛刪除時，您會指定 Azure 儲存體應保留虛刪除 blob 的時間長度。 任何虛刪除的 blob 版本都會保留在系統中，並可在虛刪除保留期限內取消刪除。 如需有關 blob 虛刪除的詳細資訊，請參閱 [Azure 儲存體 blob 的虛刪除](storage-blob-soft-delete.md)。

### <a name="deleting-a-blob-or-version"></a>刪除 blob 或版本

虛刪除可提供額外的保護來刪除 blob 版本。 如果在儲存體帳戶上啟用版本控制和虛刪除，則當您刪除 blob 時 Azure 儲存體會建立新的版本，以在刪除之前立即儲存 blob 的狀態，並刪除目前的版本。 當虛刪除保留期限到期時，新版本不會被虛刪除，且不會移除。

當您刪除舊版的 blob 時，會虛刪除該版本。 虛刪除的版本會保留在儲存體帳戶的虛刪除設定所指定的保留期間內，並在虛刪除保留期限到期時永久刪除。

若要移除舊版的 blob，請指定版本識別碼以明確地刪除它。

下圖顯示當您刪除 blob 或 blob 版本時，會發生什麼事。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="顯示刪除已啟用虛刪除之版本的圖表":::

如果在儲存體帳戶上啟用版本控制和虛刪除，則修改或刪除 blob 或 blob 版本時，不會建立虛刪除的快照集。

### <a name="restoring-a-soft-deleted-version"></a>還原虛刪除的版本

當虛刪除保留期限生效時，您可以在版本上呼叫「取消 [刪除 blob](/rest/api/storageservices/undelete-blob) 」作業來還原已虛刪除的 blob 版本。 取消 **刪除 blob** 作業會還原所有已虛刪除的 blob 版本。

使用取消 **刪除 Blob** 作業來還原虛刪除的版本，並不會將任何版本升級為目前的版本。 若要還原目前的版本，請先還原所有虛刪除的版本，然後使用 [複製 blob](/rest/api/storageservices/copy-blob) 作業來複製先前的版本，以還原 Blob。

下圖顯示如何使用「取消 **刪除 blob** 」作業來還原已虛刪除的 blob 版本，以及如何使用「 **複製 blob** 」作業來還原目前版本的 blob。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="顯示如何還原虛刪除版本的圖表":::

經過虛刪除保留期限之後，任何虛刪除的 blob 版本都會永久刪除。

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 版本設定和 blob 快照集

Blob 快照集是在特定時間點取得之 blob 的唯讀複本。 Blob 快照集和 blob 版本很類似，但您或您的應用程式會手動建立快照集，而當您的儲存體帳戶啟用 blob 版本設定時，會在寫入或刪除作業上自動建立 blob 版本。

> [!IMPORTANT]
> Microsoft 建議您在啟用 blob 版本設定之後，也會更新您的應用程式，以停止取得區塊 blob 的快照集。 如果您的儲存體帳戶已啟用版本設定，則會依版本來捕捉和保留所有區塊 blob 更新和刪除。 如果啟用 blob 版本設定，取得快照集並不會對區塊 blob 資料提供任何額外的保護，而且可能會增加成本和應用程式的複雜度。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>啟用版本控制時建立 blob 的快照集

雖然不建議這麼做，但您也可以建立也有版本設定之 blob 的快照集。 如果您在啟用版本控制時無法更新應用程式以停止取得 blob 的快照集，您的應用程式可以同時支援快照集和版本。

當您取得已建立版本之 blob 的快照集時，會在建立快照集的同時建立新版本。 取得快照集時，也會建立新的目前版本。

下圖顯示當您取得已建立版本之 blob 的快照時，會發生什麼事。 在圖表中，版本識別碼為2和3的 blob 版本與快照集包含相同的資料。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="顯示已建立版本之 blob 快照集的圖表 ":::

## <a name="authorize-operations-on-blob-versions"></a>在 blob 版本上授權作業

您可以使用下列其中一種方法來授權存取 blob 版本：

- 使用以角色為基礎的存取控制 (RBAC) 將許可權授與 Azure Active Directory (Azure AD) 安全性主體。 Microsoft 建議使用 Azure AD，以獲得更高的安全性與易用性。 如需搭配使用 Azure AD 與 blob 作業的詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 blob 和佇列](../common/storage-auth-aad.md)。
- 使用共用存取簽章 (SAS) 來委派 blob 版本的存取權。 指定代表 blob 版本之已簽署資源類型的版本識別碼， `bv` 以建立特定版本作業的 SAS 權杖。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。
- 使用帳戶存取金鑰，以共用金鑰組 blob 版本進行作業授權。 如需詳細資訊，請參閱[使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key)。

Blob 版本設定的設計是為了保護您的資料免于遭到意外或惡意刪除。 若要增強保護，刪除 blob 版本需要特殊許可權。 下列各節說明刪除 blob 版本所需的許可權。

### <a name="rbac-action-to-delete-a-blob-version"></a>用來刪除 blob 版本的 RBAC 動作

下表顯示支援刪除 blob 或 blob 版本的 RBAC 動作。

| 說明 | Blob 服務作業 | 需要 RBAC 資料動作 | RBAC 內建角色支援 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 正在刪除 blob 的目前版本 | 刪除 Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | 儲存體 Blob 資料參與者 |
| 刪除版本 | 刪除 Blob | **Microsoft. Storage/storageAccounts/blobServices/container/blob/deleteBlobVersion/action** | 儲存體 Blob 資料擁有者 |

### <a name="shared-access-signature-sas-parameters"></a>共用存取簽章 (SAS) 參數

Blob 版本的已簽署資源是 `bv` 。 如需詳細資訊，請參閱 [建立服務 SAS](/rest/api/storageservices/create-service-sas) 或 [建立使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下表顯示在 SAS 上刪除 blob 版本所需的許可權。

| **權限** | **URI 符號** | **允許的作業** |
|----------------|----------------|------------------------|
| 刪除         | x              | 刪除 blob 版本。 |

## <a name="pricing-and-billing"></a>價格和計費

啟用 blob 版本設定可能會導致您的帳戶產生額外的資料儲存體費用。 設計您的應用程式時，請務必留意產生這些費用的可能方式，以便將成本降至最低。

Blob 版本（例如 blob 快照集）的計費方式與使用中資料的費率相同。 版本的計費方式取決於您是否已明確設定基底 blob 的層級，或任何 (或快照) 的版本。 如需 blob 層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取層、非經常性存取層，以及封存存取層](storage-blob-storage-tiers.md)。

如果您未變更 blob 或版本的層級，則會向您收取跨該 blob 的唯一資料區塊、其版本，以及它可能擁有的任何快照集。 如需詳細資訊，請參閱 [未明確設定 blob 層時的計費](#billing-when-the-blob-tier-has-not-been-explicitly-set)。

如果您已變更 blob 或版本的層級，則會向您收取整個物件的費用，而不論 blob 和版本最後是否都在相同的層級中。 如需詳細資訊，請參閱在 [明確設定 blob 層時的計費](#billing-when-the-blob-tier-has-been-explicitly-set)。

> [!NOTE]
> 針對經常覆寫的資料啟用版本控制時，可能會導致儲存體容量費用增加，並在列出作業期間增加延遲。 若要減輕這些問題，請將經常覆寫的資料儲存在停用版本設定的個別儲存體帳戶中。

如需 blob 快照集計費詳細資料的詳細資訊，請參閱 [blob 快照](snapshots-overview.md)集。

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>未明確設定 blob 層時的帳單

如果您未針對基底 blob 或其任何版本明確設定 blob 層，則會向您收取跨 blob、其版本及任何可能擁有之任何快照集的唯一區塊或頁面費用。 在 blob 及其版本間共用的資料只會收取一次。 更新 blob 時，基底 blob 中的資料會從其版本中儲存的資料分歧，而唯一的資料會依每個區塊或頁面收費。

當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。 即使區塊具有相同的區塊識別碼和先前版本中的相同資料，也是如此。 當封鎖再次認可之後，它會從先前版本中的對應項分歧，並向您收取其資料的費用。 這同樣適用分頁 Blob 中以相同資料更新的頁面。

Blob 儲存體沒有方法可判斷兩個區塊是否包含相同的資料。 已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。 因為唯一的區塊會產生費用，請務必記住，在啟用版本設定時更新 blob 將會導致額外的唯一區塊及額外的費用。

啟用 blob 版本設定時，請呼叫區塊 blob 上的更新作業，讓它們更新可能的區塊數目上限。 允許對區塊進行細微控制的寫入作業是 [放置區塊](/rest/api/storageservices/put-block) 和 [放置區塊清單](/rest/api/storageservices/put-block-list)。 另一方面， [Put blob](/rest/api/storageservices/put-blob) 作業會取代整個 blob 的內容，因此可能會導致額外的費用。

下列案例示範在未明確設定 blob 層時，如何針對區塊 blob 和其版本產生費用。

#### <a name="scenario-1"></a>實例 1

在案例1中，blob 有先前的版本。 Blob 自建立版本以來尚未更新，因此只會針對唯一區塊1、2和3產生費用。

![圖1顯示基底 blob 和先前版本中唯一區塊的計費](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>案例 2

在案例2中，blob 中的圖)  (區塊3的一個區塊已更新。 雖然更新的區塊包含相同的資料和相同的識別碼，但它與舊版中的區塊3不同。 因此，此帳戶必須支付四個區塊的費用。

![圖2顯示基底 blob 和先前版本中唯一區塊的計費](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>案例 3

在案例3中，blob 已更新，但版本沒有。 區塊3已由基底 blob 中的區塊4取代，但先前的版本仍會反映區塊3。 因此，此帳戶必須支付四個區塊的費用。

![圖3顯示基底 blob 和先前版本中唯一區塊的計費](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>案例 4

在案例 4 中，基底 Blob 已完全更新，且未包含它的任何原始區塊。 如此一來，帳戶就會針對基底 blob 中的所有八個唯一區塊進行收費 &mdash; ，而在舊版中會收取四個。 如果您要使用 [Put blob](/rest/api/storageservices/put-blob) 作業寫入 blob，就會發生這種情況，因為它會取代基底 blob 的整個內容。

![圖4顯示基底 blob 和先前版本中唯一區塊的計費](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>在明確設定 blob 層時進行計費

如果您已明確設定 blob 或版本 (或快照) 的 blob 層，則會向您收取新層中物件的完整內容長度的費用，而不論它是否與原始層中的物件共用區塊。 您也必須支付原始層中最舊版本的完整內容長度。 任何其他保留在原始階層中的舊版或快照集會針對它們可能共用的唯一區塊收費，如 [未明確設定 blob 層的計費](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

#### <a name="moving-a-blob-to-a-new-tier"></a>將 blob 移至新的層

下表說明將 blob 或版本移至新階層時的計費行為。

| 當 blob 層明確設定時 .。。 | 然後您需要支付 .。。 |
|-|-|
| 具有先前版本的基底 blob | 新層中的基底 blob 和原始階層中最舊的版本，再加上其他版本中的任何唯一區塊。<sup>1</sup> |
| 具有舊版和快照集的基底 blob | 新階層中的基底 blob、原始階層中最舊的版本，以及原始層中最舊的快照集，再加上其他版本或快照<sup>1</sup>中的任何唯一區塊。 |
| 先前的版本 | 新層中的版本和原始層中的基底 blob，以及其他版本中的任何唯一區塊。<sup>1</sup> |

<sup>1</sup>如果有其他先前的版本或尚未從其原始階層移動的快照集，則會根據所包含的唯一區塊數目來收費這些版本或快照集，如 [未明確設定 blob 層的計費](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

明確設定 blob、版本或快照集的層級無法復原。 如果您將 blob 移至新的階層，然後再將它移回其原始階層，則會向您收取物件的完整內容長度，即使它與原始層中的其他物件共用區塊也一樣。

明確設定 blob、版本或快照集層的作業包括：

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- 使用指定的階層[放置 Blob](/rest/api/storageservices/put-blob)
- 使用指定的層級[放置區塊清單](/rest/api/storageservices/put-block-list)
- [複製](/rest/api/storageservices/copy-blob) 具有指定層的 Blob

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>啟用虛刪除時刪除 blob

啟用 blob 虛刪除時，如果您刪除或覆寫已明確設定其層級的基底 blob，則任何先前的虛刪除 blob 版本都會以完整內容長度計費。 如需 blob 版本控制和虛刪除如何一起運作的詳細資訊，請參閱 [blob 版本設定和虛刪除](#blob-versioning-and-soft-delete)。

下表描述已虛刪除之 blob 的計費行為，取決於是否已啟用或停用版本控制。 啟用版本控制時，會在將 blob 虛刪除時建立版本。 停用版本控制時，虛刪除 blob 會建立虛刪除快照集。

| 當您以明確設定的層級覆寫基底 blob 時 .。。 | 然後您需要支付 .。。 |
|-|-|
| 如果同時啟用 blob 虛刪除和版本控制 | 所有具有完整內容長度的現有版本（不論層級為何）。 |
| 如果已啟用 blob 虛刪除，但停用版本設定 | 所有現有的虛刪除快照集，不論層級為何，都有完整的內容長度。 |

## <a name="see-also"></a>另請參閱

- [啟用和管理 Blob 版本設定](versioning-enable.md)
- [建立 blob 的快照集](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure 儲存體 Blob 的虛刪除](storage-blob-soft-delete.md)
