---
title: 總覽-自動化 Azure Logic Apps 的部署
description: 瞭解 Azure Resource Manager 範本以自動化 Azure Logic Apps 的部署
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: 4070f373175f3497156ced011a57e2ed7bd6e770
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364253"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>總覽：使用 Azure Resource Manager 範本將 Azure Logic Apps 的部署自動化

當您準備好要自動建立及部署邏輯應用程式時，您可以將邏輯應用程式的基礎工作流程定義擴充到 [Azure Resource Manager 範本](../azure-resource-manager/management/overview.md)中。 此範本會定義用來布建及部署邏輯應用程式的基礎結構、資源、參數和其他資訊。 藉由針對不同于部署的值定義參數，也稱為 *參數化，您* 可以根據不同的部署需求，重複且一致地部署邏輯應用程式。

例如，如果您部署至開發、測試和生產環境，您可能會針對每個環境使用不同的連接字串。 您可以宣告接受不同連接字串的範本參數，然後將這些字串儲存在個別的 [參數](../azure-resource-manager/templates/parameter-files.md)檔案中。 如此一來，您就可以變更這些值，而不需要更新和重新部署範本。 如果您有機密或必須保護的參數值（例如密碼和密碼），您可以將這些值儲存在 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) 中，並讓您的參數檔案取出這些值。 不過，在這些情況下，您會重新部署以取得目前的值。

