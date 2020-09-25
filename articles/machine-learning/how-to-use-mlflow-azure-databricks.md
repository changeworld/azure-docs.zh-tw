---
title: Azure Databricks ML 實驗的 MLflow 追蹤
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設定 MLflow，以記錄來自 Azure Databricks Ml 實驗的計量和構件。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c6bcf6f228049c4f5c4d1cd0d22cb69fb9677c1a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342590"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>使用 MLflow 和 Azure Machine Learning (preview 來追蹤 Azure Databricks ML 實驗) 

在本文中，您將瞭解如何啟用 MLflow 的追蹤 URI 和記錄 API，統稱為 [MLflow 追蹤](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)，以將您的 AZURE DATABRICKS (ADB) 實驗、MLflow 和 Azure Machine Learning。

[MLflow](https://www.mlflow.org) 是一個開放原始碼程式庫，可用於管理機器學習實驗的生命週期。 MLFlow 追蹤是 MLflow 的元件，可記錄及追蹤您的定型回合計量和模型成品。 深入瞭解 [Azure Databricks 與 MLflow](https://docs.microsoft.com/azure/databricks/applications/mlflow/)。 

如需其他 MLflow 和 Azure Machine Learning 功能整合，請參閱 [使用 MLflow 和 Azure Machine Learning 來追蹤實驗執行和建立端點](how-to-use-mlflow.md) 。

>[!NOTE]
> 作為開放原始碼程式庫，MLflow 經常變更。 因此，透過 Azure Machine Learning 和 MLflow 整合所提供的功能，應該視為預覽，而且 Microsoft 不會完全支援。

> [!TIP]
> 本文件中的資訊主要適用於想要監視模型定型程序的資料科學家和開發人員。 如果您是系統管理員，且想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型執行或已完成的模型部署)，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>Prerequisites

* 安裝 `azureml-mlflow` 套件。 
    * 此套件會自動帶入 `azureml-core` [AZURE MACHINE LEARNING Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)，以提供 MLflow 的連線能力以存取您的工作區。
