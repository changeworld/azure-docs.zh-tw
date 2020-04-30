---
title: 從封存層解除凍結 blob 資料
description: 從封存儲存體解除凍結 blob，讓您可以存取資料。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684077"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>從封存層解除凍結 blob 資料

當 blob 位於封存存取層時，它會被視為離線，而且無法讀取或修改。 Blob 中繼資料會保持連線並可供使用，讓您可以列出 blob 和其屬性。 讀取和修改 blob 資料僅適用于線上層，例如經常性存取或非經常性存取。 有兩個選項可抓取和存取儲存在封存存取層中的資料。

1. 將封存的[Blob 解除凍結到線上層](#rehydrate-an-archived-blob-to-an-online-tier)-使用[設定 blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)作業來變更其層級，以將封存 blob 解除凍結至經常性或非經常性存取。
2. [將封存的 Blob 複製到線上層](#copy-an-archived-blob-to-an-online-tier)-使用[複製 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)作業來建立封存 blob 的新複本。 指定不同的 blob 名稱和 [經常性存取] 或 [非經常性存取] 的目的地層。

 如需各層的詳細資訊，請參閱[Azure Blob 儲存體：經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>將封存的 blob 解除凍結到線上層

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>將封存的 Blob 複製到線上階層

如果您不想要解除凍結封存 blob，可以選擇進行[複製 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)作業。 您的原始 blob 會在封存中維持未修改狀態，而在線上經常性存取或非經常性存取層中建立新的 blob 以供您使用。 在複製 Blob 作業中，您也可以將選擇性的 [ *x-ms-解除凍結-priority* ] 屬性設為 [標準] 或 [高]，以指定您要建立 Blob 複本的優先順序。

從封存複製 blob 可能需要數小時才能完成，視選取的解除凍結優先順序而定。 在幕後，**複製 Blob**作業會讀取您的封存來源 Blob，以在選取的目的地層中建立新的線上 Blob。 當您列出 blob 時，可能會顯示新的 blob，但在從來源封存 blob 讀取完成且資料寫入新的線上目的地 blob 之前，資料無法使用。 新的 blob 是獨立複本，對其進行任何修改或刪除並不會影響來源封存 blob。

封存 blob 只能複製到相同儲存體帳戶內的線上目的地層。 不支援將封存 blob 複製到另一個封存 blob。 下表指出 CopyBlob 的功能。

|                                           | **熱層來源**   | **非經常性存取層來源** | **封存層來源**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **經常性層目的地**                  | 支援             | 支援            | 在同一個帳戶中支援;暫止解除凍結               |
| **非經常性存取層目的地**                 | 支援             | 支援            | 在同一個帳戶中支援;暫止解除凍結               |
| **封存層目的地**              | 支援             | 支援            | 不支援         |

## <a name="pricing-and-billing"></a>價格和計費

將 blob 封存在經常性存取或非經常性存取層的解除凍結會依讀取作業和資料抓取收費。 相較于標準優先順序，使用高優先順序具有更高的作業和資料提取成本。 高優先順序解除凍結會在您的帳單上顯示為個別的明細專案。 如果傳回數 gb 的封存 blob 的高優先順序要求超過5小時，則不會向您收取高優先順序的抓取率。 不過，標準抓取率仍然適用，因為解除凍結的優先順序高於其他要求。

將 blob 從封存複製到經常性或非經常性存取層，會以讀取作業和資料抓取的方式收費。 建立新的 blob 複本時，會收取寫入作業的費用。 當您複製到線上 blob 時，不適用提早刪除費用，因為在封存層中，來源 blob 會保持未修改狀態。 如果選取此選項，高優先順序的抓取費用也適用。

封存層中的 blob 應儲存至少180天。 在180天前刪除或解除凍結封存的 blob 將會產生提早刪除費用。

> [!NOTE]
> 如需有關區塊 blob 和資料解除凍結定價的詳細資訊，請參閱[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="quickstart-scenarios"></a>快速入門案例

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>將封存 blob 解除凍結到線上層
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 [Azure 入口網站中，搜尋並選取 [**所有資源**]。

1. 選取您的儲存體帳戶。

1. 選取您的容器，然後選取您的 blob。

1. 在 [ **Blob 屬性**] 中，選取 [**變更層**]。

1. 選取 [**經常性**存取 **]** 或 [非經常性存取] 層。 

1. 選取 [**標準**] 或 [**高**] 的解除凍結優先權。

1. 選取底部的 [**儲存**]。

![變更儲存體帳戶層](media/storage-tiers/blob-access-tier.png)
![級檢查解除凍結狀態](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
下列 PowerShell 腳本可以用來變更封存 blob 的 blob 層。 `$rgName`變數必須使用您的資源組名進行初始化。 `$accountName`變數必須使用您的儲存體帳戶名稱進行初始化。 `$containerName`變數必須使用您的容器名稱進行初始化。 `$blobName`變數必須使用您的 blob 名稱進行初始化。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>使用線上層將封存 blob 複製到新的 blob
下列 PowerShell 腳本可以用來將封存 blob 複製到相同儲存體帳戶內的新 blob。 `$rgName`變數必須使用您的資源組名進行初始化。 `$accountName`變數必須使用您的儲存體帳戶名稱進行初始化。 `$srcContainerName`和`$destContainerName`變數必須使用您的容器名稱進行初始化。 `$srcBlobName`和`$destBlobName`變數必須使用您的 blob 名稱進行初始化。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Blob 儲存體層](storage-blob-storage-tiers.md)
* [依區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
* [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
* [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
