---
title: 適用於 ML 實驗的 MLflow 追蹤
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設定 MLflow，以從 ML 模型記錄計量和成品，並將您的 ML 模型部署為 web 服務。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a093fe330ccbecc33cd8dac03d6425655e90366d
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760464"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>使用 MLflow 和 Azure Machine Learning (preview 來定型和追蹤 ML 模型) 

在本文中，您將瞭解如何啟用 MLflow 的追蹤 URI 和記錄 API，統稱為 [MLflow 追蹤](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)，以將 Azure Machine Learning 連接為 MLflow 實驗的後端。 

支援的功能包括： 

+ 在您的 [Azure Machine Learning 工作區](./concept-azure-machine-learning-architecture.md#workspace)中追蹤並記錄實驗計量和構件。 如果您已針對實驗使用 MLflow 追蹤，工作區會提供集中、安全且可擴充的位置來儲存訓練計量和模型。

+ 使用 Azure Machine Learning 後端支援 (preview) 的 [MLflow 專案](https://www.mlflow.org/docs/latest/projects.html) 提交定型作業。 您可以使用 Azure Machine Learning 追蹤在本機提交作業，或透過 [Azure Machine Learning 計算](./how-to-create-attach-compute-cluster.md)將執行遷移至雲端。

+ 在 MLflow 和 Azure Machine Learning 模型登錄中追蹤和管理模型。

[MLflow](https://www.mlflow.org) 是一個開放原始碼程式庫，可用於管理機器學習實驗的生命週期。 MLFlow 追蹤是 MLflow 的元件，無論您的實驗環境是在本機電腦上、遠端計算目標、虛擬機器或 [Azure Databricks](how-to-use-mlflow-azure-databricks.md)叢集，都可記錄和追蹤您的定型回合計量和模型成品。 

>[!NOTE]
> 作為開放原始碼程式庫，MLflow 經常變更。 因此，透過 Azure Machine Learning 和 MLflow 整合所提供的功能，應該視為預覽，而且 Microsoft 不會完全支援。

下圖說明使用 MLflow 追蹤時，您可以追蹤實驗的執行計量，並將模型成品儲存在 Azure Machine Learning 工作區中。

![mlflow 與 azure machine learning 的圖表](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 本文件中的資訊主要適用於想要監視模型定型程序的資料科學家和開發人員。 如果您是系統管理員，且想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型執行或已完成的模型部署)，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比較 MLflow 和 Azure Machine Learning 用戶端

 下表摘要說明可以使用 Azure Machine Learning 的不同用戶端，以及各自的函式功能。

 MLflow 追蹤提供的計量記錄和成品儲存功能，僅可透過 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 取得。

| 功能 | MLflow 追蹤 & 部署 | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 管理工作區 |   | ✓ | ✓ | ✓ |
| 使用資料存放區  |   | ✓ | ✓ | |
| 記錄計量      | ✓ | ✓ |   | |
| 上傳成品 | ✓ | ✓ |   | |
| 檢視計量     | ✓ | ✓ | ✓ | ✓ |
| 管理計算   |   | ✓ | ✓ | ✓ |
| 部署模型    | ✓ | ✓ | ✓ | ✓ |
|監視模型效能||✓|  |   |
| 偵測資料漂移 |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Prerequisites

* 安裝 `azureml-mlflow` 套件。 
    * 此套件會自動帶入 `azureml-core` [AZURE MACHINE LEARNING Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)，以提供 MLflow 的連線能力以存取您的工作區。
* [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="track-local-runs"></a>追蹤本機執行

搭配 Azure Machine Learning 的 MLflow 追蹤可讓您將從本機執行中記錄的計量和成品儲存至 Azure Machine Learning 工作區。

匯入 `mlflow` 和 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py) 類別來存取 MLflow 的追蹤 URI，並設定您的工作區。

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

遠端執行可讓您以更強大的計算 (例如啟用 GPU 的虛擬機器或 Machine Learning Compute 叢集) 來訓練模型。 請參閱 [使用計算目標進行模型定型](how-to-set-up-training-targets.md) ，以瞭解不同的計算選項。

搭配 Azure Machine Learning 的 MLflow 追蹤可讓您將從遠端執行中記錄的計量和成品儲存至 Azure Machine Learning 工作區。 任何在其中執行 MLflow 追蹤程式碼的執行，都會自動將計量記錄到工作區。 

下列範例 conda 環境包含 `mlflow` 和 `azureml-mlflow` 做為 pip 套件。 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

在您的腳本中，使用類別設定您的計算和定型執行環境 [`Environment`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) 。 然後，  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) 使用您的遠端計算來建立計算目標。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

透過此計算和訓練回合組態，使用 `Experiment.submit()` 方法來提交執行。 此方法會自動設定 MLflow 追蹤 URI，並將記錄從 MLflow 導向您的工作區。

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>使用 MLflow Projects 定型

[MLflow Projects](https://mlflow.org/docs/latest/projects.html) 可讓您組織和描述您的程式碼，讓其他資料科學家 (或自動化工具) 執行它。 使用 Azure Machine Learning 的 MLflow 專案可讓您在工作區中追蹤和管理定型回合。 

此範例示範如何使用 Azure Machine Learning 追蹤在本機提交 MLflow 專案。

安裝 `azureml-mlflow` 套件，以在本機實驗上搭配 Azure Machine Learning 使用 MLflow 追蹤。 您可以透過 Jupyter Notebook 或程式碼編輯器來執行您的實驗。

```shell
pip install azureml-mlflow
```

匯入 `mlflow` 和 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py) 類別來存取 MLflow 的追蹤 URI，並設定您的工作區。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

使用 `set_experiment()` 設定 MLflow 實驗名稱，然後使用 `start_run()` 開始執行訓練。 然後，使用 `log_metric()` 啟動 MLflow 記錄 API，並開始記錄您的定型回合計量。

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

建立後端設定物件來儲存整合的必要資訊，例如，計算目標，以及要使用的 managed 環境類型。

```python
backend_config = {"USE_CONDA": False}
```
將 `azureml-mlflow` 套件作為 pip 相依性新增至您的環境設定檔，以便在您的工作區中追蹤計量和關鍵成品。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
提交本機執行，並確定您已設定參數 `backend = "azureml" ` 。 使用此設定時，您可以在本機提交執行，並在您的工作區中取得自動輸出追蹤、記錄檔、快照和列印錯誤的新增支援。 

在 [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)中，查看您的執行和計量。 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>在您的工作區中檢視計量和成品

MLflow 記錄中的計量和成品會保留在您的工作區中。 若要隨時查看，請瀏覽至您的工作區，然後在 [Azure Machine Learning Studio](https://ml.azure.com) 工作區中，以名稱尋找實驗。  或執行下列程式碼。 

```python
run.get_metrics()
```

## <a name="manage-models"></a>管理模型 

使用支援 MLflow 模型登錄的 [Azure Machine Learning 模型](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) 登錄來註冊及追蹤您的模型。 Azure Machine Learning 模型會與 MLflow 模型架構一致，讓您輕鬆地在不同的工作流程中匯出和匯入這些模型。 MLflow 相關的中繼資料（例如，執行識別碼）也會標記已註冊的模型以供追蹤之用。 使用者可以提交定型回合、註冊及部署從 MLflow 執行所產生的模型。 

如果您想要在一個步驟中部署和註冊您的生產環境就緒模型，請參閱 [部署和註冊 MLflow 模型](how-to-deploy-models-with-mlflow.md)。

若要從執行註冊並查看模型，請使用下列步驟：

1. 執行完成之後，請呼叫 `register_model()` 方法。

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. 使用 [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)，在您的工作區中查看已註冊的模型。

    在下列範例中，已註冊的模型 `my-model` 已標記 MLflow 追蹤中繼資料。 

    ![註冊-mlflow-模型](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. 選取 [ **成品] 索引標籤，即可** 查看與 MLflow 模型架構一致的所有模型檔案 (conda. Yaml、MLmodel、>model.pkl) 。

    ![模型-架構](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. 選取 [MLmodel] 以查看執行所產生的 MLmodel 檔。

    ![MLmodel-架構](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>清除資源

如果您不打算在您的工作區中使用記錄的計量和構件，則無法個別刪除它們。 相反地，請刪除包含儲存體帳戶和工作區的資源群組，以免產生任何費用：

1. 在 Azure 入口網站中，選取最左邊的 [資源群組]  。

   ![在 Azure 入口網站中刪除](./media/how-to-use-mlflow/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 選取 [刪除資源群組]。

1. 輸入資源群組名稱。 然後選取 [刪除]。

## <a name="example-notebooks"></a>Notebook 範例

使用[搭配 Azure ML 筆記本的 MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) 示範和擴充本文所述的概念。

> [!NOTE]
> 您可以在中找到使用 mlflow 之範例的社區驅動存放庫 https://github.com/Azure/azureml-examples 。

## <a name="next-steps"></a>後續步驟

* [使用 MLflow 部署模型](how-to-deploy-models-with-mlflow.md)。
* 監視生產模型是否有[資料漂移](./how-to-enable-data-collection.md)。
* [使用 MLflow 追蹤 Azure Databricks 執行](how-to-use-mlflow-azure-databricks.md)。
* [管理您的模型](concept-model-management-and-deployment.md)。