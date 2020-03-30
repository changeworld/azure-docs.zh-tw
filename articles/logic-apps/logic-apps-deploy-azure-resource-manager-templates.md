---
title: 部署邏輯應用程式範本
description: 瞭解如何部署為 Azure 邏輯應用創建的 Azure 資源管理器範本
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270442"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>為 Azure 邏輯應用部署 Azure 資源管理器範本

為邏輯應用創建 Azure 資源管理器範本後，可以通過以下方式部署範本：

* [Azure 門戶](#portal)
* [Visual Studio](#visual-studio)
* [Azure 電源外殼](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>通過 Azure 門戶進行部署

要自動將邏輯應用範本部署到 Azure，可以選擇以下 **"部署到 Azure"** 按鈕，該按鈕將您登錄到 Azure 門戶並提示您獲取有關邏輯應用的資訊。 然後，您可以對邏輯應用範本或參數進行任何必要的更改。

[![部署到 Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，在登錄到 Azure 門戶後，系統會提示您獲得以下資訊：

* Azure 訂用帳戶名稱
* 您想要使用的資源群組
* 邏輯應用程式位置
* 邏輯應用程式的名稱
* 測試 URI
* 接受指定的條款和條件

如需詳細資訊，請參閱下列主題：

* [概述：使用 Azure 資源管理器範本自動部署邏輯應用](logic-apps-azure-resource-manager-templates-overview.md)
* [使用 Azure 資源管理器範本和 Azure 門戶部署資源](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>透過 Visual Studio 部署

要從使用 Visual Studio 創建的 Azure 資源組專案中部署邏輯應用範本，請按照[以下步驟手動將邏輯應用部署到](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)Azure。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

要部署到特定的*Azure 資源組*，請使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題：

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

要部署到特定的*Azure 資源組*，請使用以下命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題：

* [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 進行部署

要部署邏輯應用範本和管理環境，團隊通常使用[Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)中的[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines)等工具。 Azure 管道提供[Azure 資源組部署任務](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)，可以將其添加到任何生成或發佈管道。 要授權部署和生成發佈管道，還需要 Azure 活動目錄 （AD）[服務主體](../active-directory/develop/app-objects-and-service-principals.md)。 瞭解有關[將服務主體與 Azure 管道一起使用的更多詳細資訊](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。

有關 Azure 資源管理器範本與 Azure 管道的持續集成和持續部署 （CI/CD） 的詳細資訊，請參閱以下主題和示例：

* [將資源管理器範本與 Azure 管道集成](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教程：Azure 資源管理器範本與 Azure 管道的持續集成](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [示例：從 Azure 邏輯應用連接到 Azure 服務匯流排佇列，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：從 Azure 邏輯應用連接到 Azure 存儲帳戶，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：為 Azure 邏輯應用設置函數應用操作，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：從 Azure 邏輯應用連接到集成帳戶，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [示例：使用 Azure 邏輯應用協調 Azure 管道](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

以下是使用 Azure 管道的一般高級步驟：

1. 在 Azure 管道中，創建一個空管道。

1. 選擇管道所需的資源，例如邏輯應用範本和範本參數檔，這些檔是手動生成的，或者作為生成過程的一部分。

1. 對於代理作業，查找並添加**Azure 資源組部署**任務。

   ![添加"Azure 資源組部署"任務](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用[服務主體](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)進行配置。

1. 添加對邏輯應用範本和範本參數檔的引用。

1. 視需要針對任何其他環境、自動化測試或核准者，繼續在發行程序中建置步驟。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授權 OAuth 連接

部署後，邏輯應用使用有效的參數端到端工作。 但是，您仍必須授權任何 OAuth 連接才能生成用於[驗證憑據](../active-directory/develop/authentication-scenarios.md)的有效訪問權杖。 以下是授權 OAuth 連接的方法：

* 對於自動部署，可以使用為每個 OAuth 連接提供同意的腳本。 下面是[邏輯應用連接Auth](https://github.com/logicappsio/LogicAppConnectionAuth)專案中 GitHub 中的示例腳本。

* 要手動授權 OAuth 連接，請在 Azure 門戶或 Visual Studio 中的邏輯應用設計器中打開邏輯應用。 在設計器中，授權任何必需的連接。

如果使用 Azure 活動目錄 （Azure AD）[服務主體](../active-directory/develop/app-objects-and-service-principals.md)來授權連接，請瞭解如何[在邏輯應用範本中指定服務主體參數](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視邏輯應用程式](../logic-apps/monitor-logic-apps.md)
