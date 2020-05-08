---
title: Azure 儲存體 Blob 的虛刪除 | Microsoft Docs
description: Azure 儲存體現在提供 Blob 物件的虛刪除功能，因此，當應用程式或其他儲存體帳戶使用者錯誤地修改或刪除您的資料時，您將可更輕鬆地復原資料。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 389dea74f5002cb09d7683947356d236ea8d338b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858700"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Azure 儲存體 Blob 的虛刪除

Azure 儲存體現在提供 Blob 物件的虛刪除功能，因此，當應用程式或其他儲存體帳戶使用者錯誤地修改或刪除您的資料時，您將可更輕鬆地復原資料。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>虛刪除的運作方式

啟用時，虛刪除可讓您在 blob 或 blob 快照集遭到刪除時儲存和復原資料。 這項保護也延伸至因覆寫而遭到清除的 Blob 資料。

資料刪除後會轉換為虛刪除狀態，而不是永久清除狀態。 當您在虛刪除開啟的情況下覆寫資料時，將會產生虛刪除的快照集，以儲存覆寫資料的狀態。 虛刪除的物件是不可見的，除非明確列出。 您可以設定虛刪除的資料在永久失效之前可進行復原的時間長度。

虛刪除與回溯相容，因此您不需要對應用程式進行任何變更，即可利用此功能所提供的保護。 不過，[資料復原](#recovery)導入了新的**取消刪除 Blob** API。

### <a name="configuration-settings"></a>組態設定

當您建立新帳戶時，虛刪除會預設為關閉。 現有儲存體帳戶的虛刪除也會預設為關閉。 在儲存體帳戶的有效期間內，您可以隨時將此功能切換為開啟或關閉。

當此功能關閉時，只要先前仍開啟此功能時已儲存遭虛刪除的資料，您就仍然可以存取及復原已虛刪除的資料。 當您開啟虛刪除時，您也需要設定保留期限。

保留期限能指出系統儲存虛刪除資料而可供您復原的時間長度。 對於明確刪除的 Blob 和 Blob 快照集，保留期限時鐘會在資料刪除時啟動。 對於在覆寫資料時由虛刪除功能產生的虛刪除快照集，則會在產生快照集時啟動此時鐘。 目前，您可以將虛刪除的資料保留 1 到 365 天。

您可以隨時變更虛刪除保留期限。 更新的保留期限只會套用至新刪除的資料。 先前刪除的資料，將會在該資料刪除時所設定的保留期限結束後失效。 嘗試刪除已經虛刪除的物件不會影響其到期時間。

### <a name="saving-deleted-data"></a>儲存已刪除的資料

虛刪除會在 Blob 或 Blob 快照集遭到刪除或覆寫的多種情況下保存您的資料。

使用**放置 blob**、**放置區塊**、**放置區塊清單**或**複製 blob**覆寫 blob 時，會自動產生該 blob 在寫入作業之前所處狀態的快照集。 此快照集是虛刪除快照集；除非明確列出虛刪除的物件，否則看不見此快照集。 請參閱[復原](#recovery)一節，以了解如何列出虛刪除的物件。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*虛刪除的資料是灰色，而作用中的資料則是藍色。最近寫入的資料會出現在較舊的資料下方。當使用 B1 覆寫 B0 時，會產生 B0 的虛刪除快照集。當使用 B2 覆寫 B1 時，會產生 B1 的虛刪除快照集。*

> [!NOTE]  
> 只有為目的地 Blob 的帳戶開啟虛刪除時，虛刪除才會提供複製作業的覆寫保護。

> [!NOTE]  
> 虛刪除不會為封存層中的 Blob 提供覆寫保護。 如果以任何層中的新 Blob 覆寫已封存的 Blob，被覆寫的 Blob 將會永久過期。

對快照集呼叫**刪除 Blob** 時，該快照集將會標示為已虛刪除。 此時不會產生新的快照集。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*虛刪除的資料是灰色，而作用中的資料則是藍色。最近寫入的資料會出現在較舊的資料下方。呼叫**快照集 Blob**時，B0 會成為快照集，而 B1 則是 Blob 的作用中狀態。當 B0 快照集遭到刪除時，它會標示為已虛刪除。*

對基底 Blob (本身不是快照集的任何 Blob) 呼叫**刪除 Blob** 時，該 Blob 將會標示為已虛刪除。 與先前的行為一致，對具有作用中快照集的 Blob 呼叫**刪除 Blob**，將會傳回錯誤。 對具有虛刪除快照集的 Blob 呼叫**刪除 Blob**，則不會傳回錯誤。 在虛刪除開啟時，您仍可在單一作業中刪除 Blob 及其所有的快照集。 執行此動作會將基底 Blob 和快照集標示為已虛刪除。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*虛刪除的資料是灰色，而作用中的資料則是藍色。最近寫入的資料會出現在較舊的資料下方。在此，會進行**刪除 Blob**呼叫以刪除 B2 和所有相關聯的快照集。作用中的 blob、B2 和所有相關聯的快照集都標示為已虛刪除。*

> [!NOTE]  
> 在覆寫已虛刪除的 Blob 時，將會自動產生該 Blob 在寫入作業之前所處狀態的虛刪除快照集。 覆寫的 Blob 層會由新的 Blob 繼承。

如果是刪除容器或帳戶，或是覆寫了 Blob 中繼資料和 Blob 屬性，虛刪除將不會儲存您的資料。 若要防止儲存體帳戶誤遭刪除，您可以使用 Azure Resource Manager 設定鎖定。 若要深入了解，請參閱 Azure Resource Manager 文章[鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

下表將詳細說明虛刪除開啟時的預期行為：

| REST API 作業 | 資源類型 | 描述 | 行為變更 |
|--------------------|---------------|-------------|--------------------|
| [刪除](/rest/api/storagerp/StorageAccounts/Delete) | 帳戶 | 刪除儲存體帳戶，包括其中包含的所有容器和 Blob。                           | 無變更。 已刪除之帳戶中的容器和 Blob 無法復原。 |
| [刪除容器](/rest/api/storageservices/delete-container) | 容器 | 刪除容器，包括其中包含的所有 Blob。 | 無變更。 已刪除之容器中的 Blob 無法復原。 |
| [Put Blob](/rest/api/storageservices/put-blob) \(英文\) | 區塊、附加和分頁 Blob | 建立新的 Blob，或取代容器內現有的 Blob | 如果用來取代現有的 Blob，則會自動產生該 Blob 在呼叫之前所處狀態的快照集。 這也適用于先前已虛刪除的 blob，但只有在已由相同類型（區塊、附加或分頁）的 blob 取代時。 如果取代為不同類型的 Blob，則所有已虛刪除的現有資料將會永久過期。 |
| [刪除 Blob](/rest/api/storageservices/delete-blob) | 區塊、附加和分頁 Blob | 將 Blob 或 Blob 快照集標示為要刪除。 Blob 或快照集會在後續的記憶體回收期間刪除 | 如果用來刪除 Blob 快照集，該快照集將會標示為已虛刪除。 如果用來刪除 Blob，該 Blob 將會標示為已虛刪除。 |
| [複製 Blob](/rest/api/storageservices/copy-blob) | 區塊、附加和分頁 Blob | 將來源 Blob 複製到相同儲存體帳戶或其他儲存體帳戶中的目的地 Blob。 | 如果用來取代現有的 Blob，則會自動產生該 Blob 在呼叫之前所處狀態的快照集。 這也適用于先前已虛刪除的 blob，但只有在已由相同類型（區塊、附加或分頁）的 blob 取代時。 如果取代為不同類型的 Blob，則所有已虛刪除的現有資料將會永久過期。 |
| [放置區塊](/rest/api/storageservices/put-block) | 區塊 Blob | 建立要認可作為區塊 Blob 一部分的新區塊。 | 如果用來認可區塊至作用中的 blob，則不會有任何變更。 如果用來認可區塊，且其目標 Blob 已虛刪除，則會建立新的 Blob，並自動產生快照集，以擷取已虛刪除之 Blob 的狀態。 |
| [Put Block List](/rest/api/storageservices/put-block-list) \(英文\) | 區塊 Blob | 藉由指定包含區塊 Blob 的區塊集識別碼來認可 Blob。 | 如果用來取代現有的 Blob，則會自動產生該 Blob 在呼叫之前所處狀態的快照集。 先前已虛刪除的 Blob 若是區塊 Blob (這也是唯一前提)，也會產生前述的快照集。 如果取代為不同類型的 Blob，則所有已虛刪除的現有資料將會永久過期。 |
| [放置頁面](/rest/api/storageservices/put-page) | 分頁 Blob | 將某範圍的頁面寫入分頁 Blob。 | 無變更。 使用此作業覆寫或清除的分頁 Blob 資料並不會儲存，且無法復原。 |
| [附加區塊](/rest/api/storageservices/append-block) | 附加 Blob | 將資料區塊寫入至附加 Blob 結尾 | 無變更。 |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) \(英文\) | 區塊、附加和分頁 Blob | 設定為 Blob 定義之系統屬性的值。 | 無變更。 覆寫的 Blob 屬性無法復原。 |
| [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata) | 區塊、附加和分頁 Blob | 將指定 Blob 的使用者定義中繼資料設為一或多個名稱/值配對。 | 無變更。 覆寫的 Blob 中繼資料無法復原。 |

請務必留意，呼叫「放置頁面」以覆寫或清除某範圍的分頁 Blob 時，並不會自動產生快照集。 虛擬機器磁碟由分頁 Blob 所支援，且會使用**放置頁面**來寫入資料。

### <a name="recovery"></a>修復

在虛刪除的基底 Blob 上呼叫取消[刪除 Blob](/rest/api/storageservices/undelete-blob)作業，會將它和所有相關聯的虛刪除快照集還原為使用中。 `Undelete Blob`在作用中的基底 blob 上呼叫作業，會將所有相關聯的虛刪除快照集還原為使用中。 快照集還原為作用中後，看起來會像是使用者產生的快照集；它們並不會覆寫基底 Blob。

若要將 blob 還原至特定的虛刪除快照集，您`Undelete Blob`可以在基底 blob 上呼叫。 然後，您可以將快照集複製到目前作用中的 Blob。 您也可以將快照集複製到新的 Blob。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*虛刪除的資料是灰色，而作用中的資料則是藍色。最近寫入的資料會出現在較舊的資料下方。在此，會在 blob B 上呼叫**刪除 blob** ，藉此將基底 Blob、B1 和所有相關聯的快照集還原為使用中的狀態。在第二個步驟中，會透過基底 blob 複製 B0。此複製作業會產生 B1 的虛刪除快照集。*

若要檢視已虛刪除的 Blob 和 Blob 快照集，您可以選擇在**清單 Blob** 中包含已刪除的資料。 您可以選擇僅檢視已虛刪除的基底 Blob，或是也包含已虛刪除的 Blob 快照集。 對於所有已虛刪除的資料，您可以檢視資料的刪除時間，以及資料將永久失效之前的天數。

### <a name="example"></a>範例

以下是在開啟虛刪除時，上傳、覆寫、快照集、刪除和還原名為*HelloWorld*之 blob 的主控台輸出：

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

請參閱[後續步驟](#next-steps)一節，以取得產生此輸出之應用程式的指標。

## <a name="pricing-and-billing"></a>價格和計費

所有虛刪除的資料都會比照作用中資料的相同費率計費。 對於在設定的保留期限之後永久刪除的資料，您將不需要付費。 若要深入了解快照集及其產生費用的方式，請參閱[了解快照集如何產生費用](storage-blob-snapshots.md)。

與自動產生快照集有關的交易不會計費。 系統會以寫入作業的速率，向您收取**刪除 Blob**交易的費用。

如需 Azure Blob 儲存體常規價格方面的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

在您首次開啟虛刪除時，建議您使用較小的保留期限，以清楚了解此功能對您的帳單將有何影響。

## <a name="get-started"></a>開始使用

下列步驟示範如何開始進行虛刪除。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站在儲存體帳戶上啟用 blob 的虛刪除：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶。 

2. 流覽至 [ **Blob 服務**] 底下的 [**資料保護**] 選項。

3. 在 Blob 虛**刪除**下按一下 [**已啟用**]

4. 在 [**保留原則**] 下輸入您要*保留*的天數

5. 選擇 [**儲存**] 按鈕以確認您的資料保護設定

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

若要檢視已虛刪除的 Blob，請選取 [顯示已刪除的 Blob]**** 核取方塊。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

若要檢視指定 Blob 的虛刪除快照集，請選取 Blob，然後按一下 [檢視快照集]****。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

請確定 [顯示已刪除的快照集]**** 核取方塊已選取。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

當您按一下已虛刪除的 Blob 或快照集時，請留意新的 Blob 屬性。 這些屬性會指出物件是何時刪除的，以及 Blob 或 Blob 快照集將在多少天後永久失效。 如果虛刪除的物件不是快照集，則您也可以選擇將其取消刪除。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

請記住，取消刪除 Blob 時也會取消刪除所有相關聯的快照集。 若要取消刪除作用中 Blob 的虛刪除快照集，請按一下該 Blob，然後選取 [取消刪除所有快照集]****。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

在您取消刪除 Blob 的快照集後，您可以按一下 [升階]**** 將快照集複製到根 Blob，藉以將 Blob 還原至該快照集。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要啟用虛刪除，請更新 Blob 用戶端的服務屬性。 下列範例會為訂用帳戶中的帳戶子集啟用虛刪除：

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
您可以使用下列命令來確認該虛刪除是否已開啟：

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

若要復原意外刪除的 Blob，請對這些 Blob 呼叫「取消刪除」。 請記住，無論是對作用中還是已虛刪除的 Blob 呼叫**取消刪除 Blob**，都會將所有相關聯的虛刪除快照集還原為作用中。 下列範例會容器中所有已虛刪除和作用中的 Blob 呼叫「取消刪除」：

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
若要尋找目前的虛刪除保留原則，請使用下列命令：

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

若要啟用虛刪除，請更新 Blob 用戶端的服務屬性：

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

若要確認虛刪除已開啟，請使用下列命令： 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

若要啟用虛刪除，請更新 Blob 用戶端的服務屬性：

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

若要啟用虛刪除，請更新 Blob 用戶端的服務屬性：

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

若要復原意外刪除的 Blob，請對這些 Blob 呼叫「取消刪除」。 請記住，無論是對作用中還是已虛刪除的 Blob 呼叫**取消刪除 Blob**，都會將所有相關聯的虛刪除快照集還原為作用中。 下列範例會容器中所有已虛刪除和作用中的 Blob 呼叫「取消刪除」：

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

若要復原至特定 Blob 版本，請先對 Blob 呼叫「取消刪除」，然後將所需的快照集複製到 Blob。 下列範例會將區塊 Blob 復原至其最近產生的快照集：

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>特殊考量

如果應用程式或其他儲存體帳戶使用者意外地修改或刪除您的資料，建議您開啟虛刪除。 為經常覆寫的資料啟用虛刪除，可能會導致儲存體容量費用增加，並在列出 blob 時增加延遲。 您可以藉由將經常覆寫的資料儲存在已停用虛刪除的個別儲存體帳戶中，以減輕這項額外的成本和延遲。 

## <a name="faq"></a>常見問題集

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>我可以使用虛刪除的儲存體服務嗎？

目前，虛刪除僅適用於 Blob (物件) 儲存體。

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>虛刪除是否適用於所有的儲存體帳戶類型？

是，虛刪除適用于 Blob 儲存體帳戶，以及一般用途（GPv1 和 GPv2）儲存體帳戶中的 blob。 支援 standard 和 premium 帳戶類型。 「虛刪除」適用于非受控磁片，也就是幕後的分頁 blob。 虛刪除不適用於受控磁碟。

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>虛刪除是否適用於所有儲存層？

是，虛刪除適用于所有儲存層，包括經常性存取、非經常性存取和封存。 不過，虛刪除不會為封存層中的 Blob 提供覆寫保護。

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>是否可以使用「設定 Blob 層 API」將具有虛刪除快照集的 Blob 分層？

可以。 已虛刪除的快照集會留在原始階層，但基底 Blob 會移到新階層。 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>進階儲存體帳戶有每個 Blob 100 個快照集的限制。 虛刪除快照集是否會計入這項限制中？

不會，虛刪除快照集不會計入這項限制中。

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>我是否可為現有的儲存體帳戶開啟虛刪除？

是，您可以為現有和新的儲存體帳戶設定虛刪除。

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>如果我在開啟虛刪除的情況下刪除整個帳戶或容器，是否會儲存所有相關聯的 Blob？

不會，如果您刪除整個帳戶或容器，將會永久刪除所有相關聯的 Blob。 如需保護儲存體帳戶不被意外刪除的詳細資訊，請參閱[鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>我可以檢視已刪除資料的容量計量嗎？

虛刪除的資料會計入為儲存體帳戶容量總計的一部分。 如需有關追蹤和監視儲存體容量的詳細資訊，請參閱[儲存體分析](../common/storage-analytics.md)。

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>如果我關閉虛刪除，是否仍可存取已虛刪除的資料？

是，在關閉虛刪除的情況下，您仍可存取及復原未到期的虛刪除資料。

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>我是否可從 Blob 讀取和複製已虛刪除的快照集？  

是，但您必須先對 Blob 呼叫「取消刪除」。

### <a name="is-soft-delete-available-for-all-blob-types"></a>虛刪除是否適用於所有 Blob 類型？

是，虛刪除適用于區塊 blob、附加 blob 和分頁 blob。

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>虛刪除是否適用於虛擬機器磁碟？  

「虛刪除」適用于高階和標準非受控磁片，也就是幕後的分頁 blob。 虛刪除只會協助您復原由**刪除 blob**、**放置 Blob**、**放置區塊清單**、**放置區塊**和**複製 Blob**作業所刪除的資料。 藉由呼叫**放置頁面**而覆寫的資料無法復原。

Azure 虛擬機器會使用**Put 頁面**的呼叫寫入非受控磁片，因此使用虛刪除來復原從 Azure VM 到非受控磁片的寫入，並不是支援的案例。

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>是否需要變更現有的應用程式才能使用虛刪除？

無論您使用何種 API 版本，都可以使用虛刪除。 不過，若要列出及復原已虛刪除的 Blob 和 Blob 快照集，您必須使用 2017-07-29 版的[儲存體服務 REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 或更新版本。 Microsoft 建議一律使用最新版的 Azure 儲存體 API。

## <a name="next-steps"></a>後續步驟

* [.NET 範例程式碼](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob 服務 REST API](/rest/api/storageservices/blob-service-rest-api)
* [Azure 儲存體複寫](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [使用異地冗余來設計高可用性應用程式](../common/geo-redundant-design.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [嚴重損壞修復和儲存體帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
