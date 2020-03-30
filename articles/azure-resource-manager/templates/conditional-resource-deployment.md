---
title: 帶範本的條件部署
description: 描述如何在 Azure 資源管理器範本中有條件地部署資源。
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153415"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 範本中的條件部署

有時，您需要在 Azure 資源管理器 （ARM） 範本中選擇部署資源。 使用`condition`元素指定是否部署了資源。 此元素的值會解析為 true 或 false。 當此值為 true 時，會部署資源。 當此值為 false 時，則不會部署資源。 此值只能套用至整個資源。

## <a name="new-or-existing-resource"></a>新資源或現有資源

可以使用條件部署創建新資源或使用現有資源。 下面的示例演示如何使用條件部署新的存儲帳戶或使用現有存儲帳戶。

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

當參數**new or 存在**設置為**新**時，條件將計算為 true。 已部署存儲帳戶。 但是，當**new Or 存在**設置為**現有**時，條件將評估為 false，並且未部署存儲帳戶。

如需使用 `condition` 項目的完整範例範本，請參閱[ 使用新的或現有的虛擬網路、儲存體和公用 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允許條件

可以傳遞一個參數值，指示條件是否允許。 下面的示例部署 SQL 伺服器，並可以選擇允許 Azure IP。

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

有關完整的範本，請參閱[Azure SQL 邏輯伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>運行時函數

如果將[引用](template-functions-resource.md#reference)或[清單](template-functions-resource.md#list)函數與有條件部署的資源一起使用，則即使未部署資源，也會計算該函數。 如果函數引用不存在的資源，則收到錯誤。

使用[if](template-functions-logical.md#if)函數確保僅在部署資源時為條件計算函數。 有關使用 if 和 引用有條件部署的資源的示例範本的 if 函數，請參閱[if 函數](template-functions-logical.md#if)。

將[資源設置為與條件資源完全一樣依賴于](define-resource-dependency.md)任何其他資源。 未部署條件資源時，Azure 資源管理器會自動將其從所需的依賴項中刪除。

## <a name="condition-with-complete-mode"></a>具有完整模式的條件

如果部署具有[完整模式](deployment-modes.md)的範本，並且由於條件評估為 false 而未部署資源，則結果取決於用於部署範本的 REST API 版本。 如果使用的版本早于 2019-05-10，**則不會刪除**資源。 使用 2019-05-10 或更高版本，**資源將被刪除**。 當條件為 false 時，Azure PowerShell 和 Azure CLI 的最新版本將刪除資源。

## <a name="next-steps"></a>後續步驟

* 有關創建範本的建議，請參閱[ARM 範本最佳實踐](template-best-practices.md)。
* 要創建資源的多個實例，請參閱 ARM[範本中的資源反覆運算](copy-resources.md)。