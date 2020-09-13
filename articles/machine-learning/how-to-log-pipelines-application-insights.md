---
title: 監視 & 收集管線記錄檔
titleSuffix: Azure Machine Learning
description: 將記錄新增至定型和批次評分管線，並在 Application Insights 中查看記錄的結果。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 808f71ae138922b93e3742f7ff2f526e419cf58c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645888"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>在 Application Insights 中收集警示和偵錯工具的機器學習管線記錄檔
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) python 程式庫可以用來將記錄路由至腳本的 Application Insights。 在單一位置匯總管線執行的記錄，可讓您建立查詢並診斷問題。 使用 Application Insights 可讓您追蹤一段時間的記錄，並比較跨回合的管線記錄。

將您的記錄放入一次之後，就會提供例外狀況和錯誤訊息的歷程記錄。 由於 Application Insights 與 Azure 警示整合，因此您也可以根據 Application Insights 查詢來建立警示。

## <a name="prerequisites"></a>必要條件

* 遵循下列步驟來建立 [Azure Machine Learning](./how-to-manage-workspace.md) 工作區，並 [建立您的第一個管線](./how-to-create-your-first-pipeline.md)
* [設定開發環境](./how-to-configure-environment.md)以安裝 Azure Machine Learning SDK。
* 在本機安裝 [OpenCensus Azure 監視器匯出](https://pypi.org/project/opencensus-ext-azure/) 工具套件：
  ```python
  pip install opencensus-ext-azure
  ```
* 建立 [Application Insights 實例](../azure-monitor/app/opencensus-python.md) (此檔也包含取得資源連接字串的相關資訊) 

## <a name="getting-started"></a>開始使用

本節是從 Azure Machine Learning 管線使用 OpenCensus 的特定簡介。 如需詳細的教學課程，請參閱 [OpenCensus Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

將 PythonScriptStep 新增至您的 Azure ML 管線。 使用 opencensus-ext-azure 的相依性設定您的 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true) 。 設定 `APPLICATIONINSIGHTS_CONNECTION_STRING` 環境變數。

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

建立名為 `sample_step.py`的檔案。 匯入 AzureLogHandler 類別以將記錄路由至 Application Insights。 您也需要匯入 Python 記錄程式庫。

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

接下來，將 AzureLogHandler 新增至 python 記錄器。

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>使用自訂維度進行記錄
 
根據預設，轉送至 Application Insights 的記錄不會有足夠的內容可追溯到執行或實驗。 若要讓記錄可操作來診斷問題，則需要額外的欄位。 

若要加入這些欄位，可以加入自訂維度，以提供記錄訊息的內容。 其中一個範例是當有人想要在相同的管線執行中，跨多個步驟來查看記錄時。

自訂維度組成索引鍵/值的字典， (儲存為字串、字串) 組。 然後，會將字典傳送給 Application Insights，並顯示為查詢結果中的資料行。 其個別維度可當做 [查詢參數](#additional-helpful-queries)使用。

### <a name="helpful-context-to-include"></a>包含的實用內容

| 欄位                          | 推理/範例                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 可以查詢具有相同 parent_run_id 的記錄，以查看一段時間內所有步驟的記錄，而不需要深入探討每個個別步驟                                        |
| step_id                        | 可以查詢具有相同 step_id 之記錄檔的記錄，以查看範圍僅限於個別步驟的問題發生的位置                                                        |
| step_name                      | 可以查詢記錄以查看一段時間的步驟效能。 也有助於找出最近執行的 step_id，而不需要深入瞭解入口網站 UI                                          |
| experiment_name                | 可以跨記錄進行查詢，以查看一段時間的實驗效能。 也有助於找出最近執行的 parent_run_id 或 step_id，而不需要深入瞭解入口網站 UI                   |
| run_url                 | 可以直接提供連結回到執行的調查。 |

**其他有用的欄位**

這些欄位可能需要額外的程式碼檢測，且不是由執行內容提供。

| 欄位                   | 推理/範例                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | 如果您使用 CI/CD 來部署，此欄位可將記錄相互關聯至提供步驟和管線邏輯的程式碼版本。 此連結可進一步協助診斷問題，或識別具有特定特性 (記錄/計量值的模型)  |
| run_type                       | 可以區分不同的模型類型，或是定型與計分執行 |

### <a name="creating-a-custom-dimensions-dictionary"></a>建立自訂維度字典

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python 記錄考慮

OpenCensus AzureLogHandler 是用來將 Python 記錄路由至 Application Insights。 因此，您應該考慮 Python 記錄的細微差異。 當記錄器建立時，它會有預設的記錄層級，而且會顯示大於或等於該層級的記錄。 [記錄操作手冊](https://docs.python.org/3/howto/logging-cookbook.html)是使用 Python 記錄功能的良好參考。

`APPLICATIONINSIGHTS_CONNECTION_STRING`OpenCensus 程式庫需要環境變數。 建議您設定此環境變數，而不是以管線參數形式傳遞，以避免傳遞純文字連接字串。

## <a name="querying-logs-in-application-insights"></a>查詢 Application Insights 中的記錄

路由傳送至 Application Insights 的記錄會顯示在 [追蹤] 或 [例外狀況] 下。 請務必調整您的時間範圍，以包含您的管線執行。

![Application Insights 查詢結果](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights 中的結果會顯示記錄訊息和層級、檔案路徑和程式程式碼號。 它也會顯示包含的任何自訂維度。 在此影像中，customDimensions 字典會顯示先前程式 [代碼範例](#creating-a-custom-dimensions-dictionary)中的索引鍵/值組。

### <a name="additional-helpful-queries"></a>其他實用的查詢

下列的部分查詢使用 ' customDimensions ' 層級。 這些嚴重性層級會對應到原先傳送的 Python 記錄層級。 如需其他查詢資訊，請參閱 [Azure 監視器記錄查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

| 使用案例                                                               | 查詢                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 特定自訂維度的記錄結果，例如 ' parent_run_id ' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 過去7天所有定型回合的記錄結果                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 過去7天內 severityLevel 錯誤的記錄結果              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 過去7天內 severityLevel 錯誤的記錄結果計數     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>後續步驟

一旦您的 Application Insights 實例中有記錄，就可以用來根據查詢結果設定 [Azure 監視器警示](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) 。

您也可以將查詢的結果新增至 [Azure 儀表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) ，以取得其他見解。
