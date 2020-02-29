---
title: 部署邏輯應用程式範本
description: 瞭解如何部署為 Azure Logic Apps 建立 Azure Resource Manager 範本
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162376"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>部署 Azure Logic Apps 的 Azure Resource Manager 範本

為邏輯應用程式建立 Azure Resource Manager 範本之後，您可以透過下列方式部署範本：

* [Azure 入口網站](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>透過 Azure 入口網站部署

若要將邏輯應用程式範本自動部署至 Azure，您可以選擇下列 [**部署至 azure** ] 按鈕，這會將您登入 Azure 入口網站並提示您輸入邏輯應用程式的相關資訊。 然後，您可以對邏輯應用程式範本或參數進行任何必要的變更。

[![部署至 Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，在您登入 Azure 入口網站之後，系統會提示您輸入下列資訊：

* Azure 訂用帳戶名稱
* 您想要使用的資源群組
* 邏輯應用程式位置
* 邏輯應用程式的名稱
* 測試 URI
* 接受指定的條款和條件

如需詳細資訊，請參閱下列主題：

* [總覽：使用 Azure Resource Manager 範本自動部署邏輯應用程式](logic-apps-azure-resource-manager-templates-overview.md)
* [使用 Azure Resource Manager 範本和 Azure 入口網站部署資源](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>透過 Visual Studio 部署

若要從使用 Visual Studio 建立的 Azure 資源群組專案部署邏輯應用程式範本，請遵循下列[步驟，將邏輯應用程式手動部署](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)至 azure。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署至特定的*Azure 資源群組*，請使用下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題：

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

若要部署至特定的*Azure 資源群組*，請使用下列命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題：

* [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 部署

若要部署邏輯應用程式範本和管理環境，小組通常會使用[Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)中的[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines)工具。 Azure Pipelines 提供[Azure 資源群組部署](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)工作，您可以將其新增至任何組建或發行管線。 對於部署和產生發行管線的授權，您也需要 Azure Active Directory （AD）[服務主體](../active-directory/develop/app-objects-and-service-principals.md)。 深入瞭解如何[使用 Azure Pipelines 的服務主體](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。

如需有關使用 Azure Pipelines Azure Resource Manager 範本的持續整合與持續部署（CI/CD）的詳細資訊，請參閱下列主題和範例：

* [整合 Resource Manager 範本與 Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教學課程：使用 Azure Pipelines Azure Resource Manager 範本的持續整合](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [範例：從 Azure Logic Apps 連接到 Azure 服務匯流排佇列，並使用 Azure Pipelines 在 Azure DevOps 中進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到 Azure 儲存體帳戶，並使用 Azure Pipelines 在 Azure DevOps 中進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：為 Azure Logic Apps 設定函式應用程式動作，並在 Azure DevOps 中使用 Azure Pipelines 進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到整合帳戶，並使用中的 Azure Pipelines 進行部署 Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [範例：使用 Azure Logic Apps 協調 Azure Pipelines](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

以下是使用 Azure Pipelines 的一般高階步驟：

1. 在 Azure Pipelines 中，建立空的管線。

1. 選擇管線所需的資源，例如您的邏輯應用程式範本和範本參數檔案，這些檔案是以手動方式或在組建程式中產生的。

1. 針對您的代理程式作業，尋找並新增 [ **Azure 資源群組部署**] 工作。

   ![新增「Azure 資源群組部署」工作](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用[服務主體](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)進行設定。

1. 新增邏輯應用程式範本和範本參數檔案的參考。

1. 視需要針對任何其他環境、自動化測試或核准者，繼續在發行程序中建置步驟。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授權 OAuth 連接

部署之後，您的邏輯應用程式會以有效的參數端對端運作。 不過，您仍然必須授權任何 OAuth 連線，以產生有效的存取權杖來[驗證您的認證](../active-directory/develop/authentication-scenarios.md)。 以下是您可以授權 OAuth 連線的方式：

* 針對自動化部署，您可以使用腳本來為每個 OAuth 連接提供同意。 以下是 GitHub 中[logicappconnectionauth 下方](https://github.com/logicappsio/LogicAppConnectionAuth)專案的範例腳本。

* 若要手動授權 OAuth 連線，請在邏輯應用程式設計工具中開啟邏輯應用程式，不論是在 Azure 入口網站或 Visual Studio 中。 在設計工具中，授權任何必要的連接。

如果您改為使用 Azure Active Directory （Azure AD）[服務主體](../active-directory/develop/app-objects-and-service-principals.md)來授權連線，請瞭解如何[在邏輯應用程式範本中指定服務主體參數](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Logic Apps](../logic-apps/monitor-logic-apps.md)
