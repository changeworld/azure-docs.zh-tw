---
title: 計畫 Azure 機器學習導管
titleSuffix: Azure Machine Learning
description: 使用 Python 的 Azure 機器學習 SDK 計劃 Azure 機器學習管道。 計劃管道允許您自動執行常規、耗時的任務,如數據處理、培訓和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 8e1e718fa4e6660d72203ac98bb6d427cdba2059
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024552"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>使用適用於 Python 的 Azure 機器學習 SDK 安排機器學習管

在本文中,您將學習如何以程式設計方式計劃要在 Azure 上運行的管道。 您可以選擇根據已用時間或檔案系統更改創建計畫。 基於時間的計畫可用於處理日常任務,例如監視數據漂移。 基於更改的計畫可用於對不規則或不可預知的更改(如上載的新數據或正在編輯的舊資料)做出反應。 瞭解如何創建計畫后,您將學習如何檢索和停用計劃。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有 Azure 訂閱,請建立[免費帳戶](https://aka.ms/AMLFree)。

* 安裝 Python 的 Azure 機器學習 SDK 的 Python 環境。 有關詳細資訊,請參閱[創建和管理可重用環境以使用 Azure 機器學習進行培訓和部署。](how-to-use-environments.md)

* 具有已發佈管道的機器學習工作區。 您可以使用 Azure 機器學習 SDK 在[建立和執行機器學習管道](how-to-create-your-first-pipeline.md)中建構的管道。

## <a name="initialize-the-workspace--get-data"></a>初始化工作區&取得資料

要計劃管道,您需要對工作區、已發佈管道的標識符以及要在其中創建計劃的實驗的名稱進行引用。 您可以使用以下代碼抓取這些值:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>建立計劃

要定期運行管道,您將創建一個計劃。 A`Schedule`關聯管道、實驗和觸發器。 觸發器可以是描述運行之間的等待`ScheduleRecurrence`,也可以是指定要監視更改的目錄的數據存儲路徑。 在這兩種情況下,都需要管道標識符和創建計劃的實驗的名稱。

在 python 檔案的頂部,匯`Schedule``ScheduleRecurrence`入與類別:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>建立基於時間的計劃

建構`ScheduleRecurrence`函數具有`frequency`必需的參數,該參數必須是以下字串之一:"分鐘"、"小時"、"天"、"周"或"月"。 它還需要一個整數`interval`參數,指定在計劃啟動`frequency`之間 應經過多少個單位。 可選參數允許您更具體地瞭解開始時間,詳見[計劃重複 SDK 文檔中](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)所述。

建立每`Schedule`15 分鐘開始執行的一個:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>建立變更的計劃

檔更改觸發的管道可能比基於時間的計劃更有效。 例如,您可能希望在更改檔或將新檔添加到數據目錄中時執行預處理步驟。 您可以監視對資料存儲的任何更改或資料存儲中特定目錄中的更改。 如果監視特定目錄,該目錄的子目錄中的更改_不會_觸發運行。

要建立檔案反應,`Schedule`必須在呼叫`datastore`「[計畫」](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)中設定參數。 要監視資料夾,設置`path_on_datastore`參數。

該`polling_interval`參數允許您以分鐘數分鐘表示檢查數據儲存更改的頻率。

如果管道是使用[DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [管道參數](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)建構的,`data_path_parameter_name`則可以通過設定 參數將該變數設置為已更改檔的名稱。

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>建立計畫時的選擇參數

除了前面討論的參數之外,您可以將`status``"Disabled"`參數設置為創建非活動計畫。 最後,`continue_on_step_failure`允許您傳遞一個布爾,該布爾將覆蓋管道的預設失敗行為。

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>對更複雜的工作串流使用 Azure 邏輯應用

Azure 邏輯應用支援更複雜的工作流,並且比 Azure 機器學習管道集成範圍更廣。 有關詳細資訊[,請參閱從邏輯應用觸發機器學習管道的執行](how-to-trigger-published-pipeline.md)。

## <a name="view-your-scheduled-pipelines"></a>檢視計劃導管

在 Web 瀏覽器中,導航到 Azure 機器學習。 從瀏覽面板的 **「終結點」** 部份,選擇**管線中止 。** 這將帶您到工作區中發佈的管道清單。

![AML 的導管頁面](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

在此頁面中,您可以看到有關工作區中所有管道的摘要資訊:名稱、說明、狀態等。 通過單擊管道進行鑽取。 在生成的頁面上,有關管道的更多詳細資訊,您可以深入到各個運行中。

## <a name="deactivate-the-pipeline"></a>停用管道

如果您有已發布的`Pipeline`但 未計劃,可以使用:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

如果管道已計劃,則必須首先取消計劃。 從門戶或執行程式檢索計劃的識別碼:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

一旦您要`schedule_id`關閉,請執行:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

如果再次運行`Schedule.list(ws)`,則應獲取空清單。

## <a name="next-steps"></a>後續步驟

在本文中,您使用 Python 的 Azure 機器學習 SDK 以兩種不同的方式安排管道。 一個計劃根據已用的時鐘時間重複出現。 如果在指定`Datastore`檔案或該存儲的目錄中修改檔,則運行其他計劃。 您看到了如何使用門戶檢查管道和單一運行。 最後,您學習了如何禁用計劃,以便管道停止運行。

如需詳細資訊，請參閱

> [!div class="nextstepaction"]
> [使用 Azure Machine Learning 管線來進行批次評分](tutorial-pipeline-batch-scoring-classification.md)

* 瞭解有關[導管](concept-ml-pipelines.md)的更多
* 瞭解有關[使用聚居機探索 Azure 機器學習的更多詳細資訊](samples-notebooks.md)

