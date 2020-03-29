---
title: 在應用程式見解中調試和排除機器學習管道
titleSuffix: Azure Machine Learning
description: 將日誌記錄添加到您的訓練和批次處理評分管道，並在應用程式見解中查看記錄的結果。
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776296"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>在應用程式見解中調試和排除機器學習管道
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) python 庫可用於從腳本將日誌路由到應用程式見解。 聚合管道中的日誌在一個位置運行，允許您生成查詢和診斷問題。 使用應用程式見解將允許您跟蹤日誌隨時間而過，並在運行中比較管道日誌。

將日誌放在原位後將提供異常和錯誤訊息的歷史記錄。 由於應用程式見解與 Azure 警報集成，因此還可以根據應用程式見解查詢創建警報。

## <a name="prerequisites"></a>Prerequisites

* 按照以下步驟創建 Azure[機器學習](./how-to-manage-workspace.md)工作區並[創建第一個管道](./how-to-create-your-first-pipeline.md)
* [設定開發環境](./how-to-configure-environment.md)以安裝 Azure Machine Learning SDK。
* 在本地安裝[OpenCensus Azure 監視器匯出](https://pypi.org/project/opencensus-ext-azure/)包：
  ```python
  pip install opencensus-ext-azure
  ```
* 創建[應用程式見解實例](../azure-monitor/app/opencensus-python.md)（此文檔還包含有關獲取資源的連接字串的資訊）

## <a name="getting-started"></a>開始使用

本節是有關使用 Azure 機器學習管道的 OpenCensus 的介紹。 有關詳細教程，請參閱[打開 Census Azure 監視器匯出器](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

向 Azure ML 管道添加 PythonScript 步驟。 使用對開放"ext-azure"的依賴項配置[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)。 配置`APPLICATIONINSIGHTS_CONNECTION_STRING`環境變數。

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

建立名為 `sample_step.py`的檔案。 導入 AzureLogHandler 類以將日誌路由到應用程式見解。 您還需要導入 Python 日誌記錄庫。

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

接下來，將 AzureLogHandler 添加到 python 記錄器。

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>使用自訂維度進行日誌記錄
 
預設情況下，轉發到應用程式見解的日誌將沒有足夠的上下文追溯到運行或實驗。 為了使日誌可操作以診斷問題，需要額外的欄位。 

要添加這些欄位，可以添加自訂維度以向日志消息提供上下文。 例如，當有人想要在同一管道運行中查看跨多個步驟的日誌時。

自訂維度構成鍵值（存儲為字串、字串）對的字典。 然後，字典將發送到應用程式見解，並作為列顯示在查詢結果中。 其單個維度可用作[查詢參數](#additional-helpful-queries)。

### <a name="helpful-context-to-include"></a>要包括的有用上下文

| 欄位                          | 推理/示例                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 可以查詢具有相同parent_run_id的日誌，以便隨著時間的推移查看所有步驟的日誌，而不必深入到每個步驟中                                        |
| step_id                        | 可以查詢具有相同step_id的日誌，以查看問題發生的地方，範圍縮小到單個步驟                                                        |
| step_name                      | 可以查詢日誌以查看隨時間而執行的步驟性能。 還有助於查找最近運行step_id，而無需潛入門戶 UI                                          |
| experiment_name                | 可以跨日誌查詢以查看實驗性能。 還有助於查找最近運行parent_run_id或step_id，而無需深入瞭解門戶 UI                   |
| run_url                 | 可以提供直接回運行以進行調查的連結。 |

**其他有用的欄位**

這些欄位可能需要其他代碼檢測，並且不由運行上下文提供。

| 欄位                   | 推理/示例                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | 如果使用 CI/CD 進行部署，此欄位可以將日誌與提供步驟和管道邏輯的代碼版本相關聯。 此連結可進一步説明診斷問題，或識別具有特定特徵的模型（日誌/指標值） |
| run_type                       | 可以區分不同的模型類型，或訓練與評分運行 |

### <a name="creating-a-custom-dimensions-dictionary"></a>創建自訂維度字典

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

## <a name="opencensus-python-logging-considerations"></a>打開Census Python 日誌記錄注意事項

OpenCensus AzureLogHandler 用於將 Python 日誌路由到應用程式見解。 因此，應考慮 Python 日誌記錄細微差別。 創建記錄器時，它具有預設日誌級別，並將顯示大於或等於該級別的日誌。 使用 Python 日誌記錄功能的一個很好的參考是[日誌記錄說明書](https://docs.python.org/3/howto/logging-cookbook.html)。

OpenCensus 庫需要`APPLICATIONINSIGHTS_CONNECTION_STRING`環境變數。 我們建議設置此環境變數，而不是將其作為管道參數傳入，以避免繞過純文字連接字串。

## <a name="querying-logs-in-application-insights"></a>在應用程式見解中查詢日誌

路由到應用程式見解的日誌將在"跟蹤"或"異常"下顯示。 請務必調整時間視窗以包括管道運行。

![應用程式見解查詢結果](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

應用程式見解中的結果將顯示日誌消息和級別、檔路徑和程式碼號。 它還將顯示包含的任何自訂維度。 在此圖像中，自訂 Dimensions 字典顯示上一[個代碼示例](#creating-a-custom-dimensions-dictionary)中的鍵/值對。

### <a name="additional-helpful-queries"></a>其他有用的查詢

以下一些查詢使用"自訂維度.級別"。 這些嚴重性級別對應于 Python 日誌最初發送的級別。 有關其他查詢資訊，請參閱[Azure 監視器日誌查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

| 使用案例                                                               | 查詢                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 記錄特定自訂維度的結果，例如"parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 記錄過去 7 天內所有培訓運行的結果                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 記錄過去 7 天的嚴重性級別錯誤的結果              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 過去 7 天具有嚴重性級別的錯誤的日誌結果計數     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>後續步驟

在應用程式見解實例中擁有日誌後，可以使用這些日誌根據查詢結果設置[Azure 監視器警報](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on)。

您還可以將查詢的結果添加到[Azure 儀表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query)以獲取其他見解。