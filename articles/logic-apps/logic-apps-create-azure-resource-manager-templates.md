---
title: 建立部署的邏輯應用程式範本
description: 瞭解如何在 Azure Logic Apps 中建立 Azure Resource Manager 範本以自動化部署
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75972639"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>建立 Azure Resource Manager 範本來自動化 Azure Logic Apps 的部署

為了協助您自動建立和部署邏輯應用程式，本文說明您可以為邏輯應用程式建立[Azure Resource Manager 範本](../azure-resource-manager/management/overview.md)的方式。 如需有關範本的結構和語法的總覽，其中包含您的工作流程定義和部署所需的其他資源，請參閱[總覽：使用 Azure Resource Manager 範本自動部署邏輯應用程式](logic-apps-azure-resource-manager-templates-overview.md)。

Azure Logic Apps 提供預先建立的[邏輯應用程式 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，您可以重複使用它，而不只是用來建立邏輯應用程式，還能定義要用於部署的資源和參數。 您可以在自己的商務案例中使用此範本，或自訂此範本以符合您的需求。

> [!IMPORTANT]
> 請確定您範本中的連線使用與邏輯應用程式相同的 Azure 資源群組和位置。

如需 Azure Resource Manager 範本的詳細資訊，請參閱下列主題：

* [Azure Resource Manager 範本結構和語法](../azure-resource-manager/templates/template-syntax.md)
* [編寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
* [開發針對雲端一致性的 Azure Resource Manager 範本](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>使用 Visual Studio 建立範本

若要建立可供部署的有效參數化邏輯應用程式範本，最簡單的方式是使用 Visual Studio （免費的社區版本或更新版本）和適用于 Visual Studio 的 Azure Logic Apps 工具。 接著，您可以[在 Visual Studio 中建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [，或從 Azure 入口網站尋找並下載現有的邏輯應用程式至 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)。

藉由下載您的邏輯應用程式，您可以取得範本，其中包含邏輯應用程式和其他資源（例如連接）的定義。 此範本也會*參數化*或定義的參數，這些值是用來部署邏輯應用程式和其他資源。 您可以在不同的參數檔案中提供這些參數的值。 如此一來，您就可以根據您的部署需求，更輕鬆地變更這些值。 如需詳細資訊，請參閱下列主題：

* [使用 Visual Studio 建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [使用 Visual Studio 管理邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>使用 Azure PowerShell 建立範本

您可以使用 Azure PowerShell 搭配[LogicAppTemplate 模組](https://github.com/jeffhollan/LogicAppTemplateCreator)來建立 Resource Manager 範本。 這個開放原始碼模組會先評估您的邏輯應用程式，以及邏輯應用程式所使用的任何連接。 此模組接著會使用部署所需的參數來產生範本資源。

例如，假設您有一個邏輯應用程式，它會從 Azure 服務匯流排佇列接收訊息，並將資料上傳至 Azure SQL 資料庫。 模組會保留所有協調流程邏輯，並參數化 SQL 和服務匯流排連接字串，讓您可以根據您的部署需求來提供和變更這些值。

這些範例示範如何使用 Azure Resource Manager 範本、Azure DevOps 中的 Azure Pipelines 和 Azure PowerShell 來建立和部署邏輯應用程式：

* [範例：從 Azure Logic Apps 連接到 Azure 服務匯流排的佇列](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到 Azure 儲存體帳戶](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：為 Azure Logic Apps 設定函數應用程式動作](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到整合帳戶](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>安裝 PowerShell 模組

1. 如果您還沒有這麼做，請安裝[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

1. 若要從[PowerShell 資源庫](https://www.powershellgallery.com/packages/LogicAppTemplate)安裝 LogicAppTemplate 模組的最簡單方式，請執行此命令：

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   若要更新為最新版本，請執行此命令：

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

或者，若要手動安裝，請遵循 GitHub 中的[邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator)步驟。

### <a name="install-azure-resource-manager-client"></a>安裝 Azure Resource Manager 用戶端

若要讓 LogicAppTemplate 模組使用任何 Azure 租使用者和訂用帳戶存取權杖，請安裝[Azure Resource Manager 的用戶端工具](https://github.com/projectkudu/ARMClient)，這是一個簡單的命令列工具，可呼叫 Azure Resource Manager API。

當您使用此`Get-LogicAppTemplate`工具執行命令時，命令會先透過 ARMClient 工具取得存取權杖、使用管線將權杖傳送至 PowerShell 腳本，並建立範本做為 JSON 檔案。 如需此工具的詳細資訊，請參閱這[篇有關 Azure Resource Manager 用戶端工具的文章](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)。

### <a name="generate-template-with-powershell"></a>使用 PowerShell 產生範本

若要在安裝 LogicAppTemplate 模組並[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)之後產生範本，請執行此 PowerShell 命令：

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

若要遵循從[Azure Resource Manager 用戶端工具](https://github.com/projectkudu/ARMClient)的權杖中進行管線的建議，請改為執行`$SubscriptionId`此命令，其中是您的 AZURE 訂用帳戶識別碼：

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

解壓縮之後，您可以藉由執行下列命令，從您的範本建立參數檔案：

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

若要使用 Azure Key Vault 參考（僅限靜態）進行解壓縮，請執行此命令：

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| 參數 | 必要 | 描述 |
|------------|----------|-------------|
| TemplateFile | 是 | 範本檔案的檔案路徑 |
| KeyVault | 否 | 列舉，描述如何處理可能的金鑰保存庫值。 預設值為 `None`。 |
||||

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [部署邏輯應用程式範本](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
