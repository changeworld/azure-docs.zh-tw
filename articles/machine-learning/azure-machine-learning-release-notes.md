---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning
description: 深入瞭解 Azure Machine Learning 的最新更新，以及機器學習服務和資料準備 Python Sdk。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 07ef3858cc6a514ed60a9d25046dc4ff9566fa31
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546345"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning 版本資訊

在本文中，您將瞭解 Azure Machine Learning 版本。  如需完整的 SDK 參考內容，請造訪 Azure Machine Learning 的[**Python 的主要 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)參考頁面。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.85

+ **新功能**
  + **azureml-core**
    + 取得指定工作區和訂用帳戶中 AmlCompute 資源的目前核心使用量和配額限制
  
  + **azureml-contrib-管線-步驟**
    + 讓使用者將表格式資料集當做上一個步驟的中繼結果傳遞至 parallelrunstep

+ **Bug 修正和改善**
  + **azureml-automl-執行時間**
    + 已移除對已部署預測服務要求中 y_query 資料行的需求。 
    + 已從 Dominick 的橙色 Juice 筆記本服務要求區段移除 ' y_query '。
    + 已修正 bug，以防止在已部署的模型上進行預測，並在具有日期時間資料行的資料集上操作。
    + 已針對二元和多元分類，新增 Matthews 相互關聯係數做為分類度量。
  + **azureml-contrib-解讀**
    + 已移除從 azureml explainers 的文字-contrib-解讀為文字說明已移至即將發行的解讀文字存放庫。
  + **azureml-core**
    + 資料集：檔案資料集的使用方式不再取決於要安裝在 python env 中的 numpy 和 pandas。
    + 已變更 wait_for_deployment LocalWebservice （），以檢查本機 Docker 容器的狀態，然後再嘗試 ping 其健康情況端點，大幅減少報告失敗部署所需的時間量。
    + 已修正在使用 LocalWebservice （）函式從現有的部署建立服務物件時，LocalWebservice 中使用的內部屬性初始化（）。
    + 已編輯錯誤訊息以供澄清。
    + 將名為 get_access_token （）的新方法新增至 AksWebservice，它會傳回 AksServiceAccessToken 物件，其中包含存取權杖、在時間戳記之後重新整理、時間戳記和權杖類型到期。 
    + 已取代 AksWebservice 中的現有 get_token （）方法，因為新方法會傳回這個方法傳回的所有資訊。
    + 已修改 az ml service 取得存取權杖命令的輸出。 已將 token 重新命名為 accessToken，並 refreshBy 為 refreshAfter。 已新增 expiryOn 和 tokenType 屬性。
    + 已修正 get_active_runs
  + **azureml-explain-model**
    + 已將 shap 更新至0.33.0，並將-社區轉譯為0.4。 *
  + **azureml-解讀**
    + 已將 shap 更新至0.33.0，並將-社區轉譯為0.4。 *
  + **azureml-定型-automl-執行時間**
    + 已針對二元和多元分類，新增 Matthews 相互關聯係數做為分類度量。
    + 取代程式碼中的前置處理旗標，並取代為特徵化-特徵化預設為開啟

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.83

