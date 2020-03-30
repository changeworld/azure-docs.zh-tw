---
title: Azure Functions 的最佳作法
description: 了解 Azure Functions 的最佳作法與模式。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277774"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>將 Azure Functions 效能和可靠性最佳化

本文提供指引來改善[無伺服器](https://azure.microsoft.com/solutions/serverless/)函式應用程式的效能和可靠性。  

## <a name="general-best-practices"></a>一般最佳作法

以下是如何使用 Azure Functions 建置及打造無伺服器解決方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免長時間執行的函式

大型長時間執行的函式可能會造成非預期的逾時問題。 要瞭解有關給定託管計畫的超時數的更多，請參閱[函數應用超時持續時間](functions-scale.md#timeout)。 

由於許多 Node.js 依賴項，函數可能會變大。 匯入相依性也可能會造成載入時間增加，而導致未預期的逾時。 系統會以明確和隱含方式載入相依性。 您的程式碼載入的單一模組可能會載入其本身的其他模組。 

在可能時，將大型函式重構為較小的函式集，共用運作並快速傳回回應。 例如，Webhook 或 HTTP 觸發器函數可能需要在特定時間限制內做出確認回應;網鉤通常需要立即回應。" 您可以將 HTTP 觸發程序承載傳遞到要由佇列觸發程序函式處理的佇列中。 此方法允許您推遲實際工作並返回即時回應。


### <a name="cross-function-communication"></a>跨函式通訊

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 均建置用來管理多個函式之間的狀態轉換和通訊。

如果不使用持久函數或邏輯應用與多個函數集成，最好使用存儲佇列進行跨功能通信。 主要原因是存儲佇列比其他存儲選項更便宜且更易於預配。 

儲存體佇列中個別訊息大小限制在 64 KB。 如果您需要在函式之間傳遞更大型的訊息，Azure 服務匯流排佇列可用來支援標準層中大小上限為 256 KB 的訊息，以及進階層中上限為 1 MB 的訊息。

如果您需要在處理之前篩選訊息，服務匯流排主題會很實用。

事件中樞對於支援大量通訊很有用。


### <a name="write-functions-to-be-stateless"></a>撰寫無狀態函式 

若可能，Functions 應該是無狀態和具有等冪性。 將任何必要的狀態資訊與您的資料產生關聯。 例如，正在處理訂單就可能具有相關聯的 `state` 成員。 函式本身保持無狀態時，函式可以依據該狀態處理訂單。 

特別建議計時器觸發程序使用等冪函式。 例如，如果您有絕對必須每天運行一次的內容，請編寫它，以便它在白天隨時以相同的結果運行。 當特定一天沒有工作時，函數可以退出。 如果先前的執行無法完成，下一次執行應該會定停止的位置開始。


### <a name="write-defensive-functions"></a>編寫防禦性函式

假設您的函式可能隨時會遇到例外狀況。 設計您的函式，使得它能夠在下一次執行期間從先前的失敗點繼續執行。 假設需要執行下列動作的案例︰

1. 查詢資料庫中的 10，000 行。
2. 對每個資料列建立佇列訊息，來進一步處理向下一行。
 
根據系統的複雜性，您可能有：涉及的下游服務行為不端、網路中斷或達到配額限制等。所有這些都可以隨時影響您的功能。 您必須設計您的函式，以對其做好準備。

如果在插入這些項目中的 5,000 個至佇列以進行處理之後發生失敗，您的程式碼如何因應？ 追蹤集合中您已完成的項目。 否則，您可能下一次又將它們插入。 這種雙重插入會對您的工作流程產生嚴重影響，因此[，使您的功能具有冪等性](functions-idempotent.md)。 

如果佇列項目已經過處理，請讓您的函式成為無作業。

利用已針對您在 Azure Functions 平台中所使用元件提供的防禦性措施。 例如，請參閱文件中**處理有害的佇列訊息**，以了解 [Azure 儲存體佇列觸發程序和繫結](functions-bindings-storage-queue-trigger.md#poison-messages)。 

## <a name="scalability-best-practices"></a>延展性最佳做法

有許多因素會影響函數應用實例的縮放方式。 [函式調整](functions-scale.md)文件中會提供詳細資料。  以下是一些最佳做法，可確保函式應用程式的最佳延展性。

### <a name="share-and-manage-connections"></a>共用及管理連線

盡可能重用與外部資源的連接。 請參閱[如何管理 Azure Functions 中的連線](./manage-connections.md)。

### <a name="avoid-sharing-storage-accounts"></a>避免共用存儲帳戶

創建函數應用時，必須將其與存儲帳戶關聯。 存儲帳戶連接在[AzureWeb作業存儲應用程式設定](./functions-app-settings.md#azurewebjobsstorage)中維護。 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>不要在相同函式應用程式中混用測試和實際執行程式碼

函式應用程式內的 Functions 會共用資源。 例如，共用記憶體。 如果您在生產環境中使用函式應用程式，請勿對它新增與測試相關的函式和資源。 在實際執行程式碼執行期間可能導致發生未預期的額外負荷。

對於在實際執行函式應用程式中載入的項目，請務必小心。 記憶體會在應用程式中的每個函式間平均分配。

如果在多個 .NET 函數中引用了共用組件，則將其放在公共共用資料夾中。 否則，可能會意外部署同一二進位檔案的多個版本，這些版本在函數之間的行為不同。

不要在生產代碼中使用詳細的日誌記錄，這會對性能產生負面影響。

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用非同步程式碼但避免封鎖呼叫

非同步程式設計是推薦的最佳做法，尤其是在涉及阻止 I/O 操作時。

在 C# 中，始終避免`Result`引用`Task`實例上的`Wait`屬性或調用方法。 這個方法可能會導致執行緒耗盡。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>使用多個輔助角色進程

預設情況下，函數的任何主機實例都使用單個輔助進程。 為了提高性能（尤其是使用 Python 等單線程運行時），請使用[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)來增加每個主機的輔助進程數（最多 10 個）。 然後，Azure 函數嘗試在這些輔助人員之間均勻地分配同時調用函數。 

FUNCTIONS_WORKER_PROCESS_COUNT適用于函數在擴展應用程式以滿足需求時創建的每個主機。 

### <a name="receive-messages-in-batch-whenever-possible"></a>儘可能分批接收訊息

某些觸發程序 (如事件中樞) 能夠在單一引動過程中接收一批訊息。  分批處理訊息的效能比較好。  如 [host.json 參考文件](functions-host-json.md)所述，您可以在 `host.json` 檔案中設定批次大小上限。

對於 C# 函數，可以將類型更改為強型別陣列。  例如，方法簽章可能是 `EventData[] sensorEvent`，而不是 `EventData sensorEvent`。  對於其他語言，您需要顯式將 中的`function.json``many`基數屬性設置為 ，以便啟用批次處理[，如下所示](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)。

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>設定主機的行為，更妥善處理並行作業

函式應用程式中的 `host.json` 檔案能夠設定主機執行階段和觸發程序行為。  除了批次處理行為，您可以管理數個觸發程序的並行作業。 經常調整這些選項中的值，可協助每個執行個體針對所叫用函式的需求進行適當調整。

host.json 檔中的設置應用於應用中的所有功能，在函數的*單個實例*中。 例如，如果您有一個功能應用，其中兩個 HTTP[`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings)函數和請求設置為 25，則對任一 HTTP 觸發器的請求將計入共用的 25 個併發請求。  當該函數應用縮放為 10 個實例時，這兩個函數實際上允許 250 個併發請求（每個實例 10 個實例 = 25 個併發請求）。 

其他主機配置選項見[主機.json 配置文章](functions-host-json.md)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [如何管理 Azure Functions 中的連線](manage-connections.md)
* [Azure App Service 最佳做法](../app-service/app-service-best-practices.md)
