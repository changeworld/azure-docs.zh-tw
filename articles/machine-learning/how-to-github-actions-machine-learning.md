---
title: 適用於 CI/CD 的 GitHub Actions
titleSuffix: Azure Machine Learning
description: 瞭解如何建立 GitHub Actions 工作流程來定型模型 Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: f5abeee3b1e1e44f5ce0423fc7f8170644d74508
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880469"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>搭配 Azure Machine Learning 使用 GitHub Actions

開始使用 [GitHub Actions](https://docs.github.com/en/actions) ，在 Azure Machine Learning 上訓練模型。 

> [!NOTE]
> 適用于 Azure Machine Learning 的 GitHub Actions 會依原樣提供，而且 Microsoft 不會完全支援。 如果您遇到特定動作的問題，請在該動作的儲存機制中開啟問題。 例如，如果您遇到 aml-部署動作的問題，請報告存放庫中的問題 [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) 。

## <a name="prerequisites"></a>必要條件 

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub 帳戶。 如果您沒有帳戶，請[免費](https://github.com/join)註冊。  

## <a name="workflow-file-overview"></a>工作流程檔案概觀

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

檔案有四個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1.定義服務主體。 <br /> 2.建立 GitHub 祕密。 |
|**[連接]** | 1. 連接到機器學習服務工作區。 <br /> 2. 連接到計算目標。 |
|**執行** | 1. 提交定型回合。 |
|**部署** | 1. 在 Azure Machine Learning 登錄中註冊模型。 1. 部署模型。 |

## <a name="create-repository"></a>建立存放庫

[使用 GitHub Actions 和 Azure Machine Learning 範本，從 ML Ops](https://github.com/machine-learning-apps/ml-template-azure)建立新的存放庫。 

1. 在 GitHub 上開啟 [範本](https://github.com/machine-learning-apps/ml-template-azure) 。 
2. 選取 [使用此範本]。 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="選取 [使用此範本]":::
3. 從範本建立新的存放庫。 將存放庫名稱設定為 `ml-learning` 或您選擇的名稱。 


## <a name="generate-deployment-credentials"></a>產生部署認證

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 請使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

在上述範例中，請將預留位置取代為您的訂用帳戶識別碼、資源組名和應用程式名稱。 輸出是一個 JSON 物件，內有角色指派認證可讓您存取 App Service 應用程式，如下所示。 複製此 JSON 物件以供後續使用。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>設定 GitHub 密碼

1. 在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

2. 將得自 Azure CLI 命令的整個 JSON 輸出貼到祕密的 [值] 欄位中。 將祕密命名為 `AZURE_CREDENTIALS`。

## <a name="connect-to-the-workspace"></a>連線到工作區

使用 [ [Azure Machine Learning 工作區] 動作](https://github.com/marketplace/actions/azure-machine-learning-workspace) 來連線至您的 Azure Machine Learning 工作區。 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

根據預設，動作預期會有一個檔案 `workspace.json` 。 如果您的 JSON 檔案具有不同的名稱，您可以使用輸入參數來指定它 `parameters_file` 。 如果沒有檔案，則會使用存放庫名稱來建立新的檔案。


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
動作會將工作區 Azure Resource Manager (ARM) 屬性寫入至設定檔，以供所有未來的 Azure Machine Learning GitHub Actions 挑選。 檔案會儲存至 `GITHUB_WORKSPACE/aml_arm_config.json` 。 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>連接到 Azure Machine Learning 中的計算目標

使用 [Azure Machine Learning 計算動作](https://github.com/Azure/aml-compute) 來連線至 Azure Machine Learning 中的計算目標。  如果計算目標存在，該動作將會連接到該目標。 否則，此動作會建立新的計算目標。 [AML 計算動作](https://github.com/Azure/aml-compute)僅支援 Azure ML 計算叢集和 AZURE KUBERNETES SERVICE (AKS) 。 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>提交定型回合

使用 [Azure Machine Learning 定型動作](https://github.com/Azure/aml-run) 將 ScriptRun、估算器或管線提交至 Azure Machine Learning。 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>登錄中的註冊模型

使用 [Azure Machine Learning 註冊模型] 動作](https://github.com/Azure/aml-registermodel) 來註冊要 Azure Machine Learning 的模型。

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>將模型部署至 ACI 的 Azure Machine Learning

使用 [Azure Machine Learning 部署動作](https://github.com/Azure/aml-deploy) 來部署模型，並為模型建立端點。 您也可以使用 Azure Machine Learning 部署來部署至 Azure Kubernetes Service。 請參閱 [此範例工作流程](https://github.com/Azure-Samples/mlops-enterprise-template) ，以瞭解部署至 Azure Kubernetes Service 的模型。

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>完整範例

定型您的模型，並部署至 Azure Machine Learning。 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>清除資源

當您的資源群組和儲存機制不再需要時，請刪除資源群組和您的 GitHub 存放庫，以清除您所部署的資源。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure Machine Learning SDK 來建立及執行機器學習管線](./how-to-create-machine-learning-pipelines.md)