+ **新功能**
  + 資料集：加入兩個選項 `on_error` 和 `out_of_range_datetime`，以便在資料具有錯誤值而不是以 `None`填滿時，讓 `to_pandas_dataframe` 失敗。
  + 工作區：新增具有敏感性資料之工作區的 `hbi_workspace` 旗標，以啟用進一步的加密，並停用工作區上的 advanced diagnostics。 我們也新增了在建立工作區時指定 `cmk_keyvault` 和 `resource_cmk_uri` 參數，以將您自己的金鑰帶入相關聯 Cosmos DB 實例的支援，這會在布建您的工作區時，于訂用帳戶中建立 Cosmos DB 實例。 [如需詳細資訊，請參閱這裡。](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Bug 修正和改善**
  + **azureml-automl-執行時間**
    + 已修正在 Python 版本的3.5.4 上執行 AutoML 時，造成 TypeError 引發的回歸。
  + **azureml-core**
    + 已修正不能使用 `./` 開頭之相對路徑的 `datastore.upload_files` 中的 bug。
    + 已新增所有映射類別 codepaths 的取代訊息
    + 已修正 Mooncake 區域的模型管理 URL 結構。
    + 已修正無法封裝使用 source_dir 的模型以供 Azure Functions 的問題。    
    + 已將選項新增至[環境。 build_local （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)以將映射推送至 AzureML 工作區容器登錄
    + 已更新 SDK，以後向相容的方式在 azure synapse 上使用新的權杖程式庫。
  + **azureml-解讀**
    + 已修正未提供可供下載的說明時，未傳回任何內容的錯誤（bug）。 現在會引發例外狀況，並在其他地方比對行為。
  + **azureml-pipeline-steps**
    + 當 `Estimator` 將用於 `EstimatorStep`時，不允許將 `DatasetConsumptionConfig`傳遞至 `Estimator`的 `inputs` 參數。
  + **azureml-sdk**
    + 已將 AutoML 用戶端新增至 azureml-sdk 套件，讓遠端 AutoML 執行提交，而不需要安裝完整的 AutoML 套件。
  + **azureml-定型-automl-用戶端**
    + 已更正 automl 執行的主控台輸出對齊
    + 已修正遠端 amlcompute 上可能會安裝不正確版本 pandas 的錯誤。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.81

+ **Bug 修正和改善**
  + **azureml-contrib-解讀**
    + 延遲 shap 相依性以從 azureml 解讀
  + **azureml-core**
    + 計算目標現在可以指定為對應部署設定物件的參數。 這是要部署至的計算目標名稱，而不是 SDK 物件。
    + 已將 CreatedBy 資訊新增至模型和服務物件。 可透過 <var>存取。 created_by
    + 已修正 Install-containerimage （），其未正確設定 Docker 容器的 HTTP 埠。
    + 為 `az ml dataset register` cli 命令設為選擇性 `azureml-dataprep`
  + **azureml-dataprep**
    + 已修正 to_pandas_dataframe TabularDataset 會錯誤地切換回替代讀取器並印出警告的錯誤（bug）。
  + **azureml-explain-model**
    + 延遲 shap 相依性以從 azureml 解讀
  + **azureml-pipeline-core**
    + 已新增管線步驟 `NotebookRunnerStep`，以在管線中以步驟執行本機筆記本。
    + 已移除 PublishedPipelines、排程和 PipelineEndpoints 的已淘汰 get_all 函式
  + **azureml-定型-automl-用戶端**
    + 已開始將 data_script 取代為 AutoML 的輸入。


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.79

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + 已移除要記錄的 featurizationConfig
      + 已將記錄更新為僅記錄「自動」/「關閉」/「自訂」。
  + **azureml-automl-執行時間**
    + 已新增對 pandas 的支援。 數列和 pandas。 用於偵測資料行資料類型的類別。 先前只有支援的 numpy。 ndarray
      + 已新增相關的程式碼變更，以正確地處理類別 dtype。
    + 已改善預測函數介面： y_pred 參數設為選用。 -已改善 docstrings。
  + **azureml-contrib-資料集**
    + 已修正無法裝載已標記資料集的 bug。
  + **azureml-core**
    + 修正 `Environment.from_existing_conda_environment(name, conda_environment_name)`的 Bug。 使用者可以建立環境的實例，這是本機環境的確切複本
    + 根據預設，將時間序列相關的資料集方法變更為 `include_boundary=True`。
  + **azureml-定型-automl-用戶端**
    + 已修正 [顯示輸出] 設定為 false 時，不會列印驗證結果的問題。


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.76

+ **重大變更**
  + Azureml-訓練-AutoML 升級問題
    + 升級至 azureml-automl > = 1.0.76 from azureml-定型-automl < 1.0.76 可能會導致部分安裝，導致部分 automl 匯入失敗。 若要解決這個問題，您可以執行 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd 找到的安裝腳本。 或者，如果您直接使用 pip，您可以：
      + 「pip 安裝--升級 azureml-訓練-automl」
      + 「pip 安裝--忽略-已安裝的 azureml-訓練-automl-用戶端」
    + 或者，您也可以在升級之前先卸載舊版本
      + 「pip 卸載 azureml-訓練-automl」
      + 「pip 安裝 azureml-訓練-automl」

+ **Bug 修正和改善**
  + **azureml-automl-執行時間**
    + 在計算二元分類工作的平均純量計量時，AutoML 現在會考慮 true 和 false 類別。
    + 已將 AutoML-Core 中的機器學習和訓練程式碼移至新的套件 AzureML-AutoML-Runtime。
  + **azureml-contrib-資料集**
    + 使用下載選項在加上標籤的資料集上呼叫 `to_pandas_dataframe` 時，您現在可以指定是否要覆寫現有的檔案。
    + 當呼叫會導致時間序列、標籤或影像資料行遭到卸載的 `keep_columns` 或 `drop_columns` 時，也會卸載對應的功能供 dataset 使用。
    + 已修正物件偵測工作的 pytorch 載入器問題。
  + **azureml-contrib-解讀**
    + 已從 azureml 移除說明儀表板 widget-contrib-解讀、變更的封裝，以參考中的新元件 interpret_community
    + 將解讀-社區更新為0.2.0 的版本
  + **azureml-core**
    + 改善 `workspace.datasets`的效能。
    + 已新增使用使用者名稱和密碼驗證來註冊 Azure SQL Database 資料存放區的功能
    + 從相對路徑載入 RunConfigurations 的修正。
    + 呼叫會導致時間序列資料行被卸載的 `keep_columns` 或 `drop_columns` 時，也會卸載對應的功能，以供 dataset 使用。
  + **azureml-解讀**
    + 將解讀-社區更新為0.2.0 的版本
  + **azureml-pipeline-steps**
    + 已記載 `runconfig_pipeline_params` azure machine learning 管線步驟的支援值。
  + **azureml-pipeline-core**
    + 已新增 CLI 選項，以針對管線命令下載 json 格式的輸出。
  + **azureml-train-automl**
    + 將 AzureML AutoML 分為2個套件，用戶端封裝 AzureML-定型-AutoML-用戶端和 ML 訓練套件 AzureML-定型-AutoML-執行時間
  + **azureml-定型-automl-用戶端**
    + 已新增瘦用戶端來提交 AutoML 實驗，而不需要在本機安裝任何機器學習服務相依性。
    + 已修正遠端執行中自動偵測到的延遲、滾動視窗大小和最大視野的記錄。
  + **azureml-定型-automl-執行時間**
    + 已新增新的 AutoML 套件，以隔離機器學習服務和用戶端的執行時間元件。
  + **azureml-contrib-定型-rl**
    + 已新增 SDK 中的增強式學習支援。
    + 已在 RL SDK 中新增 AmlWindowsCompute 支援。


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.74

  + **預覽功能**
    + **azureml-contrib-資料集**
      + 匯入 contrib 資料集之後，您可以呼叫 `Dataset.Labeled.from_json_lines` 而不是 `._Labeled` 來建立加上標籤的資料集。
      + 使用下載選項在加上標籤的資料集上呼叫 `to_pandas_dataframe` 時，您現在可以指定是否要覆寫現有的檔案。
      + 當您呼叫的 `keep_columns` 或 `drop_columns` 會導致卸載時間序列、標籤或影像資料行時，也會卸載 dataset 的對應功能。
      + 已修正呼叫 `dataset.to_torchvision()`時，PyTorch 載入器的問題。

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 已將模型分析新增至 preview CLI。
    + 修正 Azure 儲存體中的重大變更，導致 AzureML CLI 失敗。
    + 已針對 AKS 類型將 Load Balancer 類型新增至 LIP.MLC
  + **azureml-automl-核心**
    + 已修正偵測到最大水準時間序列的問題，具有遺漏值和多個粒紋。
    + 已修正產生交叉驗證分割期間失敗的問題。
    + 將此區段取代為 markdown 格式的訊息，以顯示在版本資訊中：-改善預測資料集中較短粒紋的處理方式。
    + 已修正在記錄期間遮罩部分使用者資訊的問題。 -改善預測執行期間的錯誤記錄。
    + 將 psutil 當做 conda 相依性新增至自動產生的 yml 部署檔案。
  + **azureml-contrib-mir**
    + 修正 Azure 儲存體中的重大變更，導致 AzureML CLI 失敗。
  + **azureml-core**
    + 修正導致在 Azure Functions 上部署模型以產生財500公司的錯誤（bug）。
    + 已修正 amlignore 檔案未套用至快照集的問題。
    + 已加入新的 API amlcompute。 get_active_runs，它會傳回在指定 amlcompute 上執行和佇列執行的產生器。
    + 已針對 AKS 類型將 Load Balancer 類型新增至 LIP.MLC。
    + 已將 append_prefix bool 參數新增至 artifacts_client 中 run.py 和 download_artifacts_from_prefix 中的 download_files。 此旗標是用來選擇性地壓平合併原始 filepath，因此只會將檔案或資料夾名稱新增至 output_directory
    + 修正具有資料集使用方式之 `run_config.yml` 的還原序列化問題。
    + 當呼叫會導致卸載時間序列資料行 `keep_columns` 或 `drop_columns` 時，也會針對資料集卸載對應的功能。
  + **azureml-解讀**
    + 已將解讀-社區版本更新為0.1.0。3
  + **azureml-train-automl**
    + 已修正 automl_step 可能無法列印驗證問題的問題。
    + 已修正 register_model 成功，即使模型的環境在本機遺失相依性也一樣。
    + 已修正某些遠端執行未啟用 docker 的問題。
    + 新增導致本機執行提前失敗的例外狀況記錄。
  + **azureml-train-core**
    + 請考慮在自動超參數微調最佳子執行的計算中執行 resume_from。
  + **azureml-pipeline-core**
    + 已修正管線引數結構中的參數處理。
    + 已新增管線描述和步驟類型 yaml 參數。
    + 管線步驟的新 yaml 格式，並已新增舊格式的取代警告。



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Web 體驗

[https://ml.azure.com](https://ml.azure.com)的 [共同作業] 工作區登陸頁面已增強，並更名為 Azure Machine Learning studio （預覽）。

在 studio 中，您可以定型、測試、部署和管理 Azure Machine Learning 的資產，例如資料集、管線、模型、端點等等。

從 studio 存取下列以 web 為基礎的撰寫工具：

| 以 Web 為基礎的工具 | 說明 | 版本 |
|-|-|-|
| 筆記本 VM （預覽） | 完全受控的雲端式工作站 | 基本 & 企業 |
| [自動化機器學習](tutorial-first-experiment-automated-ml.md)服務（預覽） | 自動化機器學習模型開發的程式碼體驗 | Enterprise |
| [設計](concept-designer.md)工具（預覽） | 拖放機器學習模型化工具，先前稱為設計師 | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning 設計工具增強功能

+ 先前稱為視覺化介面 
+   11個新[模組](algorithm-module-reference/module-reference.md)，包括 recommender、分類器和定型公用程式，包括功能工程、交叉驗證和資料轉換。

### <a name="r-sdk"></a>R SDK 
 
資料科學家和 AI 開發人員會使用[適用于 R 的 AZURE MACHINE LEARNING SDK](tutorial-1st-r-experiment.md) ，透過 Azure Machine Learning 來建立及執行機器學習工作流程。

適用于 R 的 Azure Machine Learning SDK 會使用 `reticulate` 套件來系結至 Python SDK。 藉由直接系結至 Python，適用于 R 的 SDK 可讓您從所選的任何 R 環境，存取在 Python SDK 中執行的核心物件和方法。

SDK 的主要功能包括：

+   管理用來對您的機器學習實驗進行監視、記錄及組織的雲端資源。
+   使用雲端資源（包括 GPU 加速模型訓練）來定型模型。
+   將您的模型部署為 Azure 容器實例（ACI）上的 webservices，並 Azure Kubernetes Service （AKS）。

如需完整的檔，請參閱[套件網站](https://azure.github.io/azureml-sdk-for-r)。

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning 與事件方格整合 

Azure Machine Learning 現在是事件方格的資源提供者，您可以透過 Azure 入口網站或 Azure CLI 設定機器學習事件。 使用者可以建立執行完成、模型註冊、模型部署和偵測到資料漂移的事件。 這些事件可以路由傳送至事件方格支援的事件處理常式以供取用。 如需詳細資訊，請參閱機器學習服務事件[架構](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)、[概念](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration)和[教學](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid)課程文章。

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.72

+ **新功能**
  + 透過[**datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)套件新增資料集監視器，讓您監視時間序列資料集，以便在一段時間後進行資料漂移或其他統計變更。 如果偵測到漂移或符合資料的其他條件，就可以觸發警示和事件。 如需詳細資訊，請參閱[我們的檔](https://aka.ms/datadrift)。
  + 在 Azure Machine Learning 中宣佈兩個新版本（也稱為 SKU）。 在此版本中，您現在可以建立基本或企業 Azure Machine Learning 的工作區。 所有現有的工作區都會預設為基本版本，您可以移至 Azure 入口網站或至 studio，隨時升級工作區。 您可以從 Azure 入口網站建立基本或企業工作區。 若要深入瞭解，請參閱[我們的檔](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)。 從 SDK 中，您可以使用工作區物件的「sku」屬性來判斷您的工作區版本。
  + 我們也增強了 Azure Machine Learning 計算的功能-您現在可以在 Azure 監視器中，查看叢集的計量（例如節點總計、執行中節點、總核心配額），除了查看診斷記錄以進行偵錯工具以外。 此外，您也可以在叢集上查看目前執行中或已排入佇列的回合，以及詳細資料，例如叢集中各種節點的 Ip。 您可以在入口網站中，或使用 SDK 或 CLI 中的對應函式來查看這些功能。

  + **預覽功能**
    + 我們將在 Azure Machine Learning 計算中發行本機 SSD 磁片加密的預覽支援。 請提出技術支援票證，讓您的訂用帳戶列入允許清單，以使用此功能。
    + Azure Machine Learning 批次推斷的公開預覽。 Azure Machine Learning 批次推斷是以不區分時間的大型推斷作業為目標。 批次推斷提供符合成本效益的推斷計算調整，並具有非同步應用程式的無可匹敵輸送量。 它已針對大型資料集合的高輸送量、火災和忘的推斷進行優化。
    + [**azureml-contrib-資料集**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + 已啟用標籤資料集的功能
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + CLI 現在支援模型封裝。
    + 已新增資料集 CLI。 如需詳細資訊： `az ml dataset --help`
    + 新增支援部署和封裝支援的模型（ONNX、scikit-learn、學習和 TensorFlow），而不含 InferenceConfig 實例。
    + 已在 SDK 和 CLI 中新增服務部署（ACI 和 AKS）的覆寫旗標。 如果有提供，將會覆寫現有的服務，前提是已有同名的服務。 如果服務不存在，將會建立新的服務。
    + 您可以使用兩個新的架構 Onnx 和 Tensorflow 來註冊模型。 -模型註冊可接受範例輸入資料、範例輸出資料，以及模型的資源設定。
  + **azureml-automl-核心**
    + 只有在設定執行時間條件約束時，才會在子進程中執行定型。
    + 已新增預測工作的 guardrail，以檢查指定的 max_horizon 是否會在給定的電腦上造成記憶體問題。 如果是，將會顯示 guardrail 訊息。
    + 已新增對複雜頻率（例如2年和1個月）的支援。 -如果無法判斷頻率，就會新增理解錯誤訊息。
    + 將 azureml-預設值新增至自動產生的 conda env，以解決模型部署失敗
    + 允許 Azure Machine Learning 管線中的中繼資料轉換成表格式資料集，並用於 `AutoMLStep`。
    + 已針對資料流程執行資料行目的更新。
    + 針對串流處理 Imputer 和 HashOneHotEncoder 的已執行轉換器參數更新。
    + 已將目前的資料大小和所需的最小資料大小新增至驗證錯誤訊息。
    + 已更新交叉驗證所需的最小資料大小，以保證每個驗證折迭中至少要有兩個樣本。
  + **azureml-cli-通用**
    + CLI 現在支援模型封裝。
    + 您可以使用兩個新的架構 Onnx 和 Tensorflow 來註冊模型。
    + 模型註冊可接受範例輸入資料、範例輸出資料，以及模型的資源設定。
  + **azureml-contrib-gbdt**
    + 已修正筆記本的發行通道
    + 為不支援的非 AmlCompute 計算目標新增警告
    + 已將 LightGMB 估計工具新增至 azureml-contrib-gbdt 套件
  + [**azureml-核心**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI 現在支援模型封裝。
    + 為已淘汰的資料集 Api 新增取代警告。 請參閱 https://aka.ms/tabular-dataset 的資料集 API 變更通知。
    + 如果已註冊資料集，請將[`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)變更為傳回註冊名稱和版本。
    + 修正以 dataset 做為引數 ScriptRunConfig 的 bug，無法重複使用來提交實驗執行。
    + 執行期間所抓取的資料集會受到追蹤，並可在執行詳細資料頁面中看到，或在執行完成之後呼叫[`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) 。
    + 允許 Azure Machine Learning 管線中的中繼資料轉換成表格式資料集，並用於[`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)。
    + 新增支援部署和封裝支援的模型（ONNX、scikit-learn、學習和 TensorFlow），而不含 InferenceConfig 實例。
    + 已在 SDK 和 CLI 中新增服務部署（ACI 和 AKS）的覆寫旗標。 如果有提供，將會覆寫現有的服務，前提是已有同名的服務。 如果服務不存在，將會建立新的服務。
    +  您可以使用兩個新的架構 Onnx 和 Tensorflow 來註冊模型。 模型註冊可接受範例輸入資料、範例輸出資料，以及模型的資源設定。
    + 已新增適用於 MySQL 的 Azure 資料庫的新資料存放區。 已新增在 Azure Machine Learning 管線中的 DataTransferStep 中使用適用於 MySQL 的 Azure 資料庫的範例。
    + 新增功能以從實驗中新增和移除標記從執行移除標記的功能
    + 已在 SDK 和 CLI 中新增服務部署（ACI 和 AKS）的覆寫旗標。 如果有提供，將會覆寫現有的服務，前提是已有同名的服務。 如果服務不存在，將會建立新的服務。
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 從 `azureml-contrib-datadrift` 移至 `azureml-datadrift`
    + 已新增監視漂移和其他統計量值之時間序列資料集的支援
    + 新方法會 `create_from_model()`，並 `create_from_dataset()` 到[`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class))類別。 `create()` 方法將會被取代。
    + Azure Machine Learning studio 中 Python 和 UI 中的視覺效果調整。
    + 除了資料集監視器的每日，還支援每週和每月監視排程。
    + 支援資料監視器計量的回填，以分析資料集監視器的歷程記錄資料。
    + 各種 Bug 修正
  + [**azureml-管線核心**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + 從管線 `yaml` 檔案提交 Azure Machine Learning 管線執行時，不再需要 dataprep。
  + [**azureml-定型-automl**](/python/api/azureml-train-automl-runtime/)
    + 將 azureml-預設值新增至自動產生的 conda env，以解決模型部署失敗
    + AutoML 遠端訓練現在包含 azureml-預設值，以允許重複使用定型 env 進行推斷。
  + **azureml-train-core**
    + 已在[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)估計工具中新增 PyTorch 1.3 支援

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>視覺化介面（預覽）

+ 已翻修了 Azure Machine Learning 視覺化介面（預覽）以在[Azure Machine Learning 管線](concept-ml-pipelines.md)上執行。 在視覺化介面中撰寫的管線（先前稱為「實驗」）現在已與核心 Azure Machine Learning 體驗完全整合。
  + 使用 SDK 資產的整合管理體驗
  + 視覺化介面模型、管線和端點的版本控制和追蹤
  + 重新設計的 UI
  + 已新增批次推斷部署
  + 已新增對推斷計算目標的 Azure Kubernetes Service （AKS）支援
  + 新的 Python-步驟管線撰寫工作流程
  + 視覺效果撰寫工具的新[登陸頁面](https://ml.azure.com)

+ **新模組**
  + 套用數學運算
  + 套用 SQL 轉換
  + 剪輯值
  + 摘要資料
  + 從 SQL database 匯入

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.69

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + 將模型說明限制為最佳執行，而不是每次執行的計算說明。 對本機、遠端和 ADB 進行此行為變更。
    + 新增 UI 的隨選模型說明支援
    + 已將 psutil 新增為 `automl` 和內含 psutil 的相依性，做為 amlcompute 中的 conda 相依性。
    + 已修正預測資料集上的啟發式延遲和滾動視窗大小的問題，這可能會造成線性代數錯誤
      + 已針對預測執行中啟發式決定的參數新增 print out。
  + **azureml-contrib-datadrift**
    + 如果資料集層級漂移不在第一節中，則會在建立輸出計量時新增保護。
  + **azureml-contrib-解讀**
    + azureml-contrib-說明-模型套件已重新命名為 azureml-contrib-解讀
  + **azureml-core**
    + 已新增 API 以取消註冊資料集。 `dataset.unregister_all_versions()`
    + azureml-contrib-說明-模型套件已重新命名為 azureml-contrib-解讀。
  + **[azureml-核心](https://docs.microsoft.com/python/api/azureml-core)**
    + 已新增 API 以取消註冊資料集。 集中.[unregister_all_versions （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)。
    + 已新增資料集 API 以檢查資料變更時間。 `dataset.data_changed_time`答案中所述步驟，工作帳戶即會啟用。
    + 能夠取用 `FileDataset` 和 `TabularDataset` 作為 `HyperDriveStep` 管線中 `PythonScriptStep`、`EstimatorStep`和 Azure Machine Learning 的輸入
    + 已針對具有大量檔案的資料夾改善 `FileDataset.mount` 的效能
    + 能夠在 Azure Machine Learning 管線中，使用[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset)和[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset)作為[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)、 [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)和[HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep)的輸入。
    + FileDataset 的效能。已針對具有大量檔案的資料夾改進[掛接（）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-)
    + 已在 [執行詳細資料] 中新增已知錯誤建議的 URL。
    + 已修正 run 中的錯誤（bug）。如果執行有太多子系，則要求會失敗 get_metrics
    + 已修正 run 中的錯誤（bug）。如果執行有太多子系，則要求會失敗[get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-)
    + 已新增 Arcadia 叢集上的驗證支援。
    + 建立實驗物件會取得或建立 Azure Machine Learning 工作區中的實驗，以執行歷程記錄追蹤。 實驗識別碼和封存時間會在建立時填入實驗物件中。 範例：實驗 = 實驗（工作區、「新實驗」） experiment_id = experiment.id archive （）和重新開機（）是可在實驗上呼叫的函式，以隱藏和還原實驗，使其不會顯示在 UX 中，或預設會在呼叫中傳回以列出實驗。 如果使用與封存實驗相同的名稱建立新的實驗，您可以藉由傳遞新名稱來重新開機已封存的實驗。 只能有一個具有指定名稱的作用中實驗。 範例： experiment1 = 實驗（工作區，"Active 實驗"） experiment1. archive （） # 建立新的作用中實驗，其名稱與封存的相同。 experiment2. = 實驗（工作區、「作用中實驗」） experiment1。重新開機（new_name = 「先前的作用中實驗」）實驗上的靜態方法清單（）可以接受名稱篩選和 ViewType 篩選。 ViewType 值為 "ACTIVE_ONLY"、"ARCHIVED_ONLY" 和 "ALL" 範例： archived_experiments = 實驗。 list （workspace，view_type = "ARCHIVED_ONLY"） all_first_experiments = 實驗。 list （workspace，name = "First 實驗"，view_type = "ALL"）
    + 支援使用環境進行模型部署和服務更新
  + **azureml-datadrift**
    + DataDriftDector 類別的 show 屬性不會再支援選擇性的引數 ' with_details '。 Show 屬性只會呈現資料漂移係數和特徵資料行的資料漂移比重。
    + DataDriftDetector 屬性 ' get_output ' 行為變更：
      + 輸入參數 start_time，end_time 是選擇性的，而不是強制性;
      + 在相同的叫用中使用特定 run_id 輸入特定 start_time 和/或 end_time，將會導致值錯誤例外狀況，因為它們互斥
      + 藉由輸入特定 start_time 和/或 end_time，只會傳回排程執行的結果;
      + 參數 ' daily_latest_only ' 已被取代。
    + 支援抓取以資料集為基礎的資料漂移輸出。
  + **azureml-explain-model**
    + 將 AzureML-說明模型套件重新命名為 AzureML-解讀，保留舊的封裝以提供回溯相容性
    + 已修正 `automl` bug，並將原始說明設定為分類工作，而不是根據預設從 ExplanationClient 下載的回歸
    + 新增要使用 `ScoringExplainer` 直接建立的支援 `MimicWrapper`
  + **azureml-pipeline-core**
    + 改善大型管線建立的效能
  + **azureml-train-core**
    + 已在 TensorFlow 估計工具中新增 TensorFlow 2.0 支援
  + **azureml-train-automl**
    + 建立[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment)物件會取得或建立 Azure Machine Learning 工作區中的實驗，以執行歷程記錄追蹤。 實驗識別碼和封存時間會在建立時填入實驗物件中。 範例：

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        封存[（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--)和[重新開機（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-)是可以在實驗上呼叫的函式，以隱藏和還原實驗，使其不會顯示在 UX 中，或預設會在清單實驗的呼叫中傳回。 如果使用與封存實驗相同的名稱建立新的實驗，您可以藉由傳遞新名稱來重新開機已封存的實驗。 只能有一個具有指定名稱的作用中實驗。 範例：

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        實驗上的靜態方法[清單（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-)可以接受名稱篩選和 ViewType 篩選。 ViewType 值為 "ACTIVE_ONLY"、"ARCHIVED_ONLY" 和 "ALL"。 範例：

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 支援使用環境進行模型部署和服務更新。
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)類別的 show 屬性不會再支援選擇性的引數 ' with_details '。 Show 屬性只會呈現資料漂移係數和特徵資料行的資料漂移比重。
    + DataDriftDetector 函數 [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) 行為變更：
      + 輸入參數 start_time，end_time 是選擇性的，而不是強制性;
      + 在相同叫用中使用特定 run_id 輸入特定 start_time 和/或 end_time，會導致值錯誤例外狀況，因為它們互斥。
      + 藉由輸入特定 start_time 和/或 end_time，只會傳回排程執行的結果;
      + 參數 ' daily_latest_only ' 已被取代。
    + 支援抓取以資料集為基礎的資料漂移輸出。
  + **[azureml-說明-模型](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + 將 AzureML-說明模型套件重新命名為 AzureML-解讀，並保留舊的封裝以提供回溯相容性。
    + 已修正 AutoML bug，其中原始說明已設定為分類工作，而不是預設從 ExplanationClient 下載的回歸。
    + 新增使用[MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)直接建立[ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer)的支援
  + **[azureml-管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 改善大型管線建立的效能。
  + **[azureml-定型-核心](https://docs.microsoft.com/python/api/azureml-train-core)**
    + 已在[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow)估計工具中新增 TensorFlow 2.0 支援。
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 當安裝程式反復專案失敗時，父執行將不會再失敗，因為協調流程已負責處理。
    + 已新增適用于 AutoML 實驗的本機 docker 和本機 conda 支援
    + 已新增適用于 AutoML 實驗的本機 docker 和本機 conda 支援。


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>適用于 Azure Machine Learning 工作區的新 web 體驗（預覽）

[新工作區入口網站](https://ml.azure.com)中的 [實驗] 索引標籤已更新，因此資料科學家可以更有效的方式監視實驗。 您可以探索下列功能：
+ 實驗中繼資料，輕鬆篩選和排序您的實驗清單
+ 簡化且高效能的實驗詳細資料頁面，可讓您將執行視覺化並加以比較
+ 執行詳細資料頁面的新設計，以瞭解和監視您的定型執行

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.65

  + **新功能**
    + 已新增策劃環境。 這些環境已預先設定用於一般機器學習工作的程式庫，並已預先建立並快取為 Docker 映射，以加快執行速度。 根據預設，它們會出現在工作區的環境清單中，前置詞為 "AzureML"。
    + 已新增策劃環境。 這些環境已預先設定用於一般機器學習工作的程式庫，並已預先建立並快取為 Docker 映射，以加快執行速度。 根據預設，它們會出現在[工作區](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)的環境清單中，前置詞為 "AzureML"。

  + **azureml-train-automl**
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 已新增 ADB 和 HDI 的 ONNX 轉換支援

+ **預覽功能**
  + **azureml-train-automl**
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援的經理 BERT 和 BiLSTM 做為文字 featurizer （僅限預覽）
    + 針對資料行用途和轉換器參數支援的特徵化自訂（僅限預覽）
    + 當使用者在定型期間啟用模型說明時支援的原始說明（僅限預覽）
    + 已將 `timeseries` 預測的 Prophet 新增為可訓練管線（僅限預覽）

  + **azureml-contrib-datadrift**
    + 從 azureml-contrib-datadrift 重新置放至 azureml-datadrift 的套件;未來版本將移除 `contrib` 封裝

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + 引進了 FeaturizationConfig 至 AutoMLConfig 和 AutoMLBaseSettings
    + 引進了 FeaturizationConfig 至[AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)和 AutoMLBaseSettings
      + 使用指定的資料行和功能類型覆寫特徵化的資料行用途
      + 覆寫轉換器參數
    + 已新增 explain_model （）和 retrieve_model_explanations （）的取代訊息
    + 已將 Prophet 新增為可訓練管線（僅限預覽）
    + 已新增 explain_model （）和 retrieve_model_explanations （）的取代訊息。
    + 已將 Prophet 新增為可訓練管線（僅限預覽）。
    + 已新增自動偵測目標延遲、滾動視窗大小和最大水準的支援。 如果其中一個 target_lags、target_rolling_window_size 或 max_horizon 設定為 [自動]，則會套用啟發學習法，以根據定型資料來估計對應參數的值。
    + 已修正當資料集包含一個細微性資料行時的預測，這是數數值型別，而且定型和測試集之間有間距
    + 已修正在「預測工作」的遠端執行中重複索引的相關錯誤訊息
    + 已修正當資料集包含一個資料行時的預測，這是一種數數值型別，定型和測試集之間會有間距。
    + 已修正在「預測工作」的遠端執行中重複索引的相關錯誤訊息。
    + 已新增 guardrail，以檢查資料集是否已不平衡。 如果是，則會將 guardrail 訊息寫入主控台。
  + **azureml-core**
    + 已新增透過模型物件將 SAS URL 取出至儲存體中模型的功能。 例如： model. get_sas_url （）
    + 引進 `run.get_details()['datasets']`，以取得與已提交執行相關聯的資料集
    + 新增 API `Dataset.Tabular.from_json_lines_files`，以從 JSON 行檔案建立 TabularDataset。 若要瞭解 TabularDataset 上 JSON 行檔案中的此表格式資料，請流覽 https://aka.ms/azureml-data 以取得檔。
    + 已將額外的 VM 大小欄位（OS 磁片、Gpu 數目）新增至 supported_vmsizes （）函數
    + 已將其他欄位新增至 list_nodes （）函式，以顯示執行、私用和公用 IP、埠等。
    + 在叢集布建期間指定新欄位的能力--remotelogin_port_public_access 可以設定為 [啟用] 或 [停用]，視您是否想要在建立叢集時讓 SSH 埠保持開啟或關閉。 如果您未指定，服務會聰明地開啟或關閉埠，視您是否在 VNet 內部署叢集而定。
  + **azureml-explain-model**
  + **[azureml-核心](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 已新增透過模型物件將 SAS URL 取出至儲存體中模型的功能。 例如： model。[get_sas_url （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 引進執行。[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' dataset '] 取得與已提交執行相關聯的資料集
    + 新增 API `Dataset.Tabular`。[from_json_lines_files （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) ，以從 json 行檔案建立 TabularDataset。 若要瞭解 TabularDataset 上 JSON 行檔案中的此表格式資料，請流覽 https://aka.ms/azureml-data 以取得檔。
    + 已將額外的 VM 大小欄位（OS 磁片、Gpu 數目）新增至[supported_vmsizes （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)函數
    + 已將其他欄位新增至[list_nodes （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--)函式，以顯示執行、私用和公用 IP、埠等。
    + 在叢集布建期間指定新欄位的[能力 `--remotelogin_port_public_access` 可以](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)設定為啟用或停用，視您是否想要在建立叢集時讓 SSH 埠保持開啟或關閉。 如果您未指定，服務會聰明地開啟或關閉埠，視您是否在 VNet 內部署叢集而定。
  + **[azureml-說明-模型](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + 已改善分類案例中說明輸出的檔。
    + 已在評估範例的說明上，新增上傳預測 y 值的功能。 解除鎖定更有用的視覺效果。
    + 已將說明屬性新增至 MimicWrapper，以允許取得基礎 MimicExplainer。
  + **azureml-pipeline-core**
    + 已新增筆記本以描述模組、ModuleVersion 和 ModuleStep
  + **azureml-pipeline-steps**
    + 已新增 RScriptStep 以支援透過 AML 管線執行的 R 腳本。
    + 已修正」已 azurebatchstep 中的中繼資料參數剖析，這會導致「未指定參數 SubscriptionId 的指派」錯誤訊息。
  + **azureml-train-automl**
    + 支援的 training_data、validation_data、label_column_name、weight_column_name 做為資料輸入格式
    + 已新增 explain_model （）和 retrieve_model_explanations （）的取代訊息
  + **[azureml-管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 已新增[筆記本](https://aka.ms/pl-modulestep)來描述[Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class))、 [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion)和[ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)。
  + **[azureml-管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + 已新增[RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep)以支援透過 AML 管線執行的 R 腳本。
    + 已修正[」已 azurebatchstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep)中的中繼資料參數剖析，這會導致「未指定參數 SubscriptionId 的指派」錯誤訊息。
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援的 training_data、validation_data、label_column_name、weight_column_name 做為資料輸入格式。
    + 已新增[explain_model （）](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-)和[retrieve_model_explanations （）](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)的取代訊息。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.62

+ **新功能**
  + 在 TabularDataset 上引進了 `timeseries` 特性。 這項特性可讓您輕鬆地對資料 TabularDataset 進行時間戳記篩選，例如在一段時間內取得所有資料，或在最新的資料之間進行。 若要瞭解 TabularDataset 的 `timeseries` 特性，請流覽 https://aka.ms/azureml-data 以取得範例筆記本的檔或 https://aka.ms/azureml-tsd-notebook 。
  + 已啟用 TabularDataset 和 FileDataset 的訓練。 如需範例筆記本，請造訪 https://aka.ms/dataset-tutorial 。

  + **azureml-train-core**
    + 已新增 PyTorch 估計工具中的 `Nccl` 和 `Gloo` 支援

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + 已淘汰 AutoML 設定 ' lag_length ' 和 LaggingTransformer。
    + 修正輸入資料以資料流程格式指定時的正確驗證
    + 修改 fit_pipeline .py，以產生圖形 json 並上傳至成品。
    + 使用 `Cytoscape`在 `userrun` 下轉譯圖形。
  + **azureml-core**
    + 在 ADB 程式碼中再次執行例外狀況處理，並根據新的錯誤處理將變更為。
    + 已新增筆記本 Vm 的自動 MSI 驗證。
    + 修正因重試失敗而無法上傳損毀或空模型的 bug。
    + 修正 `DataReference` 模式變更時 `DataReference` 名稱變更的 bug （例如，呼叫 `as_upload`、`as_download`或 `as_mount`時）。
    + 針對 `FileDataset.mount` 和 `FileDataset.download`，讓 `mount_point` 和 `target_path` 為選擇性。
    + 找不到時間戳記資料行的例外狀況將會擲回，如果呼叫 serials 相關的 API 時未指派微調時間戳記資料行，或是已卸載指派的時間戳記資料行。
    + 應該使用類型為 Date 的資料行來指派 Time serials 資料行，否則應為例外狀況
    + 指派 API ' with_timestamp_columns ' 的時間 serials 資料行可接受無值的微調/粗略時間戳記資料行名稱，這將會清除先前指派的時間戳記資料行。
    + 當您卸載 [粗略細微性] 或 [精細的時間戳記] 資料行時，將會擲回例外狀況，並指出可以在卸載清單中排除 timestamp 資料行或呼叫不含任何值的 with_time_stamp，以釋放時間戳記資料行
    + 當 [保留資料行] 清單中未包含粗略細微性或精細的時間戳記資料行時，將會擲回例外狀況，並指示使用者在 [保留資料行] 清單中包含時間戳記資料行，或使用 [無] 呼叫 with_time_stamp 來保持可以完成釋放時間戳記資料行的值。
    + 新增已註冊模型大小的記錄。
  + **azureml-explain-model**
    + 已修正未安裝「封裝」 python 套件時，列印到主控台的警告：「使用比支援的 lightgbm 版本還舊的版本，請升級至大於2.2.1 的版本」
    + 已修正使用分區化的下載模型說明與許多功能的全域說明
    + 已修正模擬說明遺漏輸出說明的初始化範例
    + 已修正使用具有兩種不同類型之模型的說明用戶端上傳時，set 屬性上的不可變錯誤
    + 已將 get_raw param 加入評分說明。說明（），讓一個評分說明可以同時傳回工程化和原始值。
  + **azureml-train-automl**
    + 從 AutoML 引進的公用 Api，以提供支援說明，從 `automl` 說明 SDK-較新的支援 AutoML 說明的方式，方法是將 AutoML 特徵化分離，並說明適用于 AutoML 模型之 azureml 說明 SDK 的 SDK 整合原始說明支援。
    + 從遠端訓練環境中移除 azureml-預設值。
    + 已將預設快取存放區位置從 FileCacheStore 為基礎變更為 AzureFileCacheStore，以在 Azure Databricks 的程式碼路徑上 AutoML。
    + 修正輸入資料以資料流程格式指定時的正確驗證
  + **azureml-train-core**
    + 已還原 source_directory_data_store 取代。
    + 已新增覆寫已安裝之 azureml 套件版本的功能。
    + 已在估算器的 `environment_definition` 參數中新增 dockerfile 支援。
    + 簡化了估算器中的分散式定型參數。

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>適用于 Azure Machine Learning 工作區的新 web 體驗（預覽）
新的 web 體驗可讓資料科學家和資料工程師完成其端對端機器學習服務生命週期，從準備和視覺化資料，到在單一位置訓練和部署模型。

![Azure Machine Learning 工作區 UI （預覽）](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**主要功能：**

使用這個新的 Azure Machine Learning 介面，您現在可以：
+ 管理您的筆記本或連結至 Jupyter
+ [執行自動化 ML 實驗](tutorial-first-experiment-automated-ml.md)
+ [從本機檔案、資料存放區 & web 檔案建立資料集](how-to-create-register-datasets.md)
+ 探索 & 準備模型建立的資料集
+ 監視模型的資料漂移
+ 從儀表板中查看最近的資源

在此版本中，支援下列瀏覽器： Chrome、Firefox、Safari 和 Microsoft Edge Preview。

**已知問題**

1. 如果您看到「發生錯誤，請重新整理您的瀏覽器！ 當部署正在進行時，載入區塊檔案時發生錯誤。

1. 無法刪除或重新命名筆記本和檔案中的檔案。 在公開預覽期間，您可以在筆記本 VM 中使用 Jupyter UI 或終端機來執行更新檔案作業。 因為它是已掛接的網路檔案系統，所以您在筆記本 VM 上所做的所有變更都會立即反映在 [筆記本] 工作區中。

1. 若要透過 SSH 連線到筆記本 VM：
   1. 尋找在 VM 設定期間所建立的 SSH 金鑰。 或者，在 [Azure Machine Learning] 工作區中尋找金鑰，> 開啟 [計算] 索引標籤，> 在清單中找出 [筆記本 VM] > 開啟它的屬性：從對話方塊複製金鑰。
   1. 將這些公開和私人 SSH 金鑰匯入到您的本機電腦。
   1. 使用它們來透過 SSH 連線到筆記本 VM。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.60

+ **新功能**
  + 引進了 FileDataset，它會參考您資料存放區或公用 url 中的單一或多個檔案。 檔案可以是任何格式。 FileDataset 可讓您將檔案下載或掛接至您的計算。 若要深入瞭解 FileDataset，請造訪 https://aka.ms/file-dataset 。
  + 已新增 PythonScript 步驟、Adla 步驟、Databricks 步驟、DataTransferStep 和 AzureBatch 步驟的管線 Yaml 支援

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + AutoArima 現在是僅供預覽的 suggestable 管線。
    + 改善預測的錯誤報表。
    + 在預測工作中使用自訂例外狀況，而不是泛型來改善記錄。
    + 已移除 max_concurrent_iterations 的檢查，小於反覆運算總數。
    + AutoML 模型現在會傳回 AutoMLExceptions
    + 此版本可改善自動化機器學習本機執行的執行效能。
  + **azureml-core**
    + 引進資料集。 get_all （工作區），會傳回 `TabularDataset` 的字典，以及以其註冊名稱做為索引鍵 `FileDataset` 物件。

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + 引進 `parition_format` 做為 `Dataset.Tabular.from_delimited_files` 和 `Dataset.Tabular.from_parquet.files`的引數。 每個資料路徑的分割區資訊將會根據指定的格式，解壓縮到資料行中。 ' {column_name} ' 會建立字串資料行，而 ' {column_name： yyyy/MM/dd/HH/mm/ss} ' 會建立 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來針對日期時間類型來解壓縮年、月、日、小時、分鐘和秒 Partition_format 應該從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設路徑為 '.../USA/2019/01/01/data.csv '，其中分割區是依據國家和時間，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data .csv ' 會建立字串資料行 ' Country '，其值為 ' USA '，而 datetime 資料行 ' PartitionDate ' 的值為 ' 2019-01-01 '。
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + 引進 `partition_format` 做為 `Dataset.Tabular.from_delimited_files` 和 `Dataset.Tabular.from_parquet.files`的引數。 每個資料路徑的分割區資訊將會根據指定的格式，解壓縮到資料行中。 ' {column_name} ' 會建立字串資料行，而 ' {column_name： yyyy/MM/dd/HH/mm/ss} ' 會建立 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來針對日期時間類型來解壓縮年、月、日、小時、分鐘和秒 Partition_format 應該從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設路徑為 '.../USA/2019/01/01/data.csv '，其中分割區是依據國家和時間，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data .csv ' 會建立字串資料行 ' Country '，其值為 ' USA '，而 datetime 資料行 ' PartitionDate ' 的值為 ' 2019-01-01 '。
    + `to_csv_files` 和 `to_parquet_files` 方法已新增至 `TabularDataset`。 這些方法會將資料轉換成指定格式的檔案，以啟用 `TabularDataset` 和 `FileDataset` 之間的轉換。
    + 儲存 Model. package （）所產生的 Dockerfile 時，會自動登入基底映射登錄。
    + 不再需要 ' gpu_support ';AML 現在會自動偵測並使用 nvidia docker 擴充功能（如果有的話）。 未來的版本將予以移除。
    + 已新增建立、更新和使用 PipelineDrafts 的支援。
    + 此版本可改善自動化機器學習本機執行的執行效能。
    + 使用者可以依名稱查詢執行歷程記錄中的計量。
    + 在預測工作中使用自訂例外狀況，而不是泛型來改善記錄。
  + **azureml-explain-model**
    + 已將 feature_maps 參數新增至新的 MimicWrapper，讓使用者可以取得原始功能的說明。
    + [資料集上傳] 預設會針對 [上傳說明] 關閉，而且可以使用 [upload_datasets = True] 重新啟用。
    + 已將 "is_law" 篩選參數新增至說明清單和下載功能。
    + 將方法 `get_raw_explanation(feature_maps)` 新增至全域和本機說明物件。
    + 已將版本檢查新增至具有列印警告的 lightgbm （如果支援的版本低於）
    + 已優化批次處理說明時的記憶體使用量
    + AutoML 模型現在會傳回 AutoMLExceptions
  + **azureml-pipeline-core**
    + 已新增建立、更新和使用 PipelineDrafts 的支援-可用來維護可變的管線定義，並以互動方式使用它們來執行
  + **azureml-train-automl**
    + 已建立功能以安裝支援 gpu 的特定版本 pytorch v 1.1.0、:::no-loc text="cuda"::: 工具組9.0、pytorch-轉換器，這是在遠端 python 執行時間環境中啟用經理 BERT/XLNet 所需的元件。
  + **azureml-train-core**
    + 直接在 sdk 中（而不是伺服器端）發生某些超參數空間定義錯誤的早期失敗。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning 資料準備 SDK v 1.1.14
+ **Bug 修正和改善**
  + 已使用原始路徑和認證啟用寫入 ADLS/ADLSGen2。
  + 已修正導致 `include_path=True` 無法 `read_parquet`的 bug。
  + 已修正因例外狀況「不正確屬性值： hostSecret」而造成的 `to_pandas_dataframe()` 失敗。
  + 已修正無法在 Spark 模式的 DBFS 上讀取檔案的錯誤。

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.57
+ **新功能**
  + 已啟用 `TabularDataset` 以供 AutomatedML 使用。 若要深入瞭解 `TabularDataset`，請流覽 https://aka.ms/azureml/howto/createdatasets 。

+ **Bug 修正和改善**
  + **automl-用戶端-核心-nativeclient-android**
    + 已修正在以 pandas 資料框架的形式提供定型和/或驗證標籤（y 和 y_valid）時引發的錯誤，但不是 numpy 陣列。
    + 已更新介面，以建立只需要資料和 `AutoMLBaseSettings` 物件的 `RawDataContext`。
    +  允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。 -允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
  + **azure-cli-ml**
    + 您現在可以針對 Microsoft 產生和客戶憑證的 AKS 叢集上部署的評分端點，更新 SSL 憑證。
  + **azureml-automl-核心**
    + 已修正 AutoML 中的問題，其中遺漏標籤的資料列不會正確移除。
    + 已改善 AutoML 中的錯誤記錄;完整的錯誤訊息現在一律會寫入記錄檔。
    + AutoML 已更新其封裝釘選，以包含 `azureml-defaults`、`azureml-explain-model`和 `azureml-dataprep`。 AutoML 不會再警告套件不符（`azureml-train-automl` 套件除外）。
    + 已修正 cv 分割的大小不相等的 `timeseries` 問題，導致 bin 計算失敗。
    + 針對交叉驗證定型類型執行集團反復專案時，如果我們最後無法下載在整個資料集上定型的模型，我們就會在模型權數和正在送入投票的模型之間產生不一致的情況。集團.
    + 已修正在以 pandas 資料框架的形式提供定型和/或驗證標籤（y 和 y_valid）時引發的錯誤，但不是 numpy 陣列。
    + 已修正在輸入資料表的布林資料行中遇到 None 時，預測工作的問題。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。 -允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
  + **azureml-core**
    + 已修正 blob_cache_timeout 參數順序的問題。
    + 已將外部調整和轉換例外狀況類型新增至系統錯誤。
    + 已新增對遠端執行 Key Vault 秘密的支援。 新增 Keyvault 類別，以新增、取得及列出與您的工作區相關聯之 keyvault 中的秘密。 支援的作業包括：
      + get_default_keyvault （）的. 工作區。
      + keyvault. Keyvault. set_secret （name，value）
      + keyvault. Keyvault. set_secrets （secrets_dict）
      + keyvault. Keyvault. get_secret （name）
      + keyvault. Keyvault. get_secrets （secrets_list）
      + keyvault. Keyvault. list_secrets （）
    + 在遠端執行期間取得預設 keyvault 並取得秘密的其他方法：
      + get_default_keyvault （）的. 工作區。
      + get_secret （name）執行。
      + get_secrets （secrets_list）中執行。
    + 已將其他覆寫參數新增至提交-hyperdrive CLI 命令。
    + 改善 API 呼叫的可靠性會將重試擴充至常見的要求程式庫例外狀況。
    + 新增從已提交的執行提交執行的支援。
    + 已修正 FileWatcher 中即將到期的 SAS 權杖問題，這會導致檔案在其初始權杖過期後停止上傳。
    + 支援匯入資料集 python SDK 中的 HTTP csv/tsv 檔案。
    + 已淘汰工作區. setup （）方法。 向使用者顯示的警告訊息建議改用 create （）或 get （）/from_config （）。
    + 已新增環境。 add_private_pip_wheel （），可讓您將私人自訂 python 套件 `whl`上傳至工作區，並安全地使用它們來建立/具體化環境。
    + 您現在可以針對 Microsoft 產生和客戶憑證的 AKS 叢集上部署的評分端點，更新 SSL 憑證。
  + **azureml-explain-model**
    + 已新增參數，以將模型識別碼新增到上傳的說明。
    + 已將 `is_raw` 標記新增至記憶體中的說明並上傳。
    + 已新增 azureml-說明模型套件的 pytorch 支援和測試。
  + **azureml-opendatasets**
    + 支援偵測和記錄自動測試環境。
    + 已新增類別，讓我們依縣市和郵遞區號取得人口。
  + **azureml-pipeline-core**
    + 已將標籤屬性新增至輸入和輸出埠定義。
  + **azureml-遙測**
    + 已修正不正確的遙測設定。
  + **azureml-train-automl**
    + 修正安裝程式失敗時的錯誤、安裝程式執行的「錯誤」欄位中未記錄錯誤，因此不會儲存在父執行「錯誤」中。
    + 已修正 AutoML 中的問題，其中遺漏標籤的資料列不會正確移除。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。
    + 允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
    + 現在 AutoMLStep 會將 `automl` config 傳遞至後端，以避免任何變更或新增設定參數的新問題。
    + AutoML 資料 Guardrail 現已開放公開預覽。 使用者會在定型之後看到資料 Guardrail 報表（適用于分類/回歸工作），而且也可以透過 SDK API 來存取它。
  + **azureml-train-core**
    + 已在 PyTorch 估計工具中新增 torch 1.2 支援。
  + **azureml-widget**
    + 改善分類定型的混淆矩陣圖表。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning 資料準備 SDK v 1.1.12
+ **新功能**
  + 現在可以將字串清單當做輸入傳遞給 `read_*` 方法。

+ **Bug 修正和改善**
  + 在 Spark 中執行時，`read_parquet` 的效能已大幅改善。
  + 已修正在具有不明確日期格式之單一資料行的情況下，`column_type_builder` 失敗的問題。

### <a name="azure-portal"></a>Azure Portal
+ **預覽功能**
  + [執行詳細資料] 頁面現在提供記錄檔和輸出檔案串流。 開啟預覽切換時，檔案會即時串流更新。
  + 在工作區層級設定配額的功能會以預覽形式發行。 AmlCompute 配額會在訂用帳戶層級配置，但我們現在可讓您在工作區之間散發該配額，並將其配置給公平共用和治理。 只要按一下工作區左側導覽列中的 [**使用方式 + 配額**] 分頁，然後選取 [**設定配額**] 索引標籤。請注意，您必須是訂用帳戶管理員，才能在工作區層級設定配額，因為這是跨工作區的作業。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.55

+ **新功能**
  + 以權杖為基礎的驗證現在支援對 AKS 上部署之評分端點的呼叫。 我們會繼續支援目前以金鑰為基礎的驗證，而且使用者可以一次使用其中一種驗證機制。
  + 能夠將虛擬網路（VNet）後方的 blob 儲存體註冊為數據存放區。

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + 修正 CV 分割的驗證大小較小的 bug，並導致預測性和預測的事實與 true 圖表不正確。
    + 遠端執行預測工作的記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已修正如果前置處理旗標為 True 時，`Timeseries` 的失敗。
    + 讓一些預測資料驗證錯誤訊息更容易採取動作。
    + 藉由卸載和/或資料集的消極式載入來減少 AutoML 執行的記憶體耗用量，特別是在處理常式產生之間
  + **azureml-contrib-說明-模型**
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯
    + Widget 變更：使用 `contrib`自動安裝，不再有全域功能重要性的 `nbextension` 安裝/啟用-支援說明（例如 Permutative）
    + 儀表板變更：除了 [摘要] 頁面上的 `beeswarm` 繪圖之外，也會顯示盒狀圖和 violin 繪圖-`beeswarm` 繪圖的速度更快，rerendering 繪製于 [上-k] 滑杆變更-有用的訊息，說明如何計算前 k 個可自訂的訊息，以取代未提供資料時的圖表
  + **azureml-core**
    + 已新增 Model. package （）方法，以建立可封裝模型和其相依性的 Docker 映射和 Dockerfile。
    + 已更新本機 webservices 以接受包含環境物件的 InferenceConfigs。
    + 已修正模型。當 '. ' 時，register （）產生不正確模型（針對目前目錄）會當做 model_path 參數傳遞。
    + 新增執行。 submit_child，此功能會反映實驗。將執行指定為已提交子回合的父系時，請提交。
    + 支援來自 Model 的設定選項。請在 Run 中註冊。 register_model。
    + 在現有叢集上執行 JAR 作業的能力。
    + 現在支援 instance_pool_id 和 cluster_log_dbfs_path 參數。
    + 已新增在將模型部署至 Webservice 時，使用環境物件的支援。 現在可以提供環境物件做為 InferenceConfig 物件的一部分。
    + 為新區域新增 appinsifht 對應-centralus-westus-northcentralus
    + 已新增所有資料存放區類別中所有屬性的檔。
    + 已將 blob_cache_timeout 參數新增至 `Datastore.register_azure_blob_container`。
    + 已將 save_to_directory 和 load_from_directory 方法新增至 azureml. 環境。
    + 已將「az ml 環境下載」和「az ml 環境 register」命令新增至 CLI。
    + 已新增環境。 add_private_pip_wheel 方法。
  + **azureml-explain-model**
    + 使用資料集服務（預覽）將資料集追蹤新增至說明。
    + 將全域說明從10k 串流至100時，已減少預設批次大小。
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯。
  + **azureml-mlflow**
    + 已修正 mlflow 中的錯誤（bug），這是忽略嵌套目錄的 build_image。
  + **azureml-pipeline-steps**
    + 已新增在現有 Azure Databricks 叢集上執行 JAR 作業的功能。
    + 已新增 DatabricksStep 步驟的支援 instance_pool_id 和 cluster_log_dbfs_path 參數。
    + 已新增 DatabricksStep 步驟中管線參數的支援。
  + **azureml-train-automl**
    + 已新增集團相關檔案的 `docstrings`。
    + 已將檔更新為更適當的 `max_cores_per_iteration` 和 `max_concurrent_iterations` 語言
    + 遠端執行預測工作的記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已從管線 `automlstep` 筆記本移除 get_data。
    + 已開始支援 `automlstep`中的 `dataprep`。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning 資料準備 SDK v 1.1.10

+ **新功能**
  + 您現在可以要求在特定資料行上執行特定的檢查器（例如長條圖、散佈圖等）。
  + 已將平行處理引數新增至 `append_columns`。 若為 True，則會將資料載入記憶體中，但執行將會平行執行;如果為 False，則執行會串流處理，但會進行單一執行緒。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.53

+ **新功能**
  + 自動化 Machine Learning 現在支援在遠端計算目標上定型 ONNX 模型
  + Azure Machine Learning 現在提供從先前的執行、檢查點或模型檔案繼續定型的功能。
    + 瞭解如何[使用估算器從上一次執行繼續定型](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug 修正和改善**
  + **automl-用戶端-核心-nativeclient-android**
    + 修正轉換（bug 連結）之後遺失資料行類型的 bug;
    + 允許 y_query 為在開頭（#459519）包含無（s）的物件類型。
  + **azure-cli-ml**
    + CLI 命令「模型部署」和「服務更新」現在接受參數、設定檔或兩者的組合。 參數的優先順序高於檔案中的屬性。
    + 現在可以在註冊之後更新模型描述
  + **azureml-automl-核心**
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
    + 新增要在 AutoML 估算器中使用之 NimbusML 估算器 & 管線的支援。
    + 修正集團選取程式中的 bug，即使分數保持不變也不必要地增加產生的集團。
    + 啟用跨 CV 分割重複使用某些 featurizations，以進行預測工作。 這可加快執行安裝的執行時間，其方式是 featurizations 延遲和滾動時段等昂貴 n_cross_validations 的。
    + 解決時間超出 pandas 支援的時間範圍時的問題。 如果時間小於 pd，我們現在會引發 DataException。Timestamp. min 或大於 pd。時間戳記。最大值
    + 預測現在會在定型和測試集中允許不同的頻率（如果可以對齊的話）。 例如，「每季從一月開始」和「10月開始的季度」可以對齊。
    + 屬性 "parameters" 已加入至 TimeSeriesTransformer。
    + 移除舊的例外狀況類別。
    + 在預測工作中，`target_lags` 參數現在會接受單一整數值或整數清單。 如果已提供整數，則只會建立一個 lag。 如果提供了清單，將會採用延遲的唯一值。 target_lags = [1，2，2，4] 將建立延遲1、2和4個期間。
    + 修正轉換（bug 連結）之後遺失資料行類型的 bug;
    + 在 `model.forecast(X, y_query)`中，允許 y_query 在開頭（#459519）包含無的物件類型。
    + 將預期的值新增至 `automl` 輸出
  + **azureml-contrib-datadrift**
    +  範例筆記本的改良功能，包括切換至 azureml-opendatasets，而不是 azureml-contrib opendatasets 和效能改善（在充實資料時）
  + **azureml-contrib-說明-模型**
    + Azureml-contrib 中原始功能重要性的淺轉換引數固定說明-說明-模型套件
    + 已針對 AzureML-contrib-說明模型套件，在影像說明中新增 segmentations 至影像說明
    + 新增 LimeExplainer 的 scipy sparse 支援
    + 已新增 `batch_size`，以在 `include_local=False`時模擬說明，以分批串流全域解釋以改善 DecisionTreeExplainableModel 的執行時間
  + **azureml-contrib-featureengineering**
    + 呼叫 set_featurizer_timeseries_params （）的修正： dict 數值型別變更和 null 檢查-為 `timeseries` featurizer 新增筆記本
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能
    + 已修正資料存放區上傳的錯誤（如果 `target_path` 以 `/` 開始建立空的資料夾）
    + 已修正 ServicePrincipalAuthentication 中的 `deepcopy` 問題。
    + 已將「az ml 環境 show」和「az ml 環境 list」命令新增至 CLI。
    + 環境現在支援將 base_dockerfile 指定為已經建立 base_image 的替代方法。
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已淘汰。
    + 現在可以在註冊之後更新模型描述
    + 錯誤修正：模型和映射刪除現在提供有關抓取上游物件的詳細資訊（如果因為上游相依性而刪除失敗）。
    + 已修正為某些環境建立工作區時所發生部署的空白持續時間列印的 bug。
    + 改良的工作區建立失敗例外狀況。 使用者看不到 [無法建立工作區]。 找不到 ...」作為訊息，並改為查看實際的建立失敗。
    + 在 AKS webservices 中新增權杖驗證的支援。
    + 將 `get_token()` 方法加入 `Webservice` 物件。
    + 已新增 CLI 支援來管理機器學習資料集。
    + `Datastore.register_azure_blob_container` 現在可以選擇採用 `blob_cache_timeout` 值（以秒為單位），其會設定 blobfuse 的掛接參數，以啟用此資料存放區的快取到期時間。 預設值為 [沒有超時]，也就是讀取 blob 時，它會保留在本機快取中，直到作業完成為止。 大部分的作業會偏好此設定，但某些作業需要從大型資料集讀取更多資料，而不能容納其節點。 針對這些作業，微調此參數將有助於成功完成。 微調此參數時請注意：設定值過低可能會導致效能不佳，因為 epoch 中使用的資料可能會在再次使用之前過期。 這表示所有讀取作業都是從 blob 儲存體（也就是網路）而不是本機快取完成，這會對定型時間造成負面影響。
    + 現在可以在註冊之後正確地更新模型描述
    + 模型和影像刪除現在提供有關上游物件的詳細資訊，而這些物件會導致刪除失敗
    + 使用 mlflow 來改善遠端執行的資源使用率。
  + **azureml-explain-model**
    + Azureml-contrib 中原始功能重要性的淺轉換引數固定說明-說明-模型套件
    + 新增 LimeExplainer 的 scipy sparse 支援
    + 已新增圖形線性說明包裝函式，以及另一個層級至表格式說明以說明線性模型
    + 如需說明模型程式庫中的模擬說明，請修正當 sparse 資料輸入 include_local = False 時的錯誤
    + 將預期的值新增至 `automl` 輸出
    + 已修正提供轉換引數以取得原始功能重要性時的排列功能重要性
    + 已新增 `batch_size`，以在 `include_local=False`時模擬說明，以分批串流全域解釋以改善 DecisionTreeExplainableModel 的執行時間
    + 針對模型可解釋性程式庫，已修正黑箱 explainers，其中 pandas 資料框架輸入是預測的必要項
    + 修正 `explanation.expected_values` 有時會傳回浮點數，而不是包含浮點數清單的錯誤（bug）。
  + **azureml-mlflow**
    + 改善 mlflow 的效能 set_experiment （experiment_name）
    + 修正使用 InteractiveLoginAuthentication for mlflow tracking_uri 的 bug
    + 使用 mlflow 來改善遠端執行的資源使用率。
    + 改善 azureml mlflow 套件的檔
    + Patch bug，其中 mlflow log_artifacts （"my_dir"）會將成品儲存在 "my_dir/< 成品路徑 >" 之下，而不是 "< 成品路徑 >"
  + **azureml-opendatasets**
    + 將 `opendatasets` 的 `pyarrow` 釘選到舊版本（< 0.14.0），因為新引進的記憶體問題。
    + 將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開啟的資料集類別註冊到 Azure Machine Learning 的工作區，並順暢地利用 AML 資料集功能。
    + 大幅提升非 SPARK 版本的 NoaaIsdWeather 豐富效能。
  + **azureml-pipeline-steps**
    + DatabricksStep 中的輸入和輸出現在支援 DBFS 資料存放區。
    + 已更新有關輸入/輸出之 Azure Batch 步驟的檔。
    + 在」已 azurebatchstep 中，將*delete_batch_job_after_finish*預設值變更為*true*。
  + **azureml-遙測**
    +  將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開啟的資料集類別註冊到 Azure Machine Learning 的工作區，並順暢地利用 AML 資料集功能。
    + 大幅提升非 SPARK 版本的 NoaaIsdWeather 豐富效能。
  + **azureml-train-automl**
    + 已更新 get_output 上的檔，以反映實際的傳回類型，並提供有關抓取索引鍵屬性的其他注意事項。
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
    + 將預期的值新增至 `automl` 輸出
  + **azureml-train-core**
    + 字串現已接受為自動超參數微調的計算目標
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已淘汰。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning 資料準備 SDK v 1.1。9

+ **新功能**
  + 已新增從 HTTP 或 HTTPs url 直接讀取檔案的支援。

+ **Bug 修正和改善**
  + 已改善嘗試從遠端來源（目前不支援）讀取 Parquet 資料集時的錯誤訊息。
  + 修正在 ADLS Gen 2 中寫入至 Parquet 檔案格式，並在路徑中更新 ADLS Gen 2 容器名稱時的錯誤。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>視覺化介面
+ **預覽功能**
  + 已在視覺化介面中新增「執行 R 腳本」模組。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets**現已提供為**azureml-opendatasets**。 舊的封裝仍然可以使用，但建議您使用**azureml-opendatasets**向前邁進，以取得更豐富的功能和改進。
    + 這個新封裝可讓您將開啟的資料集註冊為 Azure Machine Learning 工作區中的資料集，並利用資料集提供的任何功能。
    + 它也包含現有的功能，例如將開啟的資料集當做 Pandas/SPARK 資料框架使用，以及某些資料集（例如氣象）的位置聯結。

+ **預覽功能**
    + HyperDriveConfig 現在可以接受管線物件做為參數，以支援使用管線的超參數微調。

+ **Bug 修正和改善**
  + **azureml-train-automl**
    + 修正在轉換之後遺失資料行類型的 bug。
    + 修正 bug，以允許 y_query 是一開始就包含 None 的物件類型。
    + 已修正集團選取程式中不必要地增加產生的集團的問題，即使分數保持不變也一樣。
    + 已修正 AutoMLStep 中 whitelist_models 和 blacklist_models 設定的問題。
    + 已修正在 Azure ML 管線的內容中使用 AutoML 時，無法使用前置處理的問題。
  + **azureml-opendatasets**
    + 已將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開啟的資料集類別註冊到 Azure Machine Learning 的工作區，並順暢地利用 AML 資料集功能。
    + 大幅改善非 SPARK 版本中的 NoaaIsdWeather 更豐富效能。
  + **azureml-explain-model**
    + 已更新 interpretability 物件的線上檔。
    + 已新增在 `include_local=False`時模擬說明的 `batch_size`，以進行批次的串流全域說明，以改善模型可解釋性程式庫的 DecisionTreeExplainableModel 執行時間。
    + 修正了 `explanation.expected_values` 有時會傳回 float，而不是含有 float 的清單的問題。
    + 已將預期的值新增至說明模型程式庫中模擬說明的 `automl` 輸出。
    + 已修正提供轉換引數以取得原始功能重要性時的排列功能重要性。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能。
    + 已修正資料存放區上傳的問題，如果 `target_path` 以 `/`啟動，就會建立空的資料夾。
    + 已啟用兩個資料集的比較。
    + [模型] 和 [映射刪除] 現在會提供詳細資訊，說明如何在因為上游相依性而導致刪除失敗時，取得相依于它們的上游物件。
    + 已淘汰 auto_prepare_environment 中未使用的 RunConfiguration 設定。
  + **azureml-mlflow**
    + 已改善使用 mlflow 之遠端執行的資源使用率。
    + 已改善 azureml mlflow 套件的檔。
    + 已修正 log_artifacts mlflow （"my_dir"）會將成品儲存在 "my_dir/artifact-paths"，而不是「成品-路徑」的問題。
  + **azureml-pipeline-core**
    + 所有管線步驟的參數 hash_paths 已被取代，未來將會移除。 預設會雜湊 source_directory 的內容（除了 amlignore 或. .gitignore 中列出的檔案以外）
    + 持續改進模組和 ModuleStep 以支援計算類型特定模組，以準備進行 RunConfiguration 整合和其他變更，以在管線中解除鎖定計算類型特定的模組使用方式。
  + **azureml-pipeline-steps**
    + 」已 azurebatchstep：有關輸入/輸出的改良檔。
    + 」已 azurebatchstep：已將 delete_batch_job_after_finish 預設值變更為 true。
  + **azureml-train-core**
    + 字串現已接受為自動超參數微調的計算目標。
    + 已淘汰 auto_prepare_environment 中未使用的 RunConfiguration 設定。
    + 取代的參數 `conda_dependencies_file_path` 和 `pip_requirements_file_path` 分別用於 `conda_dependencies_file` 和 `pip_requirements_file`。
  + **azureml-opendatasets**
    + 大幅提升非 SPARK 版本的 NoaaIsdWeather 豐富效能。

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning 資料準備 SDK v 1.1。8

+ **新功能**
 + 現在可以反復查看資料流程物件，產生一連串的記錄。 請參閱 `Dataflow.to_record_iterator`的檔。
  + 現在可以反復查看資料流程物件，產生一連串的記錄。 請參閱 `Dataflow.to_record_iterator`的檔。

+ **Bug 修正和改善**
 + 提升 DataPrep SDK 的穩定性。
 + 改善 pandas 資料框架與非字串資料行索引的處理。
 + 改善資料集中 `to_pandas_dataframe` 的效能。
 + 修正了在多節點環境中執行時，Spark 執行資料集失敗的錯誤。
  + 提升 DataPrep SDK 的穩定性。
  + 改善 pandas 資料框架與非字串資料行索引的處理。
  + 改善資料集中 `to_pandas_dataframe` 的效能。
  + 修正了在多節點環境中執行時，Spark 執行資料集失敗的錯誤。

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning 資料準備 SDK v 1.1。7

我們已還原改善效能的變更，因為這會導致某些客戶使用 Azure Databricks 的問題。 如果您在 Azure Databricks 上遇到問題，可以使用下列其中一種方法升級至版本1.1.7：
1. 執行此腳本進行升級： `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 重新建立叢集，這將會安裝最新的資料準備 SDK 版本。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.45

+ **新功能**
  + 新增決策樹代理模型以模擬 azureml 中的說明-說明-模型套件
  + 能夠指定要安裝在推斷映射上的 CUDA 版本。 支援 CUDA 9.0、9.1 和10.0。
  + 您現在可以在[AZURE Ml 容器 GitHub 存放庫](https://github.com/Azure/AzureML-Containers)和[DOCKERHUB](https://hub.docker.com/_/microsoft-azureml)取得 azure ml 訓練基底映射的相關資訊
  + 已新增管線排程的 CLI 支援。 執行「az ml 管線-h」以深入瞭解
  + 已將自訂 Kubernetes 命名空間參數新增至 AKS webservice 部署設定和 CLI。
  + 所有管線步驟的 hash_paths 參數已被取代
  + Model。 register 現在支援使用 `child_paths` 參數，將多個個別檔案註冊為單一模型。

+ **預覽功能**
    + 計分 explainers 現在可以選擇性地儲存 conda 和 pip 資訊，以提供更可靠的序列化和還原序列化。
    + 自動功能選取器的錯誤修正。
    + 已更新 build_image mlflow 至新的 api，並修補新的執行所公開的 bug。

+ **Bug 修正和改善**
  + 已從 azureml 核心移除 `paramiko` 相依性。 已新增舊版計算目標附加方法的取代警告。
  + 提升執行效能。 create_children
  + 在 [模擬具有二元分類器的說明] 中，修正當教師機率用於縮放圖形值時的機率順序。
  + 已改善自動化機器學習的錯誤處理和訊息。
  + 已修正自動化機器學習的反復專案超時問題。
  + 已改善自動化機器學習的時間序列轉換效能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning 資料準備 SDK v 1.1。6

+ **新功能**
  + 已新增 top 值（`SummaryFunction.TOPVALUES`）和下值（`SummaryFunction.BOTTOMVALUES`）的摘要函數。

+ **Bug 修正和改善**
  + 大幅提升 `read_pandas_dataframe`的效能。
  + 已修正導致指向二進位檔案的資料流程發生 `get_profile()` 失敗的 bug。
  + 公開 `set_diagnostics_collection()`，以允許以程式設計方式啟用/停用遙測集合。
  + 已變更 `get_profile()`的行為。 現在會忽略 Min、Mean、Std 和 Sum 的 NaN 值，這與 Pandas 的行為一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.43

+ **新功能**
  + Azure Machine Learning 現在提供熱門機器學習和資料分析架構 Scikit-learn 的一流支援。 使用者可以使用[`SKLearn` 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)，輕鬆地訓練和部署 scikit-learn 學習模型。
    + 瞭解如何使用[scikit-learn 執行超參數微調-瞭解如何使用 HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 已新增在管線中建立 ModuleStep 以及模組和 ModuleVersion 類別的支援，以管理可重複使用的計算單位。
  + ACI webservices 現在支援透過更新的持續性 scoring_uri。 Scoring_uri 將從 IP 變更為 FQDN。 您可以藉由在 deploy_configuration 上設定 dns_name_label，來設定 FQDN 的 Dns 名稱標籤。
  + 自動化機器學習服務的新功能：
    + 適用于預測的 STL featurizer
    + 已啟用 Kmeans 叢集以進行功能掃描
  + AmlCompute 配額核准的速度變快了！ 我們現在已自動化在閾值內核准配額要求的程式。 如需有關配額如何工作的詳細資訊，請瞭解[如何管理配額](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)。

+ **預覽功能**
    + 透過 azureml MLflow 套件（[範例筆記本](https://aka.ms/azureml-mlflow-examples)）與[MLflow](https://mlflow.org) 1.0.0 追蹤進行整合。
    + 提交 Jupyter 筆記本作為執行。 [API 參考檔](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 透過 azureml-contrib-datadrift package （[範例筆記本](https://aka.ms/azureml-datadrift-example)）公開預覽[資料漂移](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class))偵測器。 資料漂移是模型精確度隨著時間而下降的其中一個主要原因。 在生產環境中提供模型的資料與定型模型的資料不同時，就會發生此情況。 AML 資料漂移偵測器可協助客戶監視資料漂移，並在偵測到漂移時傳送警示。

+ **重大變更**

+ **Bug 修正和改善**
  + RunConfiguration 載入和儲存支援針對先前的行為指定具有完整回溯相容性的完整檔案路徑。
  + 已在 ServicePrincipalAuthentication 中新增快取，預設為關閉。
  + 以相同的計量名稱來啟用多個繪圖的記錄功能。
  + 模型類別現在已正確地從 azureml. core （`from azureml.core import Model`）匯入。
  + 在管線步驟中，`hash_path` 參數現在已被取代。 新的行為是雜湊完成 source_directory，但 amlignore 或. .gitignore 中列出的檔案除外。
  + 在管線封裝中，不同的 `get_all` 和 `get_all_*` 方法已被取代，分別用來取代 `list` 和 `list_*`。
  + get_run 不再需要匯入類別，再傳回原始的執行類型。
  + 已修正某些 WebService 更新呼叫並未觸發更新的問題。
  + AKS webservices 上的計分超時應該介於5毫秒和300000ms 之間。 評分要求的允許 scoring_timeout_ms 上限已從1分鐘 i 藉為5分鐘。
  + LocalWebservice 物件現在具有 `scoring_uri` 和 `swagger_uri` 屬性。
  + 已移動輸出目錄建立，並從使用者進程輸出目錄上傳。 已啟用執行歷程記錄 SDK，以在每個使用者進程中執行。 這應該會解決分散式定型執行所遇到的一些同步處理問題。
  + 從使用者進程名稱寫入的 azureml 記錄檔名稱現在會包含進程名稱（僅適用于分散式訓練）和 PID。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning 資料準備 SDK v 1.1。5

+ **Bug 修正和改善**
  + 針對具有2位數年份格式的已轉譯日期時間值，有效年份的範圍已更新為符合 Windows 可能發行的版本。 範圍已從1930-2029 變更為1950-2049。
  + 在檔案中讀取並設定 `handleQuotedLineBreaks=True`時，`\r` 會被視為新行。
  + 已修正在某些情況下導致 `read_pandas_dataframe` 失敗的 bug。
  + 改善 `get_profile`的效能。
  + 改善的錯誤訊息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning 資料準備 SDK 1.1。4

+ **新功能**
  + 您現在可以使用下列運算式語言函式，將日期時間值解壓縮並剖析成新的資料行。
    + `RegEx.extract_record()` 將 datetime 元素解壓縮至新的資料行。
    + `create_datetime()` 會從個別的 datetime 元素建立 datetime 物件。
  + 呼叫 `get_profile()`時，您現在可以看到分量資料行標示為（est.），以清楚指出這些值是近似值。
  + 從 Azure Blob 儲存體讀取時，您現在可以使用 * * 萬用字元。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **錯誤修正**
  + 已修正與從遠端來源（Azure Blob）讀取 Parquet 檔案相關的錯誤。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.39
+ **變更**
  + 執行設定 auto_prepare_environment 選項即將淘汰，自動準備變成預設值。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning 資料準備 SDK v 1.1。3

+ **新功能**
  + 已藉由呼叫 read_postgresql 或使用資料存放區，來新增讀取 PostgresSQL 資料庫的支援。
    + 請參閱操作指南中的範例：
      + [資料內嵌筆記本](https://aka.ms/aml-data-prep-ingestion-nb)
      + [資料存放區筆記本](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug 修正和改善**
  + 已修正資料行類型轉換的問題：
  + 現在會正確地將布林值或數值資料行轉換成布林資料行。
  + 當嘗試將日期資料行設定為日期類型時，現在不會失敗。
  + 改良的 JoinType 類型和隨附的參考檔。 聯結兩個數據流時，您現在可以指定其中一種聯結類型：
    + NONE、MATCH、INNER、UNMATCHLEFT、LEFTANTI、LEFTOUTER、UNMATCHRIGHT、RIGHTANTI、RIGHTOUTER、FULLANTI、FULL。
  + 改良的資料類型推斷來辨識更多的日期格式。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

在 Azure 入口網站中，您現在可以：
+ 建立並執行自動化 ML 實驗
+ 建立筆記本 VM，以試用 Jupyter 筆記本或您自己的範例。
+ Azure Machine Learning 工作區中的全新撰寫區段（預覽），其中包括自動化 Machine Learning、視覺化介面和託管的筆記本 Vm
    + 使用自動化機器學習服務自動建立模型
    + 使用拖放視覺介面來執行實驗
    + 建立筆記本 VM 以流覽資料、建立模型及部署服務。
+ [執行報表] 和 [執行詳細資料] 頁面中的即時圖表和計量更新
+ 已更新 [執行詳細資料] 頁面中記錄、輸出和快照集的檔案檢視器。
+ [實驗] 索引標籤中新的和改良的報表建立體驗。
+ 已新增從 [Azure Machine Learning] 工作區的 [總覽] 頁面下載 config.xml 檔案的功能。
+ 支援從 Azure Databricks 工作區建立 Azure Machine Learning 工作區。

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.33
+ **新功能**
  + _工作區。 create_方法現在接受 CPU 和 GPU 叢集的預設叢集設定。
  + 如果工作區建立失敗，則會清除相依的資源。
  + 預設 Azure Container Registry SKU 已切換為 [基本]。
  + 當需要執行或建立映射時，會延遲建立 Azure Container Registry。
  + 支援定型執行的環境。

### <a name="notebook-virtual-machine"></a>筆記本虛擬機器 

使用筆記本 VM 做為適用于 Jupyter 筆記本的安全、符合企業需求的裝載環境，您可以在其中編寫機器學習服務實驗、將模型部署為 web 端點，以及使用 Python Azure Machine Learning SDK 來執行其他所有作業。 它提供數個功能：
+ [快速啟動預先設定的筆記本 VM](tutorial-1st-experiment-sdk-setup.md) ，其中包含最新版的 Azure Machine Learning SDK 和相關套件。
+ 存取是透過經過驗證的技術（例如 HTTPS、Azure Active Directory 驗證和授權）來保護。
+ 在 Azure Machine Learning 工作區 blob 儲存體帳戶中，筆記本和程式碼的可靠雲端儲存體。 您可以安全地刪除您的筆記本 VM，而不會遺失您的工作。
+ 已預先安裝的範例筆記本，可探索及試驗 Azure Machine Learning 功能。
+ Azure Vm 的完整自訂功能、任何 VM 類型、任何封裝、任何驅動程式。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v 1.0.33 已發行。

+ [Fpga](how-to-deploy-fpga-web-service.md)上的 Azure ML 硬體加速模型已正式推出。
  + 您現在可以[使用 azureml-accel 模型套件](how-to-deploy-fpga-web-service.md)來執行下列動作：
    + 訓練支援的深度類神經網路（ResNet 50、ResNet 152、DenseNet-121、VGG-16-16 和 SSD-VGG-16）的權數
    + 搭配支援的 DNN 使用轉移學習
    + 向模型管理服務註冊模型並容器化模型
    + 使用 Azure Kubernetes Service （AKS）叢集中的 FPGA 將模型部署至 Azure VM
  + 將容器部署至[Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)伺服器裝置
  + 使用此[範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)的 gRPC 端點來為您的資料評分

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ 功能優化，可動態新增 :::no-loc text="featurizers"::: 以進行效能優化。 新 :::no-loc text="featurizers":::：工作內嵌、辨識項權數、目標編碼、文字目標編碼、叢集距離
+ 智慧型 CV，可處理自動化 ML 內的定型/有效分割
+ 少量的記憶體優化變更和執行時間效能改進
+ 模型說明中的效能改進
+ 本機執行的 ONNX 模型轉換
+ 新增的次取樣支援
+ 未定義結束準則時的智慧型停止
+ 堆疊整體

+ 時間序列預測
  + 新增預測預測函數
  + 您現在可以在時間序列資料上使用輪流的交叉驗證
  + 已新增新功能來設定時間序列延遲
  + 新增功能以支援滾動視窗匯總功能
  + 在實驗設定中定義國家/地區代碼時的新假日偵測和 featurizer

+ Azure Databricks
  + 啟用時間序列預測和模型 explainabilty/interpretability 功能
  + 您現在可以取消並繼續（繼續）自動化 ML 實驗
  + 已新增多核心處理的支援

### <a name="mlops"></a>MLOps
+ **針對評分容器進行本機部署 & 的偵錯工具**<br/> 您現在可以在本機部署 ML 模型，並快速地逐一查看評分檔案和相依性，以確保其行為如預期般運作。

+ **引進了 InferenceConfig & 模型。 deploy （）**<br/> 模型部署現在支援使用專案腳本來指定源資料夾，這與 RunConfig 相同。  此外，模型部署已簡化為單一命令。

+ **Git 參考追蹤**<br/> 客戶已要求基本的 Git 整合功能一段時間，因為它有助於維護端對端的審核記錄。 我們已針對 Git 相關的中繼資料（存放庫、認可、清除狀態），在 Azure ML 中的主要實體上執行追蹤。 SDK 和 CLI 會自動收集這項資訊。

+ **模型分析 & 驗證服務**<br/> 客戶經常抱怨有困難地調整與推斷服務相關聯的計算大小。 透過我們的模型分析服務，客戶可以提供範例輸入，我們會分析16個不同的 CPU/記憶體設定，以判斷部署的最佳大小。

+ **帶入您自己的基底映射以進行推斷**<br/> 另一個常見的抱怨是，從實驗移至推斷的困難會重新共用相依性。 使用新的基底映射共用功能，您現在可以重複使用您的實驗基底映射、相依性和所有專案來進行推斷。 這應該會加速部署，並減少內部與外部迴圈之間的差距。

+ **改良的 Swagger 架構產生體驗**<br/> 先前的 swagger 產生方法容易發生錯誤，無法自動化。 我們有新的內嵌方法，可透過裝飾專案從任何 Python 函式產生 swagger 架構。 我們有開放原始碼這段程式碼，而我們的架構產生通訊協定並不會與 Azure ML 平臺結合。

+ **Azure ML CLI 已正式推出（GA）**<br/> 現在可以使用單一 CLI 命令來部署模型。 我們有一般客戶的意見反應，不會從 Jupyter 筆記本部署 ML 模型。 [**CLI 參考檔**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v 1.0.30 已發行。

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)的引進會加入新版本的已發行管線，同時維持相同的端點。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 資料準備 SDK v 1.1。2

注意：資料準備 Python SDK 將不再安裝 `numpy` 和 `pandas` 套件。 請參閱[更新的安裝指示](https://aka.ms/aml-data-prep-installation)。

+ **新功能**
  + 您現在可以使用 [資料透視轉換]。
    + 操作指南：[資料透視筆記本](https://aka.ms/aml-data-prep-pivot-nb)
  + 您現在可以在原生函式中使用正則運算式。
    + 範例：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 您現在可以使用運算式語言中的 `to_upper` 和 `to_lower` 函數。
  + 您現在可以看到資料設定檔中每個資料行的唯一值數目。
  + 對於一些常用的讀取器步驟，您現在可以傳入 `infer_column_types` 引數。 如果設定為 [`True`]，則資料準備會嘗試偵測並自動轉換資料行類型。
    + `inference_arguments` 現在已被取代。
  + 您現在可以呼叫 `Dataflow.shape`。

+ **Bug 修正和改善**
  + `keep_columns` 現在接受額外的選擇性引數 `validate_column_exists`，以檢查 `keep_columns` 的結果是否會包含任何資料行。
  + 所有讀取器步驟（從檔案讀取）現在都接受額外的選擇性引數 `verify_exists`。
  + 改善從 pandas 資料框架讀取及取得資料設定檔的效能。
  + 已修正從資料流程切割單一步驟失敗且具有單一索引的 bug。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + 您現在可以在現有的遠端計算叢集上重新提交現有的腳本執行。
  + 您現在可以在 [管線] 索引標籤上，使用新的參數來執行已發佈的管線。
  + [執行詳細資料] 現在支援新的快照集檔案檢視器。 當您提交特定執行時，可以查看目錄的快照集。 您也可以下載已提交的筆記本以開始執行。
  + 您現在可以從 Azure 入口網站取消父系執行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.23

+ **新功能**
  + Azure Machine Learning SDK 現在支援 Python 3.7。
  + Azure Machine Learning DNN 估算器現在提供內建的多版本支援。 例如，`TensorFlow` 估計工具現在接受 `framework_version` 參數，而使用者可以指定 ' 1.10 ' 或 ' 1.12 ' 的版本。 如需目前 SDK 版本所支援的版本清單，請在所要的 framework 類別上呼叫 `get_supported_versions()` （例如，`TensorFlow.get_supported_versions()`）。
  如需最新 SDK 版本所支援的版本清單，請參閱[DNN 估計工具檔](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)\ （英文 \）。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning 資料準備 SDK 1.1.1 版

+ **新功能**
  + 您可以使用 read_ * 轉換來讀取多個資料存放區/資料路徑/DataReference 來源。
  + 您可以在資料行上執行下列作業，以建立新的資料行：除法、floor、模數、乘冪、長度。
  + 資料準備現在是 Azure ML 診斷套件的一部分，而且預設會記錄診斷資訊。
    + 若要關閉此功能，請將此環境變數設定為 true： DISABLE_DPREP_LOGGER

+ **Bug 修正和改善**
  + 已改善常用類別和函式的程式碼檔。
  + 已修正無法讀取 Excel 檔案的 auto_read_file 中的錯誤（bug）。
  + 已新增覆寫 read_pandas_dataframe 中資料夾的選項。
  + 改善 dotnetcore2 相依性安裝的效能，並新增 Fedora 27/28 和 Ubuntu 1804 的支援。
  + 改善從 Azure Blob 讀取的效能。
  + 資料行類型偵測現在支援 Long 類型的資料行。
  + 已修正某些日期值顯示為時間戳記而不是 Python datetime 物件的 bug。
  + 已修正某些類型計數顯示為雙精確度（而不是整數）的 bug。


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.21

+ **新功能**
  + *Create_children*方法可讓您透過單一呼叫，以低延遲的方式建立多個子回合。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning 資料準備 SDK v 1.1。0

+ **重大變更**
  + 資料準備套件的概念已被取代，不再受到支援。 除了在一個封裝中保存多個資料流程，您也可以個別保存資料流程。
    + 操作指南：[開啟和儲存資料流程筆記本](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 資料準備現在可以辨識符合特定語義類型的資料行，並據以進行分割。 目前支援的 STypes 包括：電子郵件地址、地理座標（緯度 & 經度）、IPv4 和 IPv6 位址、美國電話號碼和美國郵遞區號。
    + 操作指南：[語義類型筆記本](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 資料準備現在支援下列作業，從兩個數值資料行產生結果資料行：減去、乘、除和模數。
  + 您可以在資料流程上呼叫 `verify_has_data()`，以檢查資料流程是否會在執行時產生記錄。

+ **Bug 修正和改善**
  + 您現在可以在數值資料行設定檔的長條圖中，指定要使用的 bin 數目。
  + `read_pandas_dataframe` 轉換現在需要資料框架具有字串或位元組類型的資料行名稱。
  + 已修正 `fill_nulls` 轉換中的 bug，如果遺漏資料行，則不會正確填入值。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.18

 + **變更**
   + Azureml-tensorboard 套件會取代 azureml-contrib-tensorboard。
   + 在此版本中，您可以在受控計算叢集（amlcompute）上設定使用者帳戶，同時建立它。 這可以藉由在布建設定中傳遞這些屬性來完成。 您可以在[SDK 參考檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)中找到更多詳細資料。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 資料準備 SDK v 1.0.17

+ **新功能**
  + 現在支援加入兩個數值資料行，以使用運算式語言來產生結果資料行。

+ **Bug 修正和改善**
  + 已改善 random_split 的檔和參數檢查。

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 資料準備 SDK v 1.0.16

+ **Bug 修正**
  + 已修正 API 變更所造成的服務主體驗證問題。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.17

+ **新功能**

  + Azure Machine Learning 現在為熱門的 DNN 架構 Chainer 提供了一流的支援。 使用[`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)類別，使用者可以輕鬆地訓練和部署 Chainer 模型。
    + 瞭解如何[使用 ChainerMN 執行分散式訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 瞭解如何[使用 HyperDrive 搭配 Chainer 執行超參數微調](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning 管線已新增根據資料存放區修改來觸發管線執行的功能。 管線[排程筆記本](https://aka.ms/pl-schedule)會更新以展示這項功能。

+ **Bug 修正和改善**
  + 我們已在 Azure Machine Learning 管線中新增支援，以便在提供給[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)的[RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)上，將 source_directory_data_store 屬性設定為所需的資料存放區（例如 blob 儲存體）。 根據預設，步驟會使用 Azure 檔案儲存體作為備份資料存放區，而當同時執行大量步驟時，可能會遇到節流問題。

### <a name="azure-portal"></a>Azure Portal

+ **新功能**
  + 報表的新拖放資料表編輯器體驗。 使用者可以將資料行從適當的位置拖曳至資料表區域，其中會顯示資料表的預覽。 可以重新排列資料行。
  + 新增記錄檔檢視器
  + [活動] 索引標籤中的實驗執行、計算、模型、映射和部署的連結

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning 資料準備 SDK v 1.0.15

+ **新功能**
  + 資料準備現在支援從資料流程寫入檔案資料流程。 也提供操作檔案資料流程名稱的功能，以建立新的檔案名。
    + 操作指南：使用檔案[資料流程筆記本](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修正和改善**
  + 改善大型資料集上的 t 摘要效能。
  + 資料準備現在支援從資料路徑讀取資料。
  + 一個熱編碼現在適用于布林值和數值資料行。
  + 其他錯誤修正。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>適用於 Python 的 Azure Machine Learning SDK v1.0.15

+ **新功能**
  + Azure Machine Learning 管線已新增」已 azurebatchstep （[筆記本](https://aka.ms/pl-azbatch)）、HyperDriveStep （筆記本）和以時間為基礎的排程功能（[筆記本](https://aka.ms/pl-schedule)）。
  +  DataTranferStep 已更新成可與 Azure SQL Server 和「適用於 PostgreSQL 的 Azure 資料庫」([otebook](https://aka.ms/pl-data-trans)) 搭配運作。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 「資料準備」現在支援使用「資料存放區」從 Azure SQL 資料庫讀取資料。

+ **變更**
  + 改善大型資料上某些作業的記憶體效能。
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**：
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
    + 如果您要執行「az login」，然後再使用 azureml-sdk，SDK 會再一次執行瀏覽器或裝置程式碼記錄檔。 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您正在使用服務主體執行「az login」以進行自動化，建議您使用 _azureml.core.authentication.ServicePrincipalAuthentication_ 類別，因為 azureml-sdk 不會使用 azure CLI 建立的認證狀態。

+ **Bug 修正**：此版本大多包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **錯誤修正**
  + 改善取得資料設定檔的效能。
  + 已修正與錯誤報告相關的輕微錯誤。

### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 全新的拖放圖表報告製作體驗。 使用者可以將資料行或屬性從 well 拖曳至圖表區域，在此區域系統將根據資料類型自動為使用者選取適當的圖表類型。 使用者可以將圖表類型變更為其他適用的類型，或新增其他屬性。

    支援的圖表類型：
    - 折線圖
    - 長條圖
    - 堆疊橫條圖
    - 盒狀圖
    - 散佈圖
    - 泡泡圖
+ 入口網站現在會動態地產生實驗報告。 當使用者將回合提交給實驗後，具有計量和圖表記錄的報告就會自動產生，以便您比較每個不同的回合。

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK for Python v1.0.8

+ **Bug 修正**：此版本大多包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **新功能**
  + 資料存放區改善 (記載於[資料存放區操作指南](https://aka.ms/aml-data-prep-datastore-nb))
    + 已新增以相應增加方式從中讀取和寫入至 Azure 檔案共用和 ADLS 資料存放區的功能。
    + 使用資料存放區時，Data Prep 現在支援使用服務主體驗證，而非互動式驗證。
    + 已新增 wasb 和 wasbs url 的支援。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning 資料準備 SDK v1.0.6

+ **錯誤修正**
  + 已修正與從 Spark 上公開可讀取的 Azure Blob 容器讀取相關的 Bug

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **Bug 修正**：此版本大多包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **新功能**
  + `to_bool` 函式現在允許將不相符的值轉換成 Error 值。 這是 `to_bool` 和 `set_column_types` 新的預設不相符行為，先前的預設行為會將不相符的值轉換為 False。
  + 在呼叫 `to_pandas_dataframe` 時，有新的選項可將數字資料行中的 null/遺失值解譯為 NaN。
  + 新增了功能，會檢查某些運算式的傳回類型，以確保類型一致性，並及早失敗。
  + 您現在可以呼叫 `parse_json`，以將資料行中的值剖析為 JSON 物件，並將其展開成多個資料行。

+ **錯誤修正**
  + 已修正錯誤，該錯誤會讓 Python 3.5.2 中的 `set_column_types` 損毀。
  + 已修正會在使用 AML 映像連線至資料存放區時當機的錯誤。

+ **更新**
  * 使用者入門教學課程、案例研究和操作指南的[範例 Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04：正式推出

Azure Machine Learning 現在已正式推出。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
在此版本中，我們透過 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 來宣布新的受控計算體驗。 這個計算目標會取代適用於 Azure Machine Learning 的 Azure Batch AI 計算。

這個計算目標：
+ 用於模型定型和批次推斷/計分
+ 是單一對多個節點的計算
+ 會為使用者執行叢集管理和作業排程
+ 會依預設自動調整
+ 同時支援 CPU 和 GPU 資源
+ 可讓您使用低優先順序的 VM 來降低成本

您可以在 Python 中，使用 Azure 入口網站或 CLI 建立 Azure Machine Learning Compute。 它必須建立於您工作區所在的區域，而且無法附加至任何其他工作區。 這個計算目標會針對您的回合使用 Docker 容器，並封裝您的相依性，以便在您的所有節點上複寫相同的環境。

> [!Warning]
> 我們建議您建立新的工作區來使用 Azure Machine Learning Compute。 使用者嘗試從現有的工作區建立 Azure Machine Learning Compute 可能會看到錯誤，但機率很低。 您工作區中現有的計算應能持續運作而不會受到影響。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **重大變更**
  + 在此版本中，我們會移除從 Azure Machine Learning 中建立 VM 的支援。 您仍然可以附加現有的雲端 VM 或遠端內部部署伺服器。
  + 我們也會移除對 BatchAI 的支援，這些功能現在全都應透過 Azure Machine Learning Compute 來支援。

+ **新增**
  + 針對機器學習管線：
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py) \(英文\)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py) \(英文\)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py) \(英文\)


+ **已更新**
  + 針對機器學習管線：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) \(英文\) 現在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) \(英文\) 現在可從 SQL 資料來源複製或複製到其中
    + 排程 SDK 中的功能，以建立和更新執行已發佈管線的排程

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning 資料準備 SDK v0.5.2
+ **重大變更**
  * `SummaryFunction.N` 已重新命名為 `SummaryFunction.Count`。

+ **錯誤修正**
  * 從遠端執行讀取和寫入資料存放區時，請使用最新的 AML 執行權杖。 以前，如果在 Python 中更新 AML 執行權杖，將不會使用已更新的 AML 執行權杖來更新資料準備執行階段。
  * 其他更清楚的錯誤訊息
  * 當 Spark 使用 `Kryo` 序列化時，to_spark_dataframe() 將不再損毀
  * 值計數偵測器現在可以顯示 1000 個以上的唯一值
  * 如果原始的資料流程沒有名稱，隨機分割就不再失敗

+ **詳細資訊**
  * [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文件和 Notebook
+ ML 管線
  + 適用於開始使用管線、批次範圍和樣式移轉範例之全新和更新的 Notebook： https://aka.ms/aml-pipeline-notebooks
  + 了解如何[建立您的第一個管線](how-to-create-your-first-pipeline.md)
  + 了解如何[使用管線執行批次預測](how-to-use-parallel-run-step.md)
+ Azure Machine Learning 計算目標
  + [範例 Notebook](https://aka.ms/aml-notebooks) 現在已更新為使用新的受控計算。
  + [了解這個計算](how-to-set-up-training-targets.md#amlcompute)。

### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 在入口網站中建立和管理 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 類型。
+ 監視 Azure Machine Learning Compute 的配額使用量和[要求配額](how-to-manage-quotas.md)。
+ 即時檢視 Azure Machine Learning Compute 叢集狀態。
+ 已新增對於建立 Azure Machine Learning Compute 和 Azure Kubernetes Service 的虛擬網路支援。
+ 使用現有的參數，重新執行您已發佈的管線。
+ 新的[自動化機器學習圖表](how-to-understand-automated-ml.md)，適用於分類模型 (升力、增益、校正、具備模型說明能力的特徵重要性圖表) 和迴歸模型 (殘差，以及具備模型說明能力的特徵重要性圖表)。
+ 您可以在 Azure 入口網站中檢視管線。




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大變更**
  * azureml.train.widgets 命名空間已移至 azureml.widgets。
  * azureml.core.compute.AmlCompute 取代了下列類別 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1

若要深入了解 Data Prep SDK，請閱讀[參考文件](https://aka.ms/data-prep-sdk)。
+ **新功能**
   * 建立新的 DataPrep CLI 來執行 DataPrep 套件，並檢視資料集或資料流程的資料設定檔
   * 重新設計 SetColumnType API 以改善可用性
   * 將 smart_read_file 重新命名為 auto_read_file
   * 資料設定檔現在包含偏度和峰度
   * 可透過分層取樣來取樣
   * 可從包含 CSV 檔案的 zip 檔案讀取
   * 可使用隨機劃分來分割資料列取向的資料集 (例如，測試-訓練集)
   * 可以藉由呼叫 `.dtypes` 從資料流程或資料設定檔取得所有資料行的資料類型
   * 可以藉由呼叫 `.row_count` 從資料流程或資料設定檔取得資料列計數

+ **錯誤修正**
   * 修正長時間的雙重轉換
   * 修正新增任何資料行之後的判斷提示
   * 修正 FuzzyGrouping 在某些情況下無法偵測到群組的問題
   * 修正排序函式以遵循多欄排列順序
   * 將 and/or 運算式修正為類似於 `pandas` 處理它們的方式
   * 修正從 dbfs 路徑讀取的方式
   * 讓錯誤訊息變得更容易了解
   * 現在，使用 AML 權杖在遠端計算目標上讀取時，不會再失敗
   * Linux DSVM 現在不會再有失敗狀況
   * 現在字串述詞中有非字串值時不會再發生損毀
   * 現在能在資料流程發生正常的失敗時，處理判斷提示錯誤
   * 現在支援 Azure Databricks 上掛接 dbutils 的儲存位置

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
Azure Machine Learning 的 Azure 入口網站具有下列更新：
  * 用於已發佈管線的新 [管線] 索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更**
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets() 取代為 Workspace.compute_targets。
  * *Run.get_context* 取代 *Run.get_submitted_run*。 第二個方法將會在後續版本中移除。
  * *PipelineData* 類別現在預期將 datastore 物件而不是 datastore_name 當作參數。 同樣地，*管線* 接受 default_datastore，而不是 default_datastore_name。

+ **新功能**
  * Azure Machine Learning 管線[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) 現在使用 MPI 步驟。
  * Jupyter Notebooks 的 RunDetails 小工具已更新以顯示管線的視覺效果。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0

+ **新功能**
  * 類型計數已新增至資料設定檔
  * 值計數與長條圖現在可以使用
  * 資料設定檔中有更多百分位數
  * 中間值可以在 Summarize 中使用
  * 現在支援 Python 3.7
  * 當您將包含資料存放區的資料流程儲存至 DataPrep 套件時，資料存放區資訊將保存為 DataPrep 套件的一部分
  * 現在支援寫入至資料存放區

+ **已修正的 Bug**
  * 64 位元不帶正負號的整數溢位現在會在 Linux 上正確處理
  * 已在 smart_read 中修正純文字檔案不正確的文字標籤
  * 字串資料行類型現在會出現在 [計量] 檢視中
  * 類型計數現在已修正，可顯示對應至單一 FieldType，而不是個別項目的 ValueKinds
  * 當路徑以字串方式提供時，Write_to_csv 不會再失敗
  * 使用 Replace 時，將 “find” 留空將不會再失敗

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新功能**
  * 建立新工作區時的多個租用戶支援。

+ **已修正的 BUG**
  * 在部署 Web 服務時，您不再需要釘選 pynacl 程式庫版本。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning 資料準備 SDK v0.3.0

+ **新功能**
  * 已新增 transform_partition_with_file(script_path) 方法，可讓使用者傳入要執行的 Python 檔案路徑

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[版本 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) 包含新功能、更多文件、錯誤修正和更多 [Notebook 範例](https://aka.ms/aml-notebooks)。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

+ **重大變更**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images、Workspace.web_services 會傳回字典，先前傳回清單。 請參閱 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 文件。

  * 自動化 Machine Learning 已將正規化均方誤差從主要計量中移除。

+ **HyperDrive**
  * 各種貝氏的 HyperDrive bug 修正，以及取得計量呼叫的效能提升。
  * Tensorflow 從 1.9 升級至 1.10
  * 針對冷啟動的 Docker 映像最佳化。
  * 作業現在會報告正確的狀態，即使是以非 0 的錯誤碼結束。
  * SDK 中的 RunConfig 屬性驗證。
  * HyperDrive 執行物件支援類似於一般執行的取消作業：不需要傳遞任何參數。
  * 改善小工具以維護分散式執行和 HyperDrive 執行的下拉式清單值狀態。
  * 已修正參數伺服器的 TensorBoard 和其他記錄檔支援。
  * 服務端上的 Intel(R) MPI 支援。
  * 修正 BatchAI 驗證期間散發執行修正的參數微調錯誤。
  * 內容管理員現在會識別主要執行個體。

+ **Azure 入口網站體驗**
  * 執行詳細資料中支援 log_table() 和 log_row()。
  * 自動建立有 1、2 或 3 個數值資料行和選擇性類別資料行的資料表和資料列圖形。

+ **自動化 Machine Learning**
  * 改良錯誤處理和文件
  * 已修正 run 屬性擷取的效能問題。
  * 已修正繼續執行的問題。
  * 已修正 :::no-loc text="ensembling"::: 反復專案問題。
  * 已修正 MAC OS 上的訓練懸置。
  * 對自訂驗證案例中的巨集平均 PR/ROC 曲線縮小取樣。
  * 已移除額外的索引邏輯。
  * 已從 get_output API 中移除篩選條件。

+ **管線**
  * 已新增 Pipeline.publish() 方法來直接發佈管線，而不需要先啟動執行。
  * 已新增 PipelineRun.get_pipeline_runs() 方法來擷取從所發佈管線產生的管線執行。

+ **Project Brainwave**
  * 已更新 FPGA 上可用的新 AI 模型支援。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning 資料準備 SDK v0.2.0
[版本 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) 包含下列功能和錯誤修正：

+ **新功能**
  * One-hot 編碼的支援
  * 分量轉換的支援

+ **已修正的 Bug：**
  * 適用於任何 Tornado 版本，不必降級 Tornado 版本
  * 值計數代表所有值，不只是前三個值

## <a name="2018-09-public-preview-refresh"></a>2018-09 (公開預覽刷新版)

Azure Machine Learning 的新重新整理版本：深入瞭解此版本： https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning](overview-what-is-azure-ml.md) 概觀。
