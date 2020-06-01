---
title: 教學課程 - 建立 Azure ML 工作區 - Resource Manager 範本
description: 在本教學課程中，您會使用 Azure Resource Manager 範本來快速部署用於機器學習的 Azure 工作區
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790712"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>教學課程：使用 Resource Manager 範本部署 Azure Machine Learning 工作區
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教學課程將說明如何使用 Azure Resource Manager 範本建立 Azure Machine Learning 工作區。 Azure Machine Learning 工作區會將您所有的機器學習資產從基準資料集組織到已部署的模型。 工作區是與同事共同作業的單一位置，可用來建立、執行及檢視實驗、管理您的訓練和推斷計算資源，以及監視部署的模型並控管版本。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://aka.ms/AMLFree)

* 若要從您的**本機環境**使用本文件中的 CLI 命令，您需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>建立工作區

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-machine-learning-create/)。

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

範本中會定義下列資源：

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces):建立 Azure ML 工作區。 在此範本中，位置和名稱是使用者可以傳入或以互動方式輸入的參數。

### <a name="deploy-the-template"></a>部署範本 

若要從 Azure CLI 使用範本，請登入並選擇您的訂用帳戶 (請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest))。 然後執行：

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

當您執行上述命令時，請輸入：

1. 專案名稱，這會成為已建立資源群組和 Azure ML 工作區名稱的基礎
1. 您想要在其中進行部署的 Azure 位置

## <a name="review-deployed-resources"></a>檢閱已部署的資源

若要查看您的 Azure ML 工作區：

1. 移至 https://portal.azure.com 。 
1. 登入 
1. 選擇您剛建立的工作區

您會看到 Azure Machine Learning 首頁： 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Azure ML 工作區的螢幕擷取畫面":::

若要查看與部署相關聯的所有資源，請按一下左上方工作區名稱的連結 (也就是螢幕擷取畫面中的 `my_templated_ws`)。 該連結會帶您前往 Azure 入口網站中的資源群組。 資源群組的名稱為 `{projectName}rg`，而工作區的名稱為 `{projectName}ws`。

## <a name="clean-up-resources"></a>清除資源

如果您不想要使用此工作區，請將其刪除。 由於工作區會與其他資源 (例如儲存體帳戶) 相關聯，因此建議您刪除已建立的整個資源群組。 您可以使用入口網站來刪除資源群組，只要按一下 [刪除] 按鈕並確認即可。 或者，您可以使用下列方式，從 CLI 刪除資源群組： 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已從 Azure Resource Manager 範本建立了 Azure Machine Learning 工作區。 如果您想要探索 Azure Machine Learning，請繼續進行此教學課程。 

> [!div class="nextstepaction"]
> [教學課程：使用 Python SDK 開始建立您的第一個 ML 實驗](tutorial-1st-experiment-sdk-setup.md)
