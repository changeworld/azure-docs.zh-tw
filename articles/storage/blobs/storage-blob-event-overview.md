---
title: 回應 Azure Blob 儲存體事件 | Microsoft Docs
description: 使用 Azure Event Grid 以訂閱 Blob 儲存體事件。
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268492"
---
# <a name="reacting-to-blob-storage-events"></a>回應 Blob 儲存體事件

Azure 存儲事件允許應用程式對事件（如創建和刪除 Blob）做出反應。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。

使用 Azure[事件網格](https://azure.microsoft.com/services/event-grid/)將事件推送到訂閱者，如 Azure 函數、Azure 邏輯應用，甚至推送到您自己的 HTTP 攔截器。 最好的部分是，你只支付你使用。

Blob 存儲將事件發送到事件網格，通過豐富的重試策略和死寫，為應用程式提供可靠的事件傳遞。

常見的 Blob 儲存體事件案例包括映像或影片處理、搜尋索引，或任何檔案導向的工作流程。 非同步檔案上傳非常適合事件。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

如果現在想要試用，請參閱以下任何快速入門文章：

|如果要使用此工具：    |請參閱本文： |
|--|-|
|Azure 入口網站    |[快速入門：使用 Azure 門戶將 Blob 存儲事件路由到 Web 終結點](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[快速入門：使用 PowerShell 將存儲事件路由到 Web 終結點](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[快速入門：使用 Azure CLI 將存儲事件路由到 Web 終結點](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

要查看使用 Azure 函數對 Blob 存儲事件做出反應的深入示例，請參閱以下文章：

- [教程：使用 Azure 資料存儲湖存儲第 2 代事件來更新資料磚塊增量表](data-lake-storage-events.md)。
- [教程：使用事件網格自動調整上傳圖像大小](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> 只有種類為 [StorageV2 (一般用途 v2)]**** 和 [BlobStorage]**** 的儲存體帳戶支援事件整合。 **儲存體 (一般用途 v1)** 不** 支援與事件方格整合。

## <a name="the-event-model"></a>事件模型

事件網格使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件消息路由到訂閱者。 此圖像說明了事件發行者、事件訂閱和事件處理常式之間的關係。

![Event Grid 模型](./media/storage-blob-event-overview/event-grid-functional-model.png)

首先，將終結點訂閱到事件。 然後，觸發事件時，事件網格服務將向終結點發送有關該事件的資料。

請參閱[Blob 存儲事件架構](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)文章以查看：

> [!div class="checklist"]
> * Blob 存儲事件的完整清單以及每個事件的觸發方式。
> * 事件網格將為每個事件發送的資料示例。
> * 資料中顯示的每個鍵值對的用途。

## <a name="filtering-events"></a>篩選事件

Blob 事件可以按創建/刪除的物件的事件種類、容器名稱或名稱[進行篩選](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)。 事件網格中的篩選器匹配主題的開頭或結尾，以便具有匹配主題的事件轉到訂閱者。

要瞭解有關如何應用篩選器的更多內容，請參閱[事件網格的篩選器事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

Blob 儲存體事件的主體使用格式：

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

若要比對儲存體帳戶的所有事件，您可以將主體篩選條件保留空白。

若要比對建立於共用前置詞的一組容器之 Blob 事件，請使用 `subjectBeginsWith` 篩選條件，例如：

```
/blobServices/default/containers/containerprefix
```

若要比對建立於特定容器之 Blob 事件，請使用 `subjectBeginsWith` 篩選條件，例如：

```
/blobServices/default/containers/containername/
```

若要比對建立於共用 Blob 名稱前置詞的特定容器之 Blob 事件，請使用 `subjectBeginsWith` 篩選條件，例如：

```
/blobServices/default/containers/containername/blobs/blobprefix
```

若要比對建立於共用 Blob 名稱前置詞的特定容器之 Blob 事件，請使用 `subjectEndsWith` 篩選條件，例如 ".log" 或 ".jpg"。 如需詳細資訊，請參閱 [Event Grid 概念](../../event-grid/concepts.md#event-subscriptions)。

## <a name="practices-for-consuming-events"></a>消費事件做法

處理 Blob 儲存體事件的應用程式應該遵循幾個建議做法：
> [!div class="checklist"]
> * 由於可設定多個訂用帳戶以將事件路由至相同的事件處理常式，因此重要的是，不要假設事件來自於特定來源，而要檢查訊息主題以確定其來自預期的儲存體帳戶。
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於消息可能會在一些延遲後到達，請使用 etag 欄位來瞭解有關物件的資訊是否仍然是最新的。 要瞭解如何使用 etag 欄位，請參閱[在 Blob 存儲中管理併發](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)。 
> * 由於消息可能無序到達，請使用序列器欄位瞭解任何特定物件上事件的順序。 序列器欄位是一個字串值，表示任何特定 Blob 名稱的事件的邏輯序列。 可以使用標準字串比較來瞭解同一 blob 名稱上的兩個事件的相對序列。
> * 請使用 [blobType] 欄位以了解 Blob 允許何種類型的作業，以及您應該使用何種類型的用戶端程式庫來存取 Blob。 有效值為 `BlockBlob` 或 `PageBlob`。 
> * 請使用帶有 `CloudBlockBlob` 和 `CloudAppendBlob` 建構函式的 [url] 欄位存取 Blob。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * 如果要確保僅在完全提交塊 Blob 時觸發**Microsoft.Storage.Blob 創建**事件，`CopyBlob`請篩選 、、`PutBlob`或`PutBlockList` `FlushWithClose` REST API 呼叫的事件。 這些 API 呼叫僅在資料完全提交到塊 Blob 後觸發**Microsoft.Storage.Blob 創建**事件。 要瞭解如何創建篩選器，請參閱[事件網格的篩選事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。


## <a name="next-steps"></a>後續步驟

深入了解 Event Grid 並嘗試 Blob 儲存體事件：

- [關於 Event Grid](../../event-grid/overview.md)
- [將 Blob 儲存體事件路由至自訂的 Web 端點](storage-blob-event-quickstart.md)
