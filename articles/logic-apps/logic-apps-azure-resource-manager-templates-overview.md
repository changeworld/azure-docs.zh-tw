---
title: 總覽-自動化 Azure Logic Apps 的部署
description: 瞭解 Azure Resource Manager 範本以自動化部署 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641819"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>總覽：使用 Azure Resource Manager 範本自動部署 Azure Logic Apps

當您準備好要自動建立和部署邏輯應用程式時，您可以將邏輯應用程式的基礎工作流程定義擴充到[Azure Resource Manager 範本](../azure-resource-manager/management/overview.md)中。 此範本會定義用來布建和部署邏輯應用程式的基礎結構、資源、參數和其他資訊。 藉由定義不同部署（也稱為*參數*化）的值參數，您可以根據不同的部署需求，重複且一致地部署邏輯應用程式。

例如，如果您部署至開發、測試和生產環境，您可能會在每個環境中使用不同的連接字串。 您可以宣告接受不同連接字串的範本參數，然後將這些字串儲存在不同的[參數](../azure-resource-manager/templates/parameter-files.md)檔案中。 如此一來，您就可以變更這些值，而不需要更新和重新部署範本。 對於您有機密或必須受到保護之參數值的案例（例如密碼和秘密），您可以將這些值儲存在[Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)中，並讓您的參數檔案抓取這些值。 不過，在這些情況下，您會重新部署以取得目前的值。

