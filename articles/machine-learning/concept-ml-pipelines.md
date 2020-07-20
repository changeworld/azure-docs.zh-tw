---
title: 什麼是 Azure Machine Learning 管線
description: 瞭解機器學習（ML）管線如何協助您建立、優化及管理機器學習工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.custom: tracking-python
ms.openlocfilehash: 7d34b54349a681061f80798a19ce65d2266f589d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559476"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>什麼是 Azure Machine Learning 管線？

在本文中，您可以瞭解 Azure Machine Learning 管線如何協助您建立、優化及管理機器學習工作流程。 這些工作流程有數個優點： 

+ 簡潔
+ 速度
+ 可重複性
+ 彈性
+ 版本控制和追蹤
+ 模組 
+ 品質保證
+ 成本控制

當您的機器學習專案移到單純的探索和反復執行之後，這些優點就會變得很明顯。 即使是簡單的單步驟管線，也很有價值。 機器學習專案通常處於複雜的狀態，因此可以讓單一工作流程的精確成就成為一個簡單的程式。

瞭解如何[建立您的第一個管線](how-to-create-your-first-pipeline.md)。

![Azure Machine Learning 中的機器學習管線](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我應該使用哪一種 Azure 管線技術？

Azure 雲端提供數個其他管線，各有不同的用途。 下表列出不同的管線和它們的用途：

| 狀況 | 主要角色 | Azure 供應專案 | OSS 供應專案 | 標準管道 | 優勢 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 模型協調流程（機器學習服務） | 資料科學家 | Azure Machine Learning 管線 | Kubeflow 管線 | 資料 > 模型 | 散發，快取，程式碼優先，重複使用 | 
| 資料協調流程（資料準備） | 資料工程師 | [Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | 資料 > 資料 | 強型別移動。 以資料為中心的活動。 |
| 程式碼 & 應用程式協調流程（CI/CD） | 應用程式開發人員/Ops | [Azure DevOps 管線](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + 模型-> 應用程式/服務 | 最開放且彈性的活動支援、核准佇列、具有管制的階段 | 

## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML 管線有哪些功能？

Azure Machine Learning 管線是一項完整機器學習工作的獨立可執行工作流程。 子工作會封裝成管線內的一系列步驟。 Azure Machine Learning 管線就像呼叫 Python 腳本一樣簡單，因此_可能會_執行任何動作。 管線_應該_著重在機器學習工作，例如：

+ 資料準備，包括匯入、驗證和清除、改寫和轉換、正規化以及暫存
+ 訓練組態，包括參數化引數、檔案路徑，以及記錄/報告組態
+ 有效率且重複地進行定型和驗證。 效率可能來自指定特定的資料子集、不同的硬體計算資源、分散式處理和進度監視
+ 部署，包括版本控制、調整、佈建和存取控制 

獨立步驟可讓多個資料科學家同時在相同的管線上工作，而不會有過度負擔的計算資源。 個別的步驟也可讓您輕鬆地針對每個步驟使用不同的計算類型/大小。

設計管線之後，在管線的定型迴圈上通常需要更多微調。 當您重新執行管線時，回合會跳至需要重新執行的步驟，例如更新的訓練腳本。 不需要重新執行的步驟會略過。 相同的分析適用于成就步驟所使用的未變更腳本。 這種重複使用的功能有助於避免執行成本高昂且耗時的步驟，例如資料內嵌和轉換（如果基礎資料尚未變更）。

使用 Azure Machine Learning，您可以針對管線中的每個步驟使用各種工具組和架構，例如 PyTorch 或 TensorFlow。 Azure 會協調您使用的各種[計算目標](concept-azure-machine-learning-architecture.md)，因此您的中繼資料可以與下游計算目標共用。

您可以直接在 Azure 入口網站或您的[工作區登陸頁面（預覽）](https://ml.azure.com)中[追蹤管線實驗的計量](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)。 發佈管線之後，您可以設定 REST 端點，讓您可以從任何平臺或堆疊重新執行管線。

簡單地說，機器學習服務週期的所有複雜工作都可以透過管線協助。 其他 Azure 管線技術有自己的優勢。 使用資料和[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)擅長的[Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities)，是持續整合和部署的正確工具。 但是，如果您的焦點是機器學習服務，Azure Machine Learning 管線可能是您工作流程需求的最佳選擇。 

## <a name="what-are-azure-ml-pipelines"></a>什麼是 Azure ML 管線？

Azure ML 管線會以已排序的步驟順序來執行完整的邏輯工作流程。 每個步驟都是分離的處理動作。 管線會在 Azure Machine Learning[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)的內容中執行。

在 ML 專案的早期階段中，您可以使用單一 Jupyter 筆記本或 Python 腳本來執行 Azure 工作區和資源設定、資料準備、執行設定、訓練和驗證的所有工作。 但是如同函式和類別很快就會成為單一命令式程式碼的偏好，ML 工作流程很快就會成為整合型筆記本或腳本。 

藉由模組化 ML 工作，管線可支援元件應該「執行（僅）一件事」的電腦科學。 在小組中進行程式設計時，模組化對於專案的成功非常重要，但即使單獨運作，即使是小型 ML 專案也牽涉到不同的工作，每個都有很大的複雜度。 作業包括：工作區設定和資料存取、資料準備、模型定義和設定，以及部署。 當一或多個工作的輸出會形成另一個工作的輸入時，任何一項工作的確切實作為詳細資料，就是下一個工作中的最佳、不相關的分散注意力。 在最糟的情況下，一個工作的計算狀態可能會造成另一個工作的錯誤。 

### <a name="analyzing-dependencies"></a>分析相依性

許多程式設計生態系統都有工具可協調資源、程式庫或編譯相依性。 一般而言，這些工具會使用檔案時間戳記來計算相依性。 當檔案變更時，只會更新（下載、重新編譯或封裝）檔案及其相依性。 Azure ML 管線會大幅擴充此概念。 如同傳統的組建工具，管線會計算步驟之間的相依性，而且只會執行必要的重新計算。 

不過，Azure ML 管線中的相依性分析比簡單的時間戳記更為複雜。 每個步驟都可以在不同的硬體和軟體環境中執行。 資料準備可能是耗時的程式，但不需要在具有強大 Gpu 的硬體上執行，某些步驟可能需要 OS 特定軟體，您可能會想要使用分散式訓練等等。 雖然優化資源的節省成本可能很重要，但手動操控硬體和軟體資源中的各種不同變化可能會很困難。 更難執行所有動作，而不會對您在步驟之間傳輸的資料造成錯誤。 

管線會解決此問題。 Azure Machine Learning 會自動協調管線步驟之間的所有相依性。 此協調流程可能包括加速和關閉 Docker 映射、附加和卸離計算資源，以及在步驟之間以一致且自動的方式移動資料。

### <a name="reusing-results"></a>重複使用結果

此外，如果您選擇，步驟的輸出可能會重複使用。 如果您指定重複使用，而且沒有任何上游相依性觸發重新計算，則管線服務會使用此步驟結果的快取版本。 這類重複使用可能會大幅減少開發時間。 如果您有複雜的資料準備工作，您可能會比絕對必要來重新執行它。 管線可減輕您的煩惱：如有必要，步驟將會執行，如果不是，則不會。

當您具現化[管線](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py)物件、將它傳遞至，並呼叫時，Azure Machine Learning 會處理所有此相依性分析、協調流程和啟動 `Experiment` `submit()` 。 

### <a name="coordinating-the-steps-involved"></a>協調所需的步驟

當您建立並執行 `Pipeline` 物件時，會發生下列高階步驟：

+ 針對每個步驟，服務會計算的需求：
    + 硬體計算資源
    + OS 資源（Docker 映射）
    + 軟體資源（Conda/virtualenv 相依性）
    + 資料輸入 
+ 服務會判斷步驟之間的相依性，產生動態執行圖形
+ 執行圖形中的每個節點執行時：
    + 此服務會設定必要的硬體和軟體環境（可能重複使用現有的資源）
    + 此步驟會執行，並將記錄和監視資訊提供給其包含的 `Experiment` 物件
    + 當步驟完成時，會將其輸出準備做為下一個步驟和/或寫入儲存體的輸入
    + 不再需要的資源已完成和卸離

![管線步驟](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>使用 Python SDK 建立管線

在[Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)中，管線是在模組中定義的 Python 物件 `azureml.pipeline.core` 。 [管線](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)物件包含一或多個[PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)物件的已排序序列。 `PipelineStep`類別是抽象的，而實際的步驟則是子類別，例如[EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py)、 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)或[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)。 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py)類別會保存可重複使用的步驟順序，以在管線之間共用。 會當做 `Pipeline` 的一部分來執行 `Experiment` 。

Azure ML 管線會與 Azure Machine Learning 工作區相關聯，而管線步驟會與該工作區中可用的計算目標相關聯。 如需詳細資訊，請參閱[建立和管理 Azure 入口網站中的 Azure Machine Learning 工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)或[Azure Machine Learning 中的計算目標？](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)。

在 Azure Machine Learning 中，計算目標是發生 ML 階段的環境。 軟體環境可能是遠端 VM、Azure Machine Learning 計算、Azure Databricks、Azure Batch 等等。 硬體環境可能也會有很大的差異，視 GPU 支援、記憶體、存放裝置等等而定。 您可以指定每個步驟的計算目標，讓您更精細地控制成本。 您可以針對專案的特定動作、資料量和效能需求，使用更多或更不強大的資源。 

## <a name="building-pipelines-with-the-designer"></a>使用設計工具建立管線

偏好視覺化設計介面的開發人員可以使用 Azure Machine Learning 設計工具來建立管線。 您可以從工作區首頁上的**設計**工具選取專案來存取此工具。  設計工具可讓您將步驟拖放到設計介面上。 若要快速開發，您可以在 ML 工作範圍內使用現有的模組;現有的模組涵蓋從資料轉換到演算法選擇，到定型到部署的所有內容。 或者，您可以結合 Python 腳本中定義的自己步驟來建立完整的自訂管線。

當您以視覺方式設計管線時，步驟的輸入和輸出會以可見的方式顯示。 您可以拖放資料連線，讓您快速瞭解和修改管線的資料流程。
 
![Azure Machine Learning 設計工具範例](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>瞭解執行圖形

管線內的步驟可能會相依于其他步驟。 Azure ML 管線服務會執行分析和協調這些相依性的工作。 產生的「執行圖形」中的節點是處理步驟。 每個步驟都可能牽涉到建立或重複使用硬體和軟體的特定組合，重複使用快取的結果等等。 此執行圖形的服務協調流程和優化，可以大幅加速 ML 階段並降低成本。 

因為步驟會獨立執行，所以必須在外部定義物件，以保存在步驟之間流動的輸入和輸出資料。 這是[DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)和[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)物件的角色。 這些資料物件會與封裝其儲存體設定[的資料存放區物件相關](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py)聯。 `PipelineStep`基類一律會使用 `name` 字串、清單 `inputs` 和清單來建立 `outputs` 。 通常，它也會有一份清單 `arguments` ，而且通常會有一份清單 `resource_inputs` 。 子類別通常也會有額外的引數（例如， `PythonScriptStep` 需要執行腳本的檔案名和路徑）。 

執行圖形是非迴圈的，但是管線可以按照週期性排程執行，而且可以執行可將狀態資訊寫入檔案系統的 Python 腳本，讓您能夠建立複雜的設定檔。 如果您設計管線，讓某些步驟可以平行或非同步方式執行，Azure Machine Learning 會以透明的方式處理展開與傳出的相依性分析和協調。 您通常不需要擔心執行圖形的詳細資料，但可透過 [[管線]](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes)屬性取得。 


### <a name="a-simple-python-pipeline"></a>簡單的 Python 管線

此程式碼片段會顯示建立及執行基本所需的物件和呼叫 `Pipeline` ：

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

此程式碼片段的開頭為一般 Azure Machine Learning 物件、a、 `Workspace` `Datastore` 、 [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)和 `Experiment` 。 然後，程式碼會建立物件來保存 `input_data` 和 `output_data` 。 陣列 `steps` 會保存單一專案， `PythonScriptStep` 這會使用資料物件並在上執行 `compute_target` 。 然後，程式碼會具現化 `Pipeline` 物件本身，傳入工作區和步驟陣列。 對的呼叫會 `experiment.submit(pipeline)` 開始執行 AZURE ML 管線。 對的呼叫 `wait_for_completion()` 會封鎖，直到管線完成為止。 

若要深入瞭解如何將您的管線連線到您的資料，請參閱[Azure Machine Learning 中的資料存取和在](concept-data.md) [ML 管線步驟之間移動資料的文章（Python）](how-to-move-data-in-out-of-pipelines.md)。 

## <a name="best-practices-when-using-pipelines"></a>使用管線時的最佳作法

如您所見，建立 Azure ML 管線比啟動腳本稍微複雜一點。 管線需要設定並建立幾個 Python 物件。 

某些會建議使用管線的情況：

* 在小組環境中：將 ML 工作劃分成多個獨立的步驟，讓開發人員可以獨立作業併發展其程式。 

* 在或接近部署時：向下美甲設定，並使用已排程和事件驅動的作業，以保持在不斷變化的資料上。

* 在 ML 專案的初期階段或單獨運作：使用管線將組建自動化。 如果您在執行新的想法之前，已開始擔心重新建立設定和計算狀態，這就是您可能會考慮使用管線將工作流程自動化的信號。 

很容易就能熱心重複使用快取的結果、對計算成本進行精細的控制，以及處理常式隔離，但管線的確具有成本。 某些反模式包括：

* 使用管線做為分隔考慮的唯一方法。 Python 的內建函式、物件和模組可讓您以更長的方式避免程式設計的狀態混淆！ 管線步驟比函式呼叫高得多。

* 管線步驟之間的繁重結合。 如果重構相依步驟經常需要修改上一個步驟的輸出，則可能是不同的步驟目前比權益更有成本。 另一個將步驟過度耦合的線索，是不是資料的步驟引數，而是用來控制處理的旗標。 

* 提前優化計算資源。 例如，通常有幾個階段可以進行資料準備，而且通常會看到「哦，這裡有一個地方可以使用 `MpiStep` 進行平行程式設計，但是這裡有一個地方可以使用較 `PythonScriptStep` 不強大的計算目標」等等。 而且，在長期的執行中，建立更細緻的步驟（例如）可能值得證明，特別是在可能使用快取的結果，而不是一律重新計算的情況下。 但管線並不是用來替代 Python 的原生 `multiprocessing` 模組。 

在專案取得大型或接近部署之前，您的管線應該更粗糙，而不是精細。 如果您將 ML 專案視為包含_階段_和管線，以提供完整的工作流程將您移至特定階段，則您會在正確的路徑上。 

## <a name="key-advantages"></a>主要優點

針對您的機器學習工作流程使用管線的主要優點如下：

|主要優點|Description|
|:-------:|-----------|
|**自動&nbsp;執行**|以可靠且自動的方式，以平行或依序執行排程步驟。 資料準備和模型化可以過去數天或數周，而管線可讓您在進程執行時，將焦點放在其他工作上。 |
|**異構計算**|使用多個在異類和可擴充的計算資源和儲存位置之間可靠地協調的管線。 在不同的計算目標（例如 HDInsight、GPU 資料科學 Vm 和 Databricks）上執行個別的管線步驟，以有效率地使用可用的計算資源。|
|**重複**|建立特定案例的管線範本，例如重新定型和批次評分。 透過簡單的 REST 呼叫，從外部系統觸發已發佈的管線。|
|**追蹤和版本控制**|使用管線 SDK 明確地為您的資料來源、輸入及輸出命名與設定版本，而不需在逐一查看時手動追蹤資料和結果路徑。 您也可以個別管理指令碼和資源，以提升產能。|
| **模組** | 區隔考慮和隔離變更，可讓軟體以更快的速度以較高的品質來發展。 | 
|**共同作業**|管線可讓資料科學家在機器學習設計流程的所有區域之間共同作業，同時能夠同時處理管線步驟。|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>選擇適當的 PipelineStep 子類別

`PythonScriptStep`是抽象的最具彈性子類別 `PipelineStep` 。 其他子類別，例如子 `EstimatorStep` 類別和 `DataTransferStep` 可以使用較少的程式碼來完成特定工作。 例如， `EstimatorStep` 只要傳入步驟、 `Estimator` 和計算目標的名稱，就可以建立。 或者，您可以覆寫輸入和輸出、管線參數和引數。 如需詳細資訊，請參閱[使用估計工具將模型定型 Azure Machine Learning](how-to-train-ml-models.md)。 

`DataTransferStep`可讓您輕鬆地在資料來源與接收之間移動資料。 以手動方式執行此傳輸的程式碼很簡單，但卻是重複的。 相反地，您可以只 `DataTransferStep` 使用名稱、資料來源的參考和資料接收，以及計算目標來建立。 [具有 DataTransferStep 的筆記本 Azure Machine Learning 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb)會示範這種彈性。

## <a name="modules"></a>單元

雖然管線步驟允許重複使用上一次執行的結果，但在許多情況下，此步驟的結構會假設必須在本機提供必要的腳本和相依檔案。 如果資料科學家想要以現有的程式碼為基礎，腳本和相依性通常必須從個別的存放庫複製。

模組與管線步驟的使用方式類似，但提供可透過工作區進行的版本控制，這可讓共同作業和大規模重複使用。 模組的設計是要在多個管線中重複使用，而且可以進化以配合不同的使用案例來調整特定的計算。 使用者可以透過工作區執行下列工作，而不需要使用外部存放庫：

* 建立新的模組，併發布現有模組的新版本
* 取代現有的版本
* 將版本標示為停用，以防止取用者使用該版本
* 指定預設版本
* 從工作區的版本取出模組，以確保小組使用相同的程式碼

如需如何在 Azure Machine Learning 管線中建立、連接及使用模組的程式碼範例，請參閱[筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)。

## <a name="next-steps"></a>後續步驟

Azure ML 管線是一項功能強大的工具，可在早期開發階段開始實現價值。 當小組和專案成長時，值會隨之增加。 本文說明如何使用 Azure Machine Learning Python SDK 來指定管線，並在 Azure 上進行協調。 您已經看過一些基本的原始程式碼，並已引進一些可用的 `PipelineStep` 類別。 您應該有了解何時使用 Azure ML 管線，以及 Azure 如何執行它們。 


+ 瞭解如何[建立您的第一個管線](how-to-create-your-first-pipeline.md)。

+ 瞭解如何對[大型資料執行批次預測](tutorial-pipeline-batch-scoring-classification.md )。

+ 請參閱適用于[管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)和[管線步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)的 SDK 參考檔。

+ 試試看[Azure Machine Learning 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的範例 Jupyter 筆記本展示。 瞭解如何[執行筆記本以探索此服務](samples-notebooks.md)。
