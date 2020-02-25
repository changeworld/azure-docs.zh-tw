---
title: Durable Functions 中的函式鏈結 - Azure
description: 了解如何執行 Durable Functions 範例來執行一連串的函式。
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562051"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions 中的函式鏈結- Hello 序列範例

函式鏈結是指以特定順序執行一連串函式的模式。 通常，一個函式的輸出必須套用至另一個函式的輸入。 此文章說明當您完成 Durable Functions 快速入門 ([C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md)) 時所建立的鏈結序列。 如需 Durable Functions 的詳細資訊，請參閱 [Durable Functions 概觀](durable-functions-overview.md)。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>函式

本文說明範例應用程式中的函式如下：

* `E1_HelloSequence`：在序列中多次呼叫 `E1_SayHello` 的[協調](durable-functions-bindings.md#orchestration-trigger)器函式。 它會儲存 `E1_SayHello` 呼叫的輸出，並記錄結果。
* `E1_SayHello`：在字串前面加上 "Hello" 的[活動](durable-functions-bindings.md#activity-trigger)函式。
* `HttpStart`：啟動協調器實例的 HTTP 觸發函式。

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence 協調器函式

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

所有 C# 協調流程函式都必須有 `DurableOrchestrationContext` 類型的參數 (在 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 組件中)。 此內容物件可讓您呼叫其他*活動*函數，並使用其 `CallActivityAsync` 方法傳遞輸入參數。

程式碼中以不同的參數值連續呼叫 `E1_SayHello` 三次。 每次呼叫的傳回值會新增至函式最後傳回的 `outputs` 清單。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> JavaScript Durable Functions 僅適用于函數2.0 執行時間。

#### <a name="functionjson"></a>function.json

如果您使用 Visual Studio Code 或 Azure 入口網站進行開發，以下是適用於協調器函式的 *function.json* 檔案內容。 大部分協調器 *function.json* 檔案看起來幾乎完全像這樣。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

重要的是 `orchestrationTrigger` 繫結類型。 所有協調器函式必須都使用此觸發程序類型。

> [!WARNING]
> 為了遵守協調器函式的「沒有 I/O」規則，當您使用 `orchestrationTrigger` 觸發程序繫結時，請勿使用任何輸入或輸出繫結。  如果需要其他輸入或輸出繫結，請改為在協調器所呼叫之 `activityTrigger` 函式的內容中使用。 如需詳細資訊，請參閱協調器函式程式[代碼條件約束](durable-functions-code-constraints.md)一文。

#### <a name="indexjs"></a>index.js

函數如下：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

所有 JavaScript 協調流程函式都必須包含 [`durable-functions` 模組](https://www.npmjs.com/package/durable-functions)。 它是可讓您以 JavaScript 撰寫 Durable Functions 的程式庫。 協調流程函式與其他 JavaScript 函式之間有三項重大差異：

1. 函數是產生器函式[。](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. 此函式會包裝在對`durable-functions`模組的`orchestrator`方法進行的呼叫中 (在此是 `df`)。
3. 此函式必須是同步的。 「Orchestrator」方法會處理呼叫「context.done」，因為函式應該只是「return」。

`context` 物件包含 `df` 持久協調流程內容物件，可讓您呼叫其他*活動*函式，並使用其 `callActivity` 方法傳遞輸入參數。 程式碼會使用不同的參數值依序呼叫 `E1_SayHello` 三次，使用 `yield` 表示執行應等候要傳回的非同步活動函式呼叫。 每個呼叫的傳回值都會加入至 `outputs` 陣列，這會在函式的結尾傳回。

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello 活動函數

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

活動會使用 `ActivityTrigger` 屬性。 使用提供的 `IDurableActivityContext` 來執行活動的相關動作，例如使用 `GetInput<T>`存取輸入值。

`E1_SayHello` 的實作是相當簡單的字串格式化作業。

您可以直接系結至傳遞至活動函式的類型，而不是系結至 `IDurableActivityContext`。 例如：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

活動函式 *和*的 `E1_SayHello`function.json`E1_HelloSequence`檔案很類似，差別在於前者使用 `activityTrigger` 繫結型別，而不是 `orchestrationTrigger` 繫結型別。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> 協調流程函式所呼叫的任何函式必須使用 `activityTrigger` 繫結。

`E1_SayHello` 的實作是相當簡單的字串格式化作業。

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

不同於 JavaScript 協調流程函式，活動函式不需要特殊安裝。 協調器函式傳遞給它的輸入位於 `context.bindings` 物件的 `activityTrigger` 繫結名稱之下 - 在此例中為 `context.bindings.name`。 繫結名稱可以設定為所匯出函式的參數並直接進行存取，這是範例程式碼所執行的作業。

---

### <a name="httpstart-client-function"></a>HttpStart 用戶端函式

您可以使用用戶端函式來啟動協調器函數的實例。 您將使用 `HttpStart` HTTP 觸發的函式來啟動 `E1_HelloSequence`的實例。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

若要與協調器互動，此函式必須包含 `DurableClient` 的輸入系結。 您可以使用用戶端來啟動協調流程。 它也可以協助您傳回 HTTP 回應，其中包含用來檢查新協調流程狀態的 Url。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/函數. json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

若要與協調器互動，此函式必須包含 `durableClient` 的輸入系結。

#### <a name="httpstartindexjs"></a>HttpStart/index .js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

使用 `df.getClient` 來取得 `DurableOrchestrationClient` 物件。 您可以使用用戶端來啟動協調流程。 它也可以協助您傳回 HTTP 回應，其中包含用來檢查新協調流程狀態的 Url。

---

## <a name="run-the-sample"></a>執行範例

若要執行 `E1_HelloSequence` 協調流程，請將下列 HTTP POST 要求傳送至 `HttpStart` 函數。

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

查看函式執行記錄。 由於[協調流程可靠性](durable-functions-orchestrations.md#reliability)主題中所述的重新執行行為，`E1_HelloSequence` 函式已啟動並完成多次。 相反地，`E1_SayHello` 只執行三次，因為這幾次函式執行不會再來一次。

## <a name="next-steps"></a>後續步驟

此範例已示範簡單的函式鏈結協調流程。 下一個範例會說明如何實作展開傳送/收合傳送模式。

> [!div class="nextstepaction"]
> [執行展開傳送/收合傳送範例](durable-functions-cloud-backup.md)
