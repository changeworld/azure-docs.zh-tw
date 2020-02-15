---
title: Azure Functions 的最佳作法
description: 了解 Azure Functions 的最佳作法與模式。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd52c4cc5cd9d4c01ddb355bdfb28d0952e65759
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210254"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>將 Azure Functions 效能和可靠性最佳化

本文提供指引來改善[無伺服器](https://azure.microsoft.com/solutions/serverless/)函式應用程式的效能和可靠性。  

## <a name="general-best-practices"></a>一般最佳作法

以下是如何使用 Azure Functions 建置及打造無伺服器解決方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免長時間執行的函式

大型長時間執行的函式可能會造成非預期的逾時問題。 若要深入瞭解特定主控方案的超時時間，請參閱[函數應用程式超時期間](functions-scale.md#timeout)。 

函式可能會因為許多 node.js 相依性而變大。 匯入相依性也可能會造成載入時間增加，而導致未預期的逾時。 系統會以明確和隱含方式載入相依性。 您的程式碼載入的單一模組可能會載入其本身的其他模組。 

在可能時，將大型函式重構為較小的函式集，共用運作並快速傳回回應。 例如，webhook 或 HTTP 觸發程式函數可能需要特定時間限制內的通知回應;webhook 通常需要立即回應。 您可以將 HTTP 觸發程序承載傳遞到要由佇列觸發程序函式處理的佇列中。 這種方法可讓您延遲實際的工作，並傳回立即的回應。


### <a name="cross-function-communication"></a>跨函式通訊

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 均建置用來管理多個函式之間的狀態轉換和通訊。

如果未使用 Durable Functions 或 Logic Apps 來與多個函式整合，最好使用儲存體佇列進行跨函式通訊。 主要的原因是儲存體佇列較便宜，而且比其他儲存體選項更容易布建。 

儲存體佇列中個別訊息大小限制在 64 KB。 如果您需要在函式之間傳遞更大型的訊息，Azure 服務匯流排佇列可用來支援標準層中大小上限為 256 KB 的訊息，以及進階層中上限為 1 MB 的訊息。

如果您需要在處理之前篩選訊息，服務匯流排主題會很實用。

事件中樞對於支援大量通訊很有用。


### <a name="write-functions-to-be-stateless"></a>撰寫無狀態函式 

若可能，Functions 應該是無狀態和具有等冪性。 將任何必要的狀態資訊與您的資料產生關聯。 例如，正在處理訂單就可能具有相關聯的 `state` 成員。 函式本身保持無狀態時，函式可以依據該狀態處理訂單。 

特別建議計時器觸發程序使用等冪函式。 例如，如果您有一定要一天執行一次的專案，請撰寫它，讓它可以在一天內以相同的結果執行。 當特定一天沒有任何工作時，此函式就會結束。 如果先前的執行無法完成，下一次執行應該會定停止的位置開始。


### <a name="write-defensive-functions"></a>編寫防禦性函式

假設您的函式可能隨時會遇到例外狀況。 設計您的函式，使得它能夠在下一次執行期間從先前的失敗點繼續執行。 假設需要執行下列動作的案例︰

1. 查詢資料庫中的10000資料列。
2. 對每個資料列建立佇列訊息，來進一步處理向下一行。
 
視系統的複雜程度而定，您可能會有：涉及下游服務的行為不正確、網路中斷或達到配額限制等等。所有這些功能都可能隨時影響您的函式。 您必須設計您的函式，以對其做好準備。

如果在插入這些項目中的 5,000 個至佇列以進行處理之後發生失敗，您的程式碼如何因應？ 追蹤集合中您已完成的項目。 否則，您可能下一次又將它們插入。 這種雙重插入可能會對您的工作流程造成嚴重影響，因此讓您的函式[成為等冪](functions-idempotent.md)。 

如果佇列項目已經過處理，請讓您的函式成為無作業。

利用已針對您在 Azure Functions 平台中所使用元件提供的防禦性措施。 例如，請參閱文件中**處理有害的佇列訊息**，以了解 [Azure 儲存體佇列觸發程序和繫結](functions-bindings-storage-queue.md#trigger---poison-messages)。 

## <a name="scalability-best-practices"></a>延展性最佳做法

有數個因素會影響函式應用程式的實例調整方式。 [函式調整](functions-scale.md)文件中會提供詳細資料。  以下是一些最佳做法，可確保函式應用程式的最佳延展性。

### <a name="share-and-manage-connections"></a>共用及管理連線

盡可能重複使用外部資源的連接。 請參閱[如何管理 Azure Functions 中的連線](./manage-connections.md)。

### <a name="avoid-sharing-storage-accounts"></a>避免共用儲存體帳戶

當您建立函數應用程式時，您必須將它與儲存體帳戶產生關聯。 儲存體帳戶連線會保留在[AzureWebJobsStorage 應用程式設定](./functions-app-settings.md#azurewebjobsstorage)中。 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>不要在相同函式應用程式中混用測試和實際執行程式碼

函式應用程式內的 Functions 會共用資源。 例如，共用記憶體。 如果您在生產環境中使用函式應用程式，請勿對它新增與測試相關的函式和資源。 在實際執行程式碼執行期間可能導致發生未預期的額外負荷。

對於在實際執行函式應用程式中載入的項目，請務必小心。 記憶體會在應用程式中的每個函式間平均分配。

如果您有多個 .NET 函式中參考的共用元件，請將它放在通用共用資料夾中。 否則，您可能會不小心部署相同二進位檔的多個版本，其行為不同。

請勿在實際執行的程式碼中使用詳細資訊記錄，這會對效能造成負面影響。

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用非同步程式碼但避免封鎖呼叫

非同步程式設計是建議的最佳作法，特別是在涉及封鎖 i/o 作業時。

在C#中，請一律避免參考 `Task` 實例上的 `Result` 屬性或呼叫 `Wait` 方法。 這個方法可能會導致執行緒耗盡。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>使用多個背景工作進程

根據預設，函式的任何主控制項實例都會使用單一背景工作進程。 若要改善效能，尤其是使用像是 Python 的單一執行緒執行時間，請使用[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)來增加每一主機的工作者進程數（最多10個）。 Azure Functions 接著會嘗試在這些背景工作中平均散發並行函式呼叫。 

FUNCTIONS_WORKER_PROCESS_COUNT 適用于在相應放大應用程式以符合需求時所建立的每個主機。 

### <a name="receive-messages-in-batch-whenever-possible"></a>儘可能分批接收訊息

某些觸發程序 (如事件中樞) 能夠在單一引動過程中接收一批訊息。  分批處理訊息的效能比較好。  如 `host.json`host.json 參考文件[所述，您可以在 ](functions-host-json.md) 檔案中設定批次大小上限。

針對C#函式，您可以將型別變更為強型別陣列。  例如，方法簽章可能是 `EventData sensorEvent`，而不是 `EventData[] sensorEvent`。  針對其他語言，您必須將 `function.json` 中的 [基數] 屬性明確設定為 [`many`]，才能啟用批次處理，[如下所示](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)。

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>設定主機的行為，更妥善處理並行作業

函式應用程式中的 `host.json` 檔案能夠設定主機執行階段和觸發程序行為。  除了批次處理行為，您可以管理數個觸發程序的並行作業。 經常調整這些選項中的值，可協助每個執行個體針對所叫用函式的需求進行適當調整。

Host. json 檔案中的設定會套用至單一函式*實例*中應用程式內的所有函式。 例如，如果您的函式應用程式有兩個 HTTP 函式，且[`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings)要求設定為25，則 HTTP 觸發程式的要求會算在共用的25個並行要求。  當該函式應用程式調整為10個實例時，這兩個函式可有效地允許250個並行要求（每個實例10個實例 * 25 個並行要求）。 

如有其他主機設定選項，請參閱[host. json](functions-host-json.md)設定一文。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [如何管理 Azure Functions 中的連線](manage-connections.md)
* [Azure App Service 最佳做法](../app-service/app-service-best-practices.md)
