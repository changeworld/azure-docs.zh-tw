---
title: 啟用 Azure Machine Learning 中的記錄
description: 瞭解如何使用預設的 Python 記錄封裝，以及使用 SDK 特有的功能，在 Azure Machine Learning 中啟用記錄。
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396144"
---
# <a name="enable-logging-in-azure-machine-learning"></a>啟用 Azure Machine Learning 中的記錄
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Python SDK 可讓您使用預設 Python 記錄套件以及使用 SDK 特有的功能啟用記錄，兩者皆可用於本機記錄和記錄至您在入口網站中的工作區。 記錄可提供開發人員應用程式狀態的即時資訊，並可協助診斷錯誤或警告。 在本文中，您會了解在下列領域啟用記錄的不同方式：

> [!div class="checklist"]
> * 定型模型和計算目標
> * 映像建立
> * 已部署的模型
> * Python `logging` 設定

[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 如需 SDK 的詳細資訊，請使用[指南](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

## <a name="training-models-and-compute-target-logging"></a>定型模型和計算目標記錄

在模型定型過程中，有多種方式可以啟用記錄，顯示的範例將說明常見的設計模式。 您可以使用 `start_logging` 類別上的 `Experiment` 函式，輕鬆地將執行相關的資料記錄至雲端的工作區。

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

如需其他記錄功能，請參閱[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)類別的參考檔。

若要在定型過程中啟用應用程式狀態的本機記錄，請使用 `show_output` 參數。 啟用詳細記錄可讓您查看定型過程的詳細資料，以及任何遠端資源或計算目標的相關資訊。 使用下列程式碼來啟用記錄實驗提交。

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

您也可以針對產生結果的執行，在 `wait_for_completion` 函式中使用相同參數。

```python
run.wait_for_completion(show_output=True)
```

SDK 也支援針對定型的特定案例使用預設的 Python 記錄套件。 下列範例在 `INFO` 物件中啟用 `AutoMLConfig` 層級的記錄。

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

再建立持續性的計算目標時，您也可以使用 `show_output` 參數。 在 `wait_for_completion` 函式中指定參數以在計算目標建立期間啟用記錄。

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>已部署模型的記錄

若要從先前部署的 Web 服務擷取記錄，請載入服務並使用 `get_logs()` 函式。 記錄可能包含部署期間發生之任何錯誤的相關詳細資訊。

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

您也可以啟用 Application Insights 來針對 Web 服務記錄自訂堆疊追蹤，這可讓您監視要求/回應次數、失敗率和例外狀況。 在現有 Web 服務上呼叫 `update()` 函式以啟用 Application Insights。

```python
service.update(enable_app_insights=True)
```

如需詳細資訊，請參閱[從 ML web 服務端點監視和收集資料](how-to-enable-app-insights.md)。

## <a name="python-native-logging-settings"></a>Python 原生記錄設定

SDK 中的特定記錄可能包含錯誤，其指示您將記錄層級設為 DEBUG。 若要設定記錄層級，請將下列程式碼加入到您的指令碼中。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>後續步驟

* [從 ML web 服務端點監視及收集資料](how-to-enable-app-insights.md)