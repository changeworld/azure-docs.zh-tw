---
title: 用於 ML 實驗的 MLflow 跟蹤
titleSuffix: Azure Machine Learning
description: 使用 Azure 機器學習設置 MLflow 以記錄在 Databricks 群集、本地環境或 VM 環境中創建的 ML 模型中的指標和專案。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983693"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>使用 MLflow 和 Azure 機器學習跟蹤模型指標（預覽）

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文演示如何啟用 MLflow 的跟蹤 URI 和日誌記錄 API（統稱為[MLflow 跟蹤](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)）來連接 MLflow 實驗和 Azure 機器學習。 這樣做使您能夠在[Azure 機器學習工作區](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)中跟蹤和記錄實驗指標和專案。 如果您已使用 MLflow 跟蹤進行實驗，則工作區可提供集中、安全和可擴展的位置來存儲培訓指標和模型。

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org)是一個開源庫，用於管理機器學習實驗的生命週期。 MLFlow 跟蹤是 MLflow 的一個元件，用於記錄和跟蹤訓練運行指標和模型工件，無論您的實驗環境位於電腦上本地、遠端計算目標、虛擬機器或 Azure 資料磚塊群集上。 

下圖說明，通過 MLflow 跟蹤，可以跟蹤實驗的運行指標，並在 Azure 機器學習工作區中存儲模型專案。

