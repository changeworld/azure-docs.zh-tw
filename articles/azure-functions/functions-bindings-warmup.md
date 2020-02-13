---
title: Azure Functions 準備觸發程式
description: 瞭解如何在 Azure Functions 中使用準備觸發程式。
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure 函式，函數，事件處理，準備，冷啟動，premium，動態計算，無伺服器架構
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167318"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions 準備觸發程式

本文說明如何在 Azure Functions 中使用準備觸發程式。 只有在高階[方案](functions-premium-plan.md)中執行的函數應用程式才支援準備觸發程式。 新增實例以調整執行中函式應用程式時，會叫用準備觸發程式。 您可以使用準備觸發程式在[預先準備程式](./functions-premium-plan.md#pre-warmed-instances)期間預先載入自訂相依性，讓您的函式可以立即開始處理要求。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

需要**3.0.5 或更高**版本的[Microsoft Azure webjob。](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 存放庫中。 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>觸發程序

準備觸發程式可讓您定義函式，該函式會在新的實例新增至執行中的應用程式時執行。 您可以使用準備函式來開啟連線、載入相依性，或執行其他任何自訂邏輯，然後您的應用程式才會開始接收流量。 

準備觸發程式是用來建立應用程式中其他函式將使用的共用相依性。 [請參閱這裡的共用](./manage-connections.md#client-code-examples)相依性範例。

請注意，只有在向外延展作業期間才會呼叫準備觸發程式，而不是在重新開機或其他非規模的新創公司。 您必須確保邏輯可以載入所有必要的相依性，而不需使用準備觸發程式。 消極式載入是達成此目標的良好模式。

## <a name="trigger---example"></a>觸發程序 - 範例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下列範例顯示[ C#函式，該](functions-dotnet-class-library.md)函式會在新增至您的應用程式時，在每個新的實例上執行。 不需要傳回值屬性。


* 您的函式必須命名為 ```warmup``` （不區分大小寫），而且每個應用程式只能有一個準備函數。
* 若要使用準備做為 .NET 類別庫函式，請確定您有3.0.5 的套件參考 **>。**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


預留位置批註會顯示應用程式中用來宣告和初始化共用相依性的位置。 
[在這裡深入瞭解共用](./manage-connections.md#client-code-examples)相依性。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)


下列範例顯示在函式 json 檔案中的準備觸發程式，以及在每個新實例上新增至您的應用程式時，將會在其中執行的[ C#腳本](functions-reference-csharp.md)函式 *。*

您的函式必須命名為 ```warmup``` （不區分大小寫），而且每個應用程式只能有一個準備函數。

以下是 *function.json* 檔案：

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是繫結至 `HttpRequest` 的 C# 指令碼：

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例顯示函式 *. json*檔案中的準備觸發程式，以及在每個新實例上新增至您的應用程式時，將會在其中執行的[JavaScript 函數](functions-reference-node.md)。

您的函式必須命名為 ```warmup``` （不區分大小寫），而且每個應用程式只能有一個準備函數。

以下是 *function.json* 檔案：

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例顯示在函式 json 檔案中的準備觸發程式，以及在每個新實例上新增至您的應用程式時，將會在其中執行的[Python](functions-reference-python.md)函式 *。*

您的函式必須命名為 ```warmup``` （不區分大小寫），而且每個應用程式只能有一個準備函數。

以下是 *function.json* 檔案：

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列範例顯示在每個新的實例新增至您的應用程式時，所執行的準備觸發程式。

您的函式必須命名為 `warmup` （不區分大小寫），而且每個應用程式只能有一個準備函數。

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>觸發程序 - 屬性

在[ C#類別庫](functions-dotnet-class-library.md)中，可以使用 `WarmupTrigger` 屬性來設定函式。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

這個範例示範如何使用「準備[」屬性。](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

請注意，您的函式必須 ```Warmup``` 呼叫，而且每個應用程式只能有一個準備函數。

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

如需完整範例，請參閱[觸發程式範例](#trigger---example)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

JAVA 中不支援準備觸發程式做為屬性。

---

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `WarmupTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
| **type** | n/a| 必要項目 - 必須設定為 `warmupTrigger`。 |
| **direction** | n/a| 必要項目 - 必須設定為 `in`。 |
| **name** | n/a| 必要-函式程式碼中使用的變數名稱。|

## <a name="trigger---usage"></a>觸發程序 - 使用方式

叫用時，不會提供任何額外的資訊給準備觸發的函式。

## <a name="trigger---limits"></a>觸發程序的 - 限制

* 準備觸發程式僅適用于高階[方案](./functions-premium-plan.md)上執行的應用程式。
* 只有在相應增加作業期間才會呼叫準備觸發程式，而不是在重新開機或其他非規模的新創公司。 您必須確保邏輯可以載入所有必要的相依性，而不需使用準備觸發程式。 消極式載入是達成此目標的良好模式。
* 一旦實例已經在執行中，就無法叫用準備觸發程式。
* 每個函式應用程式只能有一個預熱觸發函式。

## <a name="next-steps"></a>後續步驟

[深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
