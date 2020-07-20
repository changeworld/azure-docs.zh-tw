---
title: Durable Functions 中的展開傳送/收合傳送情節 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中實作展開傳送/收合傳送情節。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77562185"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Durable Functions 中的展開傳送/收合傳送情節 - 雲端備份範例

「展開傳送/收合傳送」** 是指同時執行多個函式，然後對結果執行一些彙總的模式。 本文以一個範例說明如何使用 [Durable Functions](durable-functions-overview.md) 來實作展開傳送/收合傳送情節。 範例是一個永久性函式，可將應用程式的所有或部分網站內容備份至 Azure 儲存體。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>案例概觀

在此範例中，函式會將指定目錄中的所有檔案，以遞迴方式上傳至 blob 儲存體。 還會計算已上傳的位元組總數。

您可以只撰寫一個函式來處理這一切。 您會遇到的主要問題是**延展性**。 單一函式執行只能在單一虛擬機器上執行，因此輸送量會受限於該單一 VM 的輸送量。 另一個問題是**可靠性**。 如果中途失敗，或是整個程式花費了5分鐘以上，則備份可能會在部分完成狀態下失敗。 於是就必須重新啟動。

更強固的方法是撰寫兩個一般函式：其中一個會列舉檔案，並將檔案名稱加入佇列中，另一個會讀取佇列，並將檔案上傳至 blob 儲存體。 這種方法在輸送量和可靠性方面較佳，但它需要您布建和管理佇列。 更重要的是，如果您想要再多一些功能，例如報告已上傳的位元組總數，則在**狀態管理**和**協調**方面會變得相當複雜。

Durable Functions 方法提供上述所有優點，而且額外負荷極低。

## <a name="the-functions"></a>函式

本文說明範例應用程式中的函式如下：

