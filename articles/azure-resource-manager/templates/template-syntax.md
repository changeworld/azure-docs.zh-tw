---
title: 範本結構和語法
description: 使用宣告式 JSON 語法描述 Azure Resource Manager 範本的結構和屬性。
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: b7cf30741cfd2b85046f64fddf01c414676a97e4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95911493"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>了解 ARM 範本的結構和語法 \(部分機器翻譯\)

本文說明 Azure Resource Manager (ARM) 範本的結構。 它會呈現範本的不同區段，以及這些區段中可用的屬性。

本文適用于對 ARM 範本有一些熟悉度的使用者。 它會提供範本結構的詳細資訊。 如需引導您完成建立範本之程式的逐步教學課程，請參閱 [教學課程：建立及部署您的第一個 Azure Resource Manager 範本](template-tutorial-create-first-template.md)。

## <a name="template-format"></a>範本格式

在最簡單的結構中，範本具有下列元素：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 元素名稱 | 必要 | 描述 |
|:--- |:--- |:--- |
| $schema |是 |JSON 結構描述檔案的位置，說明範本語言的版本。 您所使用的版本號碼取決於部署範圍以及您的 JSON 編輯器。<br><br>如果您使用 [VS Code 搭配 Azure Resource Manager 工具擴充](quickstart-create-templates-use-visual-studio-code.md)功能，請使用最新版本進行資源群組部署：<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>其他編輯器 (包括 Visual Studio) 可能無法處理此架構。 針對這些編輯器，請使用：<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>針對訂用帳戶部署，使用：<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>針對管理群組部署，請使用：<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>針對租使用者部署，請使用：<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |是 |範本版本 (例如 1.0.0.0)。 您可以為此元素提供任何值。 使用此值在範本中記載重大變更。 使用範本部署資源時，這個值可用來確定使用的是正確的範本。 |
| apiProfile |否 | API 版本，作為資源類型的 API 版本集合。 使用此值，以避免必須為範本中的每個資源指定 API 版本。 當您指定 API 設定檔版本，但未指定資源類型的 API 版本時，Resource Manager 會使用設定檔中所定義之資源類型的 API 版本。<br><br>將範本部署到不同的環境（例如 Azure Stack 和全域 Azure）時，API 配置檔案屬性特別有用。 使用 API 設定檔版本，確保您的範本會自動使用兩個環境中支援的版本。 如需目前的 API 設定檔版本和設定檔中所定義之資源 API 版本的清單，請參閱 [API 設定檔](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)。<br><br>如需詳細資訊，請參閱 [使用 API 設定檔來追蹤版本](templates-cloud-consistency.md#track-versions-using-api-profiles)。 |
| [parameters](#parameters) |否 |執行部署以自訂資源部署時所提供的值。 |
| [變數](#variables) |否 |範本中做為 JSON 片段以簡化範本語言運算式的值。 |
| [功能](#functions) |否 |範本中可用的使用者定義函式。 |
| [資源](#resources) |是 |在資源群組或訂用帳戶中部署或更新的資源類型。 |
| [outputs](#outputs) |否 |部署後傳回的值。 |

每個元素都有可以設定的屬性。 本文將詳細說明範本的各個區段。

## <a name="data-types"></a>資料類型

在 ARM 範本內，您可以使用下列資料類型：

* 字串
* securestring
* int
* bool
* object
* secureObject
* array

下列範本會顯示資料類型的格式。 每個類型都有正確格式的預設值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "boolParameter": {
        "type": "bool",
        "defaultValue": true
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    }
  },
  "resources": [],
  "outputs": {}
}
```

安全字串使用與字串相同的格式，而安全物件則使用與物件相同的格式。 當您將參數設定為安全字串或安全物件時，參數的值不會儲存到部署記錄中，而且不會記錄。 但是，如果您將該安全值設定為不需要安全值的屬性，此值就不會受到保護。 例如，如果您將安全字串設定為標記，該值就會儲存為純文字。 針對密碼和密碼使用安全字串。

針對以內嵌參數傳遞的整數，值的範圍可能會受到您用於部署的 SDK 或命令列工具的限制。 例如，使用 PowerShell 部署範本時，整數類型的範圍可以從-2147483648 到2147483647。 若要避免這項限制，請在 [參數](parameter-files.md)檔案中指定大型整數值。 資源類型會對整數屬性套用自己的限制。

在您的範本中指定布林值和整數值時，請勿使用引號來括住值。 以雙引號括住的開頭和結尾字串值。

物件是以左大括弧開頭，並以右大括弧結尾。 陣列是以左括弧開頭，並以右括弧結尾。

## <a name="parameters"></a>參數

在範本的 parameters 區段中，您要指定可以在部署資源時輸入的值。 一個範本的限制為 256 個參數。 您可以使用包含多個屬性的物件來減少參數數目。

參數的可用屬性包括：

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| 元素名稱 | 必要 | 描述 |
|:--- |:--- |:--- |
| 參數-名稱 |是 |參數的名稱。 必須是有效的 JavaScript 識別碼。 |
| 類型 |是 |參數值類型。 允許的類型和值為 **string**、**securestring**、**int**、**bool**、**object**、**secureObject**，以及 **array**。 請參閱 [資料類型](#data-types)。 |
| defaultValue |否 |如果未提供參數值，則會使用參數的預設值。 |
| allowedValues |否 |參數的允許值陣列，確保提供正確的值。 |
| minValue |否 |int 類型參數的最小值，含此值。 |
| maxValue |否 |int 類型參數的最大值，含此值。 |
| minLength |否 |字串、securestring 及陣列類型參數長度的最小值，含此值。 |
| maxLength |否 |字串、securestring 及陣列類型參數長度的最大值，含此值。 |
| description |否 |透過入口網站向使用者顯示的參數說明。 如需詳細資訊，請參閱[範本中的註解](#comments)。 |

如需如何使用參數的範例，請參閱 [Azure Resource Manager 範本中的參數](template-parameters.md)。

## <a name="variables"></a>變數

在 variables 區段中，您會建構可用於整個範本中的值。 您不需要定義變數，但它們通常會經由減少複雜運算式來簡化您的範本。 每個變數的格式都符合其中一個 [資料類型](#data-types)。

下列範例顯示用於定義變數的可用選項：

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

如需使用 `copy` 為變數建立數個值的詳細資訊，請參閱 [變數反復](copy-variables.md)專案。

如需如何使用變數的範例，請參閱 [Azure Resource Manager 範本中的變數](template-variables.md)。

## <a name="functions"></a>函數

在您的範本內，您可以建立自己的函式。 這些函式可供您在範本中使用。 一般而言，您會定義不想在整個範本中重複的複雜運算式。 您會從範本中支援的運算式和[函式](template-functions.md)建立使用者定義的函式。

在定義使用者函式時，有一些限制：

* 此函式無法存取變數。
* 此函式只能使用函式中定義的參數。 當您在使用者自訂函數中使用 [parameters](template-functions-deployment.md#parameters) 函式時，您會被限制為該函數的參數。
* 此函式無法呼叫其他的使用者定義函式。
* 此函式不能使用[參考函式](template-functions-resource.md#reference)。
* 函式的參數不能有預設值。

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| 元素名稱 | 必要 | 描述 |
|:--- |:--- |:--- |
| namespace |是 |自訂函式的命名空間。 使用以避免與範本函式發生名稱衝突。 |
| 函數名稱 |是 |自訂函數的名稱。 呼叫函式時，請將函數名稱與命名空間合併。 例如，若要在 contoso 命名空間中呼叫名為 uniqueName 的函式，請使用 `"[contoso.uniqueName()]"` 。 |
| 參數-名稱 |否 |要在自訂函數中使用的參數名稱。 |
| 參數-值 |否 |參數值類型。 允許的類型和值為 **string**、**securestring**、**int**、**bool**、**object**、**secureObject**，以及 **array**。 |
| 輸出類型 |是 |輸出值的類型。 輸出值支援與函數輸入參數相同的類型。 |
| 輸出-值 |是 |從函式評估並傳回的範本語言運算式。 |

如需如何使用自訂函數的範例，請參閱 [Azure Resource Manager 範本中的使用者定義函數](template-user-defined-functions.md)。

## <a name="resources"></a>資源

在資源區段中，您會定義要部署或更新的資源。

您會定義結構如下的資源：

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 元素名稱 | 必要 | 描述 |
|:--- |:--- |:--- |
| condition (條件) | 否 | 布林值，指出是否會在此部署期間佈建資源。 若為 `true`，就會在部署期間建立資源。 若為 `false`，則會略過此部署的資源。 請參閱 [條件](conditional-resource-deployment.md)。 |
| 類型 |是 |資源類型。 此值是資源提供者的命名空間與資源類型 (的組合，例如 **Microsoft. Storage/storageAccounts**) 。 若要判斷可用的值，請參閱 [範本參考](/azure/templates/)。 針對子資源，類型的格式取決於其是否在父資源內進行嵌套，或在父資源外部定義。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |
| apiVersion |是 |要用來建立資源的 REST API 版本。 若要判斷可用的值，請參閱 [範本參考](/azure/templates/)。 |
| NAME |是 |資源名稱。 此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。 將資源名稱公開到外部合作物件的 Azure 服務會驗證該名稱，以確保它不會嘗試偽造其他身分識別。 針對子資源，名稱的格式取決於其是否在父資源內進行嵌套，或在父資源外部定義。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |
| comments |否 |您在範本中記錄資源的註解。 如需詳細資訊，請參閱[範本中的註解](template-syntax.md#comments)。 |
| location |不定 |所提供資源的支援地理位置。 您可以選取任何可用的位置，但通常選擇接近您的使用者的位置很合理。 通常，將彼此互動的資源放在相同區域也合乎常理。 大部分的資源類型都需要有位置，但某些類型 (例如角色指派) 不需要位置。 請參閱 [設定資源位置](resource-location.md)。 |
| dependsOn |否 |在部署這項資源之前必須部署的資源。 Resource Manager 會評估資源之間的相依性，並依正確的順序進行部署。 資源若不互相依賴，則會平行部署資源。 值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。 只會列出此範本中已部署的資源。 此範本中未定義的資源必須已經存在。 避免加入不必要的相依性，因為可能會降低部署速度並產生循環相依性。 如需設定相依性的指引，請參閱[定義 Azure Resource Manager 範本中的相依性](define-resource-dependency.md)。 |
| tags |否 |與資源相關聯的標記。 套用標籤，既可以邏輯方式組織訂用帳戶中的資源。 |
| sku | 否 | 某些資源允許以值定義要部署的 SKU。 例如，您可以指定儲存體帳戶的備援類型。 |
| kind | 否 | 某些資源允許以值定義您所部署的資源類型。 例如，您可以指定要建立的 Cosmos DB 類型。 |
| copy |否 |如果需要多個執行個體，要建立的資源數目。 預設模式為平行。 如果您不想要同時部署所有或某些資源，請指定序列模式。 如需詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的數個執行個體](copy-resources.md)。 |
| 計劃 | 否 | 某些資源允許以值定義要部署的計劃。 例如，您可以指定虛擬機器的 Marketplace 映像。 |
| properties |否 |資源特定的組態設定。 屬性的值和您在 REST API 作業 (PUT 方法) 要求主體中提供來建立資源的值是一樣的。 您也可以指定複本陣列，以建立屬性的數個執行個體。 若要判斷可用的值，請參閱 [範本參考](/azure/templates/)。 |
| resources |否 |與正在定義的資源相依的下層資源。 只提供父資源的結構描述允許的資源類型。 沒有隱含父資源的相依性。 您必須明確定義該相依性。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |

## <a name="outputs"></a>輸出

在輸出區段中，您可以指定從部署傳回的值。 通常，您會從已部署的資源傳回值。

下列範例顯示輸出定義的結構：

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| 元素名稱 | 必要 | 描述 |
|:--- |:--- |:--- |
| 輸出-名稱 |是 |輸出值的名稱。 必須是有效的 JavaScript 識別碼。 |
| condition (條件) |否 | 布林值，指出是否傳回此輸出值。 當為 `true` 時，該值會包含在部署的輸出中。 若為 `false`，則會略過此部署的輸出值。 未指定時，預設值為 `true`。 |
| 類型 |是 |輸出值的類型。 輸出值支援與範本輸入參數相同的類型。 如果您針對輸出類型指定 **securestring** ，此值就不會顯示在部署歷程記錄中，也無法從另一個範本中取出。 若要在多個範本中使用秘密值，請將秘密儲存在 Key Vault 中，並在參數檔中參考密碼。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](key-vault-parameter.md)。 |
| value |否 |評估並傳回做為輸出值的範本語言運算式。 請指定 **值** 或 **複製**。 |
| copy |否 | 用來傳回一個以上的輸出值。 指定 **值** 或 **複製**。 如需詳細資訊，請參閱 [Azure Resource Manager 範本中的輸出反復](copy-outputs.md)專案。 |

如需如何使用輸出的範例，請參閱 [Azure Resource Manager 範本中的輸出](template-outputs.md)。

<a id="comments"></a>

## <a name="comments-and-metadata"></a>註解與中繼資料

有幾個選項可將註解與中繼資料新增到您的範本中。

### <a name="comments"></a>註解

如果是內嵌批註，您可以使用 `//` 或， `/* ... */` 但此語法不適用於所有工具。 如果您新增這種樣式的註解，請務必使用支援內嵌 JSON 註解的工具。

> [!NOTE]
> 若要使用2.3.0 版或更舊版本的 Azure CLI 來部署具有批註的範本，您必須使用 `--handle-extended-json-format` 參數。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

在 Visual Studio Code 中， [Azure Resource Manager 工具延伸](quickstart-create-templates-use-visual-studio-code.md) 模組可以自動偵測 Resource Manager 範本，並據以變更語言模式。 如果您在 VS Code 右下角看到 **Azure Resource Manager 範本** ，則可以使用內嵌批註。 內嵌註解不再被標示為無效。

![Visual Studio Code Azure Resource Manager 範本模式](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>中繼資料

幾乎在範本的任何位置都可以新增 `metadata` 物件。 Resource Manager 會略過該物件，但您的 JSON 編輯器可能會警告您該屬性無效。 在物件中，定義您需要的屬性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

對於 **參數**，新增 `description` 屬性的 `metadata` 物件。

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

透過入口網站部署範本時，您在描述中提供的文字會自動做為該參數的提示。

![顯示參數提示](./media/template-syntax/show-parameter-tip.png)

對於 **資源**，新增 `comments` 項目或中繼資料物件。 下列範例顯示註解項目和中繼資料物件。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

對於 **輸出**，將中繼資料物件新增至輸出值。

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

您無法將中繼資料物件新增至使用者定義函式。

## <a name="multi-line-strings"></a>多行字串

您可以將字串分割成多行。 例如，請參閱 location 屬性和下列 JSON 範例中的其中一個批註。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

若要使用2.3.0 版或更舊版本的 Azure CLI 來部署具有多行字串的範本，您必須使用 `--handle-extended-json-format` 參數。

## <a name="next-steps"></a>後續步驟

* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](template-functions.md)。
* 若要在部署期間合併數個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。
* 如需建立範本的建議，請參閱 [Azure Resource Manager 範本最佳做法](template-best-practices.md)。
* 如需常見問題的解答，請參閱 [ARM 範本的常見問題](frequently-asked-questions.md)。
