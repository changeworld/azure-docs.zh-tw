---
title: 如何停用 Azure Functions 中的函式
description: 瞭解如何在 Azure 函數中禁用和啟用函數。
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 11585e92e7d239731b02d06c5093f979cd65cfba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686892"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何停用 Azure Functions 中的函式

本文說明如何停用 Azure Functions 中的函式。 「停用」** 函式表示讓執行階段忽略為此函式定義的自動觸發程序。 這樣,您就可以在不停止整個函數應用的情況下阻止特定函數運行。

禁用函數的建議方法是使用格式`AzureWebJobs.<FUNCTION_NAME>.Disabled`的應用設置。 您可以通過多種方式創建和修改此應用程式設置,包括使用[Azure CLI](/cli/azure/)和[Azure 門戶](https://portal.azure.com)中的函數**的「管理**」選項卡。 

> [!NOTE]  
> 使用本文中描述的方法禁用 HTTP 觸發函數時,在本地電腦上運行時,終結點仍可能通過訪問來訪問。  

## <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)中, 使用 指令建立和修改應用設定。 以下命令通過創建名為的應用`QueueTrigger``AzureWebJobs.QueueTrigger.Disabled`設置將其設置`true`為禁用名為的函數。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

要重新啟用函數,應重新運行值為的同`false`一命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>使用門戶

您還可以在函數的 **「管理」** 選項卡上使用**函數狀態**開關。該開關的工作原理是創建和刪除`AzureWebJobs.<FUNCTION_NAME>.Disabled`應用設置。

![函式狀態切換](media/disable-function/function-state-switch.png)

> [!NOTE]  
> 門戶集成的測試功能忽略該`Disabled`設置。 這意味著,從門戶中的 **「測試」** 視窗啟動時,禁用的函數仍運行。 

## <a name="other-methods"></a>其他方法

雖然建議對所有語言和所有運行時版本使用應用程式設置方法,但還有幾種其他方法來禁用函數。 這些方法因語言和運行時版本而異,為向後相容性而維護這些方法。 

### <a name="c-class-libraries"></a>C# 類別庫

在類庫函數中,還可以使用`Disable`屬性來防止觸發該函數。 您可以使用此屬性 (沒有建構函式參數)，如下列範例所示：

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

在版本 1.x 中,您`disabled`還可以使用*函數.json*檔的屬性告訴運行時不要觸發函數。 此方法僅適用於腳本語言,如 C# 文稿和 JAVAScript。 屬性`disabled`可以設定`true`為 應用程式設定或應用程式設定的名稱:

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

您可以在 Azure 門戶中編輯檔,或使用函數的 **「管理」** 選項卡上的 **「功能狀態**」開關。門戶交換機的工作原理是更改*函數.json*檔。

![函式狀態切換](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>後續步驟

本文關於如何停用自動觸發程序。 如需觸發程序的詳細資訊，請參閱[觸發程序與繫結](functions-triggers-bindings.md)。