* `E2_BackupSiteContent`：呼叫[orchestrator function](durable-functions-bindings.md#orchestration-trigger) `E2_GetFileList` 以取得要備份之檔案清單的協調器函式，然後呼叫 `E2_CopyFileToBlob` 以備份每個檔案。
* `E2_GetFileList`：傳回目錄中檔案清單的[活動](durable-functions-bindings.md#activity-trigger)函式。
* `E2_CopyFileToBlob`：用來備份單一檔案以 Azure Blob 儲存體的活動函數。

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent 協調器函式

此協調器函式基本上會執行下列動作：

1. 接受 `rootDirectory` 值作為輸入參數。
2. 呼叫函式以取得 `rootDirectory` 下的檔案遞迴清單。
3. 執行多次平行函式呼叫，將每個檔案上傳到 Azure Blob 儲存體。
4. 等候所有上傳完成。
5. 傳回已上傳到 Azure Blob 儲存體的位元組總數。

# <a name="c"></a>[C#](#tab/csharp)

以下是實作協調器函式的程式碼：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

請注意 `await Task.WhenAll(tasks);` 這一行。 未等候函式的所有個別呼叫 `E2_CopyFileToBlob` ，這可讓它們以平行方式執行。 *not* 將這一批工作傳給 `Task.WhenAll` 時，將會傳回一個「直到所有複製作業都完成」** 才會完成的工作。 如果您熟悉 .NET 中的工作平行程式庫 (TPL)，則對此不會感到陌生。 差別在於，這些工作可以在多個虛擬機器上同時執行，而 Durable Functions 擴充功能可確保端對端執行在進程回收方面具有彈性。

結束等候 `Task.WhenAll` 之後，就可知道所有函式呼叫已完成，而值也已傳回給我們。 每次呼叫 `E2_CopyFileToBlob` 都會傳回已上傳的位元組數，因此，只要合計所有這些傳回值，就能算出位元組總數。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

函式會針對協調器函式使用標準*function.json* 。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

以下是實作協調器函式的程式碼：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

請注意 `yield context.df.Task.all(tasks);` 這一行。 未產生對函式的所有個別呼叫 `E2_CopyFileToBlob` ，這可讓它們以平行方式執行。 *not* 將這一批工作傳給 `context.df.Task.all` 時，將會傳回一個「直到所有複製作業都完成」** 才會完成的工作。 如果您熟悉 JavaScript 的 [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) ，這不是您的新功能。 差別在於，這些工作可以在多個虛擬機器上同時執行，而 Durable Functions 擴充功能可確保端對端執行在進程回收方面具有彈性。

> [!NOTE]
> 儘管工作在概念上類似於 JavaScript Promise，協調器函式還是應該使用 `context.df.Task.all` 和 `context.df.Task.any`，而不是使用 `Promise.all` 和 `Promise.race` 來管理工作平行處理。

從開始 `context.df.Task.all` ，我們知道所有函式呼叫已完成，並將值傳回給我們。 每次呼叫 `E2_CopyFileToBlob` 都會傳回已上傳的位元組數，因此，只要合計所有這些傳回值，就能算出位元組總數。

---

### <a name="helper-activity-functions"></a>協助程式活動函式

如同其他範例一樣，協助程式活動函式只不過是使用 `activityTrigger` 觸發程序繫結的一般函式。

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList 活動函數

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

檔案*上的function.js* `E2_GetFileList` 看起來如下所示：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

實作如下：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

函式會使用 `readdirp` 模組（2.x 版）以遞迴方式讀取目錄結構。

---

> [!NOTE]
> 您可能覺得奇怪，為何不能直接將此程式碼放入協調器函式中。 您可以這樣做，但這會違反協調器函式的基本規則之一，也就是永遠都不該執行 I/O，包括本機檔案系統存取。 如需詳細資訊，請參閱協調器函式程式[代碼條件約束](durable-functions-code-constraints.md)。

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob 活動函數

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> 您將需要安裝 `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet 封裝，才能執行範例程式碼。

函式會使用 Azure Functions 系結的一些先進功能（也就是使用[ `Binder` 參數](../functions-dotnet-class-library.md#binding-at-runtime)），但是您不需要擔心此逐步解說的細節。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_CopyFileToBlob`的 *function.json* 檔案也一樣簡單：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

JavaScript 執行會使用[適用于節點的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-node) ，將檔案上傳至 Azure Blob 儲存體。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

實作會從磁碟載入檔案，並以非同步方式將內容串流至 "backups" 容器中相同名稱的 blob。 傳回值是複製到儲存體的位元組數，協調器函式接著會利用此值來計算的總數。

> [!NOTE]
> 這是將 I/O 作業移入 `activityTrigger` 函式中的最佳範例。 工作不僅可以分散到多部不同的電腦，還能獲得檢查點進度的優點。 如果主機處理序由於任何原因而終止，您會知道哪些上傳已完成。

## <a name="run-the-sample"></a>執行範例

您可以藉由傳送下列 HTTP POST 要求來啟動協調流程。

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> 您叫用的 `HttpStart`函式僅適用於 JSON 格式的內容。 因此，需要 `Content-Type: application/json` 標頭，而且目錄路徑會編碼為 JSON 字串。 此外，HTTP 程式碼片段假設 `host.json` 檔案中有一個項目會從所有的 HTTP 觸發程序函式 URL 中移除預設 `api/` 前置詞。 您可以在範例的 `host.json` 檔案中找到此組態的標記。

此 HTTP 要求將會觸發 `E2_BackupSiteContent` 協調器並傳遞字串 `D:\home\LogFiles` 當作參數。 回應提供的連結可取得備份作業的狀態：

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

根據您在應用程式函式中有多少記錄檔而定，這項作業可能需要幾分鐘才能完成。 您可以在前一個 HTTP 202 回應的 `Location` 標頭中查詢 URL，以取得最新狀態。

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

在此情況下，函式仍在執行中。 您可以看到已儲存至協調器狀態的輸入，以及上次更新時間。 您可以繼續使用 `Location` 標頭值來輪詢是否已完成。 當狀態為 "Completed" 時，您會看到類似下列的 HTTP 回應值：

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

現在，您可以看到協調流程已完成，以及大約耗費多少時間完成。 您還會看到 `output` 欄位的值，這指出大約已上傳 450 KB 的記錄。

## <a name="next-steps"></a>後續步驟

此範例已說明如何實作展開傳送/收合傳送模式。 下一個範例示範如何使用[長期計時器](durable-functions-timers.md)來實作監視模式。

> [!div class="nextstepaction"]
> [執行監視範例](durable-functions-monitor.md)