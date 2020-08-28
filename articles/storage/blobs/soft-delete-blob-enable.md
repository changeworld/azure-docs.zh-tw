---
title: 啟用及管理 Blob 的虛刪除
titleSuffix: Azure Storage
description: 啟用 blob 的虛刪除，可在錯誤地修改或刪除您的資料時，更輕鬆地加以復原。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cba56e699afed8178f93bceddf6bf32a242ed124
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020842"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>啟用及管理 Blob 的虛刪除

Blob 虛刪除可保護您的資料免于不慎或錯誤地修改或刪除。 針對儲存體帳戶啟用 blob 虛刪除時，blob、blob 版本 (預覽) ，而且該儲存體帳戶中的快照集在您指定的保留期限內，可能會在刪除之後復原。

如果應用程式或其他儲存體帳戶使用者可能不小心修改或刪除您的資料，Microsoft 建議開啟 blob 虛刪除。 本文說明如何啟用 blob 的虛刪除。 如需有關 blob 虛刪除的詳細資訊，請參閱 [blob 的虛刪除](soft-delete-blob-overview.md)。

若要瞭解如何也啟用容器的虛刪除，請參閱 [啟用及管理容器的虛刪除](soft-delete-container-enable.md)。

## <a name="enable-blob-soft-delete"></a>啟用 blob 虛刪除

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站在儲存體帳戶上啟用 Blob 的虛刪除：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您的儲存體帳戶。
1. 在 [ **Blob 服務**] 下找出 [**資料保護**] 選項。
1. 將 [ **Blob 虛刪除** ] 屬性設定為 [ *已啟用*]。
1. 在 [ **保留原則**] 底下，指定 Azure 儲存體保留虛刪除之 blob 的時間長度。
1. 儲存您的變更。

![已選擇資料保護 blob 服務的 Azure 入口網站螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

若要檢視已虛刪除的 Blob，請選取 [顯示已刪除的 Blob] 核取方塊。

![反白顯示 [顯示已刪除的 blob] 選項的 [資料保護 blob 服務] 頁面螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

若要檢視指定 Blob 的虛刪除快照集，請選取 Blob，然後按一下 [檢視快照集]。

![[資料保護 blob 服務] 頁面的螢幕擷取畫面，其中反白顯示 [View 快照] 選項。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

請確定 [顯示已刪除的快照集] 核取方塊已選取。

![反白顯示 [顯示已刪除的 blob] 選項的 [View 快照] 頁面螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

當您按一下已虛刪除的 Blob 或快照集時，請留意新的 Blob 屬性。 這些屬性會指出物件是何時刪除的，以及 Blob 或 Blob 快照集將在多少天後永久失效。 如果虛刪除的物件不是快照集，則您也可以選擇將其取消刪除。

![虛刪除物件詳細資料的螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

請記住，取消刪除 Blob 時也會取消刪除所有相關聯的快照集。 若要取消刪除作用中 Blob 的虛刪除快照集，請按一下該 Blob，然後選取 [取消刪除所有快照集]。

![虛刪除 blob 詳細資料的螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

在您取消刪除 Blob 的快照集後，您可以按一下 [升階] 將快照集複製到根 Blob，藉以將 Blob 還原至該快照集。

![醒目提示 [升級] 選項的 [View 快照] 頁面螢幕擷取畫面。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

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

若要復原意外刪除的 blob，請在這些 blob 上呼叫取消 **刪除 blob** 。 請記住，無論是對作用中還是已虛刪除的 Blob 呼叫**取消刪除 Blob**，都會將所有相關聯的虛刪除快照集還原為作用中。 下列範例會在容器中的所有虛刪除和作用中 blob 上呼叫取消 **刪除 blob** ：

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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要啟用虛刪除，請更新 Blob 用戶端的服務屬性：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

若要復原意外刪除的 Blob，請對這些 Blob 呼叫「取消刪除」。 請記住，無論是對作用中還是已虛刪除的 Blob 呼叫 **Undelete**，都會將所有相關聯的虛刪除快照集還原為作用中。 下列範例會容器中所有已虛刪除和作用中的 Blob 呼叫「取消刪除」：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

若要復原至特定 Blob 版本，請先對 Blob 呼叫「取消刪除」，然後將所需的快照集複製到 Blob。 下列範例會將區塊 Blob 復原至其最近產生的快照集：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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

若要復原意外刪除的 blob，請在這些 blob 上呼叫取消 **刪除 blob** 。 請記住，無論是對作用中還是已虛刪除的 Blob 呼叫**取消刪除 Blob**，都會將所有相關聯的虛刪除快照集還原為作用中。 下列範例會在容器中的所有虛刪除和作用中 blob 上呼叫取消 **刪除 blob** ：

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

若要復原到特定的 blob 版本，請先呼叫取消 **刪除 blob** 作業，然後將所需的快照集複製到 blob。 下列範例會將區塊 Blob 復原至其最近產生的快照集：

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

## <a name="next-steps"></a>後續步驟

- [Blob 儲存體的虛刪除](soft-delete-overview.md)
- [Blob 版本設定 (預覽)](versioning-overview.md)
