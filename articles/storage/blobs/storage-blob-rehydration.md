---
title: 從存檔層重新補充 blob 資料
description: 從存檔存儲中重新補充 blob，以便可以訪問資料。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 0a7012d9daa808933a51ac05862a8a9aa4cfcf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614791"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>從存檔層重新補充 blob 資料

當 Blob 位於存檔訪問層中時，它被視為離線，無法讀取或修改。 Blob 中繼資料保持線上狀態且可用，允許您列出 Blob 及其屬性。 讀取和修改 Blob 資料僅適用于連線層，如熱層或冷層。 有兩個選項用於檢索和訪問存儲在存檔訪問層中的資料。

1. [將存檔的 Blob 重新化為連線層](#rehydrate-an-archived-blob-to-an-online-tier)- 使用["設置 Blob 層"](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)操作更改存檔 Blob 以將其凍結為熱或冷卻狀態。
2. [將存檔的 Blob 複製到連線層](#copy-an-archived-blob-to-an-online-tier)- 使用["複製 Blob"](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作創建新的存檔 Blob 副本。 指定其他 blob 名稱和熱或酷的目標層。

 有關層的詳細資訊，請參閱 Azure [Blob 存儲：熱、酷和存檔訪問層](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>將存檔的 blob 重新凍結到連線層

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>將封存的 Blob 複製到線上階層

如果不想重新凍結存檔 Blob，可以選擇執行[複製 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作。 原始 Blob 將保持未修改存檔，而在連線熱或冷層中創建新的 Blob 可供您處理。 在"複製 Blob"操作中，還可以將可選*的 x-ms-rehydrate 優先順序*屬性設置為"標準"或"高"（預覽），以指定要創建 Blob 副本的優先順序。

存檔 Blob 只能複製到同一存儲帳戶中的連線目標層。 不支援將存檔 Blob 複製到另一個存檔 Blob。

根據所選的補水優先順序，從存檔複製 Blob 可能需要數小時才能完成。 在後臺，"複製**Blob"** 操作將讀取存檔源 Blob，以在所選目標層中創建新的線上 Blob。 在列出 Blob 時，新 Blob 可能可見，但在源存檔 Blob 的讀取完成並將資料寫入新的連線目標 Blob 之前，資料不可用。 新 Blob 是作為獨立副本，對它所做的任何修改或刪除都不會影響源存檔 Blob。

## <a name="pricing-and-billing"></a>價格和計費

將存檔中存檔的 blob 重新化為熱層或冷層，作為讀取操作和資料檢索收費。 與標準優先順序相比，使用高優先順序（預覽）具有較高的操作和資料檢索成本。 高優先順序補液功能在帳單上作為單獨的行專案顯示。 如果返回幾千百萬位元組的存檔 Blob 的高優先順序請求需要超過 5 小時，則不會向您收取高優先順序檢索率的費用。 但是，標準檢索率仍然適用，因為補液優先于其他請求。

將 blob 複製到熱層或冷層將收取讀取操作和資料檢索。 寫入操作用於創建新 Blob 副本。 早期刪除費用不適用於複製到連線 Blob，因為源 Blob 在存檔層中仍未修改。 如果選中，則確實會收取高優先順序檢索費用。

存檔層中的 Blob 應至少存儲 180 天。 在 180 天之前刪除或重新補充存檔的 blob 會產生早期刪除費用。

> [!NOTE]
> 有關塊 blob 定價和資料補水的詳細資訊，請參閱 Azure[存儲定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有關出站資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資訊](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="quickstart-scenarios"></a>快速入門案例

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>將存檔 blob 重新凍結到連線層
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 在 Azure 門戶中，搜索並選擇 **"所有資源**"。

1. 選取您的儲存體帳戶。

1. 選擇容器，然後選擇 Blob。

1. 在**Blob 屬性**中，選擇 **"更改層**"。

1. 選擇 **"熱**"或"**酷**"訪問層。 

1. 選擇**標準**或**較高的**再水合優先順序。

1. 選擇 **"在底部保存**"。

![更改存儲帳戶層](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[電源殼](#tab/azure-powershell)
以下 PowerShell 腳本可用於更改存檔 Blob 的 blob 層。 必須`$rgName`使用資源組名稱初始化該變數。 必須`$accountName`使用存儲帳戶名稱初始化該變數。 必須`$containerName`使用容器名稱初始化該變數。 必須`$blobName`使用 blob 名稱初始化該變數。 
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

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>使用連線層將存檔 Blob 複製到新 Blob
以下 PowerShell 腳本可用於將存檔 Blob 複製到同一存儲帳戶中的新 Blob。 必須`$rgName`使用資源組名稱初始化該變數。 必須`$accountName`使用存儲帳戶名稱初始化該變數。 必須`$srcContainerName`使用`$destContainerName`容器名稱初始化 和 變數。 必須`$srcBlobName`使用`$destBlobName`blob 名稱初始化 和 變數。 
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

* [瞭解 Blob 存儲層](storage-blob-storage-tiers.md)
* [按區域查看 Blob 存儲和 GPv2 帳戶中的熱、酷和存檔定價](https://azure.microsoft.com/pricing/details/storage/)
* [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
* [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
