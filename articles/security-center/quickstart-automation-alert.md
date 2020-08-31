---
title: 使用 Azure Resource Manager (ARM) 範本建立特定安全性警示的安全性自動化
description: 了解如何建立 Azure 資訊安全中心自動化以觸發邏輯應用程式，您可以藉由使用 Azure Resource Manager (ARM) 範本由特定資訊安全中心警示來觸發
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: ecef318978194ac3773c54e2d9c960781086de65
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687115"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>快速入門：使用 Azure Resource Manager 範本 (ARM 範本) 來建立特定安全性警示的自動回應

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立工作流程自動化，以在 Azure 資訊安全中心收到特定安全性警示時觸發邏輯應用程式。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如需要使用 Azure 資訊安全中心的工作流程自動化功能所需的角色和權限清單，請參閱[工作流程自動化](workflow-automation.md)。


## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/)。

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>相關資源

- [**Microsoft.Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider):此自動化會在收到包含特定字串的 Azure 資訊安全中心警示時觸發邏輯應用程式。
- [**Microsoft.Logic/workflows**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider):空的可觸發邏輯應用程式。

如需其他資訊安全中心快速入門範本，請參閱這些[社群貢獻的範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security)。

## <a name="deploy-the-template"></a>部署範本

  - **PowerShell**:
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **CLI**：
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **入口網站**：

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    若要尋找此部署選項的詳細資訊，請參閱[使用部署按鈕從 GitHub 存放庫部署範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

使用 Azure 入口網站來檢查是否已部署工作流程自動化。 

1. 從 [Azure 入口網站](https://portal.azure.com)中開啟**資訊安全中心**。
1. 從頂端功能表列選取 [篩選] 圖示，然後選取您在其上部署新工作流程自動化的特定訂用帳戶。
1. 從資訊安全中心的提要欄位中開啟**工作流程自動化**，並檢查您的新自動化。

    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="影像替代文字。" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::

    >[!TIP]
    > 如果您的訂用帳戶有許多工作流程自動化，請使用 [依名稱篩選] 選項。 

## <a name="clean-up-resources"></a>清除資源

不再需要時，請使用 Azure 入口網站刪除工作流程自動化。

1. 從 [Azure 入口網站](https://portal.azure.com)中開啟**資訊安全中心**。
1. 從頂端功能表列選取 [篩選] 圖示，然後選取您在其上部署新工作流程自動化的特定訂用帳戶。
1. 從資訊安全中心的提要欄位中開啟**工作流程自動化**，並尋找要刪除的自動化。

    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="影像替代文字。" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::

1. 選取所要刪除項目的核取方塊。
1. 從工具列中選取 [刪除]。


## <a name="next-steps"></a>後續步驟

如需逐步教學課程，以引導您完成建立範本的流程，請參閱：

> [!div class="nextstepaction"]
> [教學課程：建立及部署您的第一個 ARM 範本](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)