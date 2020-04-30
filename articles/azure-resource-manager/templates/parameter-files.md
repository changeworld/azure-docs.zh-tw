---
title: 建立參數檔案
description: 建立在 Azure Resource Manager 範本部署期間傳入值的參數檔案
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a1a1f703594f8eaa572ea38ecef88b4cd6ba5a4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682901"
---
# <a name="create-resource-manager-parameter-file"></a>建立 Resource Manager 參數檔案

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 本文說明如何建立參數檔案。

## <a name="parameter-file"></a>參數檔案

參數檔案具有下列格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

請注意，參數值會以純文字的形式儲存在參數檔案中。 這種方法適用于不區分的值，例如指定資源的 SKU。 它不適用於機密值（例如密碼）。 如果您需要傳遞敏感值做為參數，請將值儲存在金鑰保存庫中，並在您的參數檔案中參考 key vault。 在部署期間，會安全地抓取敏感性值。

下列參數檔案包含純文字值和儲存在金鑰保存庫中的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

如需從金鑰保存庫使用值的詳細資訊，請參閱[使用 Azure Key Vault 在部署期間傳遞安全的參數值](key-vault-parameter.md)。

## <a name="define-parameter-values"></a>定義參數值

若要找出如何定義參數值，請開啟您要部署的範本。 查看範本的 parameters 區段。 下列範例會顯示範本中的參數。

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

要注意的第一個詳細資料是每個參數的名稱。 參數檔案中的值必須與名稱相符。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

請注意參數的類型。 參數檔案中的值必須具有相同的類型。 針對此範本，您可以將這兩個參數提供為字串。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

接下來，尋找預設值。 如果參數有預設值，您可以提供值，但您不需要這麼做。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

最後，請查看允許的值和任何限制，例如最大長度。 它們會告訴您可以為參數提供的值範圍。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

下列範例顯示不同參數類型的格式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

具名引數檔案的一般慣例是將**參數**新增至範本名稱。 例如，如果您的範本名為**azuredeploy.parameters.json」**，則您的參數檔案會命名為**azuredeploy.parameters.json」**。 此命名慣例可協助您查看範本和參數之間的連接。

若要部署至不同的環境，請建立一個以上的參數檔。 具名引數檔案時，請加入識別其使用方式的方法。 例如，使用**azuredeploy.parameters.json」-dev. json**和**azuredeploy.parameters.json」。**


## <a name="parameter-precedence"></a>參數優先順序

您可以在相同的部署作業中使用內嵌參數和本機參數檔案。 例如，您可以在部署期間指定本機參數檔案中的某些值，並新增其他內嵌值。 如果您同時為本機檔案中和內嵌的參數提供值，內嵌值的優先順序較高。

不過，當使用外部參數檔案時，您無法傳遞內嵌或本機檔案的其他值。 所有內嵌參數都會被忽略。 請提供外部檔案中的所有參數值。

## <a name="parameter-name-conflicts"></a>參數名稱衝突

如果您範本所含的參數名稱與 PowerShell 命令中的其中一個參數一樣，PowerShell 會以加上後置 **FromTemplate** 的方式呈現您範本中的參數。 例如，範本中名為 **ResourceGroupName** 的參數會與 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 您可以使用未用於部署命令的參數名稱來避免這種混淆。

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何在您的範本中定義參數，請參閱[Azure Resource Manager 範本中的參數](template-parameters.md)。
- 如需從金鑰保存庫使用值的詳細資訊，請參閱[使用 Azure Key Vault 在部署期間傳遞安全的參數值](key-vault-parameter.md)。
- 如需參數的詳細資訊，請參閱[Azure Resource Manager 範本中的參數](template-parameters.md)。
