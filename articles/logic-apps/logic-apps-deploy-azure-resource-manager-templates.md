---
title: 部署邏輯應用程式範本
description: 瞭解如何部署為 Azure Logic Apps 建立的 Azure Resource Manager 範本
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 81fad94dc02bd57f839d7ab8653bec7074e41800
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076335"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>部署 Azure Logic Apps 的 Azure Resource Manager 範本

建立邏輯應用程式的 Azure Resource Manager 範本之後，您可以透過下列方式來部署範本：

* [Azure 入口網站](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>透過 Azure 入口網站部署

若要自動將邏輯應用程式範本部署至 Azure，您可以選擇下列 [ **部署至 azure** ] 按鈕，這會將您登入 Azure 入口網站，並提示您提供邏輯應用程式的相關資訊。 然後，您可以對邏輯應用程式範本或參數進行任何必要的變更。

[![部署至 Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，在您登入 Azure 入口網站之後，系統會提示您輸入下列資訊：

* Azure 訂用帳戶名稱
* 您想要使用的資源群組
* 邏輯應用程式位置
* 邏輯應用程式的名稱
* 測試 URI
* 接受指定的條款和條件

如需詳細資訊，請參閱下列主題：

* [總覽：使用 Azure Resource Manager 範本將邏輯應用程式的部署自動化](logic-apps-azure-resource-manager-templates-overview.md)
* [使用 Azure Resource Manager 範本和 Azure 入口網站部署資源](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>透過 Visual Studio 部署

若要從您使用 Visual Studio 建立的 Azure 資源群組專案部署邏輯應用程式範本，請遵循下列 [步驟，手動將邏輯應用程式部署](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 到 azure。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署到特定的 *Azure 資源群組*，請使用下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題：

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

若要部署到特定的 *Azure 資源群組*，請使用下列命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題：

* [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 部署

若要部署邏輯應用程式範本和管理環境，團隊通常會在[Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services)中使用[Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)之類的工具。 Azure Pipelines 提供可新增至任何組建或發行管線的 [Azure 資源群組部署](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) 工作。 針對部署和產生發行管線的授權，您也需要 (AD) [服務主體](../active-directory/develop/app-objects-and-service-principals.md)的 Azure Active Directory。 深入瞭解如何搭配 [使用服務主體與 Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure)。

如需持續整合和持續部署的詳細資訊 (CI/CD) 適用于 Azure Resource Manager 的範本 Azure Pipelines，請參閱下列主題和範例：

* [整合 Resource Manager 範本與 Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教學課程：Azure Resource Manager 範本與 Azure Pipelines 的持續整合](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [範例：從 Azure Logic Apps 連接到 Azure 服務匯流排佇列，並使用 Azure DevOps 中的 Azure Pipelines 進行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到 Azure 儲存體帳戶，並使用 Azure DevOps 中的 Azure Pipelines 進行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：為 Azure Logic Apps 設定函數應用程式動作，並使用 Azure DevOps 中的 Azure Pipelines 進行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到整合帳戶，並使用 Azure DevOps 中的 Azure Pipelines 進行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [範例：使用 Azure Logic Apps 協調 Azure Pipelines](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

以下是使用 Azure Pipelines 的一般高階步驟：

1. 在 Azure Pipelines 中，建立空的管線。

1. 選擇管線所需的資源，例如您的邏輯應用程式範本和範本參數檔案，這些檔案會以手動方式或作為組建流程的一部分來產生。

1. 針對您的代理程式作業，尋找並新增 **Azure 資源群組部署** 工作。

   ![新增 [Azure 資源群組部署] 工作](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用 [服務主體](/azure/devops/pipelines/library/connect-to-azure)進行設定。

1. 將參考新增至您的邏輯應用程式範本和範本參數檔案。

1. 視需要針對任何其他環境、自動化測試或核准者，繼續在發行程序中建置步驟。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授權 OAuth 連接

部署之後，您的邏輯應用程式會以有效的參數以端對端運作，但若要產生有效的存取權杖來 [驗證您的認證](../active-directory/develop/authentication-vs-authorization.md)，您仍然必須授權或使用預先授權 OAuth 連線。 不過，您只需要部署及驗證 API 連線資源一次，這表示您不需要在後續的部署中包含這些連接資源，除非您必須更新連接資訊。 如果您使用持續整合和持續部署管線，則只會部署更新的 Logic Apps 資源，而不需要每次都重新授權連線。

以下是處理授權連接的一些建議：

* 跨相同區域中的邏輯應用程式，Preauthorize 及共用 API 連線資源。 API 連線是獨立于邏輯應用程式的 Azure 資源。 雖然邏輯應用程式具有 API 連線資源的相依性，但 API 連線資源沒有邏輯應用程式的相依性，而且會在您刪除相依的邏輯應用程式之後繼續進行。 此外，邏輯應用程式可以使用存在於其他資源群組中的 API 連接。 不過，邏輯應用程式設計工具只支援在與邏輯應用程式相同的資源群組中建立 API 連接。

  > [!NOTE]
  > 如果您考慮共用 API 連線，請確定您的解決方案可以 [處理潛在的節流問題](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling)。 節流會在連接層級進行，因此在多個邏輯應用程式中重複使用相同的連線，可能會增加節流問題的可能性。

* 除非您的案例牽涉到需要多重要素驗證的服務和系統，否則您可以使用 PowerShell 腳本來為每個 OAuth 連線提供同意，方法是在具有已提供授權和同意的作用中瀏覽器會話的虛擬機器上，以一般使用者帳戶的形式執行持續整合背景工作角色。 例如，您可以重新規劃 [Logic Apps GitHub 存放庫中 LogicAppConnectionAuth 專案](https://github.com/logicappsio/LogicAppConnectionAuth)所提供的範例腳本。

* 在邏輯應用程式設計工具中開啟邏輯應用程式，在 Azure 入口網站或 Visual Studio 中開啟邏輯應用程式，以手動授權 OAuth 連接。

* 如果您使用 Azure Active Directory (Azure AD) 的 [服務主體](../active-directory/develop/app-objects-and-service-principals.md) 來授權連接，請瞭解如何 [在邏輯應用程式範本中指定服務主體參數](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Logic Apps](../logic-apps/monitor-logic-apps.md)
