---
title: 資源上線
description: 深入瞭解如何使用 Azure 自訂提供者來執行資源上線，以將管理或設定套用至其他 Azure 資源類型。
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650405"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 自訂提供者資源上線總覽

Azure 自訂提供者資源上架是適用于 Azure 資源類型的擴充性模型。 它可讓您大規模地對現有的 Azure 資源套用作業或管理。 如需詳細資訊，請參閱[Azure 自訂提供者可以如何擴充 azure](overview.md)。 本文章說明：

- 資源上線可以執行的工作。
- 資源上線的基本概念，以及如何使用它。
- 何處可以找到要開始使用的指南和程式碼範例。

> [!IMPORTANT]
> 自訂提供者目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 某些功能可能不受支援，或可能有受限制的功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-resource-onboarding-do"></a>資源上線有哪些功能？

類似于[Azure 自訂提供者的自訂資源](./custom-providers-resources-endpoint-how-to.md)，資源上線會定義合約，以對端點進行 proxy 「上架」要求。 與自訂資源不同的是，資源上架不會建立新的資源類型。 相反地，它允許現有資源類型的延伸。 而資源上線功能會與 Azure 原則搭配運作，因此可以大規模地進行資源的管理和設定。 資源上線工作流程的一些範例：

- 在虛擬機器擴充功能上安裝和管理。
- 在 Azure 儲存體帳戶上傳和設定預設值。
- 啟用大規模的基準診斷設定。

## <a name="resource-onboarding-basics"></a>資源上線基本概念

您可以使用 CustomProviders/resourceProviders 和 CustomProviders/association 資源類型，透過 Azure 自訂提供者來設定資源上線。 若要啟用自訂提供者的資源上線，在設定過程中，請建立名為 "association" 的**resourceType** ，並具有包含 "Extension" 的**routingType** 。 CustomProviders/association 和 CustomProviders/resourceProviders 不需要屬於相同的資源群組。

以下是 Azure 自訂提供者的範例：

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
NAME | 是 | 端點定義的名稱。 針對資源上線，名稱必須是「關聯」。
routingType | Yes | 判斷具有端點的合約類型。 針對資源上線，有效的**routingTypes**為「Proxy、快取、延伸模組」和「Webhook，快取，延伸模組」。
端點 | Yes | 要作為要求路由傳送目的地的端點。 這會處理回應和要求的任何副作用。

建立具有關聯資源類型的自訂提供者之後，您可以使用 CustomProviders/association 作為目標。 CustomProviders/association 是擴充功能資源，可以擴充任何其他 Azure 資源。 建立 CustomProviders/關聯的實例時，它會接受屬性**targetResourceId**，這應該是有效的 CustomProviders/ResourceProviders 或 microsoft 解決方案/應用程式資源識別碼。 在這些情況下，會將要求轉送至您所建立之 CustomProviders/resourceProviders 實例上的關聯資源類型。

> [!NOTE]
> 如果將 Microsoft. 解決方案/應用程式資源識別碼當做**targetResourceId**提供，則必須將 CustomProviders/resourceProviders 部署在名為「公用」的受控資源群組中。

範例 Azure 自訂提供者關聯：

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

屬性 | 必要項？ | Description
---|---|---
targetResourceId | Yes | CustomProviders/resourceProviders 或 Microsoft 解決方案/應用程式的資源識別碼。

## <a name="how-to-use-resource-onboarding"></a>如何使用資源上線

資源上線功能的運作方式是使用 CustomProviders/關聯延伸模組資源擴充其他資源。 在下列範例中，會針對虛擬機器提出要求，但可以擴充任何資源。

首先，您必須建立具有關聯資源類型的自訂提供者資源。 這會宣告在建立對應的 CustomProviders/關聯資源（以自訂提供者為目標）時，將使用的回呼 URL。

範例 CustomProviders/resourceProviders 建立要求：

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

建立自訂提供者之後，您可以將其他資源設為目標，並將自訂提供者的副作用套用至它們。

範例 CustomProviders/關聯建立要求：

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

然後，此要求會轉送至您所建立之自訂提供者中所指定的端點，這是由此形式的**targetResourceId**所參考：

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

端點應該會回應應用程式/json `Content-Type` 和有效的 json 回應主體。 在 JSON 的**properties**物件底下傳回的欄位將會加入至關聯傳迴響應。

## <a name="getting-help"></a>取得說明

如果您有關于 Azure 自訂資源提供者開發的問題，請嘗試在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)詢問。 類似的問題可能已有人回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 若要深入瞭解，請參閱下列文章：

- [教學課程：使用自訂提供者的資源上線](./tutorial-resource-onboarding.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-get-started-with-custom-providers.md)
- [快速入門：建立自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [如何：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
