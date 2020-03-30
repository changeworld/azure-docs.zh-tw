---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474107"
---
Azure 函數中引發的錯誤可能來自以下任一原因：

- 使用內置 Azure 函數[觸發器和綁定](..\articles\azure-functions\functions-triggers-bindings.md)
- 對基礎 Azure 服務的 API 的調用
- 對 REST 終結點的調用
- 對用戶端庫、包或協力廠商 API 的調用

遵循可靠的錯誤處理實踐對於避免資料丟失或丟失消息非常重要。 建議的錯誤處理實踐包括以下操作：

- [啟用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用結構化錯誤處理](#use-structured-error-handling)
- [陽萎設計](../articles/azure-functions/functions-idempotent.md)
- [實施重試策略](../articles/azure-functions/functions-reliable-event-processing.md)（在適當情況下）

### <a name="use-structured-error-handling"></a>使用結構化錯誤處理

捕獲和發佈錯誤對於監視應用程式的運行狀況至關重要。 任何函數代碼的最頂層都應包括 try/catch 塊。 在 catch 塊中，可以捕獲和發佈錯誤。

### <a name="retry-support"></a>重試支援

下列觸發程序有內建的重試支援：

* [Azure Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 佇列存儲](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服務匯流排 (佇列/主題)](../articles/azure-functions/functions-bindings-service-bus.md)

預設情況下，這些觸發器重試請求最多五次。 第五次重試後，Azure 佇列存儲和 Azure 服務匯流排觸發器都會將消息寫入[有害佇列](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)。

您需要手動為任何其他觸發器或綁定類型實現重試策略。 手動實現可能包括將錯誤資訊寫入[有害訊息佇列](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)。 通過寫入毒佇列，您有機會在以後重試操作。 這個方法和 Blob 儲存體觸發程序所使用的方法相同。
