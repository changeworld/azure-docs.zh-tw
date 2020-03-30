---
title: Azure Functions 的計時器觸發程序
description: 了解如何在 Azure Functions 中使用計時器觸發程序。
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056416"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions 的計時器觸發程序 

本文說明如何在 Azure Functions 中使用計時器觸發程序。 計時器觸發程序可讓您依照排程執行函式。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 套件 2.x 版中提供計時器觸發程序。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝 - 功能 2.x 及以上

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 套件 3.x 版中提供計時器觸發程序。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例顯示每次分鐘具有五個可分割的值時執行的[C# 函數](functions-dotnet-class-library.md)（例如，如果函數從 18：57：00 開始，下一個性能將在 19：00：00 開始）。 物件[`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)傳遞到函數中。

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

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

下列範例示範 function.json** 檔案中的計時器觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。 物件[`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)傳遞到函數中。

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下列範例示範 function.json** 檔案中的計時器觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。 [計時器物件](#usage)傳遞到函數中。

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

下面的示例使用一個計時器觸發器綁定，其配置在*函數.json*檔中進行了描述。 使用綁定的實際[Python 函數](functions-reference-python.md)在*__init__.py*檔中描述。 傳遞到函數的物件是[azure.函數類型。](/python/api/azure-functions/azure.functions.timerrequest) 函數邏輯寫入日誌，指示當前調用是否由於未完成計畫發生。 

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

# <a name="java"></a>[JAVA](#tab/java)

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

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)。

該屬性的建構函式會採用 CRON 運算式或是 `TimeSpan`。 僅當功能應用`TimeSpan`在應用服務方案上運行時，才能使用。 `TimeSpan`不支援用於消費或彈性高級功能。

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

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

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

下表介紹了您在*函數.json*檔和`TimerTrigger`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設定為 "timerTrigger"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "in"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**名稱** | n/a | 代表函式程式碼中計時器物件的變數名稱。 | 
|**附表**|**ScheduleExpression**|[CRON 運算式](#ncrontab-expressions)或 [TimeSpan](#timespan) 值。 `TimeSpan` 只能用於 App Service 方案上執行的函式應用程式。 您可以將計畫運算式放在應用設置中，並將此屬性設置為用**%** 符號包裝的應用設置名稱，如本示例："%計畫AppSet%"。 |
|**runOnStartup**|**運行啟動**|如果為 `true`，當執行階段啟動時，會叫用函式。 例如，當函式應用程式因無活動而處於閒置狀態後再甦醒時、 當函數應用由於功能更改而重新開機時，以及函數應用縮小時。因此 **，如果**設置為 （）很少運行啟動`true`，尤其是在生產中。 |
|**使用監視器**|**UseMonitor**|設定為 `true` 或 `false` 以表示是否應該監視排程。 排程監視會使排程持續進行，以協助確保即使在函式應用程式執行個體重新啟動時，排程也能正確地持續運作。 如果未顯式設置，則預設值為`true`定期間隔大於或等於 1 分鐘的計畫。 若為每分鐘觸發超過一次的排程，預設值為 `false`。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> 建議不要在生產環境中將 **runOnStartup** 設定為 `true`。 使用此設定會在極度無法預期的情況下執行程式碼。 在特定的生產環境設定中，對於取用量方案中託管的應用程式，這些額外的執行會導致成本大幅增加。 例如，啟用**RunOnStartup**後，每當縮放函數應用時都會調用觸發器。 請確定在生產環境中啟用 **runOnStartup** 之前，您完全了解函式的實際執行行為。   

## <a name="usage"></a>使用量

調用計時器觸發器函數時，計時器物件將傳遞到函數中。 下列 JSON 是計時器物件的範例表示法。

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

Azure 函數使用[NCronTab](https://github.com/atifaziz/NCrontab)庫來解釋 NCRONTAB 運算式。 NCRONTAB 運算式與 CRON 運算式類似，只不過它包含一個附加的第六個欄位，用於以秒為單位的時間精度：

`{second} {minute} {hour} {day} {month} {day-of-week}`

每個欄位可以具備下列類型的值：

|類型  |範例  |觸發時間  |
|---------|---------|---------|
|特定值 |<nobr>"0 5 * * * *"</nobr>|於 hh:05:00，其中 hh 是每小時 (一小時一次)|
|所有值 (`*`)|<nobr>"0 * 5 * * *"</nobr>|於每天 5:mm:00，其中 mm 是小時中的每一分鐘 (一天 60 次)|
|範圍 (`-` 運算子)|<nobr>"5-7 * * * * *"</nobr>|於 hh:mm:05、hh:mm:06 和 hh:mm:07，其中 hh: mm 是每小時的每一分鐘 (一分鐘 3 次)|
|一組值 (`,` 運算子)|<nobr>"5,8,10 * * * * *"</nobr>|於 hh:mm:05、hh:mm:08 和 hh:mm:10，其中 hh: mm 是每小時的每一分鐘 (一分鐘 3 次)|
|間隔值 (`/` 運算子)|<nobr>"0 */5 * * * *"</nobr>|hh：00：00、hh：05：00、hh：10：00 等，通過 hh：55：00，每小時 hh（每小時 12 次）|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB 示例

下面是一些可用於 Azure 函數中計時器觸發器的 NCRONTAB 運算式示例。

|範例|觸發時間  |
|---------|---------|
|`"0 */5 * * * *"`|每隔 5 分鐘一次|
|`"0 0 * * * *"`|每小時開始時一次|
|`"0 0 */2 * * *"`|每隔 2 小時一次|
|`"0 0 9-17 * * *"`|上午 9 點到下午 5 點之間每隔一小時一次|
|`"0 30 9 * * *"`|每天上午 9:30|
|`"0 30 9 * * 1-5"`|每個工作日上午 9:30|
|`"0 30 9 * Jan Mon"`|一月每個星期一上午 9:30|


### <a name="ncrontab-time-zones"></a>NCRONTAB 時區

CRON 運算式中的數字代表時間和日期，而非時間範圍。 例如，`hour` 欄位中的 5 代表上午 5:00，而非每隔 5 小時。

CRON 運算式使用的預設時區是國際標準時間 (UTC)。 若要讓 CRON 運算式以另一個時區為基礎，請為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立應用程式設定。 將值設定為所需的時區名稱，如 [Microsoft 時區索引](https://technet.microsoft.com/library/cc749073)中所示。

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`Linux 消費計畫當前不受支援。

例如，*美加東部標準時間*是 UTC-05:00。 要讓計時器在美國東部時間每天上午 10：00 觸發觸發，請使用以下 NCRONTAB 運算式，該運算式考慮 UTC 時區：

```
"0 0 15 * * *"
``` 

或者為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立應用程式設定，並將值設為**美加東部標準時間**。  然後使用以下 NCRONTAB 運算式： 

```
"0 0 10 * * *"
``` 

當您使用 `WEBSITE_TIME_ZONE` 時，時間會隨特定時區的時間變更 (例如日光節約時間) 而調整。 

## <a name="timespan"></a>TimeSpan

 `TimeSpan` 只能用於 App Service 方案上執行的函式應用程式。

不同於 CRON 運算式，`TimeSpan` 值會指定每個函式引動過程之間的時間間隔。 如果函式在執行時間超過指定時間間隔之後完成，計時器會立即再次叫用函式。

以字串表示，當 `hh` 低於 24 時，`TimeSpan` 格式為 `hh:mm:ss`。 當前兩個數字為 24 或更高時，格式為 `dd:hh:mm`。 以下是一些範例：

|範例 |觸發時間  |
|---------|---------|
|"01:00:00" | 每小時        |
|"00:01:00"|每分鐘         |
|"24:00:00" | 每 24 天        |
|"1.00:00:00" | 每天        |

## <a name="scale-out"></a>向外延展

如果函式應用程式相應放大至多個執行個體，則只有計時器觸發函式的單一執行個體會在所有執行個體中執行。

## <a name="function-apps-sharing-storage"></a>共用儲存體的函式應用程式

如果要跨未部署到應用服務的函數應用共用存儲帳戶，則可能需要為每個應用顯式分配主機識別碼。

| Functions 版本 | 設定                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x （及以上）  | `AzureFunctionsWebHost__hostid` 環境變數 |
| 1.x               | `id`在*主機.json*                                  |

您可以省略標識值或手動將每個函數應用的標識配置設置為不同的值。

計時器觸發器使用存儲鎖，以確保當函數應用擴展到多個實例時，只有一個計時器實例。 如果兩個函數應用共用相同的標識配置，並且每個應用都使用計時器觸發器，則僅運行一個計時器。

## <a name="retry-behavior"></a>重試行為

不同於佇列觸發程序，計時器觸發程序在函式失敗後並不會重試。 當函式失敗時，必須等到下次排程的時間才會再次呼叫函式。

## <a name="troubleshooting"></a>疑難排解

如需有關當計時器觸發程序未如預期般運作時該怎麼做的資訊，請參閱[調查及報告計時器觸發的函式並未引發的問題](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至使用計時器觸發程序的快速入門](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
