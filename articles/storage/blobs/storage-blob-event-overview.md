---
title: 回應 Azure Blob 儲存體事件 | Microsoft Docs
description: 使用 Azure 事件方格來訂閱和回應 Blob 儲存體事件。 瞭解事件模型、篩選事件和取用事件的實務。
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: c4c6b5b23a0609a5d68eb72c614ce282ae04a817
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519093"
---
# <a name="reacting-to-blob-storage-events"></a>回應 Blob 儲存體事件

Azure 儲存體事件可讓應用程式回應事件，例如建立和刪除 blob。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。 最棒的是，您只需支付使用的部分。

Blob 儲存體事件是使用 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/) 推送至「訂閱者」，例如 Azure Functions、Azure Logic Apps，甚至是您自己的 HTTP 接聽程式。 事件方格透過 rich retry 原則和無效信件，提供可靠的事件傳遞給您的應用程式。

請參閱 [blob 儲存體事件架構](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 一文，以查看 blob 儲存體支援的完整事件清單。

常見的 Blob 儲存體事件案例包括映像或影片處理、搜尋索引，或任何檔案導向的工作流程。 非同步檔案上傳非常適合事件。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

如果您想要嘗試 blob 儲存體事件，請參閱下列任何快速入門文章：

|如果您想要使用此工具：    |請參閱這篇文章： |
|--|-|
|Azure 入口網站    |[快速入門：使用 Azure 入口網站將 Blob 儲存體事件路由傳送至 Web 端點](../../event-grid/blob-event-quickstart-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)|
|PowerShell    |[快速入門：使用 PowerShell 將儲存體事件路由至 web 端點](./storage-blob-event-quickstart-powershell.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)|
|Azure CLI    |[快速入門：使用 Azure CLI 將儲存體事件路由至 web 端點](./storage-blob-event-quickstart.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)|

若要查看使用 Azure 函式來回應 Blob 儲存體事件的深入範例，請參閱下列文章：

- [教學課程：使用 Azure Data Lake Storage Gen2 事件來更新 Databricks Delta 資料表](data-lake-storage-events.md)。
- [教學課程：使用 Event Grid 自動調整已上傳映像的大小](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> 只有 StorageV2 類型的儲存體帳戶 **(一般用途 v2)**、 **BlockBlobStorage** 和 **BlobStorage** 支援事件整合。 **儲存體 (一般用途 v1)** 不 *支援與* 事件方格整合。

## <a name="the-event-model"></a>事件模型

事件方格會使用 [事件訂閱](../../event-grid/concepts.md#event-subscriptions) 將事件訊息路由傳送給訂閱者。 此圖說明事件發行者、事件訂閱與事件處理常式之間的關聯性。

![Event Grid 模型](./media/storage-blob-event-overview/event-grid-functional-model.png)

首先，訂閱事件的端點。 然後，在觸發事件時，事件方格服務會將該事件的相關資料傳送至端點。

請參閱 [Blob 儲存體事件架構](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 文章以查看：

> [!div class="checklist"]
> * Blob 儲存體事件的完整清單，以及每個事件的觸發方式。
> * 事件方格會針對每個事件傳送的資料範例。
> * 出現在資料中的每個金鑰值組的用途。

## <a name="filtering-events"></a>篩選事件

Blob [事件可](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 依建立/刪除之物件的事件種類、容器名稱或名稱進行篩選。 事件方格中的篩選準則會比對主體的開頭或結尾，讓具有相符主體的事件移至訂閱者。

若要深入瞭解如何套用篩選，請參閱[事件方格的篩選事件](../../event-grid/how-to-filter-events.md)。

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
> * 當訊息可能會在延遲之後抵達時，請使用 etag 欄位來瞭解物件的相關資訊是否仍為最新狀態。 若要瞭解如何使用 etag 欄位，請參閱 [管理 Blob 儲存體中的並行](../common/storage-concurrency.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json#managing-concurrency-in-blob-storage)存取。 
> * 當訊息無法按順序抵達時，請使用 sequencer 欄位來瞭解任何特定物件上的事件順序。 Sequencer 欄位是字串值，表示任何特定 blob 名稱的事件邏輯順序。 您可以使用標準字串比較來瞭解相同 blob 名稱上兩個事件的相對順序。
> * 儲存體事件可保證至少一次傳遞至訂閱者，以確保所有訊息都會輸出。 不過，由於訂閱的重試或可用性，有時可能會發生重複的訊息。 若要深入瞭解訊息傳遞和重試，請參閱 [事件方格訊息傳遞和重試](../../event-grid/delivery-and-retry.md)。
> * 請使用 [blobType] 欄位以了解 Blob 允許何種類型的作業，以及您應該使用何種類型的用戶端程式庫來存取 Blob。 有效值為 `BlockBlob` 或 `PageBlob`。 
> * 請使用帶有 `CloudBlockBlob` 和 `CloudAppendBlob` 建構函式的 [url] 欄位存取 Blob。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * 如果您想要確保只有在完全認可區塊 Blob 時才觸發 **BlobCreated** 事件，請篩選 `CopyBlob` 、 `PutBlob` `PutBlockList` 或 `FlushWithClose` REST API 呼叫的事件。 只有在資料完全認可至區塊 Blob 之後，這些 API 呼叫才會觸發 **BlobCreated** 事件。 若要瞭解如何建立篩選，請參閱 [事件方格的篩選事件](../../event-grid/how-to-filter-events.md)。


## <a name="next-steps"></a>後續步驟

深入了解 Event Grid 並嘗試 Blob 儲存體事件：

- [關於 Event Grid](../../event-grid/overview.md)
- [Blob 儲存體事件架構](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [將 Blob 儲存體事件路由至自訂 web 端點](storage-blob-event-quickstart.md)