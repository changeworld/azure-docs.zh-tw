---
title: 如何停用 Azure Functions 中的函式
description: 了解如何停用和啟用 Azure Functions 中的函式。
ms.topic: conceptual
ms.date: 04/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 47fbd446937ea0cfd981cef2d5cdd4759f2583d4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497693"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何停用 Azure Functions 中的函式

本文說明如何停用 Azure Functions 中的函式。 「停用」函式表示讓執行階段忽略為此函式定義的自動觸發程序。 這可讓您防止特定函式執行，而不需停止整個函式應用程式。

停用函式的建議方式是使用 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 格式的應用程式設定。 您可透過數種方式來建立和修改此應用程式設定，包括使用 [Azure CLI](/cli/azure/)，以及從 [Azure 入口網站](https://portal.azure.com)中函式的 [管理] 索引標籤進行。 

> [!NOTE]  
> 當您使用本文所述的方法來停用 HTTP 觸發的函式時，仍可存取正在本機電腦上執行的端點。  

## <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中，您可使用 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令來建立和修改應用程式設定。 下列命令會建立名為 `AzureWebJobs.QueueTrigger.Disabled` 且設定為 `true`的應用程式設定，以停用名為 `QueueTrigger` 的函式。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

若要重新啟用函式，請以 `false`值重新執行相同的命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>使用入口網站

您也可使用函式 [概觀] 頁面上的 [啟用] 和 [停用] 按鈕。 這些按鈕可藉由建立和刪除 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 應用程式設定來產生作用。

![函式狀態切換](media/disable-function/function-state-switch.png)

> [!NOTE]  
> 入口網站整合的測試功能會忽略 `Disabled` 設定。 這表示在入口網站中從 [測試] 視窗啟動時，已停用的函式仍會執行。 

## <a name="other-methods"></a>其他方法

雖然建議所有語言和所有執行階段版本都使用應用程式設定方法，但還有其他幾種方法可停用函式。 系統會針對回溯相容性來維護這些因語言和執行階段版本而異的方法。 

### <a name="c-class-libraries"></a>C# 類別庫

在類別庫函式中，您也可使用 `Disable` 屬性來防止觸發函式。 您可以使用此屬性 (沒有建構函式參數)，如下列範例所示：

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

沒有建構函式參數的屬性會要求您重新編譯並重新部署專案，以變更函式的已停用狀態。 若要更彈性地使用此屬性，請包含參照布林值應用程式設定的建構函式參數，如下列範例所示：

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

這個方法可讓您藉由變更應用程式設定來啟用及停用此函式，而不需重新編譯或重新部署。 變更應用程式設定會導致函式應用程式重新啟動，因此會立即辨識已停用的狀態變更。

> [!IMPORTANT]
> `Disabled` 屬性是停用類別庫函式的唯一方式。 您不應該直接編輯為類別庫函式所產生的 *function.json* 檔案。 如果您編輯該檔案，無論您對 `disabled` 屬性做什麼都不會有任何作用。
>
> [管理] 索引標籤上的 [函式狀態] 切換也是如此，因為它可藉由變更 *function.json* 檔案來產生作用。
>
> 另請注意，入口網站可能會在此函式並未停用時，指出它已停用。

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - 指令碼語言

在 1.x 版中，您也可使用 *function.json* 檔案的 `disabled` 屬性來告知執行階段不要觸發函式。 此方法僅適用於 C# 指令碼和 JavaScript 等指令碼語言。 `disabled` 屬性可以設定為 `true` 或應用程式設定的名稱：

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
或 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

在第二個範例中，如有名為 IS_DISABLED 且設為 `true` 或 1 的應用程式設定，此函式就會停用。

>[!IMPORTANT]  
>入口網站現在會使用應用程式設定來停用 v1.x 函式。 當應用程式設定與 function.json 檔案衝突時，可能會發生錯誤。 您應該從 function.json 檔案中移除 `disabled` 屬性，以避免發生錯誤。 


## <a name="next-steps"></a>後續步驟

本文關於如何停用自動觸發程序。 如需觸發程序的詳細資訊，請參閱[觸發程序與繫結](functions-triggers-bindings.md)。
