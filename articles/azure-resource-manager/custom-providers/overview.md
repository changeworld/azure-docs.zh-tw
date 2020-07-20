---
title: 自訂提供者的總覽
description: 瞭解 Azure 自訂資源提供者，以及如何擴充 Azure API 平面以符合您的工作流程。
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80398468"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 自訂資源提供者總覽

Azure 自訂資源提供者是 Azure 的擴充性平臺。 它可讓您定義自訂 Api，以用來擴充預設的 Azure 體驗。 本檔說明：

- 如何建立和部署 Azure 自訂資源提供者。
- 如何利用 Azure 自訂資源提供者來擴充現有的工作流程。
- 何處可以找到要開始使用的指南和程式碼範例。

![自訂提供者概觀](./media/overview/overview.png)

> [!IMPORTANT]
> 自訂提供者目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自訂資源提供者可以執行的動作

以下是您可以使用 Azure 自訂資源提供者達成的一些範例：

- 延伸 Azure Resource Manager REST API 以包含內部和外部服務。
- 在現有的 Azure 工作流程之上啟用自訂案例。
- 自訂 Azure Resource Manager 的範本控制和效果。

## <a name="what-is-a-custom-resource-provider"></a>什麼是自訂資源提供者

Azure 自訂資源提供者的建立方式是在 Azure 與端點之間建立合約。 此合約會透過新的**CustomProviders/resourceProviders**來定義新資源和動作的清單。 然後，自訂資源提供者會在 Azure 中公開這些新的 Api。 Azure 自訂資源提供者是由三個部分組成：自訂資源提供者、**端點**和自訂資源。

## <a name="how-to-build-custom-resource-providers"></a>如何建立自訂資源提供者

自訂資源提供者是 Azure 與端點之間的合約清單。 此合約說明 Azure 應如何與端點互動。 資源提供者的作用就像是 proxy，會將要求和回應轉送到指定的**端點**。 資源提供者可以指定兩種類型的合約： [ [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) ] 和 [[**動作**](./custom-providers-action-endpoint-how-to.md)]。 這些是透過端點定義來啟用。 端點定義是由三個欄位所組成： **name**、 **routingType**和**endpoint**。

範例端點：

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

屬性 | 必要 | 描述
---|---|---
NAME | *是的* | 端點定義的名稱。 Azure 會透過其 API 在 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' 底下公開此名稱
routingType | *不* | 判斷具有**端點**的合約類型。 如果未指定，則會預設為 "Proxy"。
端點 | *是的* | 要作為要求路由傳送目的地的端點。 這會處理回應以及要求的任何副作用。

### <a name="building-custom-resources"></a>建立自訂資源

**ResourceTypes**描述新增至 Azure 的新自訂資源。 這些會公開基本的 RESTful CRUD 方法。 [深入瞭解如何建立自訂資源](./custom-providers-resources-endpoint-how-to.md)

使用**resourceTypes**的範例自訂資源提供者：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

針對上述範例新增至 Azure 的 Api：

HttpMethod | URI 範例 | 描述
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}？ api-version = 2018-09-01.txt-預覽 | Azure REST API 呼叫以建立新的資源。
刪除 | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}？ api-version = 2018-09-01.txt-預覽 | 用來刪除現有資源的 Azure REST API 呼叫。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}？ api-version = 2018-09-01.txt-預覽 | Azure REST API 呼叫以取得現有的資源。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources？ api-version = 2018-09-01.txt-preview | Azure REST API 呼叫，以取得現有資源的清單。

### <a name="building-custom-actions"></a>建立自訂動作

**動作**會描述新增至 Azure 的新動作。 這些可以在資源提供者之上公開，或在**resourceType**底下加以內嵌。 [深入瞭解建立自訂動作](./custom-providers-action-endpoint-how-to.md)

具有**動作**的範例自訂資源提供者：

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
  "location": "eastus"
}
```

針對上述範例新增至 Azure 的 Api：

HttpMethod | URI 範例 | 描述
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction？ api-version = 2018-09-01.txt-preview | 用來啟動動作的 Azure REST API 呼叫。

## <a name="looking-for-help"></a>尋求協助

如果您有關于 Azure 自訂資源提供者開發的問題，請嘗試詢問[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 請移至下一篇文章以建立自訂提供者。

- [快速入門：建立 Azure 自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
