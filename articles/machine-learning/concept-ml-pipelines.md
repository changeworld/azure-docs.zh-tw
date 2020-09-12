---
title: 什麼是 Azure Machine Learning 管線
description: 瞭解 machine learning (ML) 管線如何協助您建立、優化及管理機器學習工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 08/17/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2232aa46b0586ca62e80c8b9002271a67e4021c1
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651115"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>什麼是 Azure Machine Learning 管線？

在本文中，您將瞭解 Azure Machine Learning 管線如何協助您建立、優化及管理機器學習工作流程。 這些工作流程有許多優點： 

+ 簡潔
+ 速度
+ 可重複性
+ 彈性
+ 版本控制和追蹤
+ 模組 化 
+ 品質保證
+ 成本控制

當您的機器學習服務專案移至單純探索和反復專案之後，這些權益就會變成重大。 即使是簡單的單一步驟管線也很重要。 機器學習專案通常會處於複雜的狀態，而且可能會讓單一工作流程的精確成就成為簡單的程式。

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我應該使用哪一種 Azure 管線技術？

Azure 雲端提供數個其他管線，各有不同的用途。 下表列出不同的管線，以及它們的用途：

| 案例 | 主要角色 | Azure 供應專案 | OSS 供應專案 | 標準管道 | 優勢 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
|  (Machine learning) 的模型協調流程 | 資料科學家 | Azure Machine Learning 管線 | Kubeflow 管線 | 資料 > 模型 | 散發、快取、程式碼優先、重複使用 | 
| 資料協調 (資料準備)  | 資料工程師 | [Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | 資料 > 資料 | 強型別移動、以資料為中心的活動 |
| & app 協調流程 (CI/CD) 的程式碼 | 應用程式開發人員/Ops | [Azure DevOps 管線](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 程式碼 + 模型-> 的應用程式/服務 | 最開放且彈性的活動支援、核准佇列、具有管制的階段 | 

## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML 管線有何用途？

Azure Machine Learning 管線是完整機器學習工作的獨立可執行工作流程。 子工作會封裝成管線內的一系列步驟。 Azure Machine Learning 管線可以像呼叫 Python 腳本一樣簡單，因此幾乎 _可以_ 進行任何動作。 管線 _應_ 著重于機器學習工作，例如：

+ 資料準備，包括匯入、驗證和清除、改寫和轉換、正規化以及暫存
+ 訓練組態，包括參數化引數、檔案路徑，以及記錄/報告組態
+ 有效且重複地訓練和驗證。 效率可能來自指定特定的資料子集、不同的硬體計算資源、分散式處理和進度監視
+ 部署，包括版本控制、調整、佈建和存取控制

獨立的步驟可讓多個資料科學家同時在相同的管線上運作，而不需要過度負擔的計算資源。 個別的步驟也可讓您輕鬆地針對每個步驟使用不同的計算類型/大小。

設計管線之後，在管線的定型迴圈上通常需要更多微調。 當您重新執行管線時，執行會跳到需要重新執行的步驟，例如更新的定型腳本。 不需要重新執行的步驟會被略過。 

透過管線，您可以選擇針對不同的工作使用不同的硬體。 Azure 會協調您使用的各種 [計算目標](concept-azure-machine-learning-architecture.md) ，讓您的中繼資料順暢地流至下游計算目標。

您可以直接在 Azure 入口網站或[工作區登陸頁面 (preview) ](https://ml.azure.com)中[追蹤管線實驗的度量](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)。 發佈管線之後，您可以設定 REST 端點，讓您可以從任何平臺或堆疊重新執行管線。

簡單地說，機器學習服務生命週期的所有複雜工作都可以協助處理管線。 其他 Azure 管線技術有自己的優點。 [Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) 擅長在使用資料， [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 是持續整合和部署的正確工具。 但是，如果您的焦點是機器學習服務，Azure Machine Learning 管線可能是您工作流程需求的最佳選擇。 

### <a name="analyzing-dependencies"></a>分析相依性

許多程式設計生態系統具有可協調資源、程式庫或編譯相依性的工具。 一般而言，這些工具會使用檔案時間戳記來計算相依性。 當檔案變更時，只會更新它及其相依項， (下載、重新編譯或封裝) 。 Azure ML 管線擴充了這個概念。 如同傳統的組建工具，管線會計算步驟之間的相依性，而且只會執行必要的重新計算。 

不過，Azure ML 管線中的相依性分析比簡單的時間戳記更精密。 每個步驟都可以在不同的硬體和軟體環境中執行。 資料準備可能是耗時的程式，但不需要在具有強大 Gpu 的硬體上執行，某些步驟可能需要作業系統專用的軟體，您可能會想要使用分散式訓練等等。 

Azure Machine Learning 會自動協調管線步驟之間的所有相依性。 此協調流程可能包括啟動及關閉 Docker 映射、附加和卸離計算資源，以及在步驟之間以一致且自動的方式移動資料。

### <a name="coordinating-the-steps-involved"></a>協調相關步驟

當您建立並執行 `Pipeline` 物件時，會發生下列高階步驟：

+ 針對每個步驟，服務會計算下列需求：
    + 硬體計算資源
    + OS 資源 (Docker 映射 (s) # A3
    + 軟體資源 (Conda/virtualenv 相依性) 
    + 資料輸入 
+ 服務會判斷步驟之間的相依性，產生動態執行圖形
+ 當執行圖形中的每個節點執行時：
    + 服務會設定必要的硬體和軟體環境 (可能重複使用現有的資源) 
    + 此步驟會執行，並提供記錄和監視資訊給其包含 `Experiment` 物件
    + 當步驟完成時，其輸出會準備為下一個步驟的輸入，以及/或寫入至儲存體
    + 不再需要的資源會完成並卸離

![管線步驟](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>使用 Python SDK 建立管線

在 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)中，管線是在模組中定義的 python 物件 `azureml.pipeline.core` 。 [管線](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py&preserve-view=true)物件包含一或多個[PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py&preserve-view=true)物件的排序次序。 `PipelineStep`類別是抽象的，而實際的步驟則是子類別，例如[EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py&preserve-view=true)、 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py&preserve-view=true)或[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true)。 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py&preserve-view=true)類別包含可重複使用的步驟順序，可在管線間共用。 會 `Pipeline` 作為的一部分執行 `Experiment` 。

Azure ML 管線與 Azure Machine Learning 工作區相關聯，而管線步驟會與該工作區中可用的計算目標相關聯。 如需詳細資訊，請參閱 [Azure 入口網站中的建立和管理 Azure Machine Learning 工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) 或 [Azure Machine Learning 中的計算目標](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)。

### <a name="a-simple-python-pipeline"></a>簡單的 Python 管線

此程式碼片段會顯示建立並執行基本工作所需的物件和呼叫 `Pipeline` ：

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

此程式碼片段以 common Azure Machine Learning objects、a `Workspace` 、a `Datastore` 、 [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true)和 `Experiment` 。 然後，程式碼會建立物件來保存 `input_data` 和 `output_data` 。 陣列 `steps` 會保存單一元素， `PythonScriptStep` 這會使用資料物件並在上執行 `compute_target` 。 然後，程式碼會將 `Pipeline` 物件本身具現化，並在工作區和步驟陣列中傳遞。 `experiment.submit(pipeline)`開始執行 AZURE ML 管線的呼叫。 呼叫 `wait_for_completion()` 會封鎖，直到管線完成為止。 

若要深入瞭解如何將管線連接到您的資料，請參閱 [Azure Machine Learning 中的資料存取](concept-data.md) 和將 [資料移入和移出 ML 管線步驟 (Python) ](how-to-move-data-in-out-of-pipelines.md)。 

## <a name="building-pipelines-with-the-designer"></a>使用設計工具建立管線

偏好視覺化設計介面的開發人員可以使用 Azure Machine Learning 設計工具來建立管線。 您可以從工作區首頁上的 **設計** 工具選取專案來存取此工具。  設計工具可讓您在設計介面上拖放步驟。 

當您以視覺化方式設計管線時，會以可見方式顯示步驟的輸入和輸出。 您可以拖放資料連線，讓您快速瞭解並修改管線的資料流程。

![Azure Machine Learning 設計工具範例](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>主要優點

針對您的機器學習工作流程使用管線的主要優點如下：

|主要優點|描述|
|:-------:|-----------|
|**自動&nbsp;執行**|排程以平行方式或依序以可靠且自動的方式執行的步驟。 資料準備和模型化可能會在過去幾天或幾周，而管線可讓您在處理常式執行時專注于其他工作。 |
|**異類計算**|使用多個在異類和可調整的計算資源和儲存位置之間可靠協調的管線。 在不同的計算目標上執行個別的管線步驟（例如 HDInsight、GPU 資料科學 Vm 和 Databricks），以有效率地使用可用的計算資源。|
|**可 重用**|建立適用于特定案例的管線範本，例如重新定型和批次評分。 透過簡單的 REST 呼叫從外部系統觸發已發佈的管線。|
|**追蹤和版本控制**|使用管線 SDK 明確地為您的資料來源、輸入及輸出命名與設定版本，而不需在逐一查看時手動追蹤資料和結果路徑。 您也可以個別管理指令碼和資源，以提升產能。|
| **模組 化** | 區隔考慮和隔離變更可讓軟體以較高的品質以較快的速度演進。 | 
|**共同作業**|管線可讓資料科學家跨機器學習設計程式的所有區域共同作業，同時能夠同時處理管線步驟。|

## <a name="next-steps"></a>接下來的步驟

Azure ML 管線是一項功能強大的工具，可在早期開發階段開始提供價值。 當小組和專案成長時，此值會增加。 本文說明如何使用 Azure Machine Learning Python SDK 來指定管線，並在 Azure 上進行協調。 您已瞭解一些基本的原始程式碼，並引進一些 `PipelineStep` 可用的類別。 您應瞭解何時使用 Azure ML 管線，以及 Azure 如何執行它們。 


+ 瞭解如何 [建立您的第一個管線](how-to-create-your-first-pipeline.md)。

+ 瞭解如何 [針對大型資料執行批次預測](tutorial-pipeline-batch-scoring-classification.md )。

+ 請參閱適用于 [管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) 和 [管線步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true)的 SDK 參考檔。

+ 試用 Jupyter 筆記本展示 [Azure Machine Learning 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的範例。 瞭解如何 [執行筆記本以探索此服務](samples-notebooks.md)。
