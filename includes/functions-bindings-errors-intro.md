---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77474107"
---
Azure Functions 中引發的錯誤可能來自下列任何來源：

- 使用內建的 Azure Functions[觸發程式和](..\articles\azure-functions\functions-triggers-bindings.md)系結
- 基礎 Azure 服務的 Api 呼叫
- REST 端點的呼叫
- 呼叫用戶端程式庫、封裝或協力廠商 Api

遵循穩固的錯誤處理做法，是為了避免遺失資料或遺漏訊息。 建議的錯誤處理作法包括下列動作：

- [啟用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用結構化錯誤處理](#use-structured-error-handling)
- [等冪性的設計](../articles/azure-functions/functions-idempotent.md)
- [執行重試原則](../articles/azure-functions/functions-reliable-event-processing.md)（適當時）

### <a name="use-structured-error-handling"></a>使用結構化錯誤處理

捕捉和發行錯誤對於監視應用程式的健全狀況非常重要。 任何函式程式碼最上層的層級都應該包含 try/catch 區塊。 在 catch 區塊中，您可以捕捉和發行錯誤。

### <a name="retry-support"></a>重試支援

下列觸發程序有內建的重試支援：

* [Azure Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服務匯流排 (佇列/主題)](../articles/azure-functions/functions-bindings-service-bus.md)

根據預設，這些觸發程式會重試最多五次的要求。 在第五次重試之後，Azure 佇列儲存體和 Azure 服務匯流排觸發程式都會將訊息寫入[有害佇列](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)。

您必須手動為任何其他觸發程式或系結類型執行重試原則。 手動執行可能包括將錯誤資訊寫入[有害訊息佇列](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)。 藉由寫入有害佇列，您有機會在稍後重試作業。 這個方法和 Blob 儲存體觸發程序所使用的方法相同。
