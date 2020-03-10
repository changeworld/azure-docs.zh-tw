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
ms.openlocfilehash: 78ec5b6d330f03d78dcb4e798b23d588fd93398e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387180"
---
# <a name="reacting-to-blob-storage-events"></a>回應 Blob 儲存體事件

Azure 儲存體事件可讓應用程式對事件做出反應，例如建立和刪除 blob。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。

事件會使用[Azure 事件方格](https://azure.microsoft.com/services/event-grid/)推送至訂閱者，例如 Azure Functions、Azure Logic Apps，甚至是您自己的 HTTP 接聽程式。 最棒的是，您只需為使用的部分付費。

Blob 儲存體會將事件傳送至事件方格，透過豐富的重試原則和無效信件，為您的應用程式提供可靠的事件傳遞。

常見的 Blob 儲存體事件案例包括映像或影片處理、搜尋索引，或任何檔案導向的工作流程。 非同步檔案上傳非常適合事件。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

如果您想要立即試用，請參閱下列任何快速入門文章：

|如果您想要使用此工具：    |請參閱這篇文章： |
|--|-|
|Azure 入口網站    |[快速入門：使用 Azure 入口網站將 Blob 儲存體事件路由至 web 端點](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[快速入門：使用 PowerShell 將儲存體事件路由至 web 端點](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[快速入門：使用 Azure CLI 將儲存體事件路由至 web 端點](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

如果您的帳戶具有階層式命名空間，本教學課程將說明如何在 Azure Databricks：[教學課程：使用 Azure Data Lake Storage Gen2 事件來更新 Databricks Delta 資料表](data-lake-storage-events.md)中，將事件方格訂用帳戶、Azure 函式和[作業](https://docs.azuredatabricks.net/user-guide/jobs.html)連接在一起。

>[!NOTE]
> 只有種類為 [StorageV2 (一般用途 v2)] 和 [BlobStorage] 的儲存體帳戶支援事件整合。 **儲存體 (一般用途 v1)** 不支援與事件方格整合。

## <a name="the-event-model"></a>事件模型

事件方格會使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件訊息路由傳送至訂閱者。 此圖說明事件發行者、事件訂閱和事件處理常式之間的關聯性。

![Event Grid 模型](./media/storage-blob-event-overview/event-grid-functional-model.png)

首先，訂閱某個事件的端點。 然後，當觸發事件時，事件方格服務會將該事件的相關資料傳送至端點。

若要瞭解，請參閱[Blob 儲存體事件架構](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)文章：

> [!div class="checklist"]
> * Blob 儲存體事件的完整清單，以及每個事件的觸發方式。
> * 事件方格會針對每個事件傳送的資料範例。
> * 出現在資料中的每個索引鍵值組的用途。

## <a name="filtering-events"></a>篩選事件

Blob[事件可以](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)依事件種類、容器名稱或已建立/刪除之物件的名稱進行篩選。 事件方格中的篩選準則符合主體的開頭或結尾，因此具有相符主旨的事件會前往訂閱者。

若要深入瞭解如何套用篩選器，請參閱[篩選事件方格的事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

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
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 請使用 [blobType] 欄位以了解 Blob 允許何種類型的作業，以及您應該使用何種類型的用戶端程式庫來存取 Blob。 有效值為 `BlockBlob` 或 `PageBlob`。 
> * 請使用帶有 `CloudBlockBlob` 和 `CloudAppendBlob` 建構函式的 [url] 欄位存取 Blob。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * 如果您想要確保只有在完全認可區塊 Blob 時才會觸發**microsoft.storage.blobcreated**事件，請篩選 `CopyBlob`、`PutBlob`、`PutBlockList` 或 `FlushWithClose` REST API 呼叫的事件。 只有在資料完全認可至區塊 Blob 之後，這些 API 呼叫才會觸發**microsoft.storage.blobcreated**事件。 若要瞭解如何建立篩選準則，請參閱[篩選事件方格的事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。


## <a name="next-steps"></a>後續步驟

深入了解 Event Grid 並嘗試 Blob 儲存體事件：

- [關於 Event Grid](../../event-grid/overview.md)
- [將 Blob 儲存體事件路由至自訂的 Web 端點](storage-blob-event-quickstart.md)
