---
title: Durable Functions 中的版本控制 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions  擴充中實作版本控制。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74232747"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的版本控制

在應用程式的存留期之中，必然會新增、移除和變更函式。 [Durable Functions](durable-functions-overview.md) 可以採取以往不可能的方式將函式鏈結在一起，而此鏈結會影響您如何處理版本控制。

## <a name="how-to-handle-breaking-changes"></a>如何處理重大變更

有幾個重大變更的例子需要注意。 本文討論最常見的重大變更。 所有重大變更背後的主題都是：變更函式程式碼會影響新的和現有的函式協調流程。

### <a name="changing-activity-or-entity-function-signatures"></a>變更活動或實體函式簽章

簽章變更是指函式的名稱、輸入或輸出有所變更。 如果對活動或實體函式進行這種變更，它可能會中斷相依于它的任何協調器函式。 如果您更新協調器函式來配合此變更，可能會中斷現有的執行中執行個體。

例如，假設我們有下列協調器函式。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

這個簡化的函式會取得 **Foo** 的結果，然後傳給 **Bar**。 假設我們需要將 **Foo** 的傳回值從 `bool` 變更為 `int`，以支援更多種不同的結果值。 結果如下所示：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> 先前的 c # 範例是以 Durable Functions 2.x 為目標。 針對 Durable Functions 1.x，您必須使用， `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

在協調器函式的所有新執行個體中，此變更不會有問題，但會中斷任何執行中的執行個體。 例如，假設協調流程實例呼叫名為的函式時 `Foo` ，取得布林值，然後檢查點。 如果此時部署簽章變更，則通過檢查點的執行個體在繼續並重新呼叫 `context.CallActivityAsync<int>("Foo")` 時會立刻失敗。 發生此失敗的原因是記錄資料表中的結果是， `bool` 但新的程式碼嘗試將它還原序列化為 `int` 。

這個範例只是簽章變更可能中斷現有實例的許多不同方式之一。 一般而言，如果協調器需要變更呼叫函式的方式，則變更就很可能會有問題。

### <a name="changing-orchestrator-logic"></a>變更協調器邏輯

另一種版本設定問題起因於變更協調器函式程式碼，導致執行中之執行個體的重新執行邏輯發生混淆。

假設有下列協調器函式：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

現在，假設您想要新增另一個函式呼叫，此變更看似無害。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> 先前的 c # 範例是以 Durable Functions 2.x 為目標。 針對 Durable Functions 1.x，您必須使用， `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext` 。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

此變更會在 **Foo** 和 **Bar** 之間新增呼叫 **SendNotification** 函式。 簽章不變。 當現有的執行個體在呼叫 **Bar** 之後繼續時，就發生問題。 在重新執行期間，如果對**Foo**的原始呼叫傳回 `true` ，則 orchestrator Replay 會呼叫**SendNotification**，而不在其執行歷程記錄中。 結果，「永久性工作架構」會失敗，並傳回 `NonDeterministicOrchestrationException`，因為原本認為應該呼叫 **Bar**，但卻發現呼叫 **SendNotification**。 新增任何對「持久」 Api 的呼叫時，可能會發生相同類型的問題，包括 `CreateTimer` 、 `WaitForExternalEvent` 等等。

## <a name="mitigation-strategies"></a>風險降低策略

以下是解決版本控制問題的一些策略：

* 不執行任何動作
* 停止所有執行中的執行個體
* 並存部署

### <a name="do-nothing"></a>不執行任何動作

處理重大變更最簡單的方式，就是讓執行中的協調流程執行個體失敗。 新的執行個體會成功執行已變更的程式碼。

這類失敗是否為問題，取決於您的進行中實例的重要性。 如果您正在進行開發，並不在意執行中的執行個體，這樣可能就很好。 不過，您需要處理診斷管線中的例外狀況和錯誤。 如果想要避免這些事情，請考慮其他版本設定選項。

### <a name="stop-all-in-flight-instances"></a>停止所有執行中的執行個體

另一個選項是停止所有執行中的執行個體。 藉由清除內部**控制佇列**和**工作專案佇列**佇列的內容，即可停止所有實例。 實例將會永遠停滯在何處，但它們不會使您的記錄發生失敗訊息的混亂。 這種方法非常適合用於快速原型開發。

> [!WARNING]
> 這些佇列的詳細資料可能隨著時間而變更，請勿依賴此技術來處理生產工作負載。

### <a name="side-by-side-deployments"></a>並存部署

為了確保安全地部署重大變更，將重大變更與舊版並存部署就能萬無一失。 您可以採取下列任何技術：

* 將所有更新部署為全新的函式，以保持現有的功能。 這可能會很棘手，因為新函式版本的呼叫端也必須遵循相同的指導方針進行更新。
* 以不同的儲存體帳戶，將所有更新部署為新的函式應用程式。
* 使用相同的儲存體帳戶部署函數應用程式的新複本，但使用更新的 `taskHub` 名稱。 並存部署是建議的技巧。

### <a name="how-to-change-task-hub-name"></a>如何變更工作中樞名稱

您可以在 *host.json* 檔案中設定工作中樞，如下所示：

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>函數2。0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Durable Functions v1. x 的預設值為 `DurableFunctionsHub` 。 從 Durable Functions v2.0 開始，預設的工作中樞名稱會與 Azure 中的函式應用程式名稱相同，或者，如果在 Azure 外部執行，則為 `TestHubName` 。

所有 Azure 儲存體實體都是依據 `hubName` 設定值來命名。 只要指定新名稱給工作中樞，就可以確保為應用程式的新版本建立個別的佇列和記錄資料表。 不過，函數應用程式會停止處理協調流程的事件，或在先前的工作中樞名稱底下建立的實體。

建議您將函式應用程式的新版本部署到新的[部署位置](../functions-deployment-slots.md)。 部署位置可讓您並存執行函式應用程式的多個複本，而且其中只有一個是作用中的「生產」** 位置。 當您準備將新的協調流程邏輯公開到現有的基礎結構時，只要將新的版本調換到生產位置即可，就是這麼簡單。

> [!NOTE]
> 當您對協調器函式使用 HTTP 和 Webhook 觸發程序時，此策略最理想。 對於非 HTTP 觸發程式（例如佇列或事件中樞），觸發程序定義應該衍生自在交換作業中更新的[應用程式設定](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理效能和級別問題](durable-functions-perf-and-scale.md)
