---
title: 資源上線
description: 瞭解如何使用 Azure 自訂提供程式將管理或配置應用於其他 Azure 資源類型來執行資源入職。
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650405"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 自訂提供程式資源入職概述

Azure 自訂提供程式資源載入是 Azure 資源類型的擴展模型。 它允許您大規模跨現有 Azure 資源應用操作或管理。 有關詳細資訊，請參閱[Azure 自訂提供程式如何擴展 Azure](overview.md)。 本文章說明：

- 資源載入可以做什麼。
- 資源載入基礎知識以及如何使用它。
- 在哪裡可以找到指南和代碼示例以開始。

> [!IMPORTANT]
> 自訂提供程式當前處於公共預覽版中。
> 此預覽版本在沒有服務等級協定的情況下提供，我們不建議用於生產工作負載。 某些功能可能不受支援或功能受限。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-resource-onboarding-do"></a>資源載入可以做什麼？

與[Azure 自訂提供程式自訂資源](./custom-providers-resources-endpoint-how-to.md)類似，資源載入定義將"入職"請求代理到終結點的協定。 與自訂資源不同，資源載入不會創建新的資源類型。 相反，它允許擴展現有資源類型。 資源入職工作與 Azure 策略一起工作，因此可以大規模地管理和配置資源。 資源入職工作流的一些示例：

- 安裝和管理到虛擬機器擴展。
- 上載和配置 Azure 存儲帳戶上的預設值。
- 大規模啟用基線診斷設置。

## <a name="resource-onboarding-basics"></a>資源入職基礎知識

您可以使用 Microsoft.自訂提供程式/資來源提供者和 Microsoft.自訂提供程式/關聯資源類型配置通過 Azure 自訂提供程式載入的資源。 要為自訂提供程式啟用資源入職，在配置過程中，請創建一個稱為"關聯"**的資源類型**，其中包含包含"擴展"的**路由類型**。 微軟.自訂提供程式/關聯和微軟.自訂提供程式/資來源提供者不需要屬於同一資源組。

下面是 Azure 自訂提供程式的示例：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

屬性 | 必要項？ | 描述
---|---|---
NAME | 是 | 端點定義的名稱。 對於資源載入，名稱必須為"關聯"。
routingType | 是 | 確定與終結點的協定類型。 對於資源載入，有效的**路由類型**是"代理、緩存、擴展"和"Webhook、緩存、擴展"。
端點 | 是 | 要作為要求路由傳送目的地的端點。 這將處理請求的回應和任何副作用。

使用關聯資源類型創建自訂提供程式後，可以使用 Microsoft.CustomProvider/關聯進行定位。 Microsoft.自訂提供程式/關聯是可以擴展任何其他 Azure 資源的擴展資源。 創建 Microsoft.CustomProviders/關聯實例時，它將採用屬性**目標 ResourceId**，這應該是有效的 Microsoft.自訂提供程式/資來源提供者或 Microsoft.解決方案/應用程式資源識別碼。 在這些情況下，請求將轉發到您創建的 Microsoft.CustomProviders/資來源提供者實例上的關聯資源類型。

> [!NOTE]
> 如果 Microsoft.解決方案/應用程式資源識別碼 作為**目標ResourceId**提供，則必須有一個 Microsoft.自訂提供程式/資來源提供者部署在託管資源組中，名稱為"公共"。

示例 Azure 自訂提供程式關聯：

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

屬性 | 必要項？ | 描述
---|---|---
目標資源Id | 是 | 微軟的資源識別碼.自訂提供程式/資來源提供者或 Microsoft.解決方案/應用程式。

## <a name="how-to-use-resource-onboarding"></a>如何使用資源載入

資源載入的工作原理是擴展與 Microsoft 的其他資源。 在下面的示例中，對虛擬機器發出請求，但可以擴展任何資源。

首先，您需要創建具有關聯資源類型的自訂提供程式資源。 這將聲明將在創建相應的 Microsoft 時使用的回檔 URL。

示例 Microsoft.自訂提供程式/資來源提供者創建請求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

創建自訂提供程式後，可以定位其他資源，並將自訂提供程式的副作用應用於這些資源。

示例 Microsoft.自訂提供程式/關聯創建請求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

然後，此請求將轉發到您創建的自訂提供程式中指定的終結點，該終結點由以下表單**中的目標 ResourceId**引用：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

終結點應使用應用程式/json`Content-Type`和有效的 JSON 回應正文進行回應。 在 JSON**的屬性**物件下返回的欄位將添加到關聯返回回應中。

## <a name="getting-help"></a>取得說明

如果您對 Azure 自訂資來源提供者開發有疑問，請嘗試在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-custom-providers)上詢問它們。 類似的問題可能已有人回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 請參閱以下文章以瞭解更多資訊：

- [教程：使用自訂提供程式進行資源載入](./tutorial-resource-onboarding.md)
- [教程：在 Azure 中創建自訂操作和資源](./tutorial-get-started-with-custom-providers.md)
- [快速入門：創建自訂資來源提供者並部署自訂資源](./create-custom-provider.md)
- [如何：將自訂操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
