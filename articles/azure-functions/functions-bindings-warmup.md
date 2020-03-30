---
title: Azure 函數預熱觸發器
description: 瞭解如何在 Azure 函數中使用預熱觸發器。
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure 函數、功能、事件處理、預熱、冷啟動、高級、動態計算、無伺服器體系結構
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167318"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure 函數預熱觸發器

本文介紹如何使用 Azure 函數中的預熱觸發器。 預熱觸發器僅支援在[高級計畫中](functions-premium-plan.md)運行的函數應用。 當添加實例以縮放正在運行的函數應用時，將調用預熱觸發器。 您可以使用預熱觸發器在[預熱過程](./functions-premium-plan.md#pre-warmed-instances)期間預載入自訂依賴項，以便函數可以立即開始處理請求。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝 - 功能 2.x 及以上

[需要 Microsoft.Azure.WebJobs.擴展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions)NuGet 包，版本**3.0.5 或更高**版本。 套件的原始程式碼位於 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 存放庫中。 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>觸發程序

預熱觸發器允許您定義一個函數，該函數將在新實例添加到正在運行的應用時運行。 在應用開始接收流量之前，可以使用預熱函數打開連接、載入依賴項或運行任何其他自訂邏輯。 

預熱觸發器旨在創建共用依賴項，這些依賴項將由應用中的其他函數使用。 [在此處查看共用依賴項的示例](./manage-connections.md#client-code-examples)。

請注意，預熱觸發器僅在橫向擴展操作期間調用，而不是在重新開機或其他非規模啟動期間調用。 您必須確保邏輯可以載入所有必要的依賴項，而無需使用預熱觸發器。 延遲載入是實現這一點的良好模式。

## <a name="trigger---example"></a>觸發程序 - 範例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例顯示了一個[C# 函數](functions-dotnet-class-library.md)，該函數在添加到應用時將在每個新實例上運行。 不需要傳回值屬性。


* 您的函數必須命名```warmup```（不區分大小寫），並且每個應用可能只有一個預熱功能。
* 要將預熱用作 .NET 類庫函數，請確保對**Microsoft.Azure.WebJobs.擴展>= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


預留位置注釋顯示應用程式中聲明和初始化共用依賴項的位置。 
[在此處瞭解有關共用依賴項的更多。](./manage-connections.md#client-code-examples)

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
# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)


下面的示例顯示*函數.json*檔中的預熱觸發器和一個[C# 腳本函數](functions-reference-csharp.md)，該函數在添加到應用時將在每個新實例上運行。

您的函數必須命名```warmup```（不區分大小寫），並且每個應用可能只有一個預熱功能。

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下面的示例顯示*函數.json*檔中的預熱觸發器和[JavaScript 函數](functions-reference-node.md)，該函數在添加到應用時將在每個新實例上運行。

您的函數必須命名```warmup```（不區分大小寫），並且每個應用可能只有一個預熱功能。

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

# <a name="python"></a>[Python](#tab/python)

下面的示例顯示*函數.json*檔中的預熱觸發器和[Python 函數](functions-reference-python.md)，該函數在添加到應用時將在每個新實例上運行。

您的函數必須命名```warmup```（不區分大小寫），並且每個應用可能只有一個預熱功能。

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

# <a name="java"></a>[JAVA](#tab/java)

下面的示例顯示一個預熱觸發器，該觸發器在將每個新實例添加到應用時運行。

您的函數必須命名`warmup`（不區分大小寫），並且每個應用可能只有一個預熱功能。

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>觸發程序 - 屬性

在[C# 類庫中](functions-dotnet-class-library.md)，`WarmupTrigger`屬性可用於配置函數。

# <a name="c"></a>[C#](#tab/csharp)

此示例演示如何使用[預熱](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)屬性。

請注意，必須調用```Warmup```函數，並且每個應用只能有一個預熱函數。

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

有關完整示例，請參閱[觸發器示例](#trigger---example)。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

JAVA 中不支援作為屬性的預熱觸發器。

---

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表介紹了您在*函數.json*檔和`WarmupTrigger`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
| **型別** | n/a| 必要項目 - 必須設定為 `warmupTrigger`。 |
| **direction** | n/a| 必要項目 - 必須設定為 `in`。 |
| **名稱** | n/a| 必需 - 函數代碼中使用的變數名稱。|

## <a name="trigger---usage"></a>觸發程序 - 使用方式

調用預熱觸發函數時，不會向該函數提供其他資訊。

## <a name="trigger---limits"></a>觸發程序的 - 限制

* 預熱觸發器僅適用于在[高級計畫](./functions-premium-plan.md)上運行的應用。
* 預熱觸發器僅在擴展操作期間調用，而不是在重新開機或其他非規模啟動期間調用。 您必須確保邏輯可以載入所有必要的依賴項，而無需使用預熱觸發器。 延遲載入是實現這一點的良好模式。
* 實例已運行後，無法調用預熱觸發器。
* 每個函數應用只能有一個預熱觸發器函數。

## <a name="next-steps"></a>後續步驟

[深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
