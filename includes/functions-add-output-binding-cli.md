---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673374"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>將輸出繫結定義新增至函式

一個函式只能有一個觸發程序，但可以有多個輸入和輸出繫結，如此，您無須撰寫自訂整合程式碼，即可連線至其他 Azure 服務和資源。 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
您會在函式資料夾中的 *function.json* 檔案中宣告這些繫結。 在先前的快速入門中，*HttpExample* 資料夾中的 *function.json* 檔案包含 `bindings` 集合中的兩個繫結：  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
每個繫結至少有一個類型、一個方向和一個名稱。 在上述範例中，第一個繫結的類型為 `httpTrigger`、方向為 `in`。 針對 `in` 方向，`name` 會指定觸發程序叫用函式時所傳入的輸入參數名稱。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
集合中的第二個繫結名為 `res`。 此 `http` 繫結是用來寫入 HTTP 回應的輸出繫結 (`out`)。 

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
集合中第二個繫結的類型為 `http`、方向為 `out`，在此情況下，`$return` 的特殊 `name` 表示此繫結會使用函式的傳回值，而不是提供輸入參數。

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
集合中的第二個繫結名為 `res`。 此 `http` 繫結是用來寫入 HTTP 回應的輸出繫結 (`out`)。 

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
在此案例下，會將 `msg` 提供給函式作為輸出引數。 針對 `queue` 類型，您也必須在 `queueName` 中指定佇列的名稱，並在 `connection` 中提供 Azure 儲存體連線 (來自 *local.settings.json*) 的*名稱*。 
::: zone-end  
