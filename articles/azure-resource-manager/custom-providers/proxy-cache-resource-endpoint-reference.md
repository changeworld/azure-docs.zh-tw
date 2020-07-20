---
title: 自訂資源快取參考
description: Azure 自訂資源提供者的自訂資源快取參考。 本文將逐步說明執行快取自訂資源的端點需求。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650379"
---
# <a name="custom-resource-cache-reference"></a>自訂資源快取參考

本文將逐步說明執行快取自訂資源的端點需求。 如果您不熟悉 Azure 自訂資源提供者，請參閱[自訂資源提供者的總覽](overview.md)。

## <a name="how-to-define-a-cache-resource-endpoint"></a>如何定義快取資源端點

您可以藉由指定**routingType**為 "Proxy，Cache" 來建立 proxy 資源。

範例自訂資源提供者：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>建立 proxy 資源端點

執行「Proxy，快取」資源**端點**的**端點**必須處理 Azure 中新 API 的要求和回應。 在此情況下， **resourceType**會針對、和產生新的 AZURE 資源 API， `PUT` `GET` `DELETE` 以在單一資源上執行 CRUD，以及 `GET` 取得所有現有的資源：

> [!NOTE]
> Azure API 將會產生要求方法 `PUT` 、 `GET` 和，但快取 `DELETE` **端點**只需要處理 `PUT` 和 `DELETE` 。
> 我們建議您也**endpoint**要執行端點 `GET` 。

### <a name="create-a-custom-resource"></a>建立自訂資源

Azure API 連入要求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

然後，此要求會轉送至下列格式的**端點**：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

同樣地，來自**端點**的回應會轉送回給客戶。 端點的回應應該會傳回：

- 有效的 JSON 目的檔。 所有陣列和字串都應該在最上層物件下加以嵌套。
- `Content-Type`標頭應設定為 "application/json;字元集 = utf-8」。
- 自訂資源提供者將會覆寫 `name` 要求的、 `type` 和 `id` 欄位。
- 自訂資源提供者只會傳回快取 `properties` 端點物件下的欄位。

**端點**回應

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

自訂資源 `name` `id` `type` 提供者會自動產生自訂資源的、和欄位。

Azure 自訂資源提供者回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>移除自訂資源

Azure API 連入要求：

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

然後，此要求會轉送至下列格式的**端點**：

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同樣地，來自**端點**的回應會轉送回給客戶。 端點的回應應該會傳回：

- 有效的 JSON 目的檔。 所有陣列和字串都應該在最上層物件下加以嵌套。
- `Content-Type`標頭應設定為 "application/json;字元集 = utf-8」。
- 如果傳回200層級的回應，Azure 自訂資源提供者只會從其快取中移除該專案。 即使資源不存在，**端點**也應傳回204。

**端點**回應

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 自訂資源提供者回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>擷取自訂資源

Azure API 連入要求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

要求**不**會轉送到**端點**。

Azure 自訂資源提供者回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>列舉所有自訂資源

Azure API 連入要求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

此要求**不**會轉送到**端點**。

Azure 自訂資源提供者回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

- [Azure 自訂資源提供者的總覽](overview.md)
- [快速入門：建立 Azure 自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [參考：自訂資源 Proxy 參考](proxy-resource-endpoint-reference.md)