本總覽說明 Resource Manager 範本中包含邏輯應用程式工作流程定義的屬性。 範本和您的工作流程定義都使用 JSON 語法，但有一些差異，因為工作流程定義也遵循 [工作流程定義語言架構](../logic-apps/logic-apps-workflow-definition-language.md)。 例如，範本運算式和工作流程定義運算式的 [參考參數](#parameter-references) 和其可接受的值有何不同。

> [!TIP]
> 若要取得最簡單的方法，以取得幾乎可供部署的有效參數化邏輯應用程式範本，請使用 Visual Studio (免費的版或更高版本) 以及適用于 Visual Studio 的 Azure Logic Apps 工具。 然後，您可以 [在 Visual Studio 中建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [，或從 Azure 尋找現有的邏輯應用程式，並將其下載到 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)。
>
> 或者，您可以使用 [Azure PowerShell 搭配 LogicAppTemplate 模組](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)來建立邏輯應用程式範本。

本主題中的範例邏輯應用程式會使用新的電子郵件送達時引發的 [Office 365 Outlook 觸發](/connectors/office365/) 程式，以及可為電子郵件內文建立 blob 並將 blob 上傳至 Azure 儲存體容器的 [Azure Blob 儲存體動作](/connectors/azureblob/) 。 範例也會示範如何將不同于部署的值參數化。

如需 Resource Manager 範本的詳細資訊，請參閱下列主題：

* [Azure Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager 範本最佳做法](../azure-resource-manager/templates/template-best-practices.md)
* [開發針對雲端一致性的 Azure Resource Manager 範本](../azure-resource-manager/templates/templates-cloud-consistency.md)

如需邏輯應用程式、整合帳戶、整合帳戶成品和整合服務環境的特定範本資源資訊，請參閱 [Microsoft 邏輯資源類型](/azure/templates/microsoft.logic/allversions)。

如需範例邏輯應用程式範本，請參閱下列範例：

* 本主題的範例所使用的[完整範本](#full-example-template)
* GitHub 中的[範例快速入門邏輯應用程式範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create)

針對 Logic Apps REST API，請從 [Azure Logic Apps REST API 總覽](/rest/api/logic)開始。

<a name="template-structure"></a>

## <a name="template-structure"></a>範本結構

在最上層，Resource Manager 範本會遵循此結構，此結構已在 [Azure Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md) 主題中完整說明：

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

| 屬性 | 說明 |
|-----------|-------------|
| `parameters` | 宣告 [範本參數](../azure-resource-manager/templates/template-syntax.md#parameters) ，以在 Azure 中建立和自訂用於部署的資源時，接受要使用的值。 例如，這些參數會接受邏輯應用程式的名稱和位置、連線，以及部署所需的其他資源的值。 您可以將這些參數值儲存在 [參數](#template-parameter-files)檔案中，如本主題稍後所述。 如需一般詳細資訊，請參閱 [參數-Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#parameters)。 |
| `resources` | 定義要建立或更新和部署至 Azure 資源群組的 [資源](../azure-resource-manager/templates/template-syntax.md#resources) ，例如您的邏輯應用程式、連線、Azure 儲存體帳戶等等。 如需一般詳細資訊，請參閱 [資源-Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#resources)。 |
||||

您的邏輯應用程式範本會使用此檔案名格式：

**<*邏輯應用程式名稱* # C0.js開啟**

> [!IMPORTANT]
> 範本語法會區分大小寫，因此請確定您使用一致的大小寫。 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>範本參數

邏輯應用程式範本具有多個 `parameters` 存在於不同層級的物件，並執行不同的功能。 例如，在最上層，您可以針對在 Azure 中建立和部署資源時要接受和使用的值宣告 [範本參數](../azure-resource-manager/templates/template-syntax.md#parameters) ，例如：

* 您的邏輯應用程式
* 邏輯用來透過[受管理的連接器](../connectors/apis-list.md)存取其他服務和系統的連接
* 邏輯應用程式部署所需的其他資源

  例如，如果您的邏輯應用程式使用企業對企業 (B2B) 案例的 [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ，則範本的最上層 `parameters` 物件會宣告可接受該整合帳戶之資源識別碼的參數。

以下是參數定義的一般結構和語法， [Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#parameters)的完整描述：

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

此範例只會顯示用於在 Azure 中建立和部署這些資源之值的範本參數：

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

除了處理敏感性或必須保護之值的參數（例如使用者名稱、密碼和密碼）之外，所有這些參數 `defaultValue` 都包含屬性，不過在某些情況下，預設值是空值。 用於這些範本參數的部署值是由本主題稍後所述的範例 [參數](#template-parameter-files) 檔案所提供。

如需保護範本參數的詳細資訊，請參閱下列主題：

* [範本參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [改善範本參數的安全性](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [使用 Azure Key Vault 傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

其他範本物件通常會參考範本參數，讓它們可以使用通過範本參數的值，例如：

* 您 [範本的資源物件](#template-resources)（稍後在本主題中所述）會定義您想要建立和部署的 Azure 中的每個資源，例如 [邏輯應用程式的資源定義](#logic-app-resource-definition)。 這些資源通常會使用範本參數值，例如邏輯應用程式的名稱和位置，以及連接資訊。

* 在邏輯應用程式資源定義的較深層層級，您的 [工作流程定義的 parameters 物件](#workflow-definition-parameters) 會宣告參數，以供邏輯應用程式的執行時間使用。 例如，您可以針對 HTTP 觸發程式用來進行驗證的使用者名稱和密碼，宣告工作流程定義參數。 若要指定工作流程定義參數的值，請使用 `parameters` 工作流程定義 *以外* 但仍在邏輯應用程式資源定義 *內* 的物件。 在此外部 `parameters` 物件中，您可以參考先前宣告的範本參數，這些參數可以接受來自參數檔案的部署值。

參考參數時，樣板運算式和函式會使用不同的語法，並且與工作流程定義運算式和函式的行為不同。 如需有關這些差異的詳細資訊，請參閱本主題稍後的 [參數參考](#parameter-references) 。

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>最佳做法-範本參數

以下是定義參數的一些最佳作法：

* 根據您的部署需求，只針對不同的值宣告參數。 請勿在不同的部署需求中宣告保持相同的值參數。

* `defaultValue`針對所有參數（敏感性或必須受到保護的值除外），包括可指定空值的屬性。 一律將安全參數用於使用者名稱、密碼和秘密。 若要隱藏或保護敏感性參數值，請遵循下列主題中的指導方針：

  * [範本參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [改善範本參數的安全性](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [使用 Azure Key Vault 傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

* 若要區分範本參數名稱與工作流程定義參數名稱，您可以使用描述性範本參數名稱，例如： `TemplateFabrikamPassword`

如需更多範本的最佳作法，請參閱 [範本參數的最佳作法](../azure-resource-manager/templates/template-best-practices.md#parameters)。

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>範本參數檔案

若要提供範本參數的值，請將這些值儲存在 [參數](../azure-resource-manager/templates/parameter-files.md)檔案中。 如此一來，您就可以根據您的部署需求使用不同的參數檔案。 以下是要使用的檔案名格式：

* 邏輯應用程式範本檔案名： **< *邏輯應用程式名稱* # C0.js于**
* 參數檔案名： **< *邏輯-應用程式名稱* # C0.parameters.js于**

以下是參數檔案內的結構，其中包含 [用來傳遞安全參數值與 Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)的金鑰保存庫參考：

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

您的範本有一個 `resources` 物件，它是一個陣列，其中包含要在 Azure 中建立和部署的每個資源的定義，例如 [邏輯應用程式的資源定義](#logic-app-resource-definition)、連線 [資源定義](#connection-resource-definitions)，以及邏輯應用程式部署所需的任何其他資源。

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
> 範本可以包含多個邏輯應用程式的資源定義，因此請確定您的所有邏輯應用程式資源都指定相同的 Azure 資源群組。 當您使用 Visual Studio 將範本部署至 Azure 資源群組時，系統會提示您輸入要開啟的邏輯應用程式。 此外，您的 Azure 資源群組專案可以包含一個以上的範本，因此請在出現提示時，確定您選取正確的參數檔案。

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>查看資源定義

若要檢查 Azure 資源群組中所有資源的資源定義，請將 [您的邏輯應用程式從 azure 下載至 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)，這是最簡單的方式，可讓您輕鬆地建立可供部署的有效參數化邏輯應用程式範本。

如需範本資源與其屬性的一般資訊，請參閱下列主題：

* [資源-Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md#resources)
* [範本資源的最佳作法](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>邏輯應用程式資源定義

範本中邏輯應用程式的 [工作流程資源定義](/azure/templates/microsoft.logic/workflows) 會以 `properties` 物件開頭，其中包含下列資訊：

* 邏輯應用程式在部署時的狀態
* 邏輯應用程式所使用之任何整合帳戶的識別碼
* 邏輯應用程式的工作流程定義
* `parameters`設定在執行時間使用之值的物件。
* 邏輯應用程式的其他資源資訊，例如名稱、類型、位置、任何執行時間設定等

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
            "accessControl": {},
            "runtimeConfiguration": {}
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

以下是邏輯應用程式資源定義的特定屬性：

| 屬性 | 必要 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `state` | 是 | String | 邏輯應用程式在部署時的狀態， `Enabled` 表示邏輯應用程式是即時的， `Disabled` 表示邏輯應用程式為非使用中。 例如，如果您還沒準備好要讓邏輯應用程式上線，但想要部署草稿版本，您可以使用 `Disabled` 選項。 |
| `integrationAccount` | 否 | Object | 如果您的邏輯應用程式使用整合帳戶，它會儲存企業對企業 (B2B) 案例的成品，此物件會包含 `id` 屬性，此屬性會指定整合帳戶的識別碼。 |
| `definition` | 是 | Object | 邏輯應用程式的基礎工作流程定義（也就是顯示在程式碼視圖中的相同物件）會在 [工作流程定義語言主題的架構參考](../logic-apps/logic-apps-workflow-definition-language.md) 中完整說明。 在此工作流程定義中，物件會宣告 `parameters` 要在邏輯應用程式執行時間使用之值的參數。 如需詳細資訊，請參閱 [工作流程定義和參數](#workflow-definition-parameters)。 <p><p>若要在邏輯應用程式的工作流程定義中查看屬性，請在 Azure 入口網站或 Visual Studio 中，從 [設計檢視] 切換至 [程式碼查看]，或使用 [Azure 資源總管](https://resources.azure.com)之類的工具。 |
| `parameters` | 否 | Object | 要在邏輯應用程式執行時間使用的 [工作流程定義參數值](#workflow-definition-parameters) 。 這些值的參數定義會出現在 [工作流程定義的 parameters 物件](#workflow-definition-parameters)內。 此外，如果您的邏輯應用程式使用 [受管理的連接器](../connectors/apis-list.md) 來存取其他服務和系統，這個物件 `$connections` 就會包含一個物件，該物件會設定在執行時間使用的連接值。 |
| `accessControl` | 否 | Object | 用於指定邏輯應用程式的安全性屬性，例如限制對要求觸發程式或執行歷程記錄輸入和輸出的 IP 存取。 如需詳細資訊，請參閱 [安全存取邏輯應用程式](../logic-apps/logic-apps-securing-a-logic-app.md)。 |
| `runtimeConfiguration` | 否 | Object | 指定任何 `operationOptions` 屬性，以控制邏輯應用程式在執行時間的運作方式。 例如，您可以在 [高輸送量模式](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)中執行邏輯應用程式。 |
|||||

如需這些 Logic Apps 物件之資源定義的詳細資訊，請參閱 [Microsoft. 邏輯資源類型](/azure/templates/microsoft.logic/allversions)：

* [工作流程資源定義](/azure/templates/microsoft.logic/workflows)
* [整合服務環境資源定義](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [整合服務環境受控 API 資源定義](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [整合帳戶資源定義](/azure/templates/microsoft.logic/integrationaccounts)

* 整合帳戶構件：

  * [合約資源定義](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [元件資源定義](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Batch 設定資源定義](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [憑證資源定義](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [地圖資源定義](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [合作夥伴資源定義](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [架構資源定義](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [會話資源定義](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>工作流程定義和參數

邏輯應用程式的工作流程定義會出現在 `definition` 物件中，該物件會出現在 `properties` 邏輯應用程式資源定義內的物件中。 此 `definition` 物件是出現在程式碼視圖中的相同物件，並且會在 [工作流程定義語言主題的架構參考](../logic-apps/logic-apps-workflow-definition-language.md) 中完整說明。 您的工作流程定義包含內部宣告 `parameters` 物件，您可以在其中針對工作流程定義在執行時間所使用的值定義新的或編輯現有的參數。 然後，您可以在工作流程中的觸發程式或動作內參考這些參數。 根據預設， `parameters` 除非您的邏輯應用程式透過 [受管理的連接器](../connectors/apis-list.md)建立與其他服務和系統的連線，否則此物件是空的。

若要設定工作流程定義參數的值，請使用 `parameters` 工作流程定義 *以外* 但仍在邏輯應用程式資源定義 *內* 的物件。 在此外部 `parameters` 物件中，您可以參考先前宣告的範本參數，這些參數可以接受從參數檔案部署的值。

> [!TIP]
>
> 最佳做法是，不要直接參考在部署時從工作流程定義內部評估的範本參數。 相反地，請宣告工作流程定義參數，然後您可以在 `parameters` 工作流程定義 *外部* 的物件中設定，但仍在邏輯應用程式的資源定義 *內* 。 如需詳細資訊，請參閱 [參數的參考](#parameter-references)。

這個語法示範您可以在範本和工作流程定義層級宣告參數的位置，以及您可以藉由參考範本和工作流程定義參數來設定這些參數值的位置：

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

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>安全工作流程定義參數

針對在執行時間處理機密資訊、密碼、存取金鑰或秘密的工作流程定義參數，請宣告或編輯參數以使用 `securestring` 或 `secureobject` 參數類型。 您可以在工作流程定義中，同時參考此參數。 在範本的最上層，宣告具有相同類型的參數，以在部署時處理這類資訊。

若要設定工作流程定義參數的值，請使用 `parameters` 工作流程定義 *以外* 但仍在邏輯應用程式資源定義 *內* 的物件，以參考範本參數。 最後，若要在部署時將值傳遞至您的範本參數，請將該值儲存在 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) 中，並在部署時于範本所使用的 [參數](#template-parameter-files) 檔案中參考該金鑰保存庫。

此範例範本示範如何在必要時定義受保護的參數來完成這些工作，讓您可以將其值儲存在 Azure Key Vault 中：

* 針對用來驗證存取的值，宣告安全的參數。
* 在範本和工作流程定義層級使用這些值。
* 使用參數檔案來提供這些值。

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

* `defaultValue`針對所有參數（敏感性或必須受到保護的值除外），包括可指定空值的屬性。

* 一律將安全參數用於使用者名稱、密碼和秘密。 若要隱藏或保護敏感性參數值，請遵循下列主題中的指導方針：

  * [動作參數的安全性建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [工作流程定義中參數的安全性建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [使用 Azure Key Vault 傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

如需工作流程定義參數的詳細資訊，請參閱 [參數-工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md#parameters)。

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>連接資源定義

當您的邏輯應用程式使用 [受管理的連接器](../connectors/apis-list.md)建立和使用與其他服務和系統的連線時，您的範本物件就會 `resources` 包含這些連接的資源定義。 雖然您會從邏輯應用程式內建立連線，但連線是個別的 Azure 資源與其本身的資源定義。 若要檢查這些連線資源定義，請將 [您的邏輯應用程式從 Azure 下載至 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)，這是最簡單的方法，可讓您輕鬆地建立可供部署的有效參數化邏輯應用程式範本。

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

連接資源定義會參考範本的最上層參數值，這表示您可以使用參數檔案，在部署時提供這些值。 請確定連線使用與邏輯應用程式相同的 Azure 資源群組和位置。

以下是 Office 365 Outlook 連接和對應範本參數的資源定義範例：

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

邏輯應用程式的資源定義也可以透過下列方式與連接資源定義搭配使用：

* 在您的工作流程定義內，物件會宣告 `parameters` `$connections` 要在邏輯應用程式執行時間使用之連接值的參數。 此外，建立連接的觸發程式或動作會使用傳遞此參數的對應值 `$connections` 。

* 在您的工作流程定義 *外部* ，但仍在邏輯應用程式的資源定義 *內* ，另一個物件會藉 `parameters` `$connections` 由參考對應的範本參數，設定要在執行時間針對參數使用的值。 這些值會使用範本運算式來參考資源，以安全地將連接的中繼資料儲存在您的邏輯應用程式中。

  例如，中繼資料可以包含連接字串和存取權杖，您可以將其儲存在 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)中。 若要將這些值傳遞至您的範本參數，您可以在部署時範本所使用的 [參數](#template-parameter-files) 檔案中參考該金鑰保存庫。 如需參考參數差異的詳細資訊，請參閱本主題稍後的 [參數參考](#parameter-references) 。

  當您透過 Azure 入口網站或 Visual Studio 在程式碼視圖中開啟邏輯應用程式的工作流程定義時，該 `$connections` 物件會出現在工作流程定義之外，但在相同層級上。 當您手動更新工作流程定義時，在程式碼查看中的這種順序可讓這些參數更容易參考：

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* 邏輯應用程式的資源定義具有 `dependsOn` 物件，該物件會指定邏輯應用程式所使用之連線的相依性。

您建立的每個連接在 Azure 中都有唯一的名稱。 當您對相同的服務或系統建立多個連線時，每個連接名稱會附加一個數位，此數位會隨著每個建立的新連接而遞增，例如，、等等 `office365` `office365-1` 。

此範例會顯示邏輯應用程式的資源定義與 Office 365 Outlook 的連接資源定義之間的互動：

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

針對處理機密資訊、密碼、存取金鑰或秘密的連線參數，連線的資源定義包含一個 `parameterValues` 物件，該物件會指定這些值的名稱/值組格式。 若要隱藏這項資訊，您可以使用 `securestring` 或參數類型來宣告或編輯這些值的範本參數 `secureobject` 。 然後，您可以將該資訊儲存在 [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)中。 若要將這些值傳遞至您的範本參數，您可以在部署時範本所使用的 [參數](#template-parameter-files) 檔案中參考該金鑰保存庫。

以下範例提供 Azure Blob 儲存體連接的帳戶名稱和存取金鑰：

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

部署之後，您的邏輯應用程式會以有效的參數以端對端方式運作。 不過，您仍然必須授權任何 OAuth 連接，以產生有效的存取權杖來 [驗證您的認證](../active-directory/develop/authentication-vs-authorization.md)。 如需詳細資訊，請參閱 [授權 OAuth 連接](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)。

有些連線支援使用 Azure Active Directory (Azure AD) 的 [服務主體](../active-directory/develop/app-objects-and-service-principals.md) ，來授權 [已在 Azure AD 中註冊](../active-directory/develop/quickstart-register-app.md)之邏輯應用程式的連接。 例如，此 Azure Data Lake 連線資源定義會顯示如何參考處理服務主體資訊的範本參數，以及範本如何宣告這些參數：

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

| 屬性 | 說明 |
|-----------|-------------|
| `token:clientId` | 與您的服務主體相關聯的應用程式或用戶端識別碼 |
| `token:clientSecret` | 與您的服務主體相關聯的索引鍵值 |
| `token:TenantId` | Azure AD 租使用者的目錄識別碼 |
| `token:grantType` | 要求的授與類型，必須是 `client_credentials` 。 如需詳細資訊，請參閱 [Microsoft 身分識別平臺和 OAuth 2.0 用戶端認證流程](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 |
|||

**範本參數定義**

範本的最上層物件會宣告 `parameters` 這些參數來進行範例連接：

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

如需有關使用服務主體的詳細資訊，請參閱下列主題：

* [使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)
* [使用 Azure PowerShell 建立 Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps)
* [使用 Azure PowerShell 建立具有憑證的服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>參數的參考

若要參考範本參數，您可以使用範本運算式搭配 [範本](../azure-resource-manager/templates/template-functions.md)函式，這些函式會在部署時進行評估。 範本運算式使用方括弧 ( **[]** ) ：

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

若要參考工作流程定義參數，請使用在執行時間評估的 [工作流程定義語言運算式和](../logic-apps/workflow-definition-language-functions-reference.md)函式。 您可能會注意到某些範本函式和工作流程定義函式具有相同的名稱。 工作流程定義運算式的開頭是 "at" 符號 ( **@** ) ：

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

您可以將範本參數值傳遞至工作流程定義，讓邏輯應用程式在執行時間使用。 不過，請避免在您的工作流程定義中使用範本參數、運算式和語法，因為邏輯應用程式設計工具不支援樣板元素。 此外，範本語法和運算式可能會讓您的程式碼變得複雜，因為評估運算式時的差異。

相反地，請依照這些一般步驟來宣告和參考要在執行時間使用的工作流程定義參數、宣告和參考要在部署時使用的範本參數，並使用參數檔案指定要在部署時傳入的值。 如需完整的詳細資訊，請參閱本主題稍早的 [工作流程定義和參數](#workflow-definition-parameters) 一節。

1. 建立您的範本，並針對要在部署時接受和使用的值宣告範本參數。

1. 在您的工作流程定義中，宣告要在執行時間接受和使用的值參數。 然後，您可以在工作流程定義中，同時參考這些值。

1. 在 `parameters` 工作流程定義 *以外* 但仍在邏輯應用程式資源定義 *內* 的物件中，藉由參考對應的範本參數來設定工作流程定義參數的值。 如此一來，您就可以將範本參數值傳遞至您的工作流程定義參數。

1. 在 [參數] 檔案中，指定要在部署時使用的範本值。

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
