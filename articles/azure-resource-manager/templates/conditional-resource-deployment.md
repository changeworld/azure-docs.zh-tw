---
title: 使用範本的條件式部署
description: 說明如何在 Azure Resource Manager 範本中，有條件地將資源部署 (ARM 範本) 。
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 5650f7fb9f1483f2dc7059607732ecc68cbb7b9d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934776"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 範本中的條件式部署

有時您需要在 Azure Resource Manager 範本中選擇性地部署資源， (ARM 範本) 。 使用 `condition` 元素來指定是否要部署資源。 此元素的值會解析為 true 或 false。 當此值為 true 時，會部署資源。 當此值為 false 時，則不會部署資源。 此值只能套用至整個資源。

> [!NOTE]
> 條件式部署不會串聯至 [子資源](child-resource-name-type.md)。 如果您想要有條件地部署資源及其子資源，您必須將相同的條件套用至每個資源類型。

## <a name="new-or-existing-resource"></a>新的或現有的資源

您可以使用條件式部署來建立新的資源，或使用現有的資源。 下列範例示範如何使用 `condition` 部署新的儲存體帳戶或使用現有的儲存體帳戶。

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

當參數 `newOrExisting` 設定為 **new** 時，條件評估為 true。 已部署儲存體帳戶。 不過，當 `newOrExisting` 設為 [ **現有**] 時，條件會評估為 false，且不會部署儲存體帳戶。

如需使用 `condition` 項目的完整範例範本，請參閱[ 使用新的或現有的虛擬網路、儲存體和公用 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允許條件

您可以傳入指出是否允許條件的參數值。 下列範例會部署 SQL server，並選擇性地允許 Azure Ip。

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

如需完整的範本，請參閱 [AZURE SQL 邏輯伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>執行時間函式

如果您使用 [參考](template-functions-resource.md#reference) 或 [清單](template-functions-resource.md#list) 函式搭配有條件地部署的資源，即使資源未部署，也會評估函式。 如果函式參考的資源不存在，您會收到錯誤。

使用 [if](template-functions-logical.md#if) 函式，以確保只有在部署資源時，才會評估函式的條件。 請參閱 [if](template-functions-logical.md#if) 函式，以取得使用和搭配有 `if` `reference` 條件部署資源的範例範本。

您可以將 [資源設定為相依](define-resource-dependency.md) 于條件式資源，就像任何其他資源一樣。 當條件式資源未部署時，Azure Resource Manager 會自動將它從必要的相依性中移除。

## <a name="complete-mode"></a>完整模式

如果您以 [完整模式](deployment-modes.md) 部署範本，但因為評估為 false 而未部署資源 `condition` ，則結果取決於您用來部署範本的 REST API 版本。 如果您使用的版本早于2019-05-10，則 **不會刪除** 資源。 使用2019-05-10 或更新版本時， **會刪除** 資源。 當條件為 false 時，Azure PowerShell 和 Azure CLI 的最新版本會刪除資源。

## <a name="next-steps"></a>後續步驟

* 如需涵蓋條件式部署的 Microsoft Learn 模組，請參閱 [使用 ADVANCED ARM 範本功能管理複雜的雲端部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
* 如需建立範本的建議，請參閱 [ARM 範本的最佳做法](template-best-practices.md)。
* 若要建立資源的多個實例，請參閱 [ARM 範本中的資源反復](copy-resources.md)專案。
