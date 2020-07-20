---
title: 測試控管組的測試案例
description: 描述 ARM 範本測試控管組所執行的測試。
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 5c18a2658ba1af9370699004860d1743603e8143
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255738"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM 範本測試控管組的預設測試案例

本文說明使用[範本測試控管](test-toolkit.md)組執行的預設測試。 它提供通過或使測試失敗的範例。 其中包含每個測試的名稱。

## <a name="use-correct-schema"></a>使用正確的架構

測試名稱： **DeploymentTemplate 架構正確**

在您的範本中，您必須指定有效的架構值。

下列範例會**傳遞**這項測試。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

範本中的架構屬性必須設定為下列其中一個架構：

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>參數必須存在

測試名稱：**參數屬性必須存在**

您的範本應該具有 parameters 元素。 參數是讓範本在不同環境中可重複使用的必要條件。 針對部署至不同環境時變更的值，將參數新增至您的範本。

下列範例會**傳遞**這項測試：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>必須使用宣告的參數

測試名稱：**必須參考參數**

若要減少範本中的混淆，請刪除已定義但未使用的任何參數。 此測試會尋找範本中任何地方未使用的任何參數。 排除未使用的參數也可以讓您更輕鬆地部署範本，因為您不需要提供不必要的值。

## <a name="secure-parameters-cant-have-hardcoded-default"></a>安全參數不能有硬式編碼預設值

測試名稱：**安全字串參數不能有預設值**

請勿在您的範本中提供安全參數的硬式編碼預設值。 空白字串適用于預設值。

在包含機密值（例如密碼）的參數上，您可以使用類型**SecureString**或**SecureObject** 。 當參數使用安全類型時，不會記錄參數的值，或將其儲存在部署歷程記錄中。 此動作可防止惡意使用者探索敏感性值。

不過，當您提供預設值時，可以存取範本或部署歷程記錄的任何人都會發現該值。

下列範例**會失敗**這項測試：

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

下一個範例會**傳遞**此測試：

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="location-uses-parameter"></a>Location 使用參數

測試名稱：**位置不應為硬式編碼**

您範本的使用者可能會有有限的區域可供使用。 當您將資源位置設定為時 `"[resourceGroup().location]"` ，可能已在其他使用者無法存取的區域中建立資源群組。 這些使用者會被封鎖而無法使用範本。

定義每個資源的位置時，請使用預設為資源群組位置的參數。 藉由提供此參數，使用者可在方便時使用預設值，但也可以指定不同的位置。

下列範例**會失敗**這項測試，因為資源上的位置設定為 `resourceGroup().location` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

下一個範例會使用 location 參數，但此測試**會失敗**，因為 location 參數預設為硬式編碼的位置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

相反地，請建立預設為資源群組位置的參數，但允許使用者提供不同的值。 下列範例會**傳遞**這項測試。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="resources-should-have-location"></a>資源應該有位置

測試名稱：**資源應該有位置**

資源的位置應該設定為[範本運算式](template-expressions.md)或 `global` 。 範本運算式通常會使用先前測試中所述的 location 參數。

下列範例**會失敗**這項測試，因為位置不是運算式或 `global` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

下列範例會**傳遞**這項測試。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

下一個範例也會**傳遞**這種測試。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM 大小使用參數

測試名稱： **VM 大小應為參數**

不要硬式編碼虛擬機器大小。 提供參數，讓您範本的使用者可以修改已部署虛擬機器的大小。

下列範例**會失敗**這項測試。

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

請改為提供參數。

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

然後，將 VM 大小設定為該參數。

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>最小和最大值為數字

測試名稱：**最小和最大值為數字**

如果您定義參數的 min 和 max 值，請將其指定為數字。

下列範例**會失敗**這項測試：

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

請改為以數位形式提供值。 下列範例會**傳遞**這項測試：

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

如果您提供最小或最大值，而不是其他值，也會收到這個警告。

## <a name="artifacts-parameter-defined-correctly"></a>成品參數定義正確

測試名稱：**構件-參數**

當您包含和的參數時 `_artifactsLocation` `_artifactsLocationSasToken` ，請使用正確的預設值和類型。 若要通過這項測試，必須符合下列條件：

* 如果您提供一個參數，您必須提供另一個參數
* `_artifactsLocation`必須是**字串**
* `_artifactsLocation`在主要範本中必須有預設值
* `_artifactsLocation`在嵌套的範本中不能有預設值 
* `_artifactsLocation`的 `"[deployment().properties.templateLink.uri]"` 預設值必須是或原始存放庫 URL
* `_artifactsLocationSasToken`必須是**secureString**
* `_artifactsLocationSasToken`其預設值只能有空字串
* `_artifactsLocationSasToken`在嵌套的範本中不能有預設值 

## <a name="declared-variables-must-be-used"></a>必須使用宣告的變數

測試名稱：**必須參考變數**

若要減少範本中的混淆，請刪除已定義但未使用的任何變數。 這項測試會尋找範本中任何地方未使用的變數。

