---
title: 將 Blob 資料從封存層解除凍結
description: 從封存儲存體解除凍結您的 blob，讓您可以存取 blob 資料。 將封存的 blob 複製到線上層。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: a416c22c5b8e09104b20a17bc5042302fa56d8ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035139"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>將 Blob 資料從封存層解除凍結

位於封存存取層的 Blob 會被視為離線，而且無法讀取或修改。 但 Blob 中繼資料會保持連線且可以取得，讓您可列出 Blob 和其屬性。 您只能讀取和修改線上階層 (例如經常性存取層或非經常性存取層) 的 Blob 資料。 有兩個選項可用來擷取和存取儲存在封存存取層中的資料。

1. [將封存的 Blob 解除凍結到線上階層](#rehydrate-an-archived-blob-to-an-online-tier) - 使用[設定 Blob 階層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)作業來變更層級，將封存 Blob 解除凍結至經常性存取層或非經常性存取層。
2. [將封存的 Blob 複製到線上階層](#copy-an-archived-blob-to-an-online-tier) - 使用 [複製 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 作業來建立封存 Blob 的新複本。 指定不同的 Blob 名稱，並指定經常性存取層或非經常性存取層作為目的地階層。

 如需階層的詳細資訊，請參閱 [Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>將封存的 Blob 解除凍結到線上階層

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>將封存的 Blob 複製到線上階層

如果您不想將封存 Blob 解除凍結，可以選擇執行[複製 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 作業。 您的原始 Blob 會在封存中維持未修改的狀態，而新 Blob 會建立在線上的經常性存取層或非經常性存取層中，以便您使用。 在「複製 Blob」作業中，您也可以將選擇性的 [x-ms-rehydrate-priority] 屬性設定為 [標準] 或 [高]，以指定您想要建立 Blob 複本的優先順序。

從封存中複製 Blob 可能需要數小時才能完成 (視選取的解除凍結優先順序而定)。 在幕後，**複製 Blob**作業會讀取您的封存來源 Blob，以在選取的目的地層中建立新的線上 Blob。 當您列出 Blob 時，您可能會看到新的 Blob，但在完成從來源封存 Blob 讀取資料並寫入新的線上目的地 Blob 之前，您無法使用資料。 新的 Blob 是獨立複本，對其進行任何修改或刪除並不會影響來源封存 Blob。

> [!IMPORTANT]
> 在目的地上成功完成複製之前，請勿刪除來源 Blob。 如果刪除來源 Blob，目的地 Blob 可能會無法完成複製，而且內容會是空的。 您可以檢查 [x-ms-copy-status]，以判斷複製作業的狀態。

封存 Blob 只能複製到相同儲存體帳戶內的線上目的地階層。 不支援將封存 Blob 複製到另一個封存 Blob。 下表列出 CopyBlob 的功能。

|                                           | **經常性存取層來源**   | **非經常性存取層來源** | **封存層來源**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **經常性存取層目的地**                  | 支援             | 支援            | 必須在相同帳戶中才能支援；暫止解除凍結               |
| **非經常性存取層目的地**                 | 支援             | 支援            | 必須在相同帳戶中才能支援；暫止解除凍結               |
| **封存存取層目的地**              | 支援             | 支援            | 不支援         |

## <a name="pricing-and-billing"></a>價格和計費

將 Blob 從封存層解除凍結到經常性存取層或非經常性存取層時，其收費會依據讀取作業和資料擷取來計算。 相較於標準優先順序，使用「高」優先順序會有較高的作業和資料擷取成本。 高優先順序的解除凍結會在您帳單上顯示為個別的明細項目。 如果要傳回數 GB 封存 Blob 的高優先順序要求超過 5 小時，則您無須支付高優先順序的擷取費率。 不過，仍會套用標準的擷取費率，因為解除凍結的優先順序高於其他要求。

將 Blob 從封存層複製到經常性存取層或非經常性存取層時，其收費會依據讀取作業和資料擷取來計算。 建立新的 Blob 複本時，您需支付寫入作業的費用。 複製到線上 Blob 時，並不會收取提前刪除費用，因為來源 Blob 仍在封存層中保持未修改的狀態。 但會收取高優先順序的擷取費用 (如果選取高優先順序的話)。

封存層中的 Blob 應儲存至少 180 天。 在 180 天前刪除或解除凍結封存的 Blob 將會產生提前刪除費用。

> [!NOTE]
> 如需有關區塊 Blob 和資料解除凍結的定價詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="quickstart-scenarios"></a>快速入門案例

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>將封存 Blob 解除凍結到線上階層
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 [所有資源]。

1. 選取您的儲存體帳戶。

1. 選取容器，然後選取您的 Blob。

1. 在 [Blob 屬性] 中，選取 [變更階層]。

1. 選取 [經常性] 或 [非經常性] 存取層。 

1. 選取 [標準] 或 [高] 的解除凍結優先權。

1. 在底部選取 [儲存]。

![變更儲存體帳戶階層](media/storage-tiers/blob-access-tier.png)
![檢查解除凍結狀態](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
下列 PowerShell 指令碼可以用來變更封存 Blob 的 Blob 階層。 `$rgName` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。 `$containerName` 變數必須以您的容器名稱進行初始化。 `$blobName` 變數必須以您的 Blob 名稱進行初始化。 
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
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>將封存 Blob 複製到線上階層的新 Blob
下列 PowerShell 指令碼可以用來將封存 Blob 複製到相同儲存體帳戶內的新 Blob。 `$rgName` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。 `$srcContainerName` 和 `$destContainerName` 變數必須以您的容器名稱進行初始化。 `$srcBlobName` 和 `$destBlobName` 變數必須以您的 Blob 名稱進行初始化。 
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

* [了解 Blob 儲存層](storage-blob-storage-tiers.md)
* [依照區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
* [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
* [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
