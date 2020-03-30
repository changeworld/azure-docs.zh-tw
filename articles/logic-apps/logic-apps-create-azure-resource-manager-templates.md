---
title: 創建用於部署的邏輯應用範本
description: 瞭解如何創建 Azure 資源管理器範本，以便在 Azure 邏輯應用中自動部署
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972639"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>創建 Azure 資源管理器範本以自動部署 Azure 邏輯應用

為了説明自動創建和部署邏輯應用，本文介紹了為邏輯應用創建[Azure 資源管理器範本](../azure-resource-manager/management/overview.md)的方法。 有關包含工作流定義和部署所需其他資源的範本的結構和語法概述，請參閱[概述：使用 Azure 資源管理器範本自動部署邏輯應用](logic-apps-azure-resource-manager-templates-overview.md)。

Azure 邏輯應用提供了[一個預構建的邏輯應用 Azure 資源管理器範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，您可以重用該範本，不僅用於創建邏輯應用，還可以定義用於部署的資源和參數。 您可以在自己的商務案例中使用此範本，或自訂此範本以符合您的需求。

> [!IMPORTANT]
> 確保範本中的連接使用與邏輯應用相同的 Azure 資源組和位置。

有關 Azure 資源管理器範本的更多內容，請參閱以下主題：

* [Azure 資源管理器範本結構和語法](../azure-resource-manager/templates/template-syntax.md)
* [編寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
* [開發針對雲端一致性的 Azure Resource Manager 範本](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>使用視覺化工作室創建範本

要創建基本可供部署的有效參數化邏輯應用範本的最簡單方法，請使用 Visual Studio（免費社區版或更高版）和視覺化工作室的 Azure 邏輯應用工具。 然後，您可以在[Visual Studio 中創建邏輯應用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)，也可以[查找現有邏輯應用並將其從 Azure 門戶下載到 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)中。

通過下載邏輯應用，您將獲得一個範本，其中包含邏輯應用和其他資源（如連接）的定義。 該範本還*參數化*或定義用於部署邏輯應用和其他資源的值的參數。 您可以在單獨的參數檔中提供這些參數的值。 這樣，您可以更輕鬆地根據部署需求更改這些值。 如需詳細資訊，請參閱下列主題：

* [使用 Visual Studio 建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [使用 Visual Studio 管理邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>使用 Azure PowerShell 創建範本

您可以使用 Azure PowerShell 與[邏輯AppTemplate模組](https://github.com/jeffhollan/LogicAppTemplateCreator)一起創建資源管理器範本。 此開源模組首先評估邏輯應用和邏輯應用使用的任何連接。 然後，模組生成範本資源，並生成必要的部署參數。

例如，假設您有一個邏輯應用，它接收來自 Azure 服務匯流排佇列的消息並將資料上載到 Azure SQL 資料庫。 該模組保留所有業務流程邏輯，並將 SQL 和服務匯流排連接字串參數化，以便您可以根據部署需求提供和更改這些值。

這些示例演示如何使用 Azure 資源管理器範本、Azure DevOps 中的 Azure 管道和 Azure PowerShell 創建和部署邏輯應用：

* [示例：從 Azure 邏輯應用連接到 Azure 服務匯流排佇列](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：從 Azure 邏輯應用連接到 Azure 存儲帳戶](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：為 Azure 邏輯應用設置函數應用操作](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：從 Azure 邏輯應用連接到集成帳戶](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>安裝 PowerShell 模組

1. 如果尚未安裝 Azure [PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

1. 對於從[PowerShell 庫](https://www.powershellgallery.com/packages/LogicAppTemplate)安裝 LogicAppTemplate 模組的最簡單方法，請運行以下命令：

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   要更新到最新版本，請運行以下命令：

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

或者，要手動安裝，請按照 GitHub 中[的步驟進行邏輯應用範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator)。

### <a name="install-azure-resource-manager-client"></a>安裝 Azure 資源管理器用戶端

對於邏輯應用範本模組要使用任何 Azure 租戶和訂閱訪問權杖，請安裝[Azure 資源管理器用戶端工具](https://github.com/projectkudu/ARMClient)，該工具是調用 Azure 資源管理器 API 的簡單命令列工具。

使用此工具運行`Get-LogicAppTemplate`該命令時，該命令首先通過 ARMClient 工具獲取訪問權杖，將權杖管道到 PowerShell 腳本，並將範本創建為 JSON 檔。 有關該工具的詳細資訊，請參閱有關 Azure[資源管理器用戶端工具的這一篇文章](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)。

### <a name="generate-template-with-powershell"></a>使用 PowerShell 生成範本

要在安裝 LogicAppTemplate 模組和[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)後生成範本，請運行此 PowerShell 命令：

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

要遵循從[Azure 資源管理器用戶端工具](https://github.com/projectkudu/ARMClient)在權杖中管道的建議，請運行此命令，而不是`$SubscriptionId`Azure 訂閱 ID 的位置：

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

提取後，可以通過運行以下命令從範本創建參數檔：

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

對於使用 Azure 金鑰保存庫引用（僅限靜態）進行提取，運行此命令：

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| 參數 | 必要 | 描述 |
|------------|----------|-------------|
| TemplateFile | 是 | 範本檔的檔路徑 |
| KeyVault | 否 | 描述如何處理可能的關鍵保存庫值的枚舉。 預設值為 `None`。 |
||||

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [部署邏輯應用程式範本](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
