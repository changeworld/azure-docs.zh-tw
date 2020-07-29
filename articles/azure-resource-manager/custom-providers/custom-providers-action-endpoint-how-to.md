---
title: 將自訂動作新增至 Azure REST API
description: 瞭解如何將自訂動作新增至 Azure REST API。 本文將逐步解說想要執行自訂動作之端點的需求和最佳作法。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650392"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>將自訂動作新增至 Azure REST API

本文將逐步解說建立 Azure 自訂資源提供者端點來執行自訂動作的需求和最佳作法。 如果您不熟悉 Azure 自訂資源提供者，請參閱[自訂資源提供者的總覽](overview.md)。

## <a name="how-to-define-an-action-endpoint"></a>如何定義動作端點

「**端點**」（endpoint）是指向服務的 URL，它會在它與 Azure 之間執行基礎合約。 端點會定義在自訂資源提供者中，而且可以是任何可公開存取的 URL。 下列範例有一個稱為「實作用」的**動作** `myCustomAction` `endpointURL` 。

範例**ResourceProvider**：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>建立動作端點

執行**動作**的**端點**必須處理 Azure 中新 API 的要求和回應。 建立具有**動作**的自訂資源提供者時，它會在 Azure 中產生一組新的 api。 在此情況下，此動作會產生新的 Azure 動作 API 來進行 `POST` 呼叫：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 連入要求：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

然後，此要求會轉送至下列格式的**端點**：

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

同樣地，來自**端點**的回應會轉送回給客戶。 端點的回應應該會傳回：

- 有效的 JSON 目的檔。 所有陣列和字串都應該在最上層物件下加以嵌套。
- `Content-Type`標頭應設定為 "application/json;字元集 = utf-8」。

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure 自訂資源提供者回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>呼叫自訂動作

有兩種主要方式可從自訂資源提供者呼叫自訂動作：

- Azure CLI
- Azure Resource Manager 範本

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

參數 | 必要 | 說明
---|---|---
動作 | *是的* | 在**ResourceProvider**中定義之動作的名稱。
ids | *是的* | **ResourceProvider**的資源識別碼。
request-body | *不* | 將傳送至**端點**的要求主體。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

> [!NOTE]
> 動作在 Azure Resource Manager 範本中具有有限的支援。 為了在範本內呼叫動作，它必須 [`list`](../templates/template-functions-resource.md#list) 在其名稱中包含前置詞。

具有清單動作的範例**ResourceProvider** ：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Azure Resource Manager 範本的範例：

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

參數 | 必要 | 說明
---|---|---
resourceIdentifier | *是的* | **ResourceProvider**的資源識別碼。
apiVersion | *是的* | 資源執行時間的 API 版本。 這應該一律為 "2018-09-01-preview"。
functionValues | *不* | 將傳送至**端點**的要求主體。

## <a name="next-steps"></a>後續步驟

- [Azure 自訂資源提供者的總覽](overview.md)
- [快速入門：建立 Azure 自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
