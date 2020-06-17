---
title: 適用於 ML 實驗的 MLflow 追蹤
titleSuffix: Azure Machine Learning
description: 針對 Databricks 叢集、本機環境或 VM 環境中建立的 ML 模型，使用 Azure Machine Learning 設定可記錄其計量和成品的 MLflow。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779115"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>使用 MLflow 和 Azure Machine Learning 來追蹤模型計量 (預覽)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文將示範如何啟用 MLflow 的追蹤 URI 和記錄 API (統稱為 [MLflow 追蹤](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))，來連線您的 MLflow 實驗和 Azure Machine Learning。 這麼做可讓您在 [Azure Machine Learning 工作區](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)中追蹤並記錄實驗計量和成品。 如果您已針對實驗使用 MLflow 追蹤，工作區會提供集中、安全且可擴充的位置來儲存訓練計量和模型。

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) 是一個開放原始碼程式庫，可用於管理機器學習實驗的生命週期。 MLFlow 追蹤是 MLflow 的元件，不論您的實驗環境是在本機電腦、遠端計算目標、虛擬機器或 Azure Databricks 叢集上，此元件都能記錄及追蹤您的訓練執行計量和模型成品。 

下圖說明使用 MLflow 追蹤時，您可以追蹤實驗的執行計量，並將模型成品儲存在 Azure Machine Learning 工作區中。

![mlflow 與 azure machine learning 的圖表](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 本文件中的資訊主要適用於想要監視模型定型程序的資料科學家和開發人員。 如果您是系統管理員，且想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型執行或已完成的模型部署)，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比較 MLflow 和 Azure Machine Learning 用戶端

 下表摘要說明可以使用 Azure Machine Learning 的不同用戶端，以及其各自的函式功能。

 MLflow 追蹤提供的計量記錄和成品儲存功能，僅可透過 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 取得。


| | MLflow&nbsp;追蹤 <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 管理工作區 |   | ✓ | ✓ | ✓ |
| 使用資料存放區  |   | ✓ | ✓ | |
| 記錄計量      | ✓ | ✓ |   | |
| 上傳成品 | ✓ | ✓ |   | |
| 檢視計量     | ✓ | ✓ | ✓ | ✓ |
| 管理計算   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Prerequisites

