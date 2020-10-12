---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77474107"
---
在 Azure Functions 中引發的錯誤可能來自下列任何來源：

- 使用內建的 Azure Functions[觸發程式和](..\articles\azure-functions\functions-triggers-bindings.md)系結
- 基礎 Azure 服務的 Api 呼叫
- REST 端點的呼叫
- 用戶端程式庫、套件或協力廠商 Api 的呼叫

遵循穩固的錯誤處理實務，以避免資料遺失或遺漏訊息。 建議的錯誤處理作法包括下列動作：

- [啟用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用結構化錯誤處理](#use-structured-error-handling)
- [等冪性的設計](../articles/azure-functions/functions-idempotent.md)
- 在適當的情況下， ([執行重試原則](../articles/azure-functions/functions-reliable-event-processing.md)) 

### <a name="use-structured-error-handling"></a>使用結構化錯誤處理

為了監視應用程式的健康情況，捕捉和發佈錯誤是很重要的。 任何函式程式碼的最上層最上層都應該包含 try/catch 區塊。 在 catch 區塊中，您可以捕捉和發行錯誤。

### <a name="retry-support"></a>重試支援

下列觸發程序有內建的重試支援：

* [Azure Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服務匯流排 (佇列/主題)](../articles/azure-functions/functions-bindings-service-bus.md)

根據預設，這些會觸發最多五次的重試要求。 第五次重試之後，Azure 佇列儲存體和 Azure 服務匯流排都會觸發程式寫入 [有害佇列](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)的訊息。

您必須針對任何其他觸發程式或系結類型手動執行重試原則。 手動實施可能包括將錯誤資訊寫入 [有害訊息佇列](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)。 藉由寫入有害佇列，您將有機會在稍後重試作業。 這個方法和 Blob 儲存體觸發程序所使用的方法相同。
