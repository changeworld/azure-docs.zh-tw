---
title: 將 MLflow 模型部署為 web 服務
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設定 MLflow，以將 ML 模型部署為 Azure web 服務。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: aaa7dbf2ae7c8acb3b3beeb3e9098c5058af26a7
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918124"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>將 MLflow 模型部署為 Azure web 服務 (預覽版) 

在本文中，您將瞭解如何將 [MLflow](https://www.mlflow.org) 模型部署為 Azure web 服務，讓您可以運用 Azure Machine Learning 的模型管理和資料漂移偵測功能，並將其套用至您的生產模型。

Azure Machine Learning 提供下列專案的部署設定：
* Azure 容器實例 (ACI) 這是適用于快速開發/測試部署的理想選擇。
* Azure Kubernetes Service (AKS) 建議用於可調整的生產環境部署。
> [!TIP]
> 本檔中的資訊主要是供想要將其 MLflow 模型部署到 Azure Machine Learning web 服務端點的資料科學家和開發人員使用。 如果您是系統管理員，且想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型執行或已完成的模型部署)，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。
## <a name="mlflow-with-azure-machine-learning-deployment"></a>Azure Machine Learning 部署的 MLflow

MLflow 是一個開放原始碼程式庫，可用於管理機器學習實驗的生命週期。 其與 Azure Machine Learning 的整合可讓您將此管理延伸到模型定型以外的生產模型部署階段。

下圖說明使用 MLflow 部署 API 和 Azure Machine Learning 時，您可以將使用熱門架構（例如 PyTorch、Tensorflow、scikit-learn 學習等）建立的模型部署為 Azure web 服務，並在您的工作區中加以管理。 

![ 使用 azure machine learning 部署 mlflow 模型](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> 作為開放原始碼程式庫，MLflow 經常變更。 因此，透過 Azure Machine Learning 和 MLflow 整合所提供的功能，應該視為預覽，而且 Microsoft 不會完全支援。

## <a name="prerequisites"></a>先決條件

* 機器學習模型。 如果您沒有已定型的模型，請在 [此](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) 存放庫中尋找最適合您計算案例的筆記本範例，並遵循其指示。 
* [設定 MLflow 追蹤 URI 以連接 Azure Machine Learning](how-to-use-mlflow.md#track-local-runs)。
* 安裝 `azureml-mlflow` 套件。 
    * 此套件會自動帶入 `azureml-core` [AZURE MACHINE LEARNING Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)，以提供 MLflow 的連線能力以存取您的工作區。
* 查看 [您在工作區中執行 MLflow 作業所需的存取權限](how-to-assign-roles.md#mlflow-operations)。 

## <a name="deploy-to-azure-container-instance-aci"></a> (ACI) 部署至 Azure 容器實例

若要將 MLflow 模型部署到 Azure Machine Learning web 服務，您必須使用 [MLflow 追蹤 URI 來](how-to-use-mlflow.md)設定您的模型，以連接 Azure Machine Learning。 

使用 [deploy_configuration ( # B1 ](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 方法設定您的部署設定。 您也可以新增標記和描述，以協助追蹤您的 web 服務。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

然後，使用 MLflow 的 Azure Machine Learning [部署](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 方法，在一個步驟中註冊和部署模型。 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-azure-kubernetes-service-aks"></a>部署到 Azure Kubernetes Service (AKS)

若要將 MLflow 模型部署到 Azure Machine Learning web 服務，您必須使用 [MLflow 追蹤 URI 來](how-to-use-mlflow.md)設定您的模型，以連接 Azure Machine Learning。 

若要部署至 AKS，請先建立 AKS 叢集。 使用 ComputeTarget 建立 AKS 叢集 [。建立 ( # B1 ](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) 方法。 建立新的叢集可能需要20-25 分鐘的時間。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
使用 [deploy_configuration ( # B1 ](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 方法設定您的部署設定。 您也可以新增標記和描述，以協助追蹤您的 web 服務。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

然後，使用 MLflow 的 Azure Machine Learning [部署](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 方法，在一個步驟中註冊和部署模型。 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

服務部署可能需要幾分鐘的時間。

## <a name="clean-up-resources"></a>清除資源

如果您不打算使用已部署的 web 服務，請使用 `service.delete()` 將其從您的筆記本刪除。  如需詳細資訊，請參閱 WebService 的檔集 [。刪除 ( # B1 ](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)。

## <a name="example-notebooks"></a>Notebook 範例

[Azure Machine Learning 筆記本的 MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow)會示範並擴充本文中所呈現的概念。

> [!NOTE]
> 您可以在中找到使用 mlflow 之範例的社區驅動存放庫 https://github.com/Azure/azureml-examples 。

## <a name="next-steps"></a>後續步驟

* [管理您的模型](concept-model-management-and-deployment.md)。
* 監視生產模型是否有[資料漂移](./how-to-enable-data-collection.md)。
* [使用 MLflow 追蹤 Azure Databricks 執行](how-to-use-mlflow-azure-databricks.md)。

