---
title: 持久功能的零停機時間部署
description: 瞭解如何為零停機時間部署啟用持久函數業務流程。
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231263"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>持久功能的零停機時間部署

持久函數[的可靠執行模型](durable-functions-checkpointing-and-replay.md)要求業務流程具有確定性，這會給部署更新帶來額外的挑戰。 當部署包含對活動函數簽名或協調器邏輯的更改時，在運行中的業務流程實例將失敗。 這種情況對於長時間運行的業務流程（可能代表工時或工作天數）的情況尤其成為問題。

為了防止發生這些故障，您有兩個選項： 
- 延遲部署，直到所有正在運行的業務流程實例完成。
- 確保任何正在運行的業務流程實例都使用函數的現有版本。 

> [!NOTE]
> 本文為面向持久函數 1.x 的功能應用提供了指南。 它尚未更新，以考慮持久函數 2.x 中引入的更改。 有關擴展版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)。

下圖比較了實現持久函數零停機時間部署的三個主要策略： 

| 策略 |  使用時機 | 優點 | 缺點 |
| -------- | ------------ | ---- | ---- |
| [版本控制](#versioning) |  不會經常遇到[中斷更改的應用程式。](durable-functions-versioning.md) | 易於實現。 |  增加記憶體中的函數應用大小和函數數。<br/>代碼複製。 |
| [帶插槽的狀態檢查](#status-check-with-slot) | 長時間運行的業務流程持續時間不超過 24 小時或經常重疊的業務流程的系統。 | 簡單的代碼庫。<br/>不需要額外的功能應用管理。 | 需要額外的存儲帳戶或任務中心管理。<br/>在未運行業務流程時需要時間段。 |
| [應用程式路由](#application-routing) | 在業務流程未運行時沒有時間段的系統，例如具有持續超過 24 小時的業務流程或具有頻繁重疊業務流程的時間段。 | 使用具有重大更改的不斷運行業務流程來處理系統的新版本。 | 需要智慧應用程式路由器。<br/>可以最大程度地提供訂閱允許的功能應用數。 預設值為 100。 |

## <a name="versioning"></a>版本控制

定義函數的新版本，並將舊版本保留在函數應用中。 正如您在關係圖中看到的，函數的版本將成為其名稱的一部分。 由於保留以前版本的函數，因此在運行中業務流程實例可以繼續引用它們。 同時，對新業務流程實例的請求需要最新版本，業務流程用戶端函數可以從應用設置中引用該版本。

![版本控制策略](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

在此策略中，必須複製每個函數，並且必須更新對其他函數的引用。 您可以通過編寫腳本來簡化它。 下面是一個帶有遷移腳本[的示例專案](https://github.com/TsuyoshiUshio/DurableVersioning)。

>[!NOTE]
>此策略使用部署槽來避免部署期間的停機時間。 有關如何創建和使用新部署槽的更多詳細資訊，請參閱[Azure 函數部署槽](../functions-deployment-slots.md)。

## <a name="status-check-with-slot"></a>帶插槽的狀態檢查

當函數應用的當前版本在生產槽中運行時，請將函數應用的新版本部署到臨時槽。 在交換生產和暫存槽之前，請檢查是否有任何正在運行的業務流程實例。 完成所有業務流程實例後，可以執行交換。 當您沒有業務流程實例在飛行中時，此策略有效。 當業務流程不長時間運行且業務流程執行不經常重疊時，這是最佳方法。

### <a name="function-app-configuration"></a>功能應用配置

使用以下過程設置此方案。

1. [將部署槽添加到](../functions-deployment-slots.md#add-a-slot)函數應用以進行暫存和生產。

1. 對於每個插槽，將[AzureWebJobs 存儲應用程式設定](../functions-app-settings.md#azurewebjobsstorage)設置為共用存儲帳戶的連接字串。 此存儲帳戶連接字串由 Azure 函數運行時使用。 此帳戶由 Azure 函數運行時使用，並管理函數的金鑰。

1. 對於每個插槽，請創建新的應用設置，例如。 `DurableManagementStorage` 將其值設置為不同存儲帳戶的連接字串。 這些存儲帳戶由持久函數擴展用於[可靠執行](durable-functions-checkpointing-and-replay.md)。 為每個插槽使用單獨的存儲帳戶。 不要將此設置標記為部署槽設置。

1. 在函數應用的[host.json 檔的持久任務部分](durable-functions-bindings.md#hostjson-settings)中，指定`azureStorageConnectionStringName`為在步驟 3 中創建的應用設置的名稱。

下圖顯示了部署槽和存儲帳戶的描述的配置。 在此潛在的預部署方案中，函數應用的版本 2 在生產槽中運行，而版本 1 仍保留在暫存槽中。

![部署插槽和存儲帳戶](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.json 示例

以下 JSON 片段是*host.json*檔中連接字串設置的示例。

#### <a name="functions-20"></a>功能 2.0

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

### <a name="cicd-pipeline-configuration"></a>CI/CD 管道配置

將 CI/CD 管道配置為僅在函數應用沒有掛起或正在運行的業務流程實例時進行部署。 使用 Azure 管道時，可以創建檢查這些條件的函數，如以下示例所示：

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

接下來，將暫存門配置為等待，直到沒有業務流程運行。 有關詳細資訊，請參閱[使用門發佈部署控制](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![部署閘道](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure 管道檢查函數應用在部署開始之前運行業務流程實例。

![部署門（正在運行）](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

現在，應將函數應用的新版本部署到暫存槽。

![暫存槽](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

最後，交換插槽。 

未標記為部署槽設置的應用程式設定也會交換，因此版本 2 應用保留對存儲帳戶 A 的引用。由於在存儲帳戶中跟蹤業務流程狀態，因此在版本 2 應用上運行的任何業務流程將繼續在新插槽中運行，而不會中斷。

![部署位置](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

要對兩個插槽使用相同的存儲帳戶，可以更改任務中心的名稱。 在這種情況下，您需要管理插槽的狀態和應用的 HubName 設置。 要瞭解更多資訊，請參閱[持久函數中的任務中心](durable-functions-task-hubs.md)。

## <a name="application-routing"></a>應用程式路由

這種策略是最複雜的。 但是，它可用於在運行業務流程之間沒有時間的函數應用。

對於此策略，您必須在持久函數前面創建*應用程式路由器*。 此路由器可通過持久功能實現。 路由器負責：

* 部署功能應用。
* 管理持久函數的版本。 
* 將業務流程請求路由到功能應用。

第一次收到業務流程請求時，路由器執行以下任務：

1. 在 Azure 中創建新的函數應用。
2. 將函數應用的代碼部署到 Azure 中的新功能應用。
3. 將業務流程請求轉發到新應用。

路由器管理將應用代碼的哪個版本部署到 Azure 中的哪個函數應用的狀態。

![應用程式路由（首次）](media/durable-functions-zero-downtime-deployment/application-routing.png)

路由器根據隨要求傳送的版本將部署和業務流程請求定向到相應的函數應用。 它忽略修補程式版本。

部署應用的新版本時，無需進行重大更改，則可以增加修補程式版本。 路由器部署到現有功能應用，併發送對代碼的舊版本和新版本的請求，這些代碼將路由到相同的函數應用。

![應用程式路由（無中斷更改）](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

部署具有重大更改的應用的新版本時，可以增加主要版本或次要版本。 然後，應用程式路由器在 Azure 中創建新的函數應用，部署到它，並將應用新版本的請求路由到它。 在下圖中，在應用的 1.0.1 版本上運行業務流程，但 1.1.0 版本的請求將路由到新功能應用。

![應用程式路由（中斷更改）](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

路由器監視 1.0.1 版本上的業務流程狀態，並在完成所有業務流程後刪除應用。 

### <a name="tracking-store-settings"></a>跟蹤存儲設置

每個函數應用都應使用單獨的計畫佇列，可能位於單獨的存儲帳戶中。 如果要查詢應用程式所有版本中的所有業務流程實例，則可以跨函數應用共用實例和歷史記錄表。 您可以通過在[host.json](durable-functions-bindings.md#host-json) `trackingStoreConnectionStringName`設置`trackingStoreNamePrefix`檔中配置 和 設置來共用表，以便它們都使用相同的值。

有關詳細資訊，請參閱在[Azure 中管理持久函數中的實例](durable-functions-instance-management.md)。

![跟蹤存儲設置](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [版本化持久功能](durable-functions-versioning.md)

