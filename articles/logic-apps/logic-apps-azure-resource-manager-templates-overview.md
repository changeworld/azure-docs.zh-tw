---
title: 概述 - 自動部署 Azure 邏輯應用
description: 瞭解 Azure 資源管理器範本以自動部署 Azure 邏輯應用
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 920d8bfbcef33464d528306113abe6223d752889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477743"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>概述：使用 Azure 資源管理器範本自動部署 Azure 邏輯應用

當您準備好自動創建和部署邏輯應用時，可以將邏輯應用的基礎工作流定義擴展到[Azure 資源管理器範本](../azure-resource-manager/management/overview.md)中。 此範本定義用於預配和部署邏輯應用的基礎結構、資源、參數和其他資訊。 通過定義在部署時變化的值的參數（也稱為*參數化*），可以根據不同的部署需求重複和一致地部署邏輯應用。

例如，如果部署到開發、測試和生產環境，則可能為每個環境使用不同的連接字串。 您可以聲明接受不同連接字串的範本參數，然後將這些字串存儲在單獨的[參數檔中](../azure-resource-manager/templates/parameter-files.md)。 這樣，就可以更改這些值，而無需更新和重新部署範本。 對於參數值敏感或必須保護的方案（如密碼和機密），可以在[Azure 金鑰保存庫中](../azure-resource-manager/templates/key-vault-parameter.md)存儲這些值，並讓參數檔檢索這些值。 但是，在這些情況下，您將重新部署以檢索當前值。

