---
title: 將自訂動作新增至 Azure REST API
description: 瞭解如何將自訂操作添加到 Azure REST API。 本文將介紹希望實現自訂操作的終結點的要求和最佳實踐。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650392"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>將自訂操作添加到 Azure REST API

本文將介紹創建實現自訂操作的 Azure 自訂資來源提供者終結點的要求和最佳實踐。 如果您不熟悉 Azure 自訂資來源提供者，請參閱[自訂資來源提供者的概述](overview.md)。

## <a name="how-to-define-an-action-endpoint"></a>如何定義操作終結點

**終結點**是指向服務的 URL，該服務在它與 Azure 之間實現基礎協定。 終結點在自訂資來源提供者中定義，可以是任何可公開訪問的 URL。 下面的示例具有稱為`myCustomAction`由`endpointURL`實現**的操作**。

示例**資來源提供者**：

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

## <a name="building-an-action-endpoint"></a>構建操作終結點

實現**操作**的**終結點**必須處理 Azure 中新 API 的請求和回應。 創建具有**操作**的自訂資來源提供者時，它將在 Azure 中生成一組新的 API。 在這種情況下，該操作將為`POST`調用生成新的 Azure 操作 API：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 傳入請求：

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

然後，此請求將以以下形式轉發到**終結點**：

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

同樣，從**端點**的回應然後轉發回客戶。 來自終結點的回應應返回：

- 有效的 JSON 物件文檔。 所有陣列和字串都應嵌套在頂部物件下。
- 標頭`Content-Type`應設置為"應用程式/json;"字元_utf-8"。

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

Azure 自訂資來源提供者回應：

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

## <a name="calling-a-custom-action"></a>調用自訂操作

從自訂資來源提供者調用自訂操作有兩種主要方法：

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

參數 | 必要 | 描述
---|---|---
動作 | *是的* | 資源**提供程式**中定義的操作的名稱。
ids | *是的* | **資來源提供者**的資源識別碼 。
request-body | *不* | 將發送到**終結點**的請求正文。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

> [!NOTE]
> 操作在 Azure 資源管理器範本中的支援有限。 為了在範本中叫用作業，它必須在其名稱中包含[`list`](../templates/template-functions-resource.md#list)首碼。

具有清單操作的示例**資來源提供者**：

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

參數 | 必要 | 描述
---|---|---
資源識別碼 | *是的* | **資來源提供者**的資源識別碼 。
apiVersion | *是的* | 資源運行時的 API 版本。 這應始終為"2018-09-01 預覽"。
functionValues | *不* | 將發送到**終結點**的請求正文。

## <a name="next-steps"></a>後續步驟

- [Azure 自訂資來源提供者概述](overview.md)
- [快速入門：創建 Azure 自訂資來源提供者並部署自訂資源](./create-custom-provider.md)
- [教程：在 Azure 中創建自訂操作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂資源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
