---
title: 建立參數檔案
description: 創建參數檔，用於在部署 Azure 資源管理器範本期間傳入值
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: bb52d9c5ebcb0820362e5de3d6b24b0b18d742e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155616"
---
# <a name="create-resource-manager-parameter-file"></a>創建資源管理器參數檔

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 本文演示如何創建參數檔。

## <a name="parameter-file"></a>參數檔案

參數檔具有以下格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

請注意，參數值在參數檔中以純文字形式存儲。 此方法適用于不敏感的值，例如為資源指定 SKU。 它不適合敏感值，如密碼。 如果需要將敏感值作為參數傳遞，請將該值存儲在金鑰保存庫中，並在參數檔中引用金鑰保存庫。 在部署期間安全地檢索敏感值。

以下參數檔包括純文字值和存儲在金鑰保存庫中的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

有關使用金鑰保存庫中的值的詳細資訊，請參閱使用[Azure 金鑰保存庫在部署期間傳遞安全參數值](key-vault-parameter.md)。

## <a name="define-parameter-values"></a>定義參數值

要瞭解如何定義參數值，請打開要部署的範本。 查看範本的參數部分。 下面的示例顯示了範本中的參數。

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

需要注意的第一個詳細資訊是每個參數的名稱。 參數檔中的值必須與名稱匹配。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

請注意參數的類型。 參數檔中的值必須具有相同的類型。 對於此範本，可以將兩個參數作為字串提供。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

接下來，查找預設值。 如果參數具有預設值，則可以提供值，但不必提供。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

最後，查看允許的值和任何限制（如最大長度）。 它們告訴您可以為參數提供的值範圍。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>參數類型格式

下面的示例顯示了不同參數類型的格式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>檔案名稱

具名引數檔的一般約定是向範本名稱添加 **.參數**。 例如，如果範本名為**azuredeploy.json，** 則參數檔將命名為**azuredeploy.parameter.json**。 此命名約定可説明您查看範本和參數之間的連接。

要部署到不同的環境，請創建多個參數檔。 具名引數檔時，添加一種方法來標識其用途。 例如，使用**azuredeploy.參數-dev.json**和**azuredeploy.參數-prod.json**


## <a name="parameter-precedence"></a>參數優先順序

您可以在相同的部署作業中使用內嵌參數和本機參數檔案。 例如，您可以在部署期間指定本機參數檔案中的某些值，並新增其他內嵌值。 如果您同時為本機檔案中和內嵌的參數提供值，內嵌值的優先順序較高。

不過，當使用外部參數檔案時，您無法傳遞內嵌或本機檔案的其他值。 將忽略所有內聯參數。 請提供外部檔案中的所有參數值。

## <a name="parameter-name-conflicts"></a>參數名稱衝突

如果您範本所含的參數名稱與 PowerShell 命令中的其中一個參數一樣，PowerShell 會以加上後置 **FromTemplate** 的方式呈現您範本中的參數。 例如，範本中名為 **ResourceGroupName** 的參數會與 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 通過使用不用於部署命令的參數名稱，可以避免這種混淆。

## <a name="next-steps"></a>後續步驟

- 要瞭解如何在範本中定義參數，請參閱 Azure[資源管理器範本中的參數](template-parameters.md)。
- 有關使用金鑰保存庫中的值的詳細資訊，請參閱使用[Azure 金鑰保存庫在部署期間傳遞安全參數值](key-vault-parameter.md)。
- 有關參數的詳細資訊，請參閱 Azure[資源管理器範本中的參數](template-parameters.md)。