此概述描述資源管理器範本中包含邏輯應用工作流定義的屬性。 範本和工作流定義都使用 JSON 語法，但由於工作流定義也遵循[工作流定義語言架構](../logic-apps/logic-apps-workflow-definition-language.md)，因此存在一些差異。 例如，範本運算式和工作流定義運算式在[傳址參數](#parameter-references)的方式和可以接受的值方面有所不同。

> [!TIP]
> 要獲得基本可供部署的有效參數化邏輯應用範本的最簡單方法，請使用 Visual Studio（免費社區版或更高版）和 Visual Studio 的 Azure 邏輯應用工具。 然後，您可以在[Visual Studio 中創建邏輯應用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)，也可以[查找現有邏輯應用並將其從 Azure 下載到視覺化工作室](../logic-apps/manage-logic-apps-with-visual-studio.md)。
>
> 或者，您可以使用[Azure PowerShell 與邏輯AppTemplate模組一起](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)創建邏輯應用範本。

本主題中的示例邏輯應用使用[Office 365 Outlook 觸發器](/connectors/office365/)，該觸發器在新電子郵件到達時觸發[，Azure Blob 存儲操作](/connectors/azureblob/)為電子郵件正文創建 Blob 並將該 Blob 上載到 Azure 存儲容器。 這些示例還演示如何參數化部署時不同的值。

有關資源管理器範本的詳細資訊，請參閱以下主題：

* [Azure 資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager 範本最佳做法](../azure-resource-manager/templates/template-best-practices.md)
* [開發針對雲端一致性的 Azure Resource Manager 範本](../azure-resource-manager/templates/templates-cloud-consistency.md)

有關示例邏輯應用範本，請參閱以下示例：

* 用於本主題示例[的完整範本](#full-example-template)
* GitHub 中的[快速入門邏輯應用範本示例](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create)

有關特定于邏輯應用、集成帳戶和集成帳戶專案範本的資源資訊，請參閱[Microsoft.Logic 資源類型](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)。

<a name="template-structure"></a>

## <a name="template-structure"></a>範本結構

在頂層，資源管理器範本遵循此結構，Azure[資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md)主題對此進行了全面介紹：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

對於邏輯應用範本，您主要使用這些範本物件：

| 屬性 | 描述 |
|-----------|-------------|
| `parameters` | 聲明[範本參數](../azure-resource-manager/templates/template-syntax.md#parameters)，以便接受在 Azure 中創建和自訂要部署的資源時要使用的值。 例如，這些參數接受邏輯應用的名稱和位置、連接和部署所需的其他資源的值。 您可以將這些參數值存儲在[參數檔中](#template-parameter-files)，本主題稍後將對此進行介紹。 有關一般詳細資訊，請參閱[參數 - 資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md#parameters)。 |
| `resources` | 定義[用於創建](../azure-resource-manager/templates/template-syntax.md#resources)或更新和部署到 Azure 資源組的資源，例如邏輯應用、連接、Azure 存儲帳戶等。 有關一般詳細資訊，請參閱[資源 - 資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md#resources)。 |
||||

邏輯應用範本使用此檔案名格式：

**<*邏輯應用名稱*>.json**

> [!IMPORTANT]
> 範本語法區分大小寫，因此請確保使用一致的大小寫。 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>範本參數

邏輯應用範本具有多個`parameters`物件，這些物件存在於不同的級別上並執行不同的功能。 例如，在頂層，可以聲明在 Azure 中創建和部署資源時要接受和使用的值的[範本參數](../azure-resource-manager/templates/template-syntax.md#parameters)，例如：

* 邏輯應用
* 邏輯用於通過[託管連接器](../connectors/apis-list.md)訪問其他服務和系統的連接
* 邏輯應用部署所需的其他資源

  例如，如果邏輯應用將[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)用於企業對企業 （B2B） 方案，則範本的頂級`parameters`物件將聲明接受該集成帳戶的資源識別碼 的參數。

下面是參數定義的一般結構和語法，參數[- 資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md#parameters)對此進行了全面描述：

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

此示例僅顯示用於在 Azure 中創建和部署這些資源的值的範本參數：

* 邏輯應用的名稱和位置
* 用於連結到邏輯應用的集成帳戶的 ID

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

除了處理敏感值或必須保護的值（如使用者名、密碼和機密）的參數外，所有這些參數都包含`defaultValue`屬性，儘管在某些情況下，預設值為空值。 用於這些範本參數的部署值由本主題後面介紹的示例[參數檔](#template-parameter-files)提供。

有關保護範本參數的詳細資訊，請參閱以下主題：

* [範本參數的安全建議](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [提高範本參數的安全性](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [使用 Azure 金鑰保存庫傳遞安全參數值](../azure-resource-manager/templates/key-vault-parameter.md)

其他範本物件通常引用範本參數，以便它們可以使用通過範本參數的值，例如：

* [範本的資源物件](#template-resources)（在本主題中稍後介紹）定義要創建和部署的 Azure 中的每個資源，例如[邏輯應用的資源定義](#logic-app-resource-definition)。 這些資源通常使用範本參數值，例如邏輯應用的名稱、位置和連接資訊。

* 在邏輯應用的資源定義中的更深層，[工作流定義的參數物件](#workflow-definition-parameters)聲明在邏輯應用運行時使用的值的參數。 例如，您可以聲明 HTTP 觸發器用於身份驗證的使用者名和密碼的工作流定義參數。 要指定工作流定義參數的值，`parameters`請使用超出工作流定義但仍在邏輯應用資源*outside*定義*中*的物件。 在此外部`parameters`物件中，可以引用以前聲明的範本參數，這些參數可以接受從參數檔部署時的值。

傳址參數時，範本運算式和函數使用不同的語法，其行為方式與工作流定義運算式和函數不同。 有關這些差異的詳細資訊，請參閱本主題後面[對參數的引用](#parameter-references)。

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>最佳實踐 - 範本參數

以下是定義參數的一些最佳實踐：

* 僅根據部署需求為不同的值聲明參數。 不要聲明不同部署要求中保持不變的值的參數。

* 包括屬性`defaultValue`，該屬性可以為所有參數指定空值，但敏感值或必須保護的值除外。 始終對使用者名、密碼和機密使用安全參數。 要隱藏或保護敏感的參數值，請按照以下主題中的指南操作：

  * [範本參數的安全建議](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [提高範本參數的安全性](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [使用 Azure 金鑰保存庫傳遞安全參數值](../azure-resource-manager/templates/key-vault-parameter.md)

* 要區分範本參數名稱和工作流定義參數名稱，可以使用描述性範本參數名稱，例如：`TemplateFabrikamPassword`

有關更多範本最佳實踐，請參閱[範本參數的最佳做法](../azure-resource-manager/templates/template-best-practices.md#parameters)。

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>範本參數檔

要提供範本參數的值，請將這些值存儲在[參數檔中](../azure-resource-manager/templates/parameter-files.md)。 這樣，您可以根據部署需求使用不同的參數檔。 下面是要使用的檔案名格式：

* 邏輯應用範本檔案名：**<*邏輯應用名稱*>.json**
* 參數檔案名：**<*邏輯-應用程式名稱*>.parameters.json**

下面是參數檔的結構，其中包括用於[使用 Azure 金鑰保存庫傳遞安全參數值](../azure-resource-manager/templates/key-vault-parameter.md)的金鑰保存庫引用 ：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

此示例參數檔指定本主題前面聲明的範本參數的值：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>範本資源

範本具有一個`resources`物件，該物件是一個陣列，其中包含要在 Azure 中創建和部署的每個資源的定義，例如[邏輯應用的資源定義](#logic-app-resource-definition)、任何[連接資源定義](#connection-resource-definitions)以及邏輯應用需要部署的任何其他資源。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> 範本可以包括多個邏輯應用的資源定義，因此請確保所有邏輯應用資源指定相同的 Azure 資源組。 使用 Visual Studio 將範本部署到 Azure 資源組時，系統會提示您要打開哪個邏輯應用。 此外，Azure 資源組專案可以包含多個範本，因此請確保在提示時選擇正確的參數檔。

有關範本資源及其屬性的一般資訊，請參閱以下主題：

* [資源 - 資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md#resources)
* [範本資源的最佳實踐](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>邏輯應用資源定義

邏輯應用的資源定義從物件開始，`properties`其中包括以下資訊：

* 邏輯應用的部署狀態
* 邏輯應用使用的任何集成帳戶的 ID
* 邏輯應用的工作流定義
* 將`parameters`值設置在運行時使用的物件
* 有關邏輯應用的其他資源資訊，如名稱、類型、位置等

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

以下是特定于邏輯應用資源定義的屬性：

| 屬性 | 必要 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `state` | 是 | String | 邏輯應用在部署時的狀態，這意味著`Enabled`邏輯應用是即時的，並且`Disabled`意味著邏輯應用處于非活動狀態。 例如，如果您尚未準備好邏輯應用上線，但想要部署草稿版本，則可以使用 該`Disabled`選項。 |
| `integrationAccount` | 否 | Object | 如果邏輯應用使用集成帳戶（該帳戶存儲企業對企業 （B2B） 方案的專案），則此物件包括`id`屬性，該屬性指定集成帳戶的 ID。 |
| `definition` | 是 | Object | 邏輯應用的基礎工作流定義，該定義與代碼視圖中顯示的物件相同，並在[工作流定義語言的架構引用](../logic-apps/logic-apps-workflow-definition-language.md)中完全描述。 在此工作流定義中，`parameters`物件聲明在邏輯應用運行時要使用的值的參數。 有關詳細資訊，請參閱[工作流定義和參數](#workflow-definition-parameters)。 <p><p>要查看邏輯應用工作流定義中的屬性，請從 Azure 門戶或 Visual Studio 中的"設計檢視"切換到"代碼視圖"，或使用[Azure 資源資源管理器](https://resources.azure.com)等工具。 |
| `parameters` | 否 | Object | 要在邏輯應用運行時使用的[工作流定義參數值](#workflow-definition-parameters)。 這些值的參數定義顯示在[工作流定義的參數物件](#workflow-definition-parameters)中。 此外，如果邏輯應用使用[託管連接器](../connectors/apis-list.md)訪問其他服務和系統，則此物件包括一個`$connections`物件，該物件將連接值設置在運行時使用。 |
| `accessControl` | 否 | Object | 用於指定邏輯應用的安全屬性，例如將 IP 訪問限制為請求觸發器或執行歷程記錄輸入和輸出。 有關詳細資訊，請參閱[安全訪問邏輯應用](../logic-apps/logic-apps-securing-a-logic-app.md)。 |
||||

有關特定于邏輯應用、集成帳戶和集成帳戶專案範本的資源資訊，請參閱[Microsoft.Logic 資源類型](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)。

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>工作流定義和參數

邏輯應用的工作流定義將顯示在物件中`definition`，該物件將顯示在邏輯應用的資源定義`properties`中的物件中。 此`definition`物件與出現在代碼視圖中的物件相同，並在[工作流定義語言的架構引用](../logic-apps/logic-apps-workflow-definition-language.md)中完全描述。 工作流定義包括內部`parameters`聲明物件，您可以在其中為工作流定義在運行時使用的值定義新的或編輯現有參數。 然後，您可以在觸發器或工作流中的操作中引用這些參數。 預設情況下，除非邏輯`parameters`應用通過[託管連接器](../connectors/apis-list.md)創建與其他服務和系統的連接，否則此物件為空。

要設置工作流定義參數的值，`parameters`請使用超出工作流定義但仍在邏輯應用資源*outside*定義*中*的物件。 在此外部`parameters`物件中，可以引用以前聲明的範本參數，這些參數可以接受從參數檔部署時的值。

> [!TIP]
>
> 最佳做法是，不要直接引用範本參數，這些參數在部署時從工作流定義中計算。 相反，請聲明工作流定義參數，然後可以在超出工作流定義但仍在`parameters`邏輯應用的資源定義*outside**中*的物件中設置該參數。 有關詳細資訊，請參閱[對參數的引用](#parameter-references)。

此語法顯示了可以在範本和工作流定義級別聲明參數的位置，以及通過引用範本和工作流定義參數來設置這些參數值的位置：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": "[parameters('<template-parameter-name>')]"
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>安全的工作流定義參數

對於在運行時處理敏感資訊、密碼、訪問金鑰或機密的工作流定義參數，聲明或編輯參數以使用`securestring`或`secureobject`參數類型。 您可以在工作流定義中在整個過程中和內部引用此參數。 在範本的頂層，聲明具有相同類型的參數，以在部署時處理此資訊。

要設置工作流定義參數的值，請使用工作流定義*之外*但仍`parameters`位於邏輯應用資源定義*中*的物件來引用範本參數。 最後，要在部署時將該值傳遞給範本參數，請將該值存儲在[Azure 金鑰保存庫中](../azure-resource-manager/templates/key-vault-parameter.md)，並在部署時範本使用的[參數檔中](#template-parameter-files)引用該金鑰保存庫。

此示例範本演示如何通過在必要時定義安全參數來完成這些任務，以便可以在 Azure 金鑰保存庫中存儲其值：

* 聲明用於驗證訪問的值的安全參數。
* 在範本和工作流定義級別使用這些值。
* 使用參數檔提供這些值。

**範本**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**參數檔**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>最佳實踐 - 工作流定義參數

為了確保邏輯應用設計器能夠正確顯示工作流定義參數，請遵循以下最佳做法：

* 包括屬性`defaultValue`，該屬性可以為所有參數指定空值，但敏感值或必須保護的值除外。

* 始終對使用者名、密碼和機密使用安全參數。 要隱藏或保護敏感的參數值，請按照以下主題中的指南操作：

  * [指令引數的安全建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [工作流定義中參數的安全建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [使用 Azure 金鑰保存庫傳遞安全參數值](../azure-resource-manager/templates/key-vault-parameter.md)

有關工作流定義參數的詳細資訊，請參閱[參數 - 工作流定義語言](../logic-apps/logic-apps-workflow-definition-language.md#parameters)。

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>連接資源定義

當邏輯應用使用[託管連接器](../connectors/apis-list.md)創建和使用與其他服務和系統的連接時，範本`resources`的物件包含這些連接的資源定義。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

連接資源定義引用範本的頂級參數的值，這意味著您可以使用參數檔在部署時提供這些值。 確保連接使用與邏輯應用相同的 Azure 資源組和位置。

下面是 Office 365 Outlook 連接和相應範本參數的示例資源定義：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

邏輯應用的資源定義也以以下方式處理連接資源定義：

* 在工作流定義中，`parameters`物件聲明連接`$connections`值在邏輯應用運行時使用的參數。 此外，創建連接的觸發器或操作使用通過此`$connections`參數的相應值。

* *在*工作流定義之外但仍位於邏輯應用的資源定義*中*，另一個`parameters`物件通過引用相應的範本參數來設置在運行時用於`$connections`參數的值。 這些值使用範本運算式來引用在邏輯應用中安全地存儲連接中繼資料的資源。

  例如，中繼資料可以包括連接字串和訪問權杖，可以存儲在[Azure 金鑰保存庫中](../azure-resource-manager/templates/key-vault-parameter.md)。 要將這些值傳遞給範本參數，請參閱範本在部署時使用的[參數檔中](#template-parameter-files)的金鑰保存庫。 有關傳址參數差異的詳細資訊，請參閱本主題後面對[參數的引用](#parameter-references)。

  通過 Azure 門戶或 Visual Studio 在代碼視圖中打開邏輯應用的工作流定義時，`$connections`該物件將顯示在工作流定義之外，但位於同一級別。 在代碼視圖中的這種排序使這些參數更易於引用，當您手動更新工作流定義時：

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* 邏輯應用的資源定義具有一個`dependsOn`物件，該物件指定邏輯應用使用的連接的依賴項。

您創建的每個連接在 Azure 中都有一個唯一的名稱。 創建到同一服務或系統的多個連接時，每個連接名稱都附加一個數位，該數位隨著每個新連接的創建而遞增，例如 ， `office365` `office365-1`等。

此示例顯示邏輯應用的資源定義與 Office 365 Outlook 的連接資源定義之間的交互：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>安全連線參數

對於處理敏感資訊、密碼、訪問金鑰或機密的連接參數，連接的資源定義包括一個`parameterValues`以名稱值對格式指定這些值的物件。 要隱藏此資訊，可以使用 或`securestring``secureobject`參數型別宣告或編輯這些值的範本參數。 然後，可以將該資訊存儲在[Azure 金鑰保存庫中](../azure-resource-manager/templates/key-vault-parameter.md)。 要將這些值傳遞給範本參數，請參閱範本在部署時使用的[參數檔中](#template-parameter-files)的金鑰保存庫。

下面是一個示例，該示例為 Azure Blob 存儲連接提供帳戶名稱和訪問金鑰：

**參數檔**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**範本**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>對連接進行身份驗證

部署後，邏輯應用使用有效的參數端到端工作。 但是，您仍必須授權任何 OAuth 連接才能生成用於[驗證憑據](../active-directory/develop/authentication-scenarios.md)的有效訪問權杖。 有關詳細資訊，請參閱授權[OAuth 連接](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)。

某些連接支援使用 Azure 活動目錄 （Azure AD）[服務主體](../active-directory/develop/app-objects-and-service-principals.md)來授權在 Azure [AD 中註冊](../active-directory/develop/quickstart-register-app.md)的邏輯應用的連接。 例如，此 Azure 資料湖連接資源定義演示如何引用處理服務主體資訊的範本參數，以及範本如何聲明這些參數：

**連接資源定義**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| 屬性 | 描述 |
|-----------|-------------|
| `token:clientId` | 與您的服務主體關聯的應用程式或用戶端 ID |
| `token:clientSecret` | 與服務主體關聯的鍵值 |
| `token:TenantId` | Azure AD 租戶的目錄 ID |
| `token:grantType` | 請求的授予類型，必須為`client_credentials`。 有關詳細資訊，請參閱[Microsoft 標識平臺和 OAuth 2.0 用戶端憑據流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 |
|||

**範本參數定義**

範本的頂級`parameters`物件聲明示例連接的這些參數：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

有關使用服務主體的詳細資訊，請參閱以下主題：

* [使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)
* [使用 Azure PowerShell 創建 Azure 服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [使用 Azure PowerShell 創建具有證書的服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>對參數的引用

要引用範本參數，可以將範本運算式與[範本函數](../azure-resource-manager/templates/template-functions.md)一起使用，這些函數在部署時計算。 範本運算式使用方括弧 （*****）：

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

要引用工作流定義參數，請使用[工作流定義語言運算式和函數 ，這些運算式和函數](../logic-apps/workflow-definition-language-functions-reference.md)在運行時計算。 您可能會注意到某些範本函數和工作流定義函數具有相同的名稱。 工作流定義運算式以"at"符號開頭 （**@**）：

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

您可以將範本參數值傳遞給工作流定義，以便邏輯應用在運行時使用。 但是，避免在工作流定義中使用範本參數、運算式和語法，因為邏輯應用設計器不支援範本元素。 此外，範本語法和運算式可能會使代碼複雜化，因為計算運算式時存在差異。

相反，請按照這些常規步驟聲明和引用工作流定義參數，在運行時使用，聲明和引用部署時使用的範本參數，並指定使用參數檔在部署時傳遞的值。 有關詳細資訊，請參閱本主題前面的[工作流定義和參數](#workflow-definition-parameters)部分。

1. 創建範本並聲明要在部署時接受和使用的值的範本參數。

1. 在工作流定義中，聲明要在運行時接受和使用的值的參數。 然後，您可以在工作流定義中引用這些值。

1. 在`parameters`超出工作流定義但仍位於*outside*邏輯應用資源定義範圍內的物件*中*，通過引用相應的範本參數設置工作流定義參數的值。 這樣，可以將範本參數值傳遞到工作流定義參數中。

1. 在參數檔中，指定範本在部署時使用的值。

<a name="full-example-template"></a>

## <a name="full-example-template"></a>完整示例範本

下面是本主題示例使用的參數化示例範本：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立邏輯應用程式範本](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