![帶 Azure 機器學習圖的 mlflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 本文檔中的資訊主要面向想要監視模型培訓過程的資料科學家和開發人員。 如果您是對監視 Azure 機器學習中的資源使用方式和事件（如配額、已完成訓練運行或已完成的模型部署）感興趣的管理員，請參閱[監視 Azure 機器學習](monitor-azure-machine-learning.md)。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比較 MLflow 和 Azure 機器學習用戶端

 下表總結了可以使用 Azure 機器學習的不同用戶端及其各自的功能功能。

 MLflow 跟蹤提供指標日誌記錄和工件存儲功能，這些功能只能通過 Azure[機器學習 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)以其他方式提供。


| | ML流&nbsp;跟蹤 <!--& Deployment--> | Azure 機器學習 Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 管理工作區 |   | ✓ | ✓ | ✓ |
| 使用資料存儲  |   | ✓ | ✓ | |
| 日誌指標      | ✓ | ✓ |   | |
| 上傳專案 | ✓ | ✓ |   | |
| 檢視計量     | ✓ | ✓ | ✓ | ✓ |
| 管理計算   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Prerequisites

* [安裝 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* [Install the Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) on your local computer The SDK provides the connectivity for MLflow to access your workspace.
* [創建 Azure 機器學習工作區](how-to-manage-workspace.md)。

## <a name="track-local-runs"></a>跟蹤本地運行

通過 Azure 機器學習進行 MLflow 跟蹤，可以將本地運行中記錄的指標和專案存儲在 Azure 機器學習工作區中。

安裝`azureml-mlflow`包以在 Jupyter 筆記本或代碼編輯器中本地運行的實驗中使用 Azure 機器學習的 MLflow 跟蹤。

```shell
pip install azureml-mlflow
```

>[!NOTE]
>azureml.contrib 命名空間經常更改，因為我們正在改進服務。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

導入`mlflow`和[`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)類以訪問 MLflow 的跟蹤 URI 並配置工作區。

在以下代碼中，`get_mlflow_tracking_uri()`該方法向工作區分配了唯一的跟蹤 URI 位址，`ws`並將`set_tracking_uri()`MLflow 跟蹤 URI 指向該位址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>跟蹤 URI 的有效期最長為一小時或更短。 如果在閒置時間後重新開機腳本，請使用get_mlflow_tracking_uri API 獲取新的 URI。

使用 設置 MLflow`set_experiment()`實驗名稱，並開始使用`start_run()`運行。 然後使用`log_metric()`啟動 MLflow 日誌記錄 API 並開始記錄訓練運行指標。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>跟蹤遠端運行

通過 Azure 機器學習進行 MLflow 跟蹤，可以將遠端運行中記錄的指標和專案存儲在 Azure 機器學習工作區中。

通過遠端運行，您可以根據更強大的計算（如啟用 GPU 的虛擬機器或機器學習計算群集）對模型進行訓練。 請參閱[為模型培訓設置計算目標](how-to-set-up-training-targets.md)，以瞭解不同的計算選項。

使用[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)類配置計算和培訓運行環境。 在`mlflow`環境`azureml-mlflow`[`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)部分中包括和點點包。 然後用遠端[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)計算作為計算目標進行構造。

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

在培訓腳本中，導入`mlflow`以使用 MLflow 日誌記錄 API，並開始記錄運行指標。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

使用此計算和培訓回合組態，請使用 方法`Experiment.submit('train.py')`提交運行。 此方法會自動設置 MLflow 跟蹤 URI，並將日誌記錄從 MLflow 定向到工作區。

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>跟蹤 Azure 資料塊運行

通過 Azure 機器學習進行 MLflow 跟蹤，可以存儲 Azure 資料塊中記錄的指標和專案，這些指標和專案在 Azure 機器學習工作區中運行。

要使用 Azure 資料塊運行 Mlflow 實驗，需要首先創建[Azure 資料塊工作區和群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 在群集中，請確保從 PyPi 安裝*azureml-mlflow*庫，以確保群集有權訪問必要的函數和類。

在此處導入實驗筆記本，將其附加到 Azure 資料塊群集並運行實驗。 

### <a name="install-libraries"></a>安裝程式庫

要在群集上安裝庫，請導航到 **"庫"** 選項卡，然後按一下"**安裝新建"**

 ![帶 Azure 機器學習圖的 mlflow](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

在 **"包"** 欄位中，鍵入 azureml-mlflow，然後按一下"安裝"。 根據需要重複此步驟，以將其他附加包安裝到群集以進行實驗。

 ![帶 Azure 機器學習圖的 mlflow](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>設置筆記本和工作區

設置群集後，導入實驗筆記本，打開它並將群集附加到它。

以下代碼應位於實驗筆記本中。 此代碼獲取 Azure 訂閱的詳細資訊，以具現化工作區。 此代碼假定您具有現有資源組和 Azure 機器學習工作區，否則可以[創建它們](how-to-manage-workspace.md)。 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>連接 Azure 資料塊和 Azure 機器學習工作區

在[Azure 門戶](https://ms.portal.azure.com)上，可以將 Azure 資料塊 （ADB） 工作區連結到新的或現有的 Azure 機器學習工作區。 為此，請導航到亞行工作區，然後選擇右下角的 **"連結 Azure 機器學習工作區**"按鈕。 通過連結工作區，可以在 Azure 機器學習工作區中跟蹤實驗資料。 

### <a name="link-mlflow-tracking-to-your-workspace"></a>將 MLflow 跟蹤連結到工作區

具現化工作區後，設置 MLflow 跟蹤 URI。 通過執行此操作，可以將 MLflow 跟蹤連結到 Azure 機器學習工作區。 連結後，所有實驗都將在託管的 Azure 機器學習跟蹤服務中進行。

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>直接在筆記本中設置 MLflow 跟蹤

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在訓練腳本中，導入 mlflow 以使用 MLflow 日誌記錄 API，並開始記錄運行指標。 下面的示例，記錄紀元損失指標。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>自動設置 MLflow 跟蹤

與其在群集上每個後續實驗筆記本會話中手動設置跟蹤 URI，請使用此[Azure 機器學習跟蹤群集 Init 腳本](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)自動執行此操作。

正確配置後，您可以通過 MLflow 使用者介面或使用 MLflow 用戶端在 Azure 機器學習 REST API 和所有用戶端以及 Azure 資料塊中查看 MLflow 跟蹤資料。

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>在工作區中查看指標和專案

MLflow 日誌記錄中的指標和專案將保留在工作區中。 要隨時查看它們，請導航到工作區，並在[Azure 機器學習工作室](https://ml.azure.com)的工作區中按名稱查找實驗。  或運行以下代碼。 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Notebook 範例

[使用 Azure ML 筆記本的 MLflow](https://aka.ms/azureml-mlflow-examples)演示並擴展本文仲介紹的概念。

## <a name="next-steps"></a>後續步驟
* [管理您的模型](concept-model-management-and-deployment.md)。
* 監視生產模型，以進行[資料漂移](how-to-monitor-data-drift.md)。
