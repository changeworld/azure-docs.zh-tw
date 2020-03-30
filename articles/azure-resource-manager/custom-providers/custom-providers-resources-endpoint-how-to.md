---
title: 將自訂資源新增至 Azure REST API
description: 瞭解如何將自訂資源添加到 Azure REST API。 本文將介紹希望實現自訂資源的終結點的要求和最佳實踐。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650522"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>將自訂資源添加到 Azure REST API

本文將介紹創建實現自訂資源的 Azure 自訂資來源提供者終結點的要求和最佳實踐。 如果您不熟悉 Azure 自訂資來源提供者，請參閱[自訂資來源提供者的概述](overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定義資源終結點

**終結點**是指向服務的 URL，該服務在它與 Azure 之間實現基礎協定。 終結點在自訂資來源提供者中定義，可以是任何可公開訪問的 URL。 下面的示例具有稱為`myCustomResource`由`endpointURL`實現**的資源類型**。

示例**資來源提供者**：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
        "routingType": "Proxy, Cache",
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

## <a name="building-a-resource-endpoint"></a>構建資源終結點

實現**資源類型的****終結點**必須處理 Azure 中新 API 的請求和回應。 創建具有**資源類型的**自訂資來源提供者時，它將在 Azure 中生成一組新的 API。 在這種情況下，**資源類型**將為 生成`PUT`新的 Azure 資源 API，`GET`並在`DELETE`單個資源上執行 CRUD，以及`GET`檢索所有現有資源：

操作單個資源`PUT`（、 `GET` `DELETE`和 ）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

檢索所有資源`GET`（ ）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

對於自訂資源，自訂資來源提供者提供兩種類型的**路由類型**："`Proxy`和""。`Proxy, Cache`

### <a name="proxy-routing-type"></a>代理路由類型

"`Proxy`**路由類型**"代理所有請求方法到自訂資來源提供者中指定的**終結點**。 何時使用""：`Proxy`

- 需要完全控制回應。
- 將系統集成與現有資源。

要瞭解有關""資源`Proxy`"的更多[資訊，請參閱自訂資源代理引用](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>代理緩存路由類型

"`Proxy, Cache`**路由類型**"僅`PUT`代理，並將`DELETE`方法請求到自訂資來源提供者中指定的**終結點**。 自訂資來源提供者將根據存儲在其緩存`GET`中的內容自動返回請求。 如果自訂資源標記為緩存，自訂資來源提供者還將在回應中添加/覆蓋欄位，以使 API Azure 相容。 何時使用""：`Proxy, Cache`

- 創建沒有現有資源的新系統。
- 使用現有的 Azure 生態系統。

要瞭解有關""資源`Proxy, Cache`的詳細資訊，請參閱[自訂資源緩存引用](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>創建自訂資源

從自訂資來源提供者創建自訂資源有兩種主要方法：

- Azure CLI
- Azure Resource Manager 範本

### <a name="azure-cli"></a>Azure CLI

建立自訂資源：

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

參數 | 必要 | 描述
---|---|---
is-full-object | *是的* | 指出屬性物件包含其他選項，例如位置、標籤、SKU 和/或方案。
id | *是的* | 自訂資源的資源識別碼。 這應該存在於**資來源提供者**的外
properties | *是的* | 將發送到**終結點**的請求正文。

刪除 Azure 自訂資源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

參數 | 必要 | 描述
---|---|---
id | *是的* | 自訂資源的資源識別碼。 這應該存在於**資來源提供者**中。

擷取 Azure 自訂資源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

參數 | 必要 | 描述
---|---|---
id | *是的* | 自訂資源的資源識別碼。 這應該存在於**資來源提供者**的外

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

> [!NOTE]
> `id`資源要求回應包含適當的 、`name`和`type`從**終結點**。

Azure 資源管理器範本要求`id`從`name`下游終結點正確`type`返回 和 。 返回的資源回應應為以下形式：

示例**終結點**回應：

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Azure Resource Manager 範本的範例：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

參數 | 必要 | 描述
---|---|---
resourceTypeName | *是的* | 自訂提供程式中定義的**資源類型****的名稱**。
resourceProviderName | *是的* | 自訂資來源提供者實例名稱。
customResourceName | *是的* | 自訂資源名稱。

## <a name="next-steps"></a>後續步驟

- [Azure 自訂資來源提供者概述](overview.md)
- [快速入門：創建 Azure 自訂資來源提供者並部署自訂資源](./create-custom-provider.md)
- [教程：在 Azure 中創建自訂操作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [引用：自訂資源代理引用](proxy-resource-endpoint-reference.md)
- [參考：自訂資源緩存引用](proxy-cache-resource-endpoint-reference.md)