* [Azure Databricks 工作區和](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)叢集。
* [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="track-azure-databricks-runs"></a>追蹤 Azure Databricks 執行

使用 Azure Machine Learning 追蹤 MLflow，可讓您將記錄的計量和成品儲存在您的 Azure Databricks 中執行的兩個專案： 

* Azure Databricks 工作區。
* Azure Machine Learning 工作區

在您建立 Azure Databricks 工作區和叢集之後， 

1. 從 PyPi 安裝 *azureml mlflow* 程式庫，以確保您的叢集可以存取所需的函式和類別。

1. 設定您的實驗筆記本。

1. 連接您的 Azure Databricks 工作區和 Azure Machine Learning 工作區。

這些步驟的其他詳細資料會在下列各節中，讓您可以使用 Azure Databricks 成功執行 MLflow 實驗。 

## <a name="install-libraries"></a>安裝程式庫

若要在叢集上安裝程式庫，請流覽至 [連結 **庫** ] 索引標籤，然後選取 [ **安裝新**

 ![使用 azure databricks 的 mlflow](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

在 [ **封裝** ] 欄位中，輸入 azureml-mlflow，然後選取 [安裝]。 視需要重複此步驟，以將其他套件安裝到您的叢集來進行實驗。

 ![Azure DB 安裝 mlflow 程式庫](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>設定您的筆記本 

設定 ADB 叢集之後， 
1. 在左側流覽窗格中選取 [ **工作區** ]。 
1. 展開 [工作區] 下拉式功能表，然後選取 [匯**入**]
1. 拖放或流覽以尋找您的實驗筆記本，以匯入您的 ADB 工作區。
1. 選取 [匯入]。 您的實驗筆記本會自動開啟。
1. 在左上方的筆記本標題底下，選取要附加至實驗筆記本的叢集。 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>連結您的 Azure Databricks 和 Azure Machine Learning 工作區

將您的 ADB 工作區連結至 Azure Machine Learning 工作區，可讓您在 Azure Machine Learning 工作區中追蹤您的實驗資料。

若要將您的 ADB 工作區連結至新的或現有的 Azure Machine Learning 工作區， 
1. 登入 [Azure 入口網站](https://ms.portal.azure.com)。
1. 流覽至您的 ADB 工作區的 **[總覽** ] 頁面。
1. 選取右下角 **Azure Machine Learning 工作區** ] 按鈕的連結。 

 ![連結 Azure DB 和 Azure Machine Learning 工作區](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>在您的工作區中追蹤 MLflow

當您將工作區具現化之後，MLflow 追蹤會自動設定為在下列所有位置進行追蹤：

* 連結的 Azure Machine Learning 工作區。
* 您的原始 ADB 工作區。 

您所有的實驗都落在受控 Azure Machine Learning 追蹤服務中。

下列程式碼應該會在您的實驗筆記本中，以取得連結的 Azure Machine Learning 工作區。 

這段程式碼 

*  取得您的 Azure 訂用帳戶的詳細資料，以具現化您的 Azure Machine Learning 工作區。 

* 假設您有現有的資源群組和 Azure Machine Learning 工作區，否則您可以 [建立它們](how-to-manage-workspace.md)。 

* 設定實驗名稱。 此處的與 `user_name` `user_name` Azure Databricks 工作區相關聯的一致。

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>將 MLflow 追蹤設定為只在 Azure Machine Learning 工作區中追蹤

如果您想要在集中位置管理追蹤的實驗，您可以將 MLflow 追蹤設定為 **只** 在 Azure Machine Learning 工作區中追蹤。 

在您的腳本中包含下列程式碼：

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在訓練指令碼中，匯入 `mlflow` 以使用 MLflow 記錄 API，並開始記錄執行計量。 下列範例會記錄 Epoch 遺失計量。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>使用 MLflow 註冊模型

在您的模型定型之後，您可以使用方法，將模型記錄並註冊至後端追蹤伺服器 `mlflow.<model_flavor>.log_model()` 。 `<model_flavor>`是指與模型相關聯的架構。 [瞭解支援的模型](https://mlflow.org/docs/latest/models.html#model-api)類別。 

後端追蹤伺服器預設為 Azure Databricks 工作區;除非您選擇 [將 MLflow 追蹤設定為只在 Azure Machine Learning 工作區中追蹤](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)，否則後端追蹤伺服器就是 Azure Machine Learning 工作區。   

* **如果已註冊的模型名稱不存在**，此方法會註冊新的模型、建立第1版，並傳回 ModelVersion MLflow 物件。 

* **如果已有同名的已註冊模型存在**，此方法會建立新的模型版本，並傳回版本物件。 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>建立 MLflow 模型的端點

當您準備好建立 ML 模型的端點時。 您可以部署為、 

* 適用于互動式計分的 Azure Machine Learning 要求-回應 web 服務。 此部署可讓您運用 Azure Machine Learning 模型管理，並將資料漂移偵測功能套用至您的生產環境模型。 

* MLFlow 模型物件，可用於串流或批次管線，作為 Python 函式或 Azure Databricks 工作區中的 Pandas Udf。

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>將模型部署到 Azure Machine Learning 端點 
您可以利用 [mlflow](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API，將模型部署到您的 Azure Machine Learning 工作區。 如果您只將模型註冊到 Azure Databricks 工作區（如 [使用 MLflow 註冊模型](#register-models-with-mlflow) 一節中所述），請指定 `model_name` 要將模型註冊到 Azure Machine Learning 工作區的參數。 

Azure Databricks 執行可部署至下列端點： 
* [Azure 容器執行個體](how-to-use-mlflow.md#deploy-to-aci)
* [Azure Kubernetes Service](how-to-use-mlflow.md#deploy-to-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>將模型部署至 ADB 端點以進行批次評分 

您可以選擇用於批次評分的 Azure Databricks 叢集。 MLFlow 模型會載入，並當做 Spark Pandas UDF 用來對新資料進行評分。 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>清除資源

如果您不打算在您的工作區中使用記錄的計量和構件，則無法個別刪除它們。 相反地，請刪除包含儲存體帳戶和工作區的資源群組，以免產生任何費用：

1. 在 Azure 入口網站中，選取最左邊的 [資源群組]。

   ![在 Azure 入口網站中刪除](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 選取 [刪除資源群組]。

1. 輸入資源群組名稱。 然後選取 [刪除]。

## <a name="example-notebooks"></a>Notebook 範例

[Azure Machine Learning 筆記本的 MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)會示範並擴充本文中所呈現的概念。

## <a name="next-steps"></a>後續步驟

* [管理您的模型](concept-model-management-and-deployment.md)。
* [使用 MLflow 和 Azure Machine Learning 來追蹤實驗執行和建立端點](how-to-use-mlflow.md)。 
* 深入瞭解 [Azure Databricks 與 MLflow](https://docs.microsoft.com/azure/databricks/applications/mlflow/)。
