---
title: 範本資源位置
description: 介紹如何在 Azure 資源管理器範本中設置資源位置。
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156424"
---
# <a name="set-resource-location-in-arm-template"></a>在 ARM 範本中設置資源位置

部署 Azure 資源管理器 （ARM） 範本時，必須為每個資源提供位置。 該位置不需要與資源組位置位於同一位置。

## <a name="get-available-locations"></a>獲取可用位置

不同的位置支援不同的資源類型。 要獲取資源類型的受支援位置，請使用 Azure PowerShell 或 Azure CLI。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>使用位置參數

為了在部署範本時允許靈活性，請使用參數指定資源的位置。 將參數的預設值設置為`resourceGroup().location`。

下列範例顯示部署至某個 (已指定為參數) 位置的儲存體帳戶：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

* 如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](template-functions.md)。
* 有關範本檔的詳細資訊，請參閱瞭解[ARM 範本的結構和語法](template-syntax.md)。
