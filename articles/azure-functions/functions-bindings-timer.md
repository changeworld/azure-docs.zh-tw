---
title: Azure Functions 的計時器觸發程序
description: 了解如何在 Azure Functions 中使用計時器觸發程序。
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: devx-track-python
ms.openlocfilehash: 49bbc2199c7dc3040a24071b8eb5a91929d88e08
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849237"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions 的計時器觸發程序 

本文說明如何在 Azure Functions 中使用計時器觸發程序。 計時器觸發程序可讓您依照排程執行函式。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

如需如何手動執行計時器觸發函式的相關資訊，請參閱[手動執行非 HTTP 觸發的函式](./functions-manually-run-non-http.md)。

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 套件 2.x 版中提供計時器觸發程序。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 套件 3.x 版中提供計時器觸發程序。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示的[c #](functions-dotnet-class-library.md)函式會在每一分鐘執行一個可被五個 (整除的值，例如，當函式從18:57:00 開始時，下一個效能將會是 19:00:00) 。 [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)物件會傳遞至函式。

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例示範 function.json** 檔案中的計時器觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。 [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)物件會傳遞至函式。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

以下是 C# 指令碼程式碼：

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json** 檔案中的計時器觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。 [計時器物件](#usage)會傳遞至函式。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

以下是 JavaScript 程式碼：

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例會使用計時器觸發程式系結，其設定會在檔案的*function.js*中描述。 使用系結的實際[Python](functions-reference-python.md)函式會在* __ __.py*檔案中描述。 傳入函式的物件為[TimerRequest 物件](/python/api/azure-functions/azure.functions.timerrequest)類型。 函式邏輯會寫入記錄，指出目前的叫用是否因錯過的排程發生而造成。 

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

以下是 Python 程式碼：

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="java"></a>[Java](#tab/java)

下列範例函式會每五分鐘觸發並執行。 函式上的 `@TimerTrigger` 註釋會使用與 [CRON 運算式](https://en.wikipedia.org/wiki/Cron#CRON_expression)相同的字串格式來定義排程。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)。

該屬性的建構函式會採用 CRON 運算式或是 `TimeSpan`。 只有在函 `TimeSpan` 式應用程式在 App Service 計畫上執行時，您才能使用。 `TimeSpan`不支援耗用量或彈性 Premium 函數。

下列範例顯示 CRON 運算式：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

函式上的 `@TimerTrigger` 註釋會使用與 [CRON 運算式](https://en.wikipedia.org/wiki/Cron#CRON_expression)相同的字串格式來定義排程。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `TimerTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "timerTrigger"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "in"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中計時器物件的變數名稱。 | 
|**任務**|**ScheduleExpression**|[CRON 運算式](#ncrontab-expressions)或 [TimeSpan](#timespan) 值。 `TimeSpan` 只能用於 App Service 方案上執行的函式應用程式。 您可以將排程運算式放在應用程式設定中，並將此屬性設定為以符號包裝的應用程式設定名稱 **%** ，如下列範例所示： "% ScheduleAppSetting%"。 |
|**runOnStartup**|**RunOnStartup**|如果為 `true`，當執行階段啟動時，會叫用函式。 例如，當函式應用程式因無活動而處於閒置狀態後再甦醒時、 當函式應用程式因函式變更而重新開機，以及函式應用程式相應放大時。因此，如果您要將**runOnStartup**設定為 `true` ，特別是在生產環境中。 |
|**useMonitor**|**UseMonitor**|設定為 `true` 或 `false` 以表示是否應該監視排程。 排程監視會使排程持續進行，以協助確保即使在函式應用程式執行個體重新啟動時，排程也能正確地持續運作。 如果未明確設定，則預設值為表示 `true` 週期間隔大於或等於1分鐘的排程。 若為每分鐘觸發超過一次的排程，預設值為 `false`。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> 建議不要在生產環境中將 **runOnStartup** 設定為 `true`。 使用此設定會在極度無法預期的情況下執行程式碼。 在特定的生產環境設定中，對於取用量方案中託管的應用程式，這些額外的執行會導致成本大幅增加。 例如，啟用**runOnStartup**時，每當調整您的函數應用程式時，就會叫用觸發程式。 請確定在生產環境中啟用 **runOnStartup** 之前，您完全了解函式的實際執行行為。   

## <a name="usage"></a>使用量

叫用計時器觸發程式函式時，計時器物件會傳遞至函式。 下列 JSON 是計時器物件的範例表示法。

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

當目前函式引動過程晚於排程時，`IsPastDue` 屬性為 `true`。 例如，函式應用程式重新啟動可能會導致遺漏引動過程。

## <a name="ncrontab-expressions"></a>NCRONTAB 運算式 

Azure Functions 使用[NCronTab](https://github.com/atifaziz/NCrontab)程式庫來解讀 NCronTab 運算式。 NCRONTAB 運算式與 CRON 運算式相似，不同之處在于它會在開頭包含額外的第六個欄位，以用於時間精確度（以秒為單位）：

`{second} {minute} {hour} {day} {month} {day-of-week}`

每個欄位可以具備下列類型的值：

|類型  |範例  |觸發時間  |
|---------|---------|---------|
|特定值 |<nobr>"0 5 * * * *"</nobr>|於 hh:05:00，其中 hh 是每小時 (一小時一次)|
|所有值 (`*`)|<nobr>"0 * 5 * * *"</nobr>|於每天 5:mm:00，其中 mm 是小時中的每一分鐘 (一天 60 次)|
|範圍 (`-` 運算子)|<nobr>"5-7 * * * * *"</nobr>|於 hh:mm:05、hh:mm:06 和 hh:mm:07，其中 hh: mm 是每小時的每一分鐘 (一分鐘 3 次)|
|一組值 (`,` 運算子)|<nobr>"5,8,10 * * * * *"</nobr>|於 hh:mm:05、hh:mm:08 和 hh:mm:10，其中 hh: mm 是每小時的每一分鐘 (一分鐘 3 次)|
|間隔值 (`/` 運算子)|<nobr>"0 */5 * * * *"</nobr>|在 hh：00：00、hh：05：00、hh：10：00，依此類推到 hh：55：00，其中 hh 是每小時 (12 次) |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB 範例

以下是您可以在 Azure Functions 中用於計時器觸發程式的一些 NCRONTAB 運算式範例。

|範例|觸發時間  |
|---------|---------|
|`"0 */5 * * * *"`|每隔 5 分鐘一次|
|`"0 0 * * * *"`|每小時開始時一次|
|`"0 0 */2 * * *"`|每隔 2 小時一次|
|`"0 0 9-17 * * *"`|上午 9 點到下午 5 點之間每隔一小時一次|
|`"0 30 9 * * *"`|每天上午 9:30|
|`"0 30 9 * * 1-5"`|每個工作日上午 9:30|
|`"0 30 9 * Jan Mon"`|一月每個星期一上午 9:30|


### <a name="ncrontab-time-zones"></a>NCRONTAB 的時區

CRON 運算式中的數字代表時間和日期，而非時間範圍。 例如，`hour` 欄位中的 5 代表上午 5:00，而非每隔 5 小時。

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 `TimeSpan` 只能用於 App Service 方案上執行的函式應用程式。

不同於 CRON 運算式，`TimeSpan` 值會指定每個函式引動過程之間的時間間隔。 如果函式在執行時間超過指定時間間隔之後完成，計時器會立即再次叫用函式。

以字串表示，當 `hh` 低於 24 時，`TimeSpan` 格式為 `hh:mm:ss`。 當前兩個數字為 24 或更高時，格式為 `dd:hh:mm`。 這裡有一些範例：

|範例 |觸發時間  |
|---------|---------|
|"01:00:00" | 每小時        |
|"00:01:00"|每分鐘         |
|"24:00:00" | 每 24 天        |
|"1.00：00： 00" | 每天        |

## <a name="scale-out"></a>相應放大

如果函式應用程式相應放大至多個執行個體，則只有計時器觸發函式的單一執行個體會在所有執行個體中執行。

## <a name="function-apps-sharing-storage"></a>共用儲存體的函式應用程式

如果您在未部署至 app service 的函式應用程式之間共用儲存體帳戶，您可能需要明確地將主機識別碼指派給每個應用程式。

| Functions 版本 | 設定                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (和更高的)   | `AzureFunctionsWebHost__hostid` 環境變數 |
| 1.x               | `id`在*host.js開啟*                                  |

您可以省略識別值，或手動將每個函數應用程式的識別設定設為不同的值。

計時器觸發程式會使用儲存體鎖定，確保當函數應用程式相應放大至多個實例時，只有一個計時器實例。 如果兩個函式應用程式共用相同的識別設定，而且每個都使用計時器觸發程式，則只會執行一個計時器。

## <a name="retry-behavior"></a>重試行為

不同於佇列觸發程序，計時器觸發程序在函式失敗後並不會重試。 當函式失敗時，必須等到下次排程的時間才會再次呼叫函式。

## <a name="troubleshooting"></a>疑難排解

如需有關當計時器觸發程序未如預期般運作時該怎麼做的資訊，請參閱[調查及報告計時器觸發的函式並未引發的問題](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至使用計時器觸發程序的快速入門](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