* [安裝 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* 在您的本機電腦上[安裝 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) SDK 會為 MLflow 提供連線能力，以存取您的工作區。
* [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="track-local-runs"></a>追蹤本機執行

搭配 Azure Machine Learning 的 MLflow 追蹤可讓您將從本機執行中記錄的計量和成品儲存至 Azure Machine Learning 工作區。

安裝 `azureml-mlflow` 套件，以在本機中使用 Jupyter Notebook 或程式碼編輯器執行的實驗上使用 MLflow 追蹤和 Azure Machine Learning。

```shell
pip install azureml-mlflow
```

>[!NOTE]
>azureml.contrib 命名空間會因為我們致力於改善本服務而經常變更。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

匯入 `mlflow` 和 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) 類別來存取 MLflow 的追蹤 URI，並設定您的工作區。

在下列程式碼中，`get_mlflow_tracking_uri()` 方法會將唯一的追蹤 URI 位址指派給工作區 (`ws`)，而 `set_tracking_uri()` 會將 MLflow 追蹤 URI 指向該位址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>追蹤 URI 的有效時間上限為一小時或更短。 如果您在某段閒置時間之後重新啟動指令碼，請使用 get_mlflow_tracking_uri API 來取得新的 URI。

使用 `set_experiment()` 設定 MLflow 實驗名稱，然後使用 `start_run()` 開始執行訓練。 然後，使用 `log_metric()` 來啟用 MLflow 記錄 API，並開始記錄您的訓練執行計量。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>追蹤遠端執行

搭配 Azure Machine Learning 的 MLflow 追蹤可讓您將從遠端執行中記錄的計量和成品儲存至 Azure Machine Learning 工作區。

遠端執行可讓您以更強大的計算 (例如啟用 GPU 的虛擬機器或 Machine Learning Compute 叢集) 來訓練模型。 如需了解不同計算選項，請參閱[設定用於模型訓練的計算目標](how-to-set-up-training-targets.md)。

使用 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 類別來設定您的計算和訓練執行環境。 在環境的 [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 區段中包含 `mlflow` 和 `azureml-mlflow` pip 套件。 然後以您的遠端計算作為計算目標來建立 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)。

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

在訓練指令碼中，匯入 `mlflow` 以使用 MLflow 記錄 API，並開始記錄執行計量。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

透過此計算和訓練回合組態，使用 `Experiment.submit('train.py')` 方法來提交執行。 此方法會自動設定 MLflow 追蹤 URI，並將記錄從 MLflow 導向您的工作區。

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>追蹤 Azure Databricks 執行

搭配 Azure Machine Learning 的 MLflow 追蹤可讓您將從 Azure Databricks 執行中記錄的計量和成品儲存至 Azure Machine Learning 工作區。

若要搭配 Azure Databricks 執行您的 Mlflow 實驗，您必須先建立 [Azure Databricks 工作區和叢集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 在您的叢集中，請務必從 PyPi 安裝 azureml-mlflow 程式庫，以確保您的叢集能夠存取必要的功能和類別。

從這裡匯入您的實驗筆記本，將其附加至您的 Azure Databricks 叢集並執行實驗。 

### <a name="install-libraries"></a>安裝程式庫

若要在叢集上安裝程式庫，請流覽至 [程式庫] 索引標籤，然後按一下 [安裝新程式庫]

 ![mlflow 與 azure machine learning 的圖表](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

在 [套件] 欄位中輸入 azureml-mlflow，然後按一下 [安裝]。 視需要重複此步驟，以將其他套件安裝到您的叢集來進行實驗。

 ![mlflow 與 azure machine learning 的圖表](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>設定您的筆記本和工作區

一旦您的叢集設定完成，請匯入實驗筆記本並將其開啟，然後將您的叢集附加到其中。

下列程式碼應該位於您的實驗筆記本中。 此程式碼會取得您 Azure 訂用帳戶的詳細資料，以具現化工作區。 此程式碼假設您已有現有的資源群組和 Azure Machine Learning 工作區，否則的話，您也可以[建立這些項目](how-to-manage-workspace.md)。 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>連結您的 Azure Databricks 和 Azure Machine Learning 工作區

在 [Azure 入口網站](https://ms.portal.azure.com)上，您可以將 Azure Databricks (ADB) 工作區連結至新的或現有的 Azure Machine Learning 工作區。 若要這麼做，請瀏覽至您的 ADB 工作區，然後選取右下方的 [連結 Azure Machine Learning 工作區] 按鈕。 連結您的工作區可讓您在 Azure Machine Learning 工作區中追蹤實驗資料。 

### <a name="link-mlflow-tracking-to-your-workspace"></a>將 MLflow 追蹤連結至您的工作區

在您具現化工作區之後，請設定 MLflow 追蹤 URI。 如此一來，您就可以將 MLflow 追蹤連結到 Azure Machine Learning 工作區。 連結之後，您的所有實驗都會進入受控 Azure Machine Learning 追蹤服務。

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>直接在您的筆記本中設定 MLflow 追蹤

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在訓練指令碼中，匯入 mlflow 以使用 MLflow 記錄 API，並開始記錄執行計量。 下列範例會記錄 Epoch 遺失計量。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>自動設定 MLflow 追蹤

您無須在叢集上的每個後續實驗筆記本工作階段中手動設定追蹤 URI，而是使用此 [Azure Machine Learning 追蹤叢集 Init 指令碼](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)來自動執行此動作。

正確設定後，您可以在 Azure Machine Learning REST API 和所有用戶端中看到您的 MLflow 追蹤資料，也可以透過 MLflow 使用者介面或使用 MLflow 用戶端在 Azure Databricks 中看到這些資料。

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>在您的工作區中檢視計量和成品

MLflow 記錄中的計量和成品會保留在您的工作區中。 若要隨時查看，請瀏覽至您的工作區，然後在 [Azure Machine Learning Studio](https://ml.azure.com) 工作區中，以名稱尋找實驗。  或執行下列程式碼。 

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

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

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

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
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

使用[搭配 Azure ML 筆記本的 MLflow](https://aka.ms/azureml-mlflow-examples) 示範和擴充本文所述的概念。

## <a name="next-steps"></a>後續步驟
* [管理您的模型](concept-model-management-and-deployment.md)。
* 監視生產模型是否有[資料漂移](how-to-monitor-data-drift.md)。
