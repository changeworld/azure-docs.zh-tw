---
title: 如何停用 Azure Functions 中的函式
description: 瞭解如何在 Azure Functions 中停用和啟用函數。
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8922edb7aaa41bcf50dcce5257b6600f1bde224a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115555"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何停用 Azure Functions 中的函式

本文說明如何停用 Azure Functions 中的函式。 若要*停*用函式，則表示讓執行時間忽略針對函式定義的自動觸發程式。 這可讓您防止特定函式執行，而不需要停止整個函數應用程式。

停用函式的建議方式是使用格式的應用程式設定 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 。 您可以透過數種方式來建立和修改此應用程式設定，包括使用 [ [Azure CLI](/cli/azure/) ]，並從[Azure 入口網站](https://portal.azure.com)中的函式 [**管理**] 索引標籤。 

> [!NOTE]  
> 當您使用本文所述的方法來停用 HTTP 觸發的函式時，端點在本機電腦上執行時仍可能是可存取的。  

## <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中，您會使用 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令來建立和修改應用程式設定。 下列命令 `QueueTrigger` 會建立名為的應用程式設定， `AzureWebJobs.QueueTrigger.Disabled` 並將其設定為，以停用名為的函式 `true` 。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

若要重新啟用函式，請使用的值重新執行相同的命令 `false` 。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>使用入口網站

您也可以在函式的 **[總覽**] 頁面上，使用 [**啟用**] 和 [**停**用] 按鈕。 這些按鈕的作用是建立和刪除 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 應用程式設定。

![函式狀態切換](media/disable-function/function-state-switch.png)

> [!NOTE]  
> 入口網站整合的測試功能會忽略 `Disabled` 設定。 這表示在入口網站中從 [**測試**] 視窗啟動時，已停用的函式仍會執行。 

## <a name="other-methods"></a>其他方法

雖然建議所有語言和所有執行階段版本都使用應用程式設定方法，但有數個其他方法可以停用函式。 這些因語言和執行階段版本而異的方法會針對回溯相容性進行維護。 

### <a name="c-class-libraries"></a>C# 類別庫

在類別庫函式中，您也可以使用 `Disable` 屬性來防止觸發函數。 您可以使用此屬性 (沒有建構函式參數)，如下列範例所示：

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
> [管理]**** 索引標籤上的 [函式狀態]**** 切換也是如此，因為它可藉由變更 *function.json* 檔案來產生作用。
>
> 另請注意，入口網站可能會在此函式並未停用時，指出它已停用。

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - 指令碼語言

在1.x 版中，您也可以使用 `disabled` *函數. json*檔案的屬性，告訴執行時間不觸發函式。 這個方法僅適用于指令碼語言，例如 c # 腳本和 JavaScript。 `disabled`屬性可以設為， `true` 或設定為應用程式設定的名稱：

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

您可以在 Azure 入口網站中編輯檔案，或在函式的 **[總覽**] 頁面上使用 [**啟用**] 和 [**停**用] 按鈕。 此入口網站切換可藉由變更 *function.json* 檔案來產生作用。


## <a name="next-steps"></a>後續步驟

本文關於如何停用自動觸發程序。 如需觸發程序的詳細資訊，請參閱[觸發程序與繫結](functions-triggers-bindings.md)。
