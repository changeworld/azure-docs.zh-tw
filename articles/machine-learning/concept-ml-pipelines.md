---
title: 什麼是 ML 管道
titleSuffix: Azure Machine Learning
description: 在本文中,瞭解可以使用適用於 Python 的 Azure 機器學習 SDK 構建的機器學習 (ML) 管道的優勢。 數據科學家使用機器學習管道來構建、優化和管理其機器學習工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 0cefa78b6f52cc67df8817f68a9b793ab86b2a7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878573"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>什麼是 Azure 機器學習管道?

Azure 機器學習管道允許您在機器學習項目中創建工作流。 這些工作流具有許多優點: 

+ 簡單
+ 速度
+ 可重複性
+ 彈性
+ 版本和追蹤
+ 模組化 
+ 品質保證
+ 成本控制

一旦您的機器學習專案超越純粹的探索並進入反覆運算,這些優勢就變得顯著。 即使是簡單的單步管道也很有價值。 機器學習專案通常處於複雜狀態,使單個工作流的精確實現成為一個微不足道的過程可能會讓人鬆了一口氣。

瞭解如何[建立第一個導管](how-to-create-your-first-pipeline.md)。

![Azure 機器學習中的機器學習管道](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我應該使用哪種 Azure 管道技術?

Azure 雲提供了其他幾個管道,每個管道具有不同的用途。 下表列出了不同的管道及其用於:

| 狀況 | 主要角色 | Azure 產品/ | OSS 產品 | 規範管 | 優勢 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 模型編排(機器學習) | 資料科學家 | Azure Machine Learning 管線 | 庫貝流管道 | 資料 ->模型 | 分發、快取、代碼優先、重用 | 
| 資料編排(資料) | 資料工程師 | [Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | 資料 ->数据 | 強類型運動。 以數據為中心的活動。 |
| 代碼&應用程式業務流程 (CI/CD) | 套用開發人員/操作 | [Azure 開發人員導管](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 代碼 + 型號 -> 應用/服務 | 最開放和靈活的活動支援、審批佇列、帶門控的階段 | 


## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML 管道可以做什麼?

Azure 機器學習管道是完整機器學習任務的可獨立執行工作流。 子工作會封裝成管線內的一系列步驟。 Azure 機器學習管道可以像呼叫 Python 文稿管道一樣簡單,因此_可以_執行任何操作。 管_線 應_側重於機器學習任務,例如:

+ 資料準備，包括匯入、驗證和清除、改寫和轉換、正規化以及暫存
+ 訓練組態，包括參數化引數、檔案路徑，以及記錄/報告組態
+ 高效、反覆地培訓和驗證。 效率可能來自指定特定資料子集、不同的硬體計算資源、分散式處理和進度監視
+ 部署，包括版本控制、調整、佈建和存取控制 

獨立步驟允許多個數據科學家在同一管道上同時工作,而不會對計算資源進行過重負擔。 單獨的步驟還便於為每個步驟使用不同的計算類型/大小。

設計管線之後，在管線的定型迴圈上通常需要更多微調。 重新運行管道時,運行將跳轉到需要重新運行的步驟,例如更新的培訓腳本。 將跳過不需要重新運行的步驟。 相同的分析適用於用於完成步驟的未更改腳本。 如果基礎數據未更改,此重用功能有助於防止運行成本高昂且耗時的步驟,如數據引入和轉換。

使用 Azure 機器學習,您可以針對管道中的每個步驟使用不同的工具套件和框架(如 PyTorch 或 TensorFlow)。 Azure 協調您使用的各種[計算目標](concept-azure-machine-learning-architecture.md),以便可以將中間數據與下游計算目標共用。

可以直接在 Azure 門戶或[工作區登入頁(預覽)](https://ml.azure.com)中[追蹤管線的指標](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)。 發佈管道后,可以配置 REST 終結點,這允許您從任何平臺或堆疊重新運行管道。

簡而言之,機器學習生命週期的所有複雜任務都可以通過管道提供説明。 其他 Azure 管道技術也有其自身優勢。 [Azure 資料工廠管道](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities)擅長處理數據[,Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是持續整合和部署的正確工具。 但是,如果您的重點是機器學習,則 Azure 機器學習管道可能是滿足工作流需求的最佳選擇。 

## <a name="what-are-azure-ml-pipelines"></a>什麼是 Azure ML 管道?

Azure ML 管道執行完整的邏輯工作流,並執行有序的步驟序列。 每個步驟都是離散處理操作。 管道在 Azure 機器學習[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)的上下文中運行。

在 ML 專案的早期階段,最好有一個 Jupyter 筆記本或 Python 文稿,用於執行 Azure 工作區和資源配置、數據準備、運行配置、培訓和驗證的所有工作。 但是,正如函數和類很快變得比單個命令代碼塊更可取一樣,ML 工作流也很快變得優於單片筆記本或腳本。 

通過模組化 ML 任務,管道支援計算機科學,元件應"只做(只)做好一件事" 模組化顯然對於團隊程式設計時專案的成功至關重要,但即使單獨工作,即使是小型 ML 專案也涉及單獨的任務,每個任務都非常複雜。 任務包括:工作區配置和數據訪問、數據準備、模型定義和配置以及部署。 雖然一個或多個任務的輸出構成另一個任務的輸入,但任何一個任務的確切實現細節充其量都是不相關的干擾。 最壞的情況是,一個任務的計算狀態可能會導致另一個任務中的 Bug。 

### <a name="analyzing-dependencies"></a>分析依賴項

許多程式設計生態系統都有協調資源、庫或編譯依賴項的工具。 通常,這些工具使用文件時間戳來計算依賴項。 更改檔時,僅更新檔及其從屬項(下載、重新編譯或打包)。 Azure ML 管道顯著擴展了這一概念。 與傳統的生成工具一樣,管道計算步驟之間的依賴關係,並且僅執行必要的重新計算。 

但是,Azure ML 管道中的依賴項分析比簡單的時間戳更複雜。 每個步驟可能在不同的硬體和軟體環境中運行。 數據準備可能是一個耗時的過程,但不需要在具有強大 GPU 的硬體上運行,某些步驟可能需要特定於操作系統的軟體,您可能需要使用分散式培訓,等等。 雖然優化資源的成本節省可能很大,但手動處理硬體和軟體資源中所有不同變體可能非常困難。 在步驟之間傳輸的數據時,在不出錯的情況下,要做到這一切甚至更加困難。 

管道可以解決此問題。 Azure 機器學習會自動協調管道步驟之間的所有依賴關係。 此業務流程可能包括向上和向下旋轉 Docker 映射、附加和分離計算資源,以及以一致和自動的方式在步驟之間移動數據。

### <a name="reusing-results"></a>重用結果

此外,如果願意,步驟的輸出可能會重複使用。 如果將重用指定為可能,並且沒有上游依賴項觸發重新計算,則管道服務將使用步驟結果的緩存版本。 這種重用可以大大減少開發時間。 如果數據準備任務複雜,則可能更頻繁地重新運行它,而不是絕對必要的任務。 管道可以緩解你的擔憂:如有必要,步驟將運行,如果不是,它將不會運行。

所有這些依賴關係分析、業務流程和啟動都由 Azure 機器學習處理,當您實例化[管道](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py)物件、`Experiment`將其傳遞到`submit()`和調用 時。 

### <a name="coordinating-the-steps-involved"></a>協調所涉及的步驟

建立與執行物件時`Pipeline`,將執行以下進階步驟:

+ 對於每個步驟,服務計算以下各項要求:
    + 硬體計算資源
    + 作業系統資源(Docker 映像)
    + 軟體資源 (康達/ 虛擬依賴項)
    + 資料輸入 
+ 服務確定步驟之間的依賴關係,從而生成動態執行圖
+ 執行圖中的每個節點執行時:
    + 該服務設定必要的硬體和軟體環境(可能重用現有資源)
    + 該步驟運行,向其包含`Experiment`的物件提供日誌記錄和監視資訊
    + 步驟完成後,其輸出將作為下一步的輸入準備和/或寫入存儲
    + 不再需要的資源已最終確定並分離

![管道步驟](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>使用 Python SDK 建構導管

在[Azure 機器學習 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)中,管道是在`azureml.pipeline.core`模組中定義的 Python 物件。 [管道](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)物件包含一個或多個[管道步驟](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)物件的有序序列。 該`PipelineStep`類別是抽象的,實際步驟將是子類別,如[估計步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py)[、PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)或[資料傳輸步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)。 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py)類包含可在管道之間共用的可重用步驟序列。 作為`Pipeline`的一`Experiment`部分運行。

Azure ML 管道與 Azure 機器學習工作區相關聯,管道步驟與該工作區中可用的計算目標相關聯。 有關詳細資訊,請參閱在[Azure 門戶中創建和管理 Azure 機器學習工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace),或者[Azure 機器學習中的計算目標是什麼?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

在 Azure 機器學習中,計算目標是發生 ML 階段的環境。 軟體環境可以是遠端 VM、Azure 機器學習計算、Azure 數據塊、Azure 批處理等。 硬體環境也可能有很大差異,具體取決於 GPU 支援、記憶體、存儲等。 您可以為每個步驟指定計算目標,從而對成本進行細粒度控制。 您可以將更多或更少功能的資源用於專案的特定操作、資料量和性能需求。 

## <a name="building-pipelines-with-the-designer"></a>與設計器一起建構導管

喜歡可視化設計圖面的開發人員可以使用 Azure 機器學習設計器創建管道。 您可以在工作區主頁上的 **「設計器」** 選擇中存取此工具。  設計器允許您將步長拖放到設計圖面上。 為了快速開發,您可以在 ML 任務範圍內使用現有模組;現有模組涵蓋從數據轉換到演算法選擇到培訓到部署的所有內容。 或者,您可以通過合併在 Python 文本中定義自己的步驟來創建完全自訂管道。

在目視設計管道時,步驟的輸入和輸出將明顯顯示。 您可以拖放資料連接,從而快速瞭解和修改管道的數據流。
 
![Azure Machine Learning 設計工具範例](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>瞭解執行圖

管道中的步驟可能具有對其他步驟的依賴關係。 Azure ML 管道服務負責分析和協調這些依賴項。 生成的「執行圖」中的節點是處理步驟。 每個步驟可能涉及創建或重用硬體和軟體的特定組合、重用緩存的結果等。 該服務對此執行圖的編排和優化可以顯著加快 ML 階段並降低成本。 

由於步驟獨立運行,因此必須外部定義用於保存在步驟之間流動的輸入和輸出數據的物件。 這是[數據集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)和[管道數據](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)的物件的角色。 這些數據物件與封裝其存儲配置的[數據存儲](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py)對象相關聯。 基`PipelineStep`類別是`name`字串、清單`inputs`和`outputs`的清單建立。 通常,它也有一個清單`arguments`,並且通常它有一個清單`resource_inputs`。 子類通常也會具有其他參數(例如,`PythonScriptStep`需要運行文稿的檔名和路徑)。 

執行圖是一成比半的,但管道可以定期運行,並且可以運行可以將狀態資訊寫入文件系統的 Python 文本,從而可以創建複雜的配置檔。 如果設計管道以便某些步驟可以並行或非同步運行,Azure 機器學習將透明地處理扇出和扇出的依賴項分析和協調。 通常,您不必關注執行圖的詳細資訊,但它可通過[Pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes)屬性獲得。 


### <a name="a-simple-python-pipeline"></a>簡單的 Python 導管

此程式碼段顯示建立與執行`Pipeline`基本 : 所需的物件和呼叫:

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

代碼段`Workspace`從常見的 Azure 機器`Datastore`學習物件`Experiment`、、、、[計算目標](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)以及 開頭。 然後,程式碼建立要`input_data`保留的物件`output_data`與 。 陣列`steps`包含一個元素,`PythonScriptStep`該 元素將使用數據物件並在上`compute_target`運行。 然後,代碼實例化`Pipeline`物件本身,在工作區和步驟陣組中傳遞。 開始`experiment.submit(pipeline)`Azure ML 管道運行的呼叫。 對`wait_for_completion()`塊的調用,直到管道完成。 

要瞭解有關將導管連線到資料的詳細資訊,請參閱[Azure 機器學習中的資料存取](concept-data.md)以及[將資料移至 ML 管道步驟 (Python) 中和之間的文章](how-to-move-data-in-out-of-pipelines.md)。 

## <a name="best-practices-when-using-pipelines"></a>使用導管時的最佳做法

如您所見,創建 Azure ML 管道比啟動文本要複雜一些。 管道需要配置和創建幾個 Python 物件。 

建議使用導管的一些情況:

* 在團隊環境中:將 ML 任務劃分為多個獨立步驟,以便開發人員可以獨立地工作並發展其程式。 

* 在部署或接近部署時:確定配置並使用計劃操作和事件驅動操作來保持數據更改的頂部。

* 在 ML 專案的早期階段或單獨工作:使用管道自動執行生成。 如果您開始擔心在實現新想法之前重新創建配置和計算狀態,則可能會考慮使用管道自動執行工作流。 

人們很容易對重用緩存的結果、對計算成本的細粒度控制以及過程隔離充滿熱情,但管道確實具有成本。 一些反模式包括:

* 使用管道作為分離關注點的唯一手段。 Python 的內置功能、物件和模組可以避免混淆編程狀態! 管道步驟比函數調用貴得多。

* 管道步驟之間的重耦合。 如果重構從屬步驟經常需要修改上一步的輸出,則單獨的步驟當前可能更具有成本而不是收益。 步驟過度耦合的另一個線索是,對步驟的參數不是數據,而是標誌來控制處理。 

* 過早優化計算資源。 例如,數據準備通常有幾個階段,人們經常可以看到"哦,在這裡我可以使用並行`MpiStep`程式設計,但在這裡,我可以使用具有不太強大的計算目標的",`PythonScriptStep`等等。 也許,從長遠來看,創建這樣的細粒度步驟可能是值得的,特別是如果有可能使用緩存的結果,而不是總是重新計算。 但是管道並不是要替代 Python 的`multiprocessing`本機 模組。 

在項目獲得大型或接近部署之前,管道應該更粗糙,而不是細粒度。 如果您認為 ML 專案涉及_階段_,而管道則提供完整的工作流來移動您透過特定階段,則您正走在正確的軌道上。 

## <a name="key-advantages"></a>主要優點

為機器學習工作流使用管道的主要優點是:

|主要優點|描述|
|:-------:|-----------|
|**自動&nbsp;執行**|計劃以可靠和無人值守的方式並行或按順序運行的步驟。 數據準備和建模可以持續數天或數周,管道允許您在流程運行時專注於其他任務。 |
|**異質計算**|使用多個管道,這些管道可在異構和可擴展的計算資源和存儲位置之間可靠地協調。 通過在不同計算目標(如 HDInsight、GPU 資料科學 VM 和 DataBRICKS)上運行單個管道步驟,從而高效利用可用的計算資源。|
|**可重用**|為特定方案(如重新訓練和批處理評分)創建管道範本。 通過簡單的 REST 調用從外部系統觸發已發布的管道。|
|**追蹤和版本控制**|使用管線 SDK 明確地為您的資料來源、輸入及輸出命名與設定版本，而不需在逐一查看時手動追蹤資料和結果路徑。 您也可以個別管理指令碼和資源，以提升產能。|
| **模組化** | 分離關注區域和隔離更改使軟體能夠以更快的速度以更高的質量發展。 | 
|**共同作業**|管道允許數據科學家在機器學習設計過程的所有領域進行協作,同時能夠同時處理管道步驟。|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>選擇正確的管道步驟子類

是`PythonScriptStep`抽`PipelineStep`象的最靈活的子類。 其他子類(如`EstimatorStep`子類)`DataTransferStep`可以用較少的代碼完成特定任務。 例如,只需傳入`EstimatorStep`步驟`Estimator`、 與計算目標的名稱即可建立 。 或者,您可以重寫輸入和輸出、管道參數和參數。 有關詳細資訊,請參閱[使用估計器 使用 Azure 機器學習訓練模型](how-to-train-ml-models.md)。 

使`DataTransferStep`數據在數據源和接收器之間移動變得容易。 手動執行此傳輸的代碼簡單明瞭,但重複。 相反,只需創建`DataTransferStep`具有名稱、對數據源和數據源的引用以及計算目標。 [帶有數據傳輸步驟](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb)的筆記本 Azure 機器學習管道演示了這種靈活性。

## <a name="modules"></a>模組

雖然管道步驟允許重用上一個運行的結果,但在許多情況下,步驟的構造假定所需的腳本和從屬文件必須在本地可用。 如果數據科學家希望在現有代碼的基礎上構建,則腳本和依賴項通常必須從單獨的存儲庫克隆。

模組的使用與管道步驟類似,但通過工作區提供版本控制,從而實現大規模協作和可重用性。 模組設計為在多個管道中重複使用,並可以發展以根據不同的用例調整特定的計算。 使用者可以通過工作區執行以下任務,而無需使用外部存儲庫:

* 建立新模組並發布現有模組的新版本
* 棄用現有版本
* 標記禁用的版本以防止使用者使用該版本
* 指定預設版本
* 依版本從工作區檢索模組,以確保團隊使用相同的代碼

有關如何在 Azure 機器學習管道中建立、連線和使用模組的代碼範例,請參閱[筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)。

## <a name="next-steps"></a>後續步驟

Azure ML 管道是一個強大的工具,在早期開發階段開始交付價值。 隨著團隊和項目的增長,該值會隨之增加。 本文解釋了如何使用 Azure 機器學習 Python SDK 指定管道並在 Azure 上進行編排。 您已經看到了一些基本的原始程式碼,並介紹了一些可用`PipelineStep`的類。 您應該瞭解何時使用 Azure ML 管道以及 Azure 如何執行它們。 


+ 瞭解如何[建立第一個導管](how-to-create-your-first-pipeline.md)。

+ 瞭解如何[對大數據執行批次處理預測](tutorial-pipeline-batch-scoring-classification.md )。

+ 有關[管道核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)和[管道步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py),請參閱 SDK 參考文檔。

+ 試用顯示[Azure 機器學習管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的 Jupyter 筆記本示例。 瞭解如何[執行筆記本以探索此服務](samples-notebooks.md)。