## <a name="dynamic-variable-should-not-use-concat"></a>動態變數不應使用 concat

測試名稱：**動態變數參考不應使用 Concat**

有時候，您需要根據另一個變數或參數的值，以動態方式來建立變數。 設定值時，請勿使用[concat](template-functions-string.md#concat)函數。 相反地，請使用包含可用選項的物件，並在部署期間從物件動態取得其中一個屬性。

下列範例會**傳遞**這項測試。 **CurrentImage**變數會在部署期間以動態方式設定。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>使用最新的 API 版本

測試名稱： **ApiVersions 應該是最新**的

每個資源的 API 版本都應該使用最新版本。 測試會評估您用於該資源類型可用版本的版本。

## <a name="use-hardcoded-api-version"></a>使用硬式編碼 API 版本

測試名稱：**不允許提供者 apiVersions**

資源類型的 API 版本會決定可用的屬性。 在您的範本中提供硬式編碼的 API 版本。 不要取出在部署期間決定的 API 版本。 您不知道有哪些可用的屬性。

下列範例**會失敗**這項測試。

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

下列範例會**傳遞**這項測試。

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>屬性不可以是空的

測試名稱：**範本不應包含空白**

不要將屬性硬式編碼為空值。 空值包括 null 和空字串、物件或陣列。 如果您已將屬性設定為空值，請從您的範本移除該屬性。 不過，您可以在部署期間將屬性設為空白值，例如透過參數。

## <a name="use-resource-id-functions"></a>使用資源識別碼函式

測試名稱：**識別碼應該衍生自資源識別碼**

指定資源識別碼時，請使用其中一個資源識別碼函數。 允許的函式為：

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

請勿使用 concat 函數來建立資源識別碼。 下列範例**會失敗**這項測試。

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

下一個範例會**傳遞**這種測試。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 函式具有正確的參數

測試名稱：**資源識別碼不應包含**

產生資源識別碼時，請勿針對選擇性參數使用不必要的函數。 根據預設， [resourceId](template-functions-resource.md#resourceid)功能會使用目前的訂用帳戶和資源群組。 您不需要提供這些值。  

下列範例會使這項測試**失敗**，因為您不需要提供目前的訂用帳戶識別碼和資源組名。

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

下一個範例會**傳遞**這種測試。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

此測試適用于：

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

`reference`若為和 `list*` ，當您使用來建立資源識別碼時，測試就**會失敗** `concat` 。

## <a name="dependson-cant-be-conditional"></a>dependsOn 不可以是條件式

測試名稱： **DependsOn 不得為條件**式

設定部署相依性時，請勿使用[if](template-functions-logical.md#if)函式來測試條件。 如果其中一個資源依存于有條件地[部署](conditional-resource-deployment.md)的資源，請設定相依性，就像使用任何資源一樣。 當條件式資源未部署時，Azure Resource Manager 會自動將其從所需的相依性移除。

下列範例**會失敗**這項測試。

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

下一個範例會**傳遞**這種測試。

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>嵌套或連結的部署無法使用 debug

測試名稱：**部署資源不得為 Debug**

當您使用 [ **Microsoft .resources/部署**] 資源類型定義[嵌套或連結的範本](linked-templates.md)時，您可以啟用該範本的偵錯工具。 當您需要測試該範本時，如果您已準備好在生產環境中使用該範本，則會進行偵錯工具。

## <a name="admin-user-names-cant-be-literal-value"></a>管理員使用者名稱不可以是常值

測試名稱： **AdminUsername 不應為常**值

設定系統管理員使用者名稱時，請不要使用常值。

下列範例**會失敗**這項測試：

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

請改用參數。 下列範例會**傳遞**這項測試：

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>使用最新的 VM 映射

測試名稱： **VM 映射應該使用最新版本**

如果您的範本包含具有映射的虛擬機器，請確定它使用的是最新版的映射。

## <a name="use-stable-vm-images"></a>使用穩定的 VM 映射

測試名稱：**虛擬機器-不應為預覽**狀態

虛擬機器不應使用預覽映射。

下列範例**會失敗**這項測試。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

下列範例會**傳遞**這項測試。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>不要使用 Microsoft.managedidentity 延伸模組

測試名稱：**不得使用 ManagedIdentityExtension**

請勿將 Microsoft.managedidentity 擴充功能套用至虛擬機器。 如需詳細資訊，請參閱[如何停止使用虛擬機器受控識別擴充功能和開始使用 Azure Instance Metadata Service](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md)。

## <a name="outputs-cant-include-secrets"></a>輸出不能包含秘密

測試名稱：**輸出不得包含秘密**

請勿在輸出區段中包含任何可能公開秘密的值。 範本的輸出會儲存在部署歷程記錄中，因此惡意使用者可以找到該資訊。

下列範例**會失敗**測試，因為它在輸出值中包含安全參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

下列範例**會失敗**，因為它會在輸出中使用[list *](template-functions-resource.md#list)函數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何執行測試控管組，請參閱[使用 ARM 範本測試控管](test-toolkit.md)組。