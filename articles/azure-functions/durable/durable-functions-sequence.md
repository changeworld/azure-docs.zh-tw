---
title: Durable Functions 中的函式鏈結 - Azure
description: 了解如何執行 Durable Functions 範例來執行一連串的函式。
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562051"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions 中的函式鏈結- Hello 序列範例

函式鏈結是指以特定順序執行一連串函式的模式。 通常，一個函式的輸出必須套用至另一個函式的輸入。 此文章說明當您完成 Durable Functions 快速入門 ([C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md)) 時所建立的鏈結序列。 如需 Durable Functions 的詳細資訊，請參閱 [Durable Functions 概觀](durable-functions-overview.md)。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>函式

本文說明範例應用程式中的函式如下：

* `E1_HelloSequence`：一個[協調器函數](durable-functions-bindings.md#orchestration-trigger)，`E1_SayHello`在序列中多次調用。 它會儲存 `E1_SayHello` 呼叫的輸出，並記錄結果。
* `E1_SayHello`：一個[活動函數](durable-functions-bindings.md#activity-trigger)，用"你好"來預置字串。
* `HttpStart`：啟動協調器實例的 HTTP 觸發函數。

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence協調器功能

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

所有 C# 協調流程函式都必須有 `DurableOrchestrationContext` 類型的參數 (在 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 組件中)。 此上下文物件允許您調用其他*活動*函數並使用其`CallActivityAsync`方法傳遞輸入參數。

程式碼中以不同的參數值連續呼叫 `E1_SayHello` 三次。 每次呼叫的傳回值會新增至函式最後傳回的 `outputs` 清單。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

> [!NOTE]
> JavaScript 持久函數僅適用于函數 2.0 運行時。

#### <a name="functionjson"></a>function.json

如果您使用 Visual Studio Code 或 Azure 入口網站進行開發，以下是適用於協調器函式的 *function.json* 檔案內容。 大部分協調器 *function.json* 檔案看起來幾乎完全像這樣。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

重要的是 `orchestrationTrigger` 繫結類型。 所有協調器函式必須都使用此觸發程序類型。

> [!WARNING]
> 為了遵守協調器函式的「沒有 I/O」規則，當您使用 `orchestrationTrigger` 觸發程序繫結時，請勿使用任何輸入或輸出繫結。  如果需要其他輸入或輸出繫結，請改為在協調器所呼叫之 `activityTrigger` 函式的內容中使用。 有關詳細資訊，請參閱[協調器函數代碼約束](durable-functions-code-constraints.md)一文。

#### <a name="indexjs"></a>index.js

下面是函數：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

所有 JavaScript 業務流程函數都必須包括[`durable-functions`模組](https://www.npmjs.com/package/durable-functions)。 它是一個庫，使您能夠在 JavaScript 中編寫持久函數。 協調流程函式與其他 JavaScript 函式之間有三項重大差異：

1. 該函數是[一個產生器函數。 .](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. 此函式會包裝在對`durable-functions`模組的`orchestrator`方法進行的呼叫中 (在此是 `df`)。
3. 此函式必須是同步的。 「Orchestrator」方法會處理呼叫「context.done」，因為函式應該只是「return」。

該`context`物件包含一個`df`持久業務流程上下文物件，允許您調用其他*活動*函數並使用其`callActivity`方法傳遞輸入參數。 程式碼會使用不同的參數值依序呼叫 `E1_SayHello` 三次，使用 `yield` 表示執行應等候要傳回的非同步活動函式呼叫。 每個調用的傳回值將添加到陣列中`outputs`，該陣列在函數的末尾返回。

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello活動功能

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

活動使用`ActivityTrigger`屬性。 使用 提供`IDurableActivityContext`來執行與活動相關的操作，例如使用`GetInput<T>`訪問輸入值。

`E1_SayHello` 的實作是相當簡單的字串格式化作業。

可以直接綁定到傳遞到`IDurableActivityContext`活動函數的類型，而不是綁定到 。 例如：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/功能.json

活動函式 `E1_SayHello` 和 `E1_HelloSequence`的 *function.json*檔案很類似，差別在於前者使用 `activityTrigger` 繫結型別，而不是 `orchestrationTrigger` 繫結型別。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> 協調流程函式所呼叫的任何函式必須使用 `activityTrigger` 繫結。

`E1_SayHello` 的實作是相當簡單的字串格式化作業。

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/索引.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

不同於 JavaScript 協調流程函式，活動函式不需要特殊安裝。 協調器函式傳遞給它的輸入位於 `context.bindings` 物件的 `activityTrigger` 繫結名稱之下 - 在此例中為 `context.bindings.name`。 繫結名稱可以設定為所匯出函式的參數並直接進行存取，這是範例程式碼所執行的作業。

---

### <a name="httpstart-client-function"></a>HttpStart 用戶端函數

可以使用用戶端函數啟動協調器函數的實例。 您將使用`HttpStart`HTTP 觸發函數啟動 的`E1_HelloSequence`實例。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

要與協調器交互，該函數必須包含`DurableClient`輸入綁定。 您可以使用用戶端啟動業務流程。 它還可以説明您返回包含用於檢查新業務流程狀態的 URL 的 HTTP 回應。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/函數.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

要與協調器交互，該函數必須包含`durableClient`輸入綁定。

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

用於`df.getClient`獲取`DurableOrchestrationClient`物件。 您可以使用用戶端啟動業務流程。 它還可以説明您返回包含用於檢查新業務流程狀態的 URL 的 HTTP 回應。

---

## <a name="run-the-sample"></a>執行範例

要執行業務流程`E1_HelloSequence`，請向`HttpStart`函數發送以下 HTTP POST 請求。

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> 先前的 HTTP 程式碼片段假設 `host.json` 檔案中有一個項目會從所有的 HTTP 觸發程序函式 URL 中移除預設 `api/` 前置詞。 您可以在範例的 `host.json` 檔案中找到此組態的標記。

例如，如果您在名為 "myfunctionapp" 的函式應用程式中執行範例，請將 "{host}" 取代為 "myfunctionapp.azurewebsites.net"。

結果會是 HTTP 202 回應，就像這樣 (為了簡潔起見已修剪)：

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

現在，協調流程已排入佇列，並立即開始執行。 `Location` 標頭中的 URL 可以用來檢查執行的狀態。

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

結果會是協調流程的狀態。 執行和完成都很快速，您會看到 *Completed* 狀態，而且回應看起來像這樣 (為了簡潔起見已修剪)：

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

如您所見，執行個體的 `runtimeStatus` 是 *Completed*，而 `output` 包含協調器函式執行的 JSON 序列化結果。

> [!NOTE]
> 您可以對其他觸發程序類型實作類似的起始邏輯，例如 `queueTrigger`、`eventHubTrigger` 或 `timerTrigger`。

查看函式執行記錄。 因為[協調流程可靠性](durable-functions-orchestrations.md#reliability)主題中所述的重新執行行為，所以函式已啟動並完成多次。`E1_HelloSequence` 相反地，`E1_SayHello` 只執行三次，因為這幾次函式執行不會再來一次。

## <a name="next-steps"></a>後續步驟

此範例已示範簡單的函式鏈結協調流程。 下一個範例會說明如何實作展開傳送/收合傳送模式。

> [!div class="nextstepaction"]
> [執行展開傳送/收合傳送範例](durable-functions-cloud-backup.md)
