---
title: 使用範本的條件式部署
description: 描述如何有條件地在 Azure Resource Manager 範本中部署資源。
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: effa7fe6ee1393e44a124bc087609da5d4898210
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84259315"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 範本中的條件式部署

有時候，您需要在 Azure Resource Manager （ARM）範本中選擇性地部署資源。 使用 `condition` 元素來指定是否部署資源。 此元素的值會解析為 true 或 false。 當此值為 true 時，會部署資源。 當此值為 false 時，則不會部署資源。 此值只能套用至整個資源。

> [!NOTE]
> 條件式部署不會 cascade 至[子資源](child-resource-name-type.md)。 如果您想要有條件地部署資源及其子資源，您必須將相同的條件套用至每個資源類型。

## <a name="new-or-existing-resource"></a>新的或現有的資源

您可以使用條件式部署來建立新的資源，或使用現有的資源。 下列範例顯示如何使用條件來部署新的儲存體帳戶，或使用現有的儲存體帳戶。

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

當參數**newOrExisting**設定為**new**時，條件會評估為 true。 已部署儲存體帳戶。 不過，當**newOrExisting**設定為 [**現有**] 時，條件會評估為 false，而且不會部署儲存體帳戶。

如需使用 `condition` 項目的完整範例範本，請參閱[ 使用新的或現有的虛擬網路、儲存體和公用 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允許條件

您可以傳入參數值，指出是否允許條件。 下列範例會部署 SQL server，並選擇性地允許 Azure Ip。

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

如需完整的範本，請參閱[AZURE SQL 邏輯伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>執行時間函式

如果您使用[reference](template-functions-resource.md#reference)或[list](template-functions-resource.md#list)函數搭配有條件地部署的資源，即使未部署資源，也會評估該函數。 如果函數參考不存在的資源，就會收到錯誤。

使用[if](template-functions-logical.md#if)函式可確保只有在部署資源時，才會針對條件評估函式。 如需對有條件部署的資源搭配使用 if 和 reference 的範例範本，請參閱 [if 函式](template-functions-logical.md#if)。

您可以將[資源設定為相依](define-resource-dependency.md)于條件式資源，就像任何其他資源一樣。 當條件式資源未部署時，Azure Resource Manager 會自動將其從所需的相依性移除。

## <a name="complete-mode"></a>完整模式

如果您部署具有[完整模式](deployment-modes.md)的範本，但因為條件評估為 false 而未部署資源，則結果會取決於您用來部署範本的 REST API 版本。 如果您使用早于2019-05-10 的版本，則**不會刪除**資源。 使用2019-05-10 或更新版本時，**會刪除**資源。 當條件為 false 時，Azure PowerShell 和 Azure CLI 的最新版本會刪除資源。

## <a name="next-steps"></a>後續步驟

* 如需建立範本的建議，請參閱[ARM 範本的最佳做法](template-best-practices.md)。
* 若要建立資源的多個實例，請參閱[ARM 範本中的資源反復](copy-resources.md)專案。
