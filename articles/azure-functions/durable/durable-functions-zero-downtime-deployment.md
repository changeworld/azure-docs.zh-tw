---
title: Durable Functions 的零停機部署
description: 瞭解如何啟用您的 Durable Functions 協調流程，以進行零停機部署。
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 45f87898f7da432e5bdd09061e74c33a1a8fe41b
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165697"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Durable Functions 的零停機部署

Durable Functions 的[可靠執行模型](durable-functions-checkpointing-and-replay.md)需要協調流程具有決定性，這會在您部署更新時產生額外的挑戰。 當部署包含活動函式簽章或 orchestrator 邏輯的變更時，進行中的協調流程實例會失敗。 對於長時間執行的協調流程實例而言，這種情況特別有問題，這可能代表數小時或幾天的工作。

若要避免發生這些失敗，您有兩個選項： 
- 延遲部署，直到所有執行中的協調流程實例都完成為止。
- 請確定任何執行中的協調流程實例都使用您的函式的現有版本。 

下圖比較三個主要策略，以達到 Durable Functions 的零停機部署： 

| 策略 |  使用時機 | 優點 | 缺點 |
| -------- | ------------ | ---- | ---- |
| [版本控制](#versioning) |  不常遇到[重大變更](durable-functions-versioning.md)的應用程式。 | 容易執行。 |  增加記憶體中的函數應用程式大小和函式數目。<br/>程式碼重複。 |
| [具有位置的狀態檢查](#status-check-with-slot) | 沒有長時間執行的協調流程持續超過24小時或經常重迭之協調流程的系統。 | 簡單的程式碼基底。<br/>不需要額外的函數應用程式管理。 | 需要額外的儲存體帳戶或任務管理中樞。<br/>當沒有任何協調流程正在執行時，需要一段時間。 |
| [應用程式路由](#application-routing) | 在協調流程未執行時沒有時間週期的系統，例如，協調流程過去超過24小時或經常重迭協調流程的時間週期。 | 處理新版本的系統，持續執行具有重大變更的協調流程。 | 需要智慧型應用程式路由器。<br/>可能會使您的訂用帳戶所允許的函式應用程式數目達到上限。 預設值為 100。 |

## <a name="versioning"></a>版本控制

定義函式的新版本，並將舊版本保留在函數應用程式中。 如您在圖表中所見，函式的版本會成為其名稱的一部分。 因為已保留舊版的函式，所以進行中的協調流程實例可以繼續參考它們。 同時，新的協調流程實例的要求會呼叫最新版本，您的協調流程用戶端函式可以從應用程式設定參考。

![版本控制策略](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

在此策略中，每個函數都必須複製，而且必須更新其對其他函式的參考。 您可以撰寫腳本，讓它更容易。 以下是包含遷移腳本的[範例專案](https://github.com/TsuyoshiUshio/DurableVersioning)。

>[!NOTE]
>此策略會使用部署位置來避免部署期間的停機時間。 如需如何建立和使用新部署位置的詳細資訊，請參閱[Azure Functions 部署](../functions-deployment-slots.md)位置。

## <a name="status-check-with-slot"></a>具有位置的狀態檢查

當您的函式應用程式目前版本在生產位置中執行時，請將新版本的函式應用程式部署到您的預備位置。 交換生產和預備位置之前，請檢查是否有任何正在執行的協調流程實例。 完成所有協調流程實例之後，您就可以進行交換。 當您在沒有協調流程實例的情況下有可預測的期間時，此策略會運作。 當您的協調流程不是長時間執行，且您的協調流程執行頻率不常重迭時，這是最佳的方法。

### <a name="function-app-configuration"></a>函數應用程式設定

請使用下列程式來設定此案例。

1. [將部署位置新增](../functions-deployment-slots.md#add-a-slot)至您的函式應用程式以進行臨時和生產環境。

1. 針對每個位置，將[AzureWebJobsStorage 應用程式](../functions-app-settings.md#azurewebjobsstorage)設定設為共用儲存體帳戶的連接字串。 Azure Functions 執行時間會使用此儲存體帳戶連接字串。 此帳戶是由 Azure Functions 執行時間使用，並會管理函式的金鑰。

1. 針對每個位置，建立新的應用程式設定，例如 `DurableManagementStorage` 。 將其值設定為不同儲存體帳戶的連接字串。 Durable Functions 延伸模組會使用這些儲存體帳戶來進行[可靠的執行](durable-functions-checkpointing-and-replay.md)。 針對每個位置使用個別的儲存體帳戶。 請勿將此設定標記為部署位置設定。

1. 在您函式應用程式host.js的 [ [durableTask] 區段](durable-functions-bindings.md#hostjson-settings)中，將指定 `azureStorageConnectionStringName` 為您在步驟3中建立的應用程式設定名稱。

下圖顯示部署位置和儲存體帳戶的描述設定。 在這種可能的預先部署案例中，函式應用程式的第2版會在生產位置中執行，而第1版會保留在預備位置。

![部署位置和儲存體帳戶](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>範例 host.js

下列 JSON 片段是*host.json*檔案之連接字串設定的範例。

#### <a name="functions-20"></a>函數2。0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD 管線設定

只有當您的函式應用程式沒有擱置或執行中的協調流程實例時，才能設定 CI/CD 管線進行部署。 當您使用 Azure Pipelines 時，您可以建立函數來檢查這些條件，如下列範例所示：

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

接下來，設定暫存閘道等待，直到沒有任何協調流程正在執行為止。 如需詳細資訊，請參閱[使用閘道發行部署控制](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![部署閘道](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines 檢查函式應用程式，以在部署開始之前執行協調流程實例。

![部署閘道 (執行) ](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

現在，您的函式應用程式的新版本應部署至預備位置。

![預備位置](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

最後，交換位置。 

未標示為部署位置設定的應用程式設定也會一併交換，因此第2版應用程式會保留其對儲存體帳戶 A 的參考。由於協調流程狀態會在儲存體帳戶中追蹤，因此在第2版應用程式上執行的任何協調流程都會繼續在新的位置中執行，而不會中斷。

![部署位置](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

若要針對這兩個位置使用相同的儲存體帳戶，您可以變更您的工作中樞名稱。 在此情況下，您必須管理位置的狀態和應用程式的 HubName 設定。 若要深入瞭解，請參閱[Durable Functions 中的工作中樞](durable-functions-task-hubs.md)。

## <a name="application-routing"></a>應用程式路由

此策略是最複雜的。 不過，它可以用於沒有執行協調流程之間時間的函數應用程式。

針對此策略，您必須在 Durable Functions 前面建立*應用程式路由器*。 此路由器可以使用 Durable Functions 來執行。 路由器必須負責：

* 部署函數應用程式。
* 管理 Durable Functions 的版本。 
* 將協調流程要求路由傳送至函數應用程式。

第一次收到協調流程要求時，路由器會執行下列工作：

1. 在 Azure 中建立新的函數應用程式。
2. 將函數應用程式的程式碼部署至 Azure 中的新函式應用程式。
3. 將協調流程要求轉寄到新的應用程式。

路由器會管理您的應用程式代碼版本部署至 Azure 中哪個函式應用程式的狀態。

![第一次 (應用程式路由) ](media/durable-functions-zero-downtime-deployment/application-routing.png)

路由器會根據要求所傳送的版本，將部署和協調流程要求導向至適當的函式應用程式。 它會忽略修補程式版本。

當您部署應用程式的新版本而未進行重大變更時，您可以遞增修補程式版本。 路由器會部署至您現有的函式應用程式，並傳送舊版和新版本之程式碼的要求，而這會路由至相同的函式應用程式。

![應用程式路由 (沒有重大變更) ](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

當您使用重大變更來部署新版本的應用程式時，您可以遞增主要或次要版本。 然後，應用程式路由器會在 Azure 中建立新的函式應用程式、將其部署至其中，並將應用程式新版本的要求路由傳送至其中。 在下圖中，在應用程式的1.0.1 版上執行協調流程會繼續執行，但1.1.0 版本的要求會路由傳送至新的函式應用程式。

![應用程式路由 (重大變更) ](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

路由器會監視1.0.1 版本上的協調流程狀態，並在所有協調流程完成之後移除應用程式。 

### <a name="tracking-store-settings"></a>追蹤存放區設定

每個函數應用程式都應該使用個別的排程佇列，可能在不同的儲存體帳戶中。 如果您想要跨所有應用程式版本查詢所有協調流程實例，您可以在函式應用程式中共用實例和歷程記錄資料表。 您可以藉由在 [配置 `trackingStoreConnectionStringName` `trackingStoreNamePrefix` 檔] 中的 [ [host.js](durable-functions-bindings.md#host-json)設定和設定，讓它們全都使用相同的值，藉以共用資料表。

如需詳細資訊，請參閱[在 Azure 中的 Durable Functions 中管理實例](durable-functions-instance-management.md)。

![追蹤存放區設定](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [版本控制 Durable Functions](durable-functions-versioning.md)

