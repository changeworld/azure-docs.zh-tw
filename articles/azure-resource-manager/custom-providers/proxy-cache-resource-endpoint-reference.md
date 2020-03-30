---
title: 自訂資源快取參考
description: Azure 自訂資來源提供者的自訂資源緩存引用。 本文將介紹實現緩存自訂資源的終結點的要求。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650379"
---
# <a name="custom-resource-cache-reference"></a>自訂資源緩存引用

本文將介紹實現緩存自訂資源的終結點的要求。 如果您不熟悉 Azure 自訂資來源提供者，請參閱[自訂資來源提供者的概述](overview.md)。

## <a name="how-to-define-a-cache-resource-endpoint"></a>如何定義緩存資源終結點

可以通過將**路由類型**指定為"代理、緩存"來創建代理資源。

自訂資來源提供者示例：

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

## <a name="building-proxy-resource-endpoint"></a>構建代理資源終結點

實現"代理、緩存"資源**終結點**的**終結點**必須處理 Azure 中新 API 的請求和回應。 在這種情況下，**資源類型**將為 生成`PUT`新的 Azure 資源 API，`GET`並在`DELETE`單個資源上執行 CRUD，以及`GET`檢索所有現有資源：

> [!NOTE]
> Azure API 將生成請求方法`PUT` `GET`，`DELETE`和 ， 但緩存**終結點**只需要處理`PUT``DELETE`和 。
> 我們建議**終結點**也實現`GET`。

### <a name="create-a-custom-resource"></a>建立自訂資源

Azure API 傳入請求：

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

然後，此請求將以以下形式轉發到**終結點**：

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

同樣，從**端點**的回應然後轉發回客戶。 來自終結點的回應應返回：

- 有效的 JSON 物件文檔。 所有陣列和字串都應嵌套在頂部物件下。
- 標頭`Content-Type`應設置為"應用程式/json;"字元_utf-8"。
- 自訂資來源提供者將覆蓋 請求`name`的`type`、`id`和 欄位。
- 自訂資來源提供者將僅返回緩存終結點的物件下的`properties`欄位。

**端點**回應：

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

自訂`name`資源`id`提供程式將自動`type`為自訂資源生成 和 欄位。

Azure 自訂資來源提供者回應：

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

Azure API 傳入請求：

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

然後，此請求將以以下形式轉發到**終結點**：

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同樣，從**端點**的回應然後轉發回客戶。 來自終結點的回應應返回：

- 有效的 JSON 物件文檔。 所有陣列和字串都應嵌套在頂部物件下。
- 標頭`Content-Type`應設置為"應用程式/json;"字元_utf-8"。
- 僅當返回 200 級回應時，Azure 自訂資來源提供者才會從其緩存中刪除該專案。 即使資源不存在，**終結點**也應返回 204。

**端點**回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 自訂資來源提供者回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>擷取自訂資源

Azure API 傳入請求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

請求**不會**轉發到**終結點**。

Azure 自訂資來源提供者回應：

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

### <a name="enumerate-all-custom-resources"></a>枚舉所有自訂資源

Azure API 傳入請求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

此請求**不會**轉發到**終結點**。

Azure 自訂資來源提供者回應：

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

- [Azure 自訂資來源提供者概述](overview.md)
- [快速入門：創建 Azure 自訂資來源提供者並部署自訂資源](./create-custom-provider.md)
- [教程：在 Azure 中創建自訂操作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [引用：自訂資源代理引用](proxy-resource-endpoint-reference.md)
