---
title: 快速入門：您的第一個 REST API 查詢
description: 在本快速入門中，您將遵循步驟來呼叫 REST API 的 Resource Graph 端點，並執行您的第一個查詢。
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: 8776a107484691ffab72f2e1622ed5837375b7fb
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802540"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>快速入門：使用 REST API 執行您的第一個 Resource Graph 查詢

搭配 REST API 使用 Azure Resource Graph 的第一步是檢查您是否有可呼叫 REST API 的工具。 接著，本快速入門會逐步引導您呼叫 Azure Resource Graph REST API 端點來執行查詢並擷取結果。

在此程序結束時，您將會有用來呼叫 REST API 端點的工具，並可執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>開始使用 REST API

如果您不熟悉 REST API，請先詳閱 [Azure REST API 參考](/rest/api/azure/)，以對 REST API 有一般的了解，特別是要求 URI 和要求本文。 本文使用這些概念提供使用 Azure Resource Graph 的方向，而且假設您已具備其使用知識。 [ARMClient](https://github.com/projectkudu/ARMClient) 這類工具及其他元件可自動處理授權，建議初學者使用它們。

如需 Azure Resource Graph 的規格，請參閱 [Azure Resource Graph REST API](/rest/api/azure-resourcegraph/)。

### <a name="rest-api-and-powershell"></a>REST API 和 PowerShell

如果您還沒有發出 REST API 呼叫的工具，請考慮使用 PowerShell 取得這些指示。 下列程式碼範例會取得向 Azure 進行驗證的標頭。 產生驗證標頭 (有時也稱為**持有人權杖**)，並提供 REST API URI 以連接到任何參數或**要求本文**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

取代上面 **$restUri** 變數中的 `{subscriptionId}`，以取得您訂用帳戶的相關資訊。 $Response 變數會保存 `Invoke-RestMethod` Cmdlet 的結果，它可使用 [Convertfrom-json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 這類的 Cmdlet 加以剖析。 如果 REST API 服務端點應該有**要求本文**，請提供一個 JSON 格式的變數給 `Invoke-RestMethod` 的 `-Body` 參數。

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

在 REST API 工具新增至您選擇的環境後，您就可以試試看簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源，以及每個資源的**名稱**與**資源類型**。

在每個 REST API 呼叫的要求本文中，都有一個變數需要取代為您自己的值：

- `{subscriptionID}` - 以您的訂用帳戶識別碼取代

1. 使用 REST API 和 `resources` 端點來執行您的第一個 Azure Resource Graph 查詢：

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 要求本文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
         }
     }
     ```

   > [!NOTE]
   > 由於此查詢範例並未提供排序修飾詞 (例如 `order by`)，因此執行此查詢多次將可能會對每個要求產生一組不同的資源。

1. 更新 `resouces` 端點的呼叫，並將**查詢**變更為 `order by` **名稱** 屬性：

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 要求本文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
         }
     }
     ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 更新 `resources` 端點的呼叫，並變更**查詢**，以先 `order by` **名稱**屬性，然後 `limit` 為只顯示前五個結果：

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 要求本文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
         }
     }
     ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依**名稱**屬性排序，但仍限制為只顯示前五個結果。

如需更多 Azure Resource Graph 的 REST API 呼叫範例，請參閱 [Azure Resource Graph REST 範例](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples)。

## <a name="clean-up-resources"></a>清除資源

REST API 沒有可解除安裝的程式庫或模組。 如果您已安裝 _ARMClient_ 或 _Postman_ 之類的工具來進行呼叫，而且不再需要這些工具，則您可以立即解除安裝此工具。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已呼叫 Resource Graph REST API 端點，並執行了您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)
