---
title: 觸發 Azure Machine Learning 管線
titleSuffix: Azure Machine Learning
description: 觸發的管線可讓您將例行的例行工作自動化，例如資料處理、定型和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 12/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 15d8a198df4769b94bced49b82f7be827c771994
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630901"
---
# <a name="trigger-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>使用適用于 Python 的 Azure Machine Learning SDK 來觸發機器學習管線

在本文中，您將瞭解如何以程式設計方式排程要在 Azure 上執行的管線。 您可以選擇根據經過時間或檔案系統變更來建立排程。 以時間為基礎的排程可以用來處理例行的工作，例如監視資料漂移。 以變更為基礎的排程可以用來回應異常或無法預測的變更，例如上傳的新資料或正在編輯的舊資料。 在瞭解如何建立排程之後，您將瞭解如何取出和停用排程。 最後，您將瞭解如何使用 Azure 邏輯應用程式，以允許更複雜的觸發邏輯或行為。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立 [免費帳戶](https://aka.ms/AMLFree)。

* 安裝適用于 Python 之 Azure Machine Learning SDK 的 Python 環境。 如需詳細資訊，請參閱 [使用 Azure Machine Learning 建立和管理可重複使用的環境，以進行定型和部署。](how-to-use-environments.md)

* 具有已發佈管線的 Machine Learning 工作區。 您可以使用內建的 [AZURE MACHINE LEARNING SDK 來建立及執行機器學習管線](how-to-create-your-first-pipeline.md)。

## <a name="initialize-the-workspace--get-data"></a>& 取得資料來初始化工作區

若要排程管線，您需要參考您的工作區、已發佈管線的識別碼，以及您想要在其中建立排程的實驗名稱。 您可以使用下列程式碼來取得這些值：

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

## <a name="create-a-schedule"></a>建立排程

若要定期執行管線，您將會建立排程。 A 會 `Schedule` 關聯管線、實驗和觸發程式。 觸發程式可以是 `ScheduleRecurrence` 描述執行之間等候的，或是指定要監看變更之目錄的資料存放區路徑。 無論是哪一種情況，您都需要管線識別碼，以及要在其中建立排程的實驗名稱。

在您的 python 檔案頂端，匯入 `Schedule` 和 `ScheduleRecurrence` 類別：

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>建立以時間為基礎的排程

此 `ScheduleRecurrence` 函數的必要 `frequency` 引數必須是下列其中一個字串： "Minute"、"Hour"、"Day"、"Week" 或 "Month"。 它也需要整數 `interval` 引數，以指定 `frequency` 排程開始之間應經過的單位數。 選擇性引數可讓您更明確地瞭解開始時間，如 [SCHEDULERECURRENCE SDK](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py)檔中所述。

建立 `Schedule` ，每隔15分鐘開始執行一次：

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>建立以變更為基礎的排程

檔案變更所觸發的管線可能比以時間為基礎的排程更有效率。 例如，您可能想要在檔案變更時，或將新檔案新增至資料目錄時執行前置處理步驟。 您可以監視資料存放區的任何變更，或資料存放區中特定目錄內的變更。 如果您監視特定的目錄，該目錄之子目錄內的變更將 _不_ 會觸發執行。

若要建立檔案回應 `Schedule` ，您必須 `datastore` 在 [排程] 的呼叫中設定參數[。](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-) 若要監視資料夾，請設定 `path_on_datastore` 引數。

`polling_interval`引數可讓您指定檢查資料存放區是否有變更的頻率（以分鐘為單位）。

如果管線是使用 [資料路徑](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) [>pipelineparameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py)所建立，您可以藉由設定引數，將該變數設定為已變更之檔案的名稱 `data_path_parameter_name` 。

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>建立排程時的選擇性引數

除了先前討論的引數之外，您還可以將 `status` 引數設定為， `"Disabled"` 以建立非使用中的排程。 最後，可 `continue_on_step_failure` 讓您傳遞將覆寫管線預設失敗行為的布林值。

## <a name="view-your-scheduled-pipelines"></a>查看您的已排程管線

在您的網頁瀏覽器中，流覽至 Azure Machine Learning。 從導覽面板的 [ **端點** ] 區段中，選擇 [ **管線端點**]。 這會帶您前往工作區中發佈的管線清單。

![AML 的管線頁面](./media/how-to-trigger-published-pipeline/scheduled-pipelines.png)

在此頁面中，您可以看到工作區中所有管線的摘要資訊：名稱、描述、狀態等等。 在您的管線中按一下，以深入瞭解。 在產生的頁面上，有更多有關管線的詳細資料，您可以向下切入到個別的執行。

## <a name="deactivate-the-pipeline"></a>停用管線

如果您有已 `Pipeline` 發佈但未排程的，您可以使用下列程式來停用它：

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

如果已排程管線，您必須先取消排程。 從入口網站或藉由執行下列程式，來取出排程的識別碼：

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

當您想 `schedule_id` 要停用之後，請執行：

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

如果您接著再執行 `Schedule.list(ws)` 一次，則應該會取得空白清單。

## <a name="use-azure-logic-apps-for-complex-triggers"></a>針對複雜的觸發程式使用 Azure Logic Apps 

您可以使用 [Azure 邏輯應用程式](../logic-apps/logic-apps-overview.md)來建立更複雜的觸發程式規則或行為。

若要使用 Azure 邏輯應用程式來觸發 Machine Learning 管線，您將需要已發佈 Machine Learning 管線的 REST 端點。 [建立併發布您的管線](how-to-create-your-first-pipeline.md)。 然後 `PublishedPipeline` 使用管線識別碼來尋找的 REST 端點：

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>建立邏輯應用程式

現在，建立 [Azure 邏輯應用程式](../logic-apps/logic-apps-overview.md) 實例。 如果您想要的話，請 [使用 (ISE) 的整合服務環境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ，並 [設定客戶管理的金鑰](../logic-apps/customer-managed-keys-integration-service-environment.md) ，以供邏輯應用程式使用。

布建您的邏輯應用程式之後，請使用下列步驟來設定管線的觸發程式：

1. [建立系統指派的受控識別](../logic-apps/create-managed-service-identity.md) ，以授與應用程式存取您的 Azure Machine Learning 工作區。

1. 流覽至 [邏輯應用程式設計工具] 視圖，然後選取 [空白邏輯應用程式] 範本。 
    > [!div class="mx-imgBorder"]
    > ![空白範本](media/how-to-trigger-published-pipeline/blank-template.png)

1. 在設計工具中，搜尋 **blob**。 選取 [ **新增或修改 Blob 時] (屬性只)** 觸發程式，並將此觸發程式新增至邏輯應用程式。
    > [!div class="mx-imgBorder"]
    > ![新增觸發程序](media/how-to-trigger-published-pipeline/add-trigger.png)

1. 填入您想要監視的 blob 儲存體帳戶的連線資訊，以進行 blob 新增或修改。 選取要監視的容器。 
 
    選擇要輪詢適用于您的更新的 **間隔** 和 **頻率** 。  

    > [!NOTE]
    > 此觸發程式會監視選取的容器，但不會監視子資料夾。

1. 新增在偵測到新的或已修改的 blob 時，將會執行的 HTTP 動作。 選取 [ **+ 新增步驟**]，然後搜尋並選取 [HTTP] 動作。

  > [!div class="mx-imgBorder"]
  > ![搜尋 HTTP 動作](media/how-to-trigger-published-pipeline/search-http.png)

  使用下列設定來設定您的動作：

  | 設定 | 值 | 
  |---|---|
  | HTTP 動作 | POST |
  | URI |您[發現為必要條件](#prerequisites)之已發佈管線的端點 |
  | 驗證模式 | 受控識別 |

1. 設定您的排程，以設定您可能擁有的任何 [資料路徑 PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 值：

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    使用 `DataStoreName` 您新增至工作區的作為必要條件[](#prerequisites)。
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 設定](media/how-to-trigger-published-pipeline/http-settings.png)

1. 選取 [ **儲存** ]，您的排程現在已就緒。

> [!IMPORTANT]
> 如果您使用 Azure 角色型存取控制 (Azure RBAC) 來管理管線的存取權，請 [設定管線案例的許可權 (定型或計分) ](how-to-assign-roles.md#common-scenarios)。

## <a name="next-steps"></a>下一步

在本文中，您已使用適用于 Python 的 Azure Machine Learning SDK，以兩種不同的方式來排程管線。 會根據經過的時鐘時間重複一次排程。 如果在指定的上 `Datastore` 或在該存放區的目錄內修改檔案，則會執行另一個排程。 您已瞭解如何使用入口網站來檢查管線和個別執行。 您已瞭解如何停用排程，讓管線停止執行。 最後，您已建立 Azure 邏輯應用程式來觸發管線。 

如需詳細資訊，請參閱

> [!div class="nextstepaction"]
> [使用 Azure Machine Learning 管線來進行批次評分](tutorial-pipeline-batch-scoring-classification.md)

* 深入瞭解 [管線](concept-ml-pipelines.md)
* 深入瞭解如何 [使用 Jupyter 探索 Azure Machine Learning](samples-notebooks.md)