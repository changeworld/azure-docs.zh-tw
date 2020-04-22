---
title: 樣本結構和語法
description: 使用宣告式 JSON 語法描述 Azure Resource Manager 範本的結構和屬性。
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 60d800eb5251fb3454ba60a67bd109261c6ff9d4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687873"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>瞭解 ARM 樣本結構和語法

本文介紹 Azure 資源管理員 (ARM) 範本的結構。 它會呈現範本的不同區段，以及這些區段中可用的屬性。

本文面向熟悉 ARM 範本的使用者。 它提供有關範本結構的詳細資訊。 有關指導您完成範本建立過程的分步教程,請參閱[教學:建立和部署第一個 Azure 資源管理員樣本](template-tutorial-create-first-template.md)。

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
| $schema |是 |JSON 結構描述檔案的位置，說明範本語言的版本。 您使用的版本號取決於部署範圍和 JSON 編輯器。<br><br>如果將[VS 代碼與 Azure 資源管理員工具延伸一起使用](use-vs-code-to-create-template.md),請使用最新版本進行資源群組部署:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>其他編輯器(包括 Visual Studio)可能無法處理此架構。 對於這些編輯器,請使用:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>針對訂用帳戶部署，使用：<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>對管理群組部署,請使用:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>對於租戶部署,請使用:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |是 |範本版本 (例如 1.0.0.0)。 您可以為此元素提供任何值。 使用此值在範本中記載重大變更。 使用範本部署資源時，這個值可用來確定使用的是正確的範本。 |
| apiProfile |否 | 用作資源類型的 API 版本的集合的 API 版本。 使用此值可避免為範本中的每個資源指定 API 版本。 當您指定 API 設定檔版本並且不為資源類型指定 API 版本時,資源管理器會使用設定檔中定義的資源類型的 API 版本。<br><br>將範本部署到不同的環境(如 Azure 堆疊和全域 Azure)時,API 配置檔屬性特別有用。 使用 API 設定檔版本確保樣本自動使用兩個環境都支援的版本。 有關目前 API 設定檔版本的清單和設定檔中定義的資源 API 版本,請參考[API 設定檔](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)。<br><br>關於詳細資訊,請參考[API 設定檔追蹤版本](templates-cloud-consistency.md#track-versions-using-api-profiles)。 |
| [參數](#parameters) |否 |執行部署以自訂資源部署時所提供的值。 |
| [變數](#variables) |否 |範本中做為 JSON 片段以簡化範本語言運算式的值。 |
| [功能](#functions) |否 |範本中可用的使用者定義函式。 |
| [資源](#resources) |是 |在資源群組或訂用帳戶中部署或更新的資源類型。 |
| [輸出](#outputs) |否 |部署後傳回的值。 |

每個元素都有可以設定的屬性。 本文將詳細說明範本的各個區段。

## <a name="parameters"></a>參數

在範本的 parameters 區段中，您會指定可在部署資源時輸入的值。 範本中限制使用 256 個參數。 通過使用包含多個屬性的物件,可以減少參數數。

參數的可用屬性包括:

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
| 參數名稱 |是 |參數的名稱。 必須是有效的 JavaScript 識別碼。 |
| type |是 |參數值類型。 允許的類型和值為 **string**、**securestring**、**int**、**bool**、**object**、**secureObject**，以及 **array**。 請參考[資料型態](#data-types)。 |
| defaultValue |否 |如果未提供參數值，則會使用參數的預設值。 |
| allowedValues |否 |參數的允許值陣列，確保提供正確的值。 |
| minValue |否 |int 類型參數的最小值，含此值。 |
| maxValue |否 |int 類型參數的最大值，含此值。 |
| minLength |否 |字串、securestring 及陣列類型參數長度的最小值，含此值。 |
| maxLength |否 |字串、securestring 及陣列類型參數長度的最大值，含此值。 |
| description |否 |透過入口網站向使用者顯示的參數說明。 如需詳細資訊，請參閱[範本中的註解](#comments)。 |

有關如何使用參數的範例,請參考[Azure 資源管理員樣本中的參數](template-parameters.md)。

### <a name="data-types"></a>資料類型

對於作為內聯參數傳遞的整數,值範圍可能受用於部署的 SDK 或命令列工具的限制。 例如,當使用 PowerShell 部署範本時,整數類型的範圍可以從 -2147483648 到 2147483647。 為了避免此限制,請在[參數檔中](parameter-files.md)指定較大的整數值。 資源類型對整數屬性應用其自己的限制。

在範本中指定布林值和整數值時,不要用引弧括括值。 使用雙引號開始和結束字串值。

物件以左大括弧開始,以右大括弧結尾。 陣列從左括弧開始,以右括弧結尾。

資源部署後無法讀取安全字串和安全物件。

有關格式化資料類型的範例,請參考[參數類型格式](parameter-files.md#parameter-type-formats)。

## <a name="variables"></a>變數

在 variables 區段中，您會建構可用於整個範本中的值。 您不需要定義變數，但它們通常會經由減少複雜運算式來簡化您的範本。

下面的範例顯示了用於定義變數的可用選項:

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

有關使用為`copy`變數建立多個值的資訊,請參閱[變數的發運算](copy-variables.md)。

有關如何使用變數的範例,請參閱[Azure 資源管理員樣本中的變數](template-variables.md)。

## <a name="functions"></a>函式

在您的範本內，您可以建立自己的函式。 這些函式可供您在範本中使用。 通常,您定義不希望在整個範本中重複的複雜表達式。 您會從範本中支援的運算式和[函式](template-functions.md)建立使用者定義的函式。

在定義使用者函式時，有一些限制：

* 此函式無法存取變數。
* 此函式只能使用函式中定義的參數。 在使用者定義的函數中使用[參數函數](template-functions-deployment.md#parameters)時,將限制為該函數的參數。
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
| namespace |是 |自定義函數的命名空間。 用於避免與範本函數的命名衝突。 |
| 函數名稱 |是 |自定義函數的名稱。 調用函數時,將函數名稱與命名空間合併。 例如,要在命名空間 contoso 中呼叫名為唯一名稱的`"[contoso.uniqueName()]"`函數 ,請使用 。 |
| 參數名稱 |否 |要在自定義函數中使用的參數的名稱。 |
| parameter-value |否 |參數值類型。 允許的類型和值為 **string**、**securestring**、**int**、**bool**、**object**、**secureObject**，以及 **array**。 |
| 輸出類型 |是 |輸出值的類型。 輸出值支援與函數輸入參數相同的類型。 |
| 輸出值 |是 |從函數計算和返回的範本語言運算式。 |

有關如何使用自訂函數的範例,請參閱 Azure[資源管理器樣本中的使用者定義的函數](template-user-defined-functions.md)。

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
| condition (條件) | 否 | 布林值，指出是否會在此部署期間佈建資源。 若為 `true`，就會在部署期間建立資源。 若為 `false`，則會略過此部署的資源。 請參考[條件](conditional-resource-deployment.md)。 |
| type |是 |資源類型。 此值是資源提供程式的命名空間和資源類型(如**Microsoft.儲存/存儲帳戶**)的組合。 要確定可用值,請參閱[樣本參考](/azure/templates/)。 對於子資源,類型的格式取決於它是嵌套在父資源中還是在父資源外部定義。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |
| apiVersion |是 |要用來建立資源的 REST API 版本。 要確定可用值,請參閱[樣本參考](/azure/templates/)。 |
| NAME |是 |資源名稱。 此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。 向外部方公開資源名稱的 Azure 服務將驗證該名稱,以確保它不是試圖欺騙其他標識。 對於子資源,名稱的格式取決於它是嵌套在父資源中還是在父資源外部定義。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |
| comments |否 |您在範本中記錄資源的註解。 如需詳細資訊，請參閱[範本中的註解](template-syntax.md#comments)。 |
| location |不定 |所提供資源的支援地理位置。 您可以選取任何可用的位置，但通常選擇接近您的使用者的位置很合理。 通常，將彼此互動的資源放在相同區域也合乎常理。 大部分的資源類型都需要有位置，但某些類型 (例如角色指派) 不需要位置。 請參考[設定資源位置](resource-location.md)。 |
| dependsOn |否 |在部署這項資源之前必須部署的資源。 Resource Manager 會評估資源之間的相依性，並依正確的順序進行部署。 資源若不互相依賴，則會平行部署資源。 值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。 只會列出此範本中已部署的資源。 此範本中未定義的資源必須已經存在。 避免加入不必要的相依性，因為可能會降低部署速度並產生循環相依性。 如需設定相依性的指引，請參閱[定義 Azure Resource Manager 範本中的相依性](define-resource-dependency.md)。 |
| tags |否 |與資源相關聯的標記。 套用標籤，既可以邏輯方式組織訂用帳戶中的資源。 |
| sku | 否 | 某些資源允許以值定義要部署的 SKU。 例如，您可以指定儲存體帳戶的備援類型。 |
| kind | 否 | 某些資源允許以值定義您所部署的資源類型。 例如，您可以指定要建立的 Cosmos DB 類型。 |
| copy |否 |如果需要多個執行個體，要建立的資源數目。 預設模式為平行。 如果您不想要同時部署所有或某些資源，請指定序列模式。 如需詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的數個執行個體](copy-resources.md)。 |
| 計劃 | 否 | 某些資源允許以值定義要部署的計劃。 例如，您可以指定虛擬機器的 Marketplace 映像。 |
| properties |否 |資源特定的組態設定。 屬性的值和您在 REST API 作業 (PUT 方法) 要求主體中提供來建立資源的值是一樣的。 您也可以指定複本陣列，以建立屬性的數個執行個體。 要確定可用值,請參閱[樣本參考](/azure/templates/)。 |
| resources |否 |與正在定義的資源相依的下層資源。 只提供父資源的結構描述允許的資源類型。 沒有隱含父資源的相依性。 您必須明確定義該相依性。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |

## <a name="outputs"></a>輸出

在輸出區段中，您可以指定從部署傳回的值。 通常,從已部署的資源返回值。

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
| 輸出名稱 |是 |輸出值的名稱。 必須是有效的 JavaScript 識別碼。 |
| condition (條件) |否 | 布林值，指出是否傳回此輸出值。 當為 `true` 時，該值會包含在部署的輸出中。 若為 `false`，則會略過此部署的輸出值。 未指定時，預設值為 `true`。 |
| type |是 |輸出值的類型。 輸出值支援與範本輸入參數相同的類型。 如果為輸出類型指定**安全字串**,則該值不會顯示在部署歷史記錄中,並且無法從其他範本檢索。 要在多個範本中使用機密值,請使用金鑰保管庫中存儲該機密,並在參數檔中引用該機密。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](key-vault-parameter.md)。 |
| value |否 |評估並傳回做為輸出值的範本語言運算式。 指定**值**或**複製**。 |
| copy |否 | 用於返回多個輸出值。 指定**值**或**複製**。 有關詳細資訊,請參閱[Azure 資源管理器樣本中的輸出反覆運算](copy-outputs.md)。 |

有關如何使用輸出的範例,請參閱[Azure 資源管理員樣本中的輸出](template-outputs.md)。

<a id="comments" />

## <a name="comments-and-metadata"></a>註解與中繼資料

有幾個選項可將註解與中繼資料新增到您的範本中。

### <a name="comments"></a>註解

對於內聯註釋,可以使用`//``/* ... */`或但此語法不能適用於所有工具。 不能使用門戶範本編輯器處理具有內聯註釋的範本。 如果您新增這種樣式的註解，請務必使用支援內嵌 JSON 註解的工具。

> [!NOTE]
> 要將具有註釋的範本與版本 2.3.0 或更版本的 Azure`--handle-extended-json-format`CLI 一起使用,則必須使用該開關。

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

在可視化工作室代碼中[,Azure 資源管理器工具擴展](use-vs-code-to-create-template.md#install-resource-manager-tools-extension)可以自動檢測資源管理器範本並相應地更改語言模式。 如果在 VS 代碼的右下角看到**Azure 資源管理器範本**,則可以使用內聯註釋。 內嵌註解不再被標示為無效。

![視覺化工作室代碼 Azure 資源管理員樣本模式](./media/template-syntax/resource-manager-template-editor-mode.png)

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

對於**參數**，新增 `description` 屬性的 `metadata` 物件。

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

對於**資源**，新增 `comments` 項目或中繼資料物件。 下列範例顯示註解項目和中繼資料物件。

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

對於**輸出**，將中繼資料物件新增至輸出值。

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

您可以將字串分成多行。 例如,請參閱以下 JSON 示例中的位置屬性和註釋之一。

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

要使用 Azure CLI 版本 2.3.0 或更版本的範本來部署具有多行字`--handle-extended-json-format`串的範本 ,必須使用該交換機。

## <a name="next-steps"></a>後續步驟

* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](template-functions.md)。
* 若要在部署期間合併數個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。
* 如需建立範本的建議，請參閱 [Azure Resource Manager 範本最佳做法](template-best-practices.md)。
* 如需對於建立可跨越全部 Azure 環境和 Azure Stack 使用的 Resource Manager 範本相關建議，請參閱[開發針對雲端一致性的 Azure Resource Manager 範本](templates-cloud-consistency.md)。
