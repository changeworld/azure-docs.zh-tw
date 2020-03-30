---
title: Durable Functions 中的版本控制 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions  擴充中實作版本控制。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232747"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的版本控制

在應用程式的存留期之中，必然會新增、移除和變更函式。 [Durable Functions](durable-functions-overview.md) 可以採取以往不可能的方式將函式鏈結在一起，而此鏈結會影響您如何處理版本控制。

## <a name="how-to-handle-breaking-changes"></a>如何處理重大變更

有幾個重大變更的例子需要注意。 本文討論最常見的重大變更。 所有重大變更背後的主題都是：變更函式程式碼會影響新的和現有的函式協調流程。

### <a name="changing-activity-or-entity-function-signatures"></a>更改活動或實體函數簽名

簽章變更是指函式的名稱、輸入或輸出有所變更。 如果對活動或實體函數進行了此類更改，則可能會中斷依賴于它的任何協調器函數。 如果您更新協調器函式來配合此變更，可能會中斷現有的執行中執行個體。

例如，假設我們有以下協調器函數。

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
> 前面的 C# 示例針對持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

在協調器函式的所有新執行個體中，此變更不會有問題，但會中斷任何執行中的執行個體。 例如，考慮業務流程實例調用名為 的`Foo`函數的情況，獲取布林值，然後獲取檢查點。 如果此時部署簽章變更，則通過檢查點的執行個體在繼續並重新呼叫 `context.CallActivityAsync<int>("Foo")` 時會立刻失敗。 發生此失敗的原因是歷史記錄表中的結果是`bool`，但新代碼嘗試將其反序列化到`int`中。

此示例只是簽名更改可以中斷現有實例的多種不同方法之一。 一般而言，如果協調器需要變更呼叫函式的方式，則變更就很可能會有問題。

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
> 前面的 C# 示例針對持久函數 2.x。 對於持久函數 1.x，必須使用`DurableOrchestrationContext`而不是`IDurableOrchestrationContext`。 有關不同版本之間的差異的詳細資訊，請參閱[持久函數版本](durable-functions-versions.md)一文。

此變更會在 **Foo** 和 **Bar** 之間新增呼叫 **SendNotification** 函式。 簽章不變。 當現有的執行個體在呼叫 **Bar** 之後繼續時，就發生問題。 在重播期間，如果對**Foo**的原始`true`調用返回，則協調器重播將調用**Send通知**，這不是在其執行歷史記錄中。 結果，「永久性工作架構」會失敗，並傳回 `NonDeterministicOrchestrationException`，因為原本認為應該呼叫 **Bar**，但卻發現呼叫 **SendNotification**。 將任何調用添加到"持久"API（包括`CreateTimer`、`WaitForExternalEvent`等）時，可能會出現相同類型的問題。

## <a name="mitigation-strategies"></a>風險降低策略

以下是解決版本控制問題的一些策略：

* 不執行任何動作
* 停止所有執行中的執行個體
* 並存部署

### <a name="do-nothing"></a>不執行任何動作

處理重大變更最簡單的方式，就是讓執行中的協調流程執行個體失敗。 新的執行個體會成功執行已變更的程式碼。

此類故障是否是一個問題，取決於在飛行中實例的重要性。 如果您正在進行開發，並不在意執行中的執行個體，這樣可能就很好。 但是，您需要處理診斷管道中的異常和錯誤。 如果想要避免這些事情，請考慮其他版本設定選項。

### <a name="stop-all-in-flight-instances"></a>停止所有執行中的執行個體

另一個選項是停止所有執行中的執行個體。 通過清除**內部控制佇列**和**工作項佇列**的內容，可以停止所有實例。 實例將永遠停留在它們的位置，但它們不會用失敗消息使日誌變得混亂。 這種方法在快速原型開發中是理想的。

> [!WARNING]
> 這些佇列的詳細資料可能隨著時間而變更，請勿依賴此技術來處理生產工作負載。

### <a name="side-by-side-deployments"></a>並存部署

為了確保安全地部署重大變更，將重大變更與舊版並存部署就能萬無一失。 您可以採取下列任何技術：

* 將所有更新部署為全新的函數，使現有函數保留為現有功能。 這可能很棘手，因為必須根據相同的準則更新新功能版本的調用方。
* 以不同的儲存體帳戶，將所有更新部署為新的函式應用程式。
* 使用同一存儲帳戶但具有更新`taskHub`的名稱部署函數應用的新副本。 並行部署是推薦的技術。

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

#### <a name="functions-20"></a>功能 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

持久函數 v1.x 的預設值為`DurableFunctionsHub`。 從持久函數 v2.0 開始，預設任務中心名稱與 Azure 中的函數應用名稱相同，或者如果`TestHubName`在 Azure 之外運行。

所有 Azure 儲存體實體都是依據 `hubName` 設定值來命名。 只要指定新名稱給工作中樞，就可以確保為應用程式的新版本建立個別的佇列和記錄資料表。 但是，函數應用將停止處理以前任務中心名稱下創建的業務流程或實體的事件。

建議您將函式應用程式的新版本部署到新的[部署位置](../functions-deployment-slots.md)。 部署位置可讓您並存執行函式應用程式的多個複本，而且其中只有一個是作用中的「生產」** 位置。 當您準備將新的協調流程邏輯公開到現有的基礎結構時，只要將新的版本調換到生產位置即可，就是這麼簡單。

> [!NOTE]
> 當您對協調器函式使用 HTTP 和 Webhook 觸發程序時，此策略最理想。 對於非 HTTP 觸發器（如佇列或事件中心），觸發器定義應派生自作為交換操作的一部分進行更新[的應用設置](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理效能和級別問題](durable-functions-perf-and-scale.md)
