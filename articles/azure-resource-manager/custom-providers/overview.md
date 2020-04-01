---
title: 自訂提供程式概述
description: 瞭解 Azure 自訂資源提供者以及如何擴展 Azure API 平面以適合工作流。
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398468"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 自訂資源提供程式概述

Azure 自定義資源提供程式是 Azure 的擴展平臺。 它允許您定義可用於豐富預設 Azure 體驗的自訂 API。 本文件介紹:

- 如何生成和部署 Azure 自訂資源提供程式。
- 如何使用 Azure 自訂資源提供程式來擴展現有工作流。
- 在哪裡可以找到指南和代碼示例以開始。

![自訂提供者概觀](./media/overview/overview.png)

> [!IMPORTANT]
> 自定義提供程式當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自訂資源提供者可以做什麼

以下是使用 Azure 自訂資源提供者可以實現的一些範例:

- 擴展 Azure 資源管理器 REST API 以包括內部和外部服務。
- 在現有 Azure 工作流之上啟用自定義方案。
- 自定義 Azure 資源管理器範本控制項和效果。

## <a name="what-is-a-custom-resource-provider"></a>什麼是自訂資源提供者

Azure 自定義資源提供程式是通過在 Azure 和終結點之間創建協定來進行的。 此協定透過新資源 Microsoft 定義新資源和操作**的清單**。 然後,自定義資源提供程式將在 Azure 中公開這些新 API。 Azure 自訂資源提供程式由三部分組成:自定義資源提供程式、**終結點**和自定義資源。

## <a name="how-to-build-custom-resource-providers"></a>如何建構自訂資源提供者

自定義資源提供程式是 Azure 和終結點之間的協定清單。 此協定描述 Azure 應如何與終結點進行交互。 資源提供者的行為類似於代理,並將要求和回應轉寄到指定終結點和從指定的**終結點**。 資源提供者可以指定兩種類型的協定:[**資源類型**](./custom-providers-resources-endpoint-how-to.md)和[**操作**](./custom-providers-action-endpoint-how-to.md)。 這些通過終結點定義啟用。 終結點定義由三個字段組成:**名稱**,**路由類型**與**終結點**。

範例終結點:

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
routingType | *不* | 決定**終結點的協定型態**。 如果未指定，則會預設為 "Proxy"。
端點 | *是的* | 要作為要求路由傳送目的地的端點。 這會處理回應以及要求的任何副作用。

### <a name="building-custom-resources"></a>建置自訂資源

**資源類型**描述添加到 Azure 的新自訂資源。 這些公開了基本的 RESTful CRUD 方法。 [檢視有關建立自訂資源的更多資訊](./custom-providers-resources-endpoint-how-to.md)

範例自訂資源提供者與**資源類型**:

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

為此範例加入 Azure 的 API:

HttpMethod | URI 範例 | 描述
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定義資源/[自定義資源名稱]?api版本_2018-09-01預覽 | Azure REST API 呼叫以建立新資源。
刪除 | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定義資源/[自定義資源名稱]?api版本_2018-09-01預覽 | 要刪除現有資源的 Azure REST API 呼叫。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定義資源/[自定義資源名稱]?api版本_2018-09-01預覽 | 用於檢索現有資源的 Azure REST API 呼叫。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定義資源?api版本_2018-09-01預覽 | 用於檢索現有資源清單的 Azure REST API 呼叫。

### <a name="building-custom-actions"></a>建置自訂作業

**操作**描述添加到 Azure 的新操作。 這些可以公開在資源提供程序的頂部或嵌套在**資源類型**下。 檢視[有關建立自訂操作的更多](./custom-providers-action-endpoint-how-to.md)

**範例自訂**資源提供者的操作 :

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

為此範例加入 Azure 的 API:

HttpMethod | URI 範例 | 描述
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>我的自定義行動?api版本_2018-09-01預覽 | Azure REST API 呼叫以啟動操作。

## <a name="looking-for-help"></a>尋求協助

如果對 Azure 自訂資源提供程式開發有疑問,請嘗試詢問[堆疊溢出](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 請移至下一篇文章以建立自訂提供者。

- [快速入門:建立 Azure 自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [教學:在 Azure 中建立自訂操作和資源](./tutorial-get-started-with-custom-providers.md)
- [如何:將自訂操作新增到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何:將自訂資源新增到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
