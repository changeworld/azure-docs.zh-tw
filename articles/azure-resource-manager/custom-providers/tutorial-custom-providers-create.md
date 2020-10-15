---
title: 建立並使用自訂提供者
description: 本教學課程說明如何建立和使用 Azure 自訂提供者。 使用自訂提供者來變更 Azure 上的工作流程。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503747"
---
# <a name="create-and-use-a-custom-provider"></a>建立並使用自訂提供者

自訂提供者是 Azure 和端點之間的合約。 透過自訂提供者，您可以在 Azure 上變更工作流程。 本教學課程會說明自訂提供者的建立程序。 如果您不熟悉 Azure 自訂提供者，請參閱 [Azure 自訂資源提供者的概觀](overview.md)。

## <a name="create-a-custom-provider"></a>建立自訂提供者

> [!NOTE]
> 本教學課程不會示範如何撰寫端點。 如果您沒有 RESTFUL 端點，請遵循[撰寫 RESTful 端點的教學課程](./tutorial-custom-providers-function-authoring.md)，這是目前教學課程的基礎。

建立端點之後，您可以建立自訂提供者來產生提供者與端點之間的合約。 您可以透過自訂提供者指定端點定義的清單：

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

屬性 | 必要 | 描述
---|---|---
**name** | 是 | 端點定義的名稱。 Azure 會透過其 API 在 /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName} 底下公開此名稱
**routingType** | 否 | 端點合約類型。 如果未指定值，則會預設為 "Proxy"。
**endpoint** | 是 | 要作為要求路由傳送目的地的端點。 此端點會處理回應及要求的任何副作用。

**endpoint**的值是 Azure 函式應用程式的觸發程序 URL。 `<yourapp>`、`<funcname>` 和 `<functionkey>` 的預留位置必須取代為所建立函式應用程式的值。

## <a name="define-custom-actions-and-resources"></a>定義自訂動作和資源

自訂提供者包含在 **actions** 和 **resourceTypes** 屬性底下建模的端點定義清單。 **actions**屬性會對應至自訂提供者所公開的自訂動作，而 **resourceTypes** 屬性則是自訂資源。 在本教學課程中，自訂提供者具有名為 `myCustomAction` 的 **actions** 屬性和名為 `myCustomResources` 的 **resourceTypes** 屬性。

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>部署自訂提供者

> [!NOTE]
> 您必須將 **endpoint** 值取代為先前教學課程所建立函式應用程式中的觸發程序 URL。

您可以使用 Azure Resource Manager 範本來部署上述自訂提供者：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>使用自訂動作和資源

建立自訂提供者之後，您可以使用新的 Azure API。 下列索引標籤說明如何呼叫和使用自訂提供者。

### <a name="custom-actions"></a>自訂動作

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` 和 `{resourceGroupName}` 預留位置必須取代為您部署自訂提供者所在的訂用帳戶和資源群組。

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

參數 | 必要 | 描述
---|---|---
*action* | 是 | 自訂提供者中定義的動作名稱
*ids* | 是 | 自訂題中者的資源識別碼
*request-body* | 否 | 將會傳送至端點的要求本文

# <a name="template"></a>[範本](#tab/template)

無。

---

### <a name="custom-resources"></a>自訂資源

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` 和 `{resourceGroupName}` 預留位置必須取代為您部署自訂提供者所在的訂用帳戶和資源群組。

#### <a name="create-a-custom-resource"></a>建立自訂資源

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

參數 | 必要 | 描述
---|---|---
*is-full-object* | 是 | 指出屬性物件是否包含其他選項，例如位置、標籤、SKU 或方案。
*id* | 是 | 自訂資源的資源識別碼。 此識別碼是自訂提供者資源識別碼的延伸。
*properties* | 是 | 將會傳送至端點的要求本文。

#### <a name="delete-a-custom-resource"></a>刪除自訂資源

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

參數 | 必要 | 描述
---|---|---
*id* | 是 | 自訂資源的資源識別碼。 此識別碼是自訂提供者資源識別碼的延伸。

#### <a name="retrieve-a-custom-resource"></a>擷取自訂資源

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

參數 | 必要 | 描述
---|---|---
*id* | 是 | 自訂資源的資源識別碼。 此識別碼是自訂提供者資源識別碼的延伸。

# <a name="template"></a>[範本](#tab/template)

Resource Manager 範本範例：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

參數 | 必要 | 描述
---|---|---
*resourceTypeName* | 是 | 自訂提供者中所定義 **resourceTypes** 的 `name` 值。
*resourceProviderName* | 是 | 自訂提供者執行個體名稱。
*customResourceName* | 是 | 自訂資源名稱。

---

> [!NOTE]
> 自訂提供者的部署和使用完成之後，請記得清除任何已建立的資源，包括 Azure 函式應用程式。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 如需詳細資訊，請參閱

- [操作說明：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [操作說明：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