本總覽描述 Resource Manager 範本中的屬性，其中包含邏輯應用程式工作流程定義。 範本和您的工作流程定義都使用 JSON 語法，但有一些差異存在，因為工作流程定義也會遵循[工作流程定義語言架構](../logic-apps/logic-apps-workflow-definition-language.md)。 例如，範本運算式和工作流程定義運算式的[參考參數](#parameter-references)和其可接受的值有何不同。

> [!TIP]
> 若要取得最適合部署的有效參數化邏輯應用程式範本，請使用 Visual Studio （免費的社區版本或更新版本）和適用于 Visual Studio 的 Azure Logic Apps 工具。 接著，您可以[在 Visual Studio 中建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [，或從 Azure 尋找並下載現有的邏輯應用程式至 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)。
>
> 或者，您可以使用[Azure PowerShell 搭配 LogicAppTemplate 模組](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)來建立邏輯應用程式範本。

本主題中的範例邏輯應用程式使用[Office 365 Outlook 觸發](/connectors/office365/)程式，會在新的電子郵件送達時引發，以及為電子郵件內文建立 blob 並將該 blob 上傳至 Azure 儲存體容器的[Azure Blob 儲存體動作](/connectors/azureblob/)。 範例也會示範如何參數化不同于部署的值。

如需 Resource Manager 範本的詳細資訊，請參閱下列主題：

* [Azure Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager 範本最佳做法](../azure-resource-manager/templates/template-best-practices.md)
* [開發針對雲端一致性的 Azure Resource Manager 範本](../azure-resource-manager/templates/templates-cloud-consistency.md)

如需範例邏輯應用程式範本，請參閱下列範例：

* 本主題範例所使用的[完整範本](#full-example-template)
* GitHub 中的[範例快速入門邏輯應用程式範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create)

如需邏輯應用程式、整合帳戶和整合帳戶成品特有的範本資源資訊，請參閱[Microsoft. 邏輯資源類型](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)。

<a name="template-structure"></a>

## <a name="template-structure"></a>範本結構

在最上層，Resource Manager 範本會遵循此結構，如[Azure Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md)主題中所述：

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

針對邏輯應用程式範本，您主要會使用這些範本物件：

| 屬性 | 描述 |
|-----------|-------------|
| `parameters` | 宣告[範本參數](../azure-resource-manager/templates/template-syntax.md#parameters)，以便在 Azure 中建立和自訂要部署的資源時，接受要使用的值。 例如，這些參數會接受您的邏輯應用程式名稱和位置、連線，以及部署所需的其他資源的值。 您可以將這些參數值儲存在[參數](#template-parameter-files)檔案中，本主題稍後會加以描述。 如需一般詳細資料，請參閱[參數-Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#parameters)。 |
| `resources` | 定義要建立或更新並部署至 Azure 資源群組的[資源](../azure-resource-manager/templates/template-syntax.md#resources)，例如您的邏輯應用程式、連線、Azure 儲存體帳戶等等。 如需一般詳細資料，請參閱[資源-Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#resources)。 |
||||

您的邏輯應用程式範本會使用此檔案名格式：

**<*邏輯-應用程式名稱*> json**

> [!IMPORTANT]
> 範本語法會區分大小寫，因此請確定您使用一致的大小寫。 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>範本參數

邏輯應用程式範本有多`parameters`個存在於不同層級的物件，並執行不同的功能。 例如，您可以在最上層宣告[範本參數](../azure-resource-manager/templates/template-syntax.md#parameters)，以取得在 Azure 中建立及部署資源時要接受並在部署時使用的值，例如：

* 您的邏輯應用程式
* 您的邏輯用來透過[受控連接器](../connectors/apis-list.md)存取其他服務和系統的連接
* 您的邏輯應用程式部署所需的其他資源

  例如，如果您的邏輯應用程式使用企業對企業（B2B）案例的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，範本的最上層`parameters`物件會宣告可接受該整合帳戶之資源識別碼的參數。

以下是參數定義的一般結構和語法，其完全由[參數 Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#parameters)所描述：

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

這個範例只會顯示在 Azure 中用來建立和部署這些資源的值範本參數：

* 邏輯應用程式的名稱和位置
* 連結至邏輯應用程式的整合帳戶所使用的識別碼

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

除了處理敏感性或必須受到保護之值的參數（例如使用者名稱、密碼和秘密）以外，所有這些參數都包含`defaultValue`屬性，但在某些情況下，預設值是空值。 本主題稍後所述的範例[參數](#template-parameter-files)檔案會提供這些範本參數所要使用的部署值。

如需保護範本參數的詳細資訊，請參閱下列主題：

* [範本參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [改善範本參數的安全性](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [使用 Azure Key Vault 傳遞受保護的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

其他範本物件通常會參考樣板參數，讓它們可以使用透過範本參數傳遞的值，例如：

* 本主題稍後所述[的範本資源物件](#template-resources)會定義 Azure 中您想要建立及部署的每個資源，例如[邏輯應用程式的資源定義](#logic-app-resource-definition)。 這些資源通常會使用範本參數值，例如您的邏輯應用程式名稱和位置，以及連接資訊。

* 在邏輯應用程式資源定義的更深入層級中，您的[工作流程定義的 parameters 物件](#workflow-definition-parameters)會宣告值的參數，以用於邏輯應用程式的執行時間。 例如，您可以針對 HTTP 觸發程式用來進行驗證的使用者名稱和密碼，宣告工作流程定義參數。 若要指定工作流程定義參數的值，請`parameters`使用工作流程定義*以外*但仍在邏輯應用程式資源定義*內*的物件。 在此外部`parameters`物件中，您可以參考先前宣告的樣板參數，其可接受從參數檔案部署的值。

參考參數時，樣板運算式和函式會使用不同的語法，而且與工作流程定義運算式和函式的行為不同。 如需這些差異的詳細資訊，請參閱本主題稍後的[參數參考](#parameter-references)。

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>最佳做法-範本參數

以下是定義參數的一些最佳作法：

* 根據您的部署需求，僅針對不同的值宣告參數。 請不要針對不同部署需求中保持不變的值宣告參數。

* 針對所有`defaultValue`參數包含可指定空值的屬性，但對於敏感性或必須保護的值除外。 請一律將安全參數用於使用者名稱、密碼和秘密。 若要隱藏或保護敏感性參數值，請遵循下列主題中的指導方針：

  * [範本參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [改善範本參數的安全性](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [使用 Azure Key Vault 傳遞受保護的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

* 若要區別範本參數名稱與工作流程定義參數名稱，您可以使用描述性範本參數名稱，例如：`TemplateFabrikamPassword`

如需更多範本的最佳做法，請參閱[範本參數的最佳做法](../azure-resource-manager/templates/template-best-practices.md#parameters)。

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>範本參數檔案

若要提供範本參數的值，請將這些值儲存在[參數](../azure-resource-manager/templates/parameter-files.md)檔案中。 如此一來，您就可以根據您的部署需求來使用不同的參數檔案。 以下是要使用的檔案名格式：

* 邏輯應用程式範本檔案名： ** <*邏輯-應用程式名稱*> json**
* 參數檔案名： ** <*邏輯-應用程式名稱*>. parameters. json**

以下是參數檔案內的結構，其中包含用來[傳遞安全參數值與 Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)的金鑰保存庫參考：

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

這個範例參數檔案會指定本主題稍早所宣告的範本參數值：

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

您的範本有`resources`一個物件，它是一個陣列，其中包含要在 Azure 中建立及部署的每個資源的定義，例如[邏輯應用程式的資源定義](#logic-app-resource-definition)、任何連線[資源定義](#connection-resource-definitions)，以及邏輯應用程式部署所需的任何其他資源。

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
> 範本可以包含多個邏輯應用程式的資源定義，因此請確定您的所有邏輯應用程式資源都指定相同的 Azure 資源群組。 當您使用 Visual Studio 將範本部署至 Azure 資源群組時，系統會提示您要開啟哪一個邏輯應用程式。 此外，您的 Azure 資源群組專案可以包含一個以上的範本，因此請務必在出現提示時選取正確的參數檔案。

如需範本資源及其屬性的一般資訊，請參閱下列主題：

* [資源-Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#resources)
* [範本資源的最佳作法](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>邏輯應用程式資源定義

邏輯應用程式的資源定義會以`properties`物件開頭，其中包含下列資訊：

* 您的邏輯應用程式在部署時的狀態
* 邏輯應用程式所使用之任何整合帳戶的識別碼
* 邏輯應用程式的工作流程定義
* 設定`parameters`要在執行時間使用之值的物件。
* 邏輯應用程式的其他資源資訊，例如名稱、類型、位置等等

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

以下是您的邏輯應用程式資源定義特有的屬性：

| 屬性 | 必要 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `state` | 是 | String | 您的邏輯應用程式在部署時`Enabled`的狀態，即表示您的`Disabled`邏輯應用程式是即時的，表示邏輯應用程式為非使用中。 例如，如果您還沒有準備好讓邏輯應用程式上線，但想要部署草稿版本，您可以使用`Disabled`選項。 |
| `integrationAccount` | 否 | Object | 如果您的邏輯應用程式使用整合帳戶，其中儲存了企業對企業（B2B）案例的成品，此物件會包含`id`屬性，以指定整合帳戶的識別碼。 |
| `definition` | 是 | Object | 邏輯應用程式的基礎工作流程定義，也就是出現在程式碼視圖中的相同物件，而且會在[工作流程定義語言的架構參考](../logic-apps/logic-apps-workflow-definition-language.md)主題中完整說明。 在此工作流程定義中`parameters` ，物件會宣告要在邏輯應用程式執行時間使用之值的參數。 如需詳細資訊，請參閱[工作流程定義和參數](#workflow-definition-parameters)。 <p><p>若要在邏輯應用程式的工作流程定義中查看屬性，請從 [設計檢視] 切換至 Azure 入口網站或 Visual Studio 中的 [程式碼視圖]，或使用[Azure 資源總管](https://resources.azure.com)之類的工具。 |
| `parameters` | 否 | Object | 要在邏輯應用程式執行時間使用的[工作流程定義參數值](#workflow-definition-parameters)。 這些值的參數定義會出現在您[工作流程定義的 parameters 物件](#workflow-definition-parameters)中。 此外，如果您的邏輯應用程式使用[managed 連接器](../connectors/apis-list.md)來存取其他服務和系統，此物件`$connections`會包含物件，以設定要在執行時間使用的連接值。 |
| `accessControl` | 否 | Object | 用於指定邏輯應用程式的安全性屬性，例如限制要求觸發程式的 IP 存取或執行歷程記錄輸入和輸出。 如需詳細資訊，請參閱[保護邏輯應用程式的存取](../logic-apps/logic-apps-securing-a-logic-app.md)。 |
||||

如需邏輯應用程式、整合帳戶和整合帳戶成品特有的範本資源資訊，請參閱[Microsoft. 邏輯資源類型](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)。

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>工作流程定義和參數

邏輯應用程式的工作流程定義會出現`definition`在物件中，它會`properties`出現在邏輯應用程式資源定義內的物件中。 這個`definition`物件是出現在程式碼視圖中的相同物件，而且會在[工作流程定義語言的架構參考](../logic-apps/logic-apps-workflow-definition-language.md)主題中完整說明。 您的工作流程定義包含`parameters`內部宣告物件，您可以在此為工作流程定義在執行時間所使用的值定義新的或編輯現有的參數。 接著，您可以在工作流程中的觸發程式內或動作中參考這些參數。 根據預設，除非`parameters`您的邏輯應用程式會透過[受控連接器](../connectors/apis-list.md)建立與其他服務和系統的連線，否則此物件會是空的。

若要設定工作流程定義參數的值，請`parameters`使用工作流程定義*以外*但仍在邏輯應用程式資源定義*內*的物件。 在此外部`parameters`物件中，您可以接著參考先前宣告的範本參數，其可接受從參數檔案部署的值。

> [!TIP]
>
> 最佳做法是不要直接參考在部署時從工作流程定義中評估的範本參數。 相反地，宣告工作流程定義參數，您可以在工作流程定義`parameters` *之外*的物件中設定，但仍在邏輯應用程式的資源定義*內*。 如需詳細資訊，請參閱[參數的參考](#parameter-references)。

此語法顯示您可以在範本和工作流程定義層級宣告參數的位置，以及您可以藉由參考範本和工作流程定義參數來設定這些參數值的位置：

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
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
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

### <a name="secure-workflow-definition-parameters"></a>安全工作流程定義參數

針對在執行時間處理機密資訊、密碼、存取金鑰或密碼的工作流程定義參數，宣告或編輯參數以使用`securestring`或`secureobject`參數類型。 您可以在工作流程定義中，同時參考此參數。 在範本的最上層，宣告具有相同類型的參數，以便在部署時處理此資訊。

若要設定工作流程定義參數的值，請使用`parameters`工作流程定義*外*但仍在邏輯應用程式資源定義*內*的物件，以參考樣板參數。 最後，若要在部署時將值傳遞給您的範本參數，請將該值儲存在[Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)中，並在部署時于範本所使用的[參數](#template-parameter-files)檔案中參考該金鑰保存庫。

此範例範本會顯示如何在必要時定義安全的參數來完成這些工作，讓您可以將其值儲存在 Azure Key Vault 中：

* 針對用來驗證存取的值，宣告安全的參數。
* 在範本和工作流程定義層級上使用這些值。
* 請使用參數檔案來提供這些值。

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

**參數檔案**

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

## <a name="best-practices---workflow-definition-parameters"></a>最佳做法-工作流程定義參數

若要確保邏輯應用程式設計工具可以正確地顯示工作流程定義參數，請遵循下列最佳作法：

* 針對所有`defaultValue`參數包含可指定空值的屬性，但對於敏感性或必須保護的值除外。

* 請一律將安全參數用於使用者名稱、密碼和秘密。 若要隱藏或保護敏感性參數值，請遵循下列主題中的指導方針：

  * [動作參數的安全性建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [工作流程定義中參數的安全性建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [使用 Azure Key Vault 傳遞安全參數值](../azure-resource-manager/templates/key-vault-parameter.md)

如需工作流程定義參數的詳細資訊，請參閱[參數-工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md#parameters)。

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>連接資源定義

當您的邏輯應用程式使用[受管理的連接器](../connectors/apis-list.md)建立並使用其他服務和系統的連接時`resources` ，您的範本物件會包含這些連線的資源定義。

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

連接資源定義會參考其值的範本最上層參數，這表示您可以在部署時使用參數檔案來提供這些值。 請確定連線使用與邏輯應用程式相同的 Azure 資源群組和位置。

以下是 Office 365 Outlook 連線的範例資源定義和對應的範本參數：

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

邏輯應用程式的資源定義也可以透過下列方式來使用連線資源定義：

* 在您的工作流程定義`parameters`內，物件`$connections`會宣告連接值的參數，以便在邏輯應用程式執行時間使用。 此外，建立連接的觸發程式或動作會使用通過此`$connections`參數的對應值。

* 在您的工作流程定義*之外*，但仍在邏輯應用程式的`parameters`資源定義*內*，另一個物件會藉由`$connections`參考對應的範本參數，設定要在執行時間用於參數的值。 這些值會使用範本運算式來參考資源，以安全地將連接的中繼資料儲存在邏輯應用程式中。

  例如，中繼資料可以包含連接字串和存取權杖，您可以將它們儲存在[Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)中。 若要將這些值傳遞至您的範本參數，您可以在部署時于範本所使用的[參數](#template-parameter-files)檔案中參考該金鑰保存庫。 如需參考參數之差異的詳細資訊，請參閱本主題稍後的[參數參考](#parameter-references)。

  當您透過 Azure 入口網站或 Visual Studio 在程式碼視圖中開啟邏輯應用程式的工作流程定義`$connections`時，物件會出現在工作流程定義外部，但位於相同的層級。 當您手動更新工作流程定義時，程式碼視圖中的這個順序可讓您更輕鬆地參考這些參數：

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* 邏輯應用程式的資源定義具有`dependsOn`物件，可指定邏輯應用程式所使用之連接的相依性。

您建立的每個連線在 Azure 中都有唯一的名稱。 當您建立多個與相同服務或系統的連接時，每個連接名稱都會附加一個數位，而每個新的連接`office365` `office365-1`都會遞增，例如、等等。

這個範例會顯示邏輯應用程式的資源定義與 Office 365 Outlook 的連線資源定義之間的互動：

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

針對處理機密資訊、密碼、存取金鑰或密碼的連線參數，連線的資源定義會包含`parameterValues`物件，該物件會以名稱/值配對格式指定這些值。 若要隱藏這項資訊，您可以使用`securestring`或`secureobject`參數類型來宣告或編輯這些值的範本參數。 然後，您可以將該資訊儲存在[Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)中。 若要將這些值傳遞至您的範本參數，您可以在部署時于範本所使用的[參數](#template-parameter-files)檔案中參考該金鑰保存庫。

以下範例會提供 Azure Blob 儲存體連接的帳戶名稱和存取金鑰：

**參數檔案**

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

### <a name="authenticate-connections"></a>驗證連接

部署之後，您的邏輯應用程式會以有效的參數端對端運作。 不過，您仍然必須授權任何 OAuth 連線，以產生有效的存取權杖來[驗證您的認證](../active-directory/develop/authentication-scenarios.md)。 如需詳細資訊，請參閱[授權 OAuth 連接](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)。

某些連線支援使用 Azure Active Directory （Azure AD）[服務主體](../active-directory/develop/app-objects-and-service-principals.md)來授權[在 Azure AD 中註冊](../active-directory/develop/quickstart-register-app.md)之邏輯應用程式的連接。 例如，此 Azure Data Lake 連線資源定義會顯示如何參考處理服務主體資訊的範本參數，以及範本宣告這些參數的方式：

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
| `token:clientId` | 與服務主體相關聯的應用程式或用戶端識別碼 |
| `token:clientSecret` | 與服務主體相關聯的金鑰值 |
| `token:TenantId` | Azure AD 租使用者的目錄識別碼 |
| `token:grantType` | 要求的授與類型，必須是`client_credentials`。 如需詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0 用戶端認證流程](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 |
|||

**範本參數定義**

範本的最上層`parameters`物件會針對範例連接宣告這些參數：

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

如需使用服務主體的詳細資訊，請參閱下列主題：

* [使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)
* [使用 Azure PowerShell 建立 Azure 服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [使用 Azure PowerShell 建立具有憑證的服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>參數的參考

若要參考樣板參數，您可以使用樣板運算式搭配[範本](../azure-resource-manager/templates/template-functions.md)函式，這些函式會在部署時進行評估。 範本運算式會使用方括弧（**[]**）：

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

若要參考工作流程定義參數，您可以使用[工作流程定義語言運算式和](../logic-apps/workflow-definition-language-functions-reference.md)函式，這些函式會在執行時間進行評估。 您可能會注意到有些範本函數和工作流程定義函式具有相同的名稱。 工作流程定義運算式的開頭為 "at" 符號**@**（）：

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

您可以將範本參數值傳遞至您的工作流程定義，讓邏輯應用程式在執行時間使用。 不過，請避免在您的工作流程定義中使用範本參數、運算式和語法，因為邏輯應用程式設計工具不支援樣板元素。 此外，範本語法和運算式可能會使您的程式碼變得複雜，因為評估運算式時的差異。

相反地，請遵循下列一般步驟來宣告和參考要在執行時間使用的工作流程定義參數、宣告和參考要在部署時使用的範本參數，以及使用參數檔案指定部署時要傳入的值。 如需完整詳細資料，請參閱本主題稍早的[工作流程定義和參數](#workflow-definition-parameters)一節。

1. 建立您的範本，並在部署時宣告要接受和使用之值的範本參數。

1. 在您的工作流程定義中，宣告要在執行時間接受和使用之值的參數。 接著，您可以在工作流程定義中，參考這些值。

1. 在工作`parameters`流程定義*外*但仍在邏輯應用程式資源定義*內部*的物件中，藉由參考對應的範本參數來設定工作流程定義參數的值。 如此一來，您就可以將範本參數值傳遞至您的工作流程定義參數。

1. 在 [參數] 檔案中，指定要在部署時使用之範本的值。

<a name="full-example-template"></a>

## <a name="full-example-template"></a>完整範例範本

以下是本主題範例所使用的參數化範例範本：

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
