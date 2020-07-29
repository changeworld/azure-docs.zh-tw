---
title: 將自訂資源新增至 Azure REST API
description: 瞭解如何將自訂資源新增至 Azure REST API。 本文將逐步解說想要執行自訂資源之端點的需求和最佳作法。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650522"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>將自訂資源新增至 Azure REST API

本文將逐步解說建立 Azure 自訂資源提供者端點來執行自訂資源的需求和最佳作法。 如果您不熟悉 Azure 自訂資源提供者，請參閱[自訂資源提供者的總覽](overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定義資源端點

「**端點**」（endpoint）是指向服務的 URL，它會在它與 Azure 之間執行基礎合約。 端點會定義在自訂資源提供者中，而且可以是任何可公開存取的 URL。 下列範例中的**resourceType**稱為「實作為」 `myCustomResource` `endpointURL` 。

範例**ResourceProvider**：

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

## <a name="building-a-resource-endpoint"></a>建立資源端點

執行**resourceType**的**端點**必須處理 Azure 中新 API 的要求和回應。 建立具有**resourceType**的自訂資源提供者時，它會在 Azure 中產生一組新的 api。 在此情況下， **resourceType**會針對、和產生新的 AZURE 資源 API， `PUT` `GET` `DELETE` 以在單一資源上執行 CRUD，以及抓取 `GET` 所有現有的資源：

操作單一資源（ `PUT` 、 `GET` 和 `DELETE` ）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

取出所有資源（ `GET` ）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

針對自訂資源，自訂資源提供者提供兩種類型的**routingTypes**： " `Proxy` " 和 " `Proxy, Cache` "。

### <a name="proxy-routing-type"></a>proxy 路由類型

" `Proxy` " **RoutingType**會將所有要求方法 proxy 至自訂資源提供者中指定的**端點**。 使用 "" 的時機 `Proxy` ：

- 需要對回應的完整控制。
- 整合系統與現有資源。

若要深入瞭解「」 `Proxy` 資源，請參閱[自訂資源 proxy 參考](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>proxy 快取路由類型

" `Proxy, Cache` " 只會**routingType** proxy `PUT` ，並 `DELETE` 向自訂資源提供者中指定的**端點**要求方法。 自訂資源提供者會 `GET` 根據它儲存在快取中的內容，自動傳回要求。 如果自訂資源是以快取標記，自訂資源提供者也會在回應中新增/覆寫欄位，使 Api 符合規範。 使用 "" 的時機 `Proxy, Cache` ：

- 建立沒有現有資源的新系統。
- 使用現有的 Azure 生態系統。

若要深入瞭解「」 `Proxy, Cache` 資源，請參閱[自訂資源](proxy-cache-resource-endpoint-reference.md)快取參考

## <a name="creating-a-custom-resource"></a>建立自訂資源

有兩種主要方式可從自訂資源提供者建立自訂資源：

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

參數 | 必要 | 說明
---|---|---
is-full-object | *是的* | 指出屬性物件包含其他選項，例如位置、標籤、SKU 和/或方案。
id | *是的* | 自訂資源的資源識別碼。 這應該存在於**ResourceProvider**
properties | *是的* | 將傳送至**端點**的要求主體。

刪除 Azure 自訂資源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

參數 | 必要 | 描述
---|---|---
id | *是的* | 自訂資源的資源識別碼。 這應該存在於**ResourceProvider**中。

擷取 Azure 自訂資源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

參數 | 必要 | 描述
---|---|---
id | *是的* | 自訂資源的資源識別碼。 這應該存在於**ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

> [!NOTE]
> 資源要求回應包含來自端點的適當 `id` 、 `name` 和 `type` 。 **endpoint**

Azure Resource Manager 範本需要 `id` `name` `type` 從下游端點正確傳回、和。 傳回的資源回應應採用下列格式：

範例**端點**回應：

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

參數 | 必要 | 說明
---|---|---
resourceTypeName | *是的* | 自訂提供者中定義的**resourceType** **名稱**。
resourceProviderName | *是的* | 自訂資源提供者實例名稱。
customResourceName | *是的* | 自訂資源名稱。

## <a name="next-steps"></a>後續步驟

- [Azure 自訂資源提供者的總覽](overview.md)
- [快速入門：建立 Azure 自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [參考：自訂資源 Proxy 參考](proxy-resource-endpoint-reference.md)
- [參考：自訂資源快取參考](proxy-cache-resource-endpoint-reference.md)
