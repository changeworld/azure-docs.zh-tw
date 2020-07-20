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
ms.date: 03/10/2020
ms.openlocfilehash: 4f1e8602c55329f88460921446ebaa05ed640e39
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041756"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning 版本資訊

在本文中，您將瞭解 Azure Machine Learning 版本。  如需完整的 SDK 參考內容，請造訪 Azure Machine Learning 的[**Python 的主要 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)參考頁面。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>適用于 Python 的 Azure Machine Learning SDK 1.9。0

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 以 AutoML 自動產生的評分腳本中的 AZUREML_MODEL_DIR 環境變數取代 get_model_path （）。 也新增了遙測來追蹤 init （）期間的失敗。
    + 已移除在 AutoMLConfig 中指定的能力 `enable_cache`
    + 已修正在特定預測執行期間，執行可能會因服務錯誤而失敗的 bug
    + 已改善在期間針對特定模型的錯誤處理`get_output`
    + 已針對使用 y 轉換器的分類，修正呼叫 fitted_model. 配合（X，y）
    + 已啟用預測工作的自訂向前填滿 imputer
    + 將使用新的 ForecastingParameters 類別，而不是以 dict 格式預測參數
    + 改善的目標延遲自動偵測
    + 新增多個 noded、多 gpu 分散式特徵化與經理 BERT 的有限可用性
  + **azureml-automl-runtime**
    + Prophet 現在會執行加法季節性模型，而不是乘法。
    + 修正了 short 粒紋時的問題，其頻率與長粒紋的不同，會導致執行失敗。
  + **azureml-contrib-automl-dnn-願景**
    + 收集用於定型和評分的系統/gpu 統計資料和記錄平均
  + **azureml-contrib-mir**
    + 已新增 ManagedInferencing 中的啟用-應用程式深入解析旗標支援
  + **azureml-core**
    + 當目前的計算無法存取資料來源時，會允許略過驗證，以驗證這些 API 的參數。
      + TabularDataset. time_before （end_time，include_boundary = True，validate = True）
      + TabularDataset. time_after （start_time，include_boundary = True，validate = True）
      + TabularDataset. time_recent （time_delta，include_boundary = True，validate = True）
      + TabularDataset. time_between （start_time，end_time，include_boundary = True，validate = True）
    + 新增模型清單的架構篩選支援，以及在筆記本中新增 NCD automl 範例
    + 針對資料存放區. register_azure_blob_container 和資料存放區。 register_azure_file_share （只有支援 SAS 權杖的選項），我們已更新欄位的檔字串， `sas_token` 以包含一般讀取和寫入案例的最低許可權需求。
    + 淘汰中的 _with_auth param get_mlflow_tracking_uri （）
  + **azureml-mlflow**
    + 新增使用 AzureML 部署本機 file://模型的支援-MLflow
    + 淘汰中的 _with_auth param get_mlflow_tracking_uri （）
  + **azureml-opendatasets**
    + 最近發佈的 Covid-19 追蹤資料集現可透過 SDK 取得
  + **azureml-pipeline-core**
    + 在 pip 相依性中未包含「azureml-預設值」時登出警告
    + 改善便箋轉譯。
    + 已新增在將分隔的檔案剖析為 PipelineOutputFileDataset 時，所加上的引號換行支援。
    + PipelineDataset 類別已被取代。 如需詳細資訊，請參閱 https://aka.ms/dataset-deprecation 。 若要瞭解如何搭配使用資料集與管線，請參閱 https://aka.ms/pipeline-with-dataset 。
  + **azureml-pipeline-steps**
    + 檔更新至 azureml-管線-步驟。
    +  新增 ParallelRunConfig 的支援， `load_yaml()` 讓使用者定義以其他設定或個別檔案內嵌的環境
  + **azureml-訓練-automl-用戶端**。
    + 已移除在 AutoMLConfig 中指定的能力 `enable_cache`
  + **azureml-train-automl-runtime**
    + 已新增具有經理 BERT 的多 noded 多 gpu 分散式特徵化的有限可用性。
    + 在以 ADB 為基礎的自動化機器學習執行中，新增了不相容套件的錯誤處理。
  + **azureml-widget**
    + 檔更新 azureml-widget。

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>適用于 Python 的 Azure Machine Learning SDK 1.8。0
  
  + **預覽功能**
    + **azureml-contrib-公平**此 `azureml-contrib-fairness` 套件提供開放原始碼公平評估和 unfairness 緩和套件[Fairlearn](https://fairlearn.github.io)和 Azure Machine Learning studio 之間的整合。 特別是，封裝可讓模型公平評估儀表板在 AzureML 執行過程中上傳，並出現在 Azure Machine Learning studio 中

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 支援取得 init 容器的記錄檔。
    + 已新增 CLI 命令以管理 ComputeInstance
  + **azureml-automl-core**
    + 使用者現在可以針對時間序列工作啟用堆疊集團反復專案，但可能會有可能過度學習的警告。
    + 已新增新的使用者例外狀況，如果快取 `azureml.automl.core.shared.exceptions.CacheStoreCorruptedException` 存放區內容已遭修改，則會引發此類型
  + **azureml-automl-runtime**
    + 如果使用者停用特徵化，則不會再啟用 [類別平衡]。  
  + **azureml-contrib-itp**
    + 支援 CmAks 計算類型。 您可以將自己的 AKS 叢集附加至工作區，以進行定型作業。
  + **azureml-contrib-notebook**
    + Contrib-筆記本套件的檔改良功能。
  + **azureml-contrib-pipeline-steps**
    + Contrib--管線-步驟套件的檔改進。
  + **azureml-core**
    + 新增 set_connection、get_connection、list_connections、delete_connection 函式，讓客戶在工作區連線資源上運作
    + Azureml-coore/azureml. 例外狀況套件的檔更新。
    + Azureml 核心套件的檔更新。
    + ComputeInstance 類別的檔更新。
    + Azureml-core/azureml 套件的檔改善。
    + 適用于 azureml 核心中 webservice 相關類別的檔改良功能。
    + 支援使用者選取的資料存放區來儲存分析資料
    + 已新增模型清單 API 的展開和 page_count 屬性
    + 已修正移除覆寫屬性會導致提交的執行因還原序列化錯誤而失敗的 bug。
    + 已修正下載或裝載參考單一檔案的 FileDataset 時，不一致的資料夾結構。
    + To_spark_dataframe 載入 parquet 檔案的資料集現在的速度更快，而且支援所有的 parquet 和 Spark SQL 資料類型。
    + 支援取得 init 容器的記錄檔。
    + AutoML 回合現在會標示為執行平行執行步驟的子回合。
  + **azureml-datadrift**
    + Contrib-筆記本套件的檔改良功能。
  + **azureml-dataprep**
    + To_spark_dataframe 載入 parquet 檔案的資料集現在的速度更快，而且支援所有的 parquet 和 Spark SQL 資料類型。
    + To_pandas_dataframe 的 OutOfMemory 問題時，會有更佳的記憶體處理。
  + **azureml-interpret**
    + 已升級的 azureml-解讀以使用解讀-社區0.12 版。 *
  + **azureml-mlflow**
    + Mlflow 的檔改善。
    + 使用 MLFlow 新增 AML 模型登錄的支援。
  + **azureml-opendatasets**
    + 新增了 Python 3.8 的支援
  + **azureml-pipeline-core**
    + 已更新 `PipelineDataset` 檔，讓它清楚是內部類別。
    + ParallelRunStep 更新以接受一個引數的多個值，例如： "--group_column_names"、"Col1"、"Col2"、"Col3"
    + 已移除管線中 AutoMLStep 之中繼資料使用的 passthru_automl_config 需求。
  + **azureml-pipeline-steps**
    + 檔對 azureml-管線-步驟套件的改善。
    + 已移除管線中 AutoMLStep 之中繼資料使用的 passthru_automl_config 需求。
  + **azureml-telemetry**
    + 對 azureml-遙測的檔改善。
  + **azureml-train-automl-client**
    + 修正在 `experiment.submit()` 物件上呼叫兩次，而 `AutoMLConfig` 導致不同行為的 bug。
    + 使用者現在可以針對時間序列工作啟用堆疊集團反復專案，但可能會有可能過度學習的警告。
    + 已變更 AutoML 執行行為，以在服務擲回使用者錯誤時引發 UserErrorException
    + 修正在遠端計算目標上執行 AutoML 實驗時，導致不會產生 azureml_automl .log 或遺失記錄檔的 bug。
    + 針對具有不平衡類別的分類資料集，我們會套用權數平衡，如果功能 sweeper 判斷子取樣資料的量，則權數平衡會依照特定閾值來改善分類工作的效能。
    + AutoML 回合現在會標示為執行平行執行步驟的子回合。
  + **azureml-train-automl-runtime**
    + 已變更 AutoML 執行行為，以在服務擲回使用者錯誤時引發 UserErrorException
    + AutoML 回合現在會標示為執行平行執行步驟的子回合。

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>適用于 Python 的 Azure Machine Learning SDK 1.7。0

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 藉由清除 cli 命令和封裝相依性，完成從 mir contrib 移除模型分析的作業，可在核心中取得模型分析。
    + 將最小 Azure Cli 版本升級至2.3。0
  + **azureml-automl-core**
    + 由於自訂的轉換器參數，特徵化步驟 fit_transform （）的例外狀況訊息較佳。
    + 針對深度學習轉換器模型（例如自動化 ML 中的經理 BERT），新增多個語言的支援。
    + 從檔中移除已淘汰的 lag_length 參數。
    + 已改善預測參數檔。 Lag_length 參數已被取代。
  + **azureml-automl-runtime**
    + 修正了當預測/測試時間中的其中一個類別資料行是空的時，所引發的錯誤。
    + 修正當啟用回顧功能且資料包含簡短粒紋時，所發生的執行失敗。
    + 已修正當延遲或滾動視窗設定為「自動」時，出現重複時間索引錯誤訊息的問題。
    + 已修正資料集上 Prophet 和 Arima 模型的問題，其中包含回顧功能。
    + 已在預測工作的日期時間以外的其他資料行中，新增1677-09-21 或2262-04-11 之後的日期支援。 改善的錯誤訊息。
    + 已改善預測參數檔。 Lag_length 參數已被取代。
    + 由於自訂的轉換器參數，特徵化步驟 fit_transform （）的例外狀況訊息較佳。
    + 針對深度學習轉換器模型（例如自動化 ML 中的經理 BERT），新增多個語言的支援。
    + 導致某些 OSErrors 的快取作業會引發使用者錯誤。
    + 已新增檢查，以確保定型和驗證資料具有相同的數目和資料行集合
    + 已修正當資料包含引號時，自動產生的 AutoML 計分腳本的問題
    + 啟用包含 Prophet 模型之 AutoML Prophet 和 ensembled 模型的說明。
    + 最近的客戶問題顯示了即時網站錯誤，讓我們將訊息沿著類別平衡來記錄，即使類別平衡邏輯未正確啟用也是一樣。 使用此 PR 移除這些記錄/訊息。
  + **azureml-cli-通用**
    + 藉由清除 cli 命令和封裝相依性，完成從 mir contrib 移除模型分析的作業，可在核心中取得模型分析。
  + **azureml-contrib-reinforcementlearning**
    + 負載測試工具
  + **azureml-core**
    + Script_run_config 上的檔變更。 .py
    + 修正列印執行提交-管線 CLI 的輸出時的 bug
    + Azureml-core/azureml 的檔改進。資料
    + 修正使用 hdfs getconf 命令來抓取儲存體帳戶的問題
    + 改良的 register_azure_blob_container 和 register_azure_file_share 檔
  + **azureml-datadrift**
    + 已改善停用和啟用資料集漂移監視器的執行
  + **azureml-interpret**
    + 在說明用戶端中，從成品上傳之前，移除 json 序列化之前的 Nan 或 Inf
    + 更新至最新版本的解讀-社區，以改善具有許多功能和類別之全域說明的記憶體不足錯誤
    + 新增 true_ys 選擇性參數來說明上傳，以在 studio UI 中啟用其他功能
    + 改善 download_model_explanations （）和 list_model_explanations （）效能
    + 對筆記本進行小型調整，以協助進行調試
  + **azureml-opendatasets**
    + azureml-opendatasets 需要 dataprep 版或更高版本的1.4.0。 已在偵測到較低版本時新增警告
  + **azureml-pipeline-core**
    + 這種變更可讓使用者在呼叫模組時，為 moduleVersion 提供選擇性的 runconfig。Publish_python_script。
    + [啟用節點帳戶] 可以是 azureml 中 ParallelRunStep 的管線參數。步驟
  + **azureml-pipeline-steps**
    + 這種變更可讓使用者在呼叫模組時，為 moduleVersion 提供選擇性的 runconfig。Publish_python_script。
  + **azureml-train-automl-client**
    + 針對深度學習轉換器模型（例如自動化 ML 中的經理 BERT），新增多個語言的支援。
    + 從檔中移除已淘汰的 lag_length 參數。
    + 已改善預測參數檔。 Lag_length 參數已被取代。
  + **azureml-train-automl-runtime**
    + 啟用包含 Prophet 模型之 AutoML Prophet 和 ensembled 模型的說明。
    + Automl-* 套件的檔更新。
  + **azureml-train-core**
    + 支援 PyTorch 估計工具中的 TensorFlow 2.1 版
    + Azureml-訓練核心套件的改善。
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>適用于 Python 的 Azure Machine Learning SDK 1.6。0

+ **新功能**
  + **azureml-automl-runtime**
    + AutoML 預測現在支援客戶預測超過預先指定的最大範圍，而不需要重新定型模型。 當預測目的地比指定的最大範圍更遠時，預測（）函數仍然會使用遞迴作業模式，將點預測到之後的日期。 如需新功能的圖例，請參閱「預測-預測-函式」筆記本在[資料夾](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)中的「預測距離最大水準」一節。
  
  + **azureml-pipeline-steps**
    + ParallelRunStep 現在已發行，而且屬於**azureml-管線-步驟**套件的一部分。 Azureml 中現有的 ParallelRunStep- **contrib-管線-步驟**套件已被取代。 公開預覽版本的變更：
      + 已新增 `run_max_try` 選擇性的可設定參數，以控制任何指定批次的最大呼叫執行方法，預設值為3。
      + 不會再自動產生 Pipelineparameters.json。 下列可設定的值可以明確地設為 PipelineParameter。
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Process_count_per_node 的預設值變更為1。 使用者應該調整此值以獲得更好的效能。 最佳做法是將設定為 GPU 或 CPU 節點的數目。
      + ParallelRunStep 不會插入任何套件，使用者必須在環境定義中包含**azureml 核心**和**azureml dataprep [pandas，熔斷器]** 套件。 如果使用自訂 docker 映射搭配 user_managed_dependencies 則使用者需要在映射上安裝 conda。
      
+ **重大變更**
  + **azureml-pipeline-steps**
    + 已淘汰使用 iris.dprep 做為 AutoMLConfig 的有效輸入類型
  + **azureml-train-automl-client**
    + 已淘汰使用 iris.dprep 做為 AutoMLConfig 的有效輸入類型

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 修正在 `get_output` 要求使用者降級用戶端期間可能會列印警告的 bug。
    + 已更新 Mac 以依賴 cudatoolkit = 9.0，因為它在版本10尚無法使用。
    + 在遠端計算上定型時，移除 phrophet 和 xgboost 模型的限制。
    + 改善 AutoML 中的記錄
    + 已改善預測工作中自訂特徵化的錯誤處理。
    + 已新增功能，可讓使用者包含落後功能來產生預測。
    + 錯誤訊息的更新，以正確顯示使用者錯誤。
    + 支援搭配 training_data 使用 cv_split_column_names
    + 更新記錄例外狀況訊息和追溯。
  + **azureml-automl-runtime**
    + 啟用護欄以預測遺漏值 imputations。
    + 改善 AutoML 中的記錄
    + 已針對資料準備例外狀況新增更細緻的錯誤處理
    + 在遠端計算上定型時，移除 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime`和 `azureml-automl-runtime` 已更新、和的相依性 `pytorch` `scipy` `cudatoolkit` 。 我們現在支援 `pytorch==1.4.0` 、 `scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243` 。
    + 已改善預測工作中自訂特徵化的錯誤處理。
    + 已改善預測資料集頻率偵測機制。
    + 已修正在某些資料集上進行 Prophet 模型定型的問題。
    + 已改善預測期間的最大水準自動偵測。
    + 已新增功能，可讓使用者包含落後功能來產生預測。
    +  新增預測函數中的功能，讓您能夠在定型的範圍以外提供預測，而不需要重新定型預測模型。
    + 支援搭配 training_data 使用 cv_split_column_names
  + **azureml-contrib-automl-dnn-預測**
    + 改善 AutoML 中的記錄
  + **azureml-contrib-mir**
    + 已在 ManagedInferencing 中新增對 Windows 服務的支援
    + 移除舊的 MIR 工作流程，例如附加 MIR 計算、SingleModelMirWebservice 類別-清除放在 contrib-MIR 套件中的模型分析
  + **azureml-contrib-pipeline-steps**
    + YAML 支援的次要修正
    + ParallelRunStep 已發行至公開上市 contrib。步驟有淘汰通知，並移至 azureml. 管線。步驟
  + **azureml-contrib-reinforcementlearning**
    + RL 負載測試工具
    + RL 估計工具具有智慧型預設值
  + **azureml-core**
    + 移除舊的 MIR 工作流程，例如附加 MIR 計算、SingleModelMirWebservice 類別-清除放在 contrib-MIR 套件中的模型分析
    + 修正分析失敗時提供給使用者的資訊：包含的要求識別碼，並改寫要更有意義的訊息。 已將新的分析工作流程新增至分析處理常式
    + 大幅改善資料集執行失敗時的錯誤文字。
    + 已新增工作區私用連結 CLI 支援。
    + 已將選擇性參數新增 `invalid_lines` 至 `Dataset.Tabular.from_json_lines_files` ，以允許指定如何處理包含無效 JSON 的程式程式碼。
    + 我們將在下一版中淘汰計算的執行式建立。 我們建議您建立實際的 Amlcompute 叢集做為持續性計算目標，並使用叢集名稱作為執行設定中的計算目標。 請參閱這裡的範例筆記本： aka.ms/amlcomputenb
    + 大幅改善了資料集執行失敗時的錯誤訊息。
  + **azureml-dataprep**
    + 已發出警告，以更明確地升級 pyarrow 版本。
    + 已改善錯誤處理和傳回的訊息，以因應執行資料流程失敗的情況。
  + **azureml-interpret**
    + Azureml-解讀套件的檔更新。
    + 已修正 interpretability 套件和筆記本，以與最新的 sklearn 更新相容
  + **azureml-opendatasets**
    + 沒有傳回任何資料時，就會傳回 None。
    + 改善 to_pandas_dataframe 的效能。
  + **azureml-pipeline-core**
    + 從 YAML 載入的 ParallelRunStep 快速修正程式已中斷
    + ParallelRunStep 已發行至公開上市 contrib。步驟有淘汰通知，並移至 azureml。步驟-新功能包括：1。 資料集，PipelineParameter 為2。 Run_max_retry 3 的新參數。 可設定的 append_row 輸出檔名稱
  + **azureml-pipeline-steps**
    + 已取代的 iris.dprep 作為輸入資料的有效類型。
    + 從 YAML 載入的 ParallelRunStep 快速修正程式已中斷
    + ParallelRunStep 已發行至公開上市 contrib。步驟有淘汰通知，並移至 azureml。步驟-新功能包括：
      + 資料集 PipelineParameter
      + 新參數 run_max_retry
      + 可設定的 append_row 輸出檔名稱
  + **azureml-telemetry**
    + 更新記錄例外狀況訊息和追溯。
  + **azureml-train-automl-client**
    + 改善 AutoML 中的記錄
    + 錯誤訊息的更新，以正確顯示使用者錯誤。
    + 支援搭配 training_data 使用 cv_split_column_names
    + 已取代的 iris.dprep 作為輸入資料的有效類型。
    + 已更新 Mac 以依賴 cudatoolkit = 9.0，因為它在版本10尚無法使用。
    + 在遠端計算上定型時，移除 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime`和 `azureml-automl-runtime` 已更新、和的相依性 `pytorch` `scipy` `cudatoolkit` 。 我們現在支援 `pytorch==1.4.0` 、 `scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243` 。
    + 已新增功能，可讓使用者包含落後功能來產生預測。
  + **azureml-train-automl-runtime**
    + 改善 AutoML 中的記錄
    + 已針對資料準備例外狀況新增更細緻的錯誤處理
    + 在遠端計算上定型時，移除 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime`和 `azureml-automl-runtime` 已更新、和的相依性 `pytorch` `scipy` `cudatoolkit` 。 我們現在支援 `pytorch==1.4.0` 、 `scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243` 。
    + 錯誤訊息的更新，以正確顯示使用者錯誤。
    + 支援搭配 training_data 使用 cv_split_column_names
  + **azureml-train-core**
    + 已新增一組新的 HyperDrive 特定例外狀況。 hyperdrive 現在會擲回詳細的例外狀況。
  + **azureml-widgets**
    + AzureML widget 不會顯示在 JupyterLab 中
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>適用于 Python 的 Azure Machine Learning SDK v 1.5。0

+ **新功能**
  + **預覽功能**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning 使用[光線](https://ray.io)架構來發行增強式學習的預覽支援。 `ReinforcementLearningEstimator`可讓您在 Azure Machine Learning 中，跨 GPU 和 CPU 計算目標來定型增強式學習代理程式。

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 修正我先前 PR 中不小心留下的警告記錄檔。 記錄檔已用於偵測，並不小心遺留在後方。
    + Bug 修正：通知用戶端程式代碼剖析期間的部分失敗
  + **azureml-automl-core**
    + 當資料集有多個時間序列時，啟用時間序列的平行處理，藉以加速 AutoML 預測中的 Prophet/AutoArima 模型。 為了受益于這項新功能，建議您在 AutoMLConfig 中設定 "max_cores_per_iteration =-1" （亦即，使用所有可用的 cpu 核心）。
    + 修正主控台介面中的列印護欄 KeyError
    + 已修正 experimentation_timeout_hours 的錯誤訊息
    + 已取代 AutoML 的 Tensorflow 模型。
  + **azureml-automl-runtime**
    + 已修正 experimentation_timeout_hours 的錯誤訊息
    + 從快取存放區嘗試還原序列化時，已修正未分類的例外狀況
    + 當資料集有多個時間序列時，啟用時間序列的平行處理，藉以加速 AutoML 預測中的 Prophet/AutoArima 模型。
    + 已修正測試/預測集不包含定型集其中一個粒紋的資料集上，具有已啟用之滾動視窗的預測。
    + 已改善遺漏資料的處理
    + 已修正資料集預測期間預測間隔的問題，其中包含未及時對齊的時間序列。
    + 針對預測工作新增更好的資料圖形驗證。
    + 已改善頻率偵測。
    + 如果無法產生預測工作的交叉驗證折迭，則建立更好的錯誤訊息。
    + 修正主控台介面，以正確列印遺失的值 guardrail。
    + 在 AutoMLConfig 中強制執行 cv_split_indices 輸入的資料類型檢查。
  + **azureml-cli-通用**
    + Bug 修正：通知用戶端程式代碼剖析期間的部分失敗
  + **azureml-contrib-mir**
    + 新增 contrib. mir. RevisionStatus 類別，它會轉送目前部署之 MIR 修訂的相關資訊，以及使用者所指定的最新版本。 這個類別包含在 MirWebservice 物件的 ' deployment_status ' 屬性底下。
    + 在 MirWebservice 類型及其子類別 SingleModelMirWebservice 的 Webservices 上啟用更新。
  + **azureml-contrib-reinforcementlearning**
    + 已新增對光線0.8.3 的支援
    + AmlWindowsCompute 僅支援將 Azure 檔案儲存體做為掛接的儲存體
    + 已將 health_check_timeout 重新命名為 health_check_timeout_seconds
    + 已修正一些類別/方法的描述。
  + **azureml-core**
    + 已啟用 WASB-在 USGovernment 和中國雲端中 > Blob 轉換。
    + 修正 bug，讓讀者角色可以使用 az ml run CLI 命令來取得執行資訊
    + 使用輸入資料集，在 Azure ML 遠端執行期間移除不必要的記錄。
    + RCranPackage 現在支援 CRAN 套件版本的 "version" 參數。
    + Bug 修正：通知用戶端程式代碼剖析期間的部分失敗
    + 已新增 azureml 核心的歐洲樣式浮點處理。
    + 已啟用 Azure ml sdk 中的工作區私人連結功能。
    + 當使用建立 TabularDataset 時 `from_delimited_files` ，您可以藉由設定布林值引數，指定是否應該將空值載入為 None 或空字串 `empty_as_string` 。
    + 已新增資料集的歐洲樣式浮點處理。
    + 已改善資料集掛接失敗的錯誤訊息。
  + **azureml-datadrift**
    + 來自 SDK 的資料漂移結果查詢有一個 bug，其無法區分最小值、最大值和平均特徵計量，因而產生重複的值。 我們已修正此錯誤，方法是將目標或基準加在計量名稱前面。 Before：重複的最小值、最大值、平均值。 After： target_min、target_max、target_mean、baseline_min、baseline_max、baseline_mean。
  + **azureml-dataprep**
    + 在確保資料傳遞所需的 .NET 相依性時，改善寫入限制的 python 環境處理。
    + 已修正具有前置空白記錄之檔案上的資料流程建立。
    + 已加入類似于的錯誤處理選項 `to_partition_iterator` `to_pandas_dataframe` 。
  + **azureml-interpret**
    + 減少說明路徑長度限制，以降低超過 Windows 限制的可能性
    + 使用線性代理模型，以模擬說明建立的 sparse 說明錯誤修正。
  + **azureml-opendatasets**
    + 修正 MNIST 之資料行的問題時，會剖析為字串，其應為 int。
  + **azureml-pipeline-core**
    + 當使用內嵌在 ModuleStep 中的模組時，允許選項 regenerate_outputs。
  + **azureml-train-automl-client**
    + 已取代 AutoML 的 Tensorflow 模型。
    + 修正使用者在原生模式中允許清單不支援的演算法
    + 修正 AutoMLConfig 的檔。
    + 在 AutoMLConfig 中強制執行 cv_split_indices 輸入的資料類型檢查。
    + 已修正 show_output 中 AutoML 執行失敗的問題
  + **azureml-train-automl-runtime**
    + 修正集團反復專案中的 bug，這會導致無法成功從開始模型下載超時。
  + **azureml-train-core**
    + 修正 azureml. dnn. Nccl 類別中的打字錯誤。
    + 支援 PyTorch 估計工具中的 PyTorch 1.5 版
    + 修正使用定型架構估算器時，無法在 Fairfax 區域中提取架構映射的問題

  
## <a name="2020-05-04"></a>2020-05-04
**新的筆記本體驗**

您現在可以直接在 Azure Machine Learning 的 studio web 體驗中，建立、編輯和共用機器學習服務的筆記本和檔案。 您可以從這些筆記本內的[Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)中，使用所有可用的類別和方法，從[這裡](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks)開始著手

**引進的新功能：**

+ VS Code 使用的改良編輯器（摩納哥編輯器） 
+ UI/UX 改良功能
+ 資料格工具列
+ 新增筆記本工具列和計算控制項
+ 筆記本狀態列 
+ 內嵌內核切換
+ R 支援
+ 協助工具和當地語系化改善
+ 命令選擇區
+ 其他鍵盤快速鍵
+ 隔
+ 改善的效能和可靠性

從 studio 存取下列以 web 為基礎的撰寫工具：
    
| 以 Web 為基礎的工具  |     Description  | 版本 | 
|---|---|---|
| Azure ML Studio 筆記本   |     筆記本檔案的第一次類別撰寫，並支援 Azure ML Python SDK 中所有可用的作業。 | 基本 & 企業  |   

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>適用于 Python 的 Azure Machine Learning SDK 1.4。0

+ **新功能**
  + AmlCompute 叢集現在支援在布建時于叢集上設定受控識別。 只要指定您是否要使用系統指派的身分識別或使用者指派的身分識別，並在後者的情況下傳遞 identityId。 接著，您可以設定許可權來存取各種資源（例如儲存體或 ACR），方法是使用計算的身分識別來安全地存取資料，而不是 AmlCompute 目前採用的權杖型方法。 如需參數的詳細資訊，請參閱我們的 SDK 參考。
  

+ **重大變更**
  + AmlCompute 叢集支援以執行為基礎之建立的預覽功能，我們將在兩周內規劃淘汰。 您可以使用 Amlcompute 類別，繼續以一律的方式建立持續性計算目標，但是在不久的未來，將不支援指定識別碼 "Amlcompute" 做為執行設定中計算目標的特定方法。 

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 在計算資料行中的唯一值數目時，啟用 unhashable 類型的支援。
  + **azureml-core**
    + 已改善使用 TabularDataset 從 Azure Blob 儲存體讀取時的穩定性。
    + 已改善的 `grant_workspace_msi` 參數檔 `Datastore.register_azure_blob_store` 。
    + 已修正的錯誤， `datastore.upload` 以支援以 `src_dir` 或結尾的引數 `/` `\` 。
    + 嘗試上傳到沒有存取金鑰或 SAS 權杖的 Azure Blob 儲存體資料存放區時，新增了可操作的錯誤訊息。
  + **azureml-interpret**
    + 已針對上傳的說明，將視覺效果資料的上限新增至檔案大小。
  + **azureml-train-automl-client**
    + 明確檢查 label_column_name & weight_column_name 參數，讓 AutoMLConfig 的類型為 string。
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep 現在支援將資料集當做管線參數。 使用者可以使用範例資料集來建立管線，並且可以針對新的管線執行變更相同類型（檔案或表格式）的輸入資料集。

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>適用于 Python 的 Azure Machine Learning SDK 1.3。0

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已在定型後作業前後新增額外的遙測。
    + 針對長度超過100的數列，使用條件式總和平方（CSS）訓練來加速自動 ARIMA 訓練。 請注意，所使用的長度會儲存為常數，ARIMA_TRIGGER_CSS_TRAINING_LENGTH 在/src/azureml-automl-core/azureml/automl/core/shared/constants.py 的 TimeSeriesInternal 類別中
    + 已改善預測執行的使用者記錄，現在會在記錄檔中顯示有關目前正在執行之階段的詳細資訊。
    + 不允許的 target_rolling_window_size 設為小於2的值
  + **azureml-automl-runtime**
    + 已改善發現重複時間戳記時所顯示的錯誤訊息。
    + 不允許的 target_rolling_window_size 設為小於2的值。
    + 已修正延遲插補失敗。 此問題是因為每季分解數列所需的觀察數目不足所造成。 「Seasonalized」資料是用來計算部分 autocorrelation 函數（PACF），以判斷延遲長度。
    + 已啟用資料行用途特徵化自訂特徵化 config 的預測工作。現在支援數值和類別做為預測工作的資料行用途。
    + 已針對特徵化 config 的預測工作啟用 drop column 特徵化自訂。
    + 已藉由特徵化 config 啟用預測工作的插補自訂。現在支援目標資料行的常數值插補，以及定型資料的 mean、中位數、most_frequent 和常數值插補。
  + **azureml-contrib-pipeline-steps**
    + 接受要傳遞至 ParallelRunConfig 的字串計算名稱
  + **azureml-core**
    +  已新增環境。複製（new_name） API 以建立環境物件的複本
    +  Environment.docker。 base_dockerfile 接受 filepath。 如果能夠解析檔案，內容將會讀入 base_dockerfile 環境屬性中
    + 當使用者以手動方式在 Environment.docker 中設定值時，自動重設 base_image 和 base_dockerfile 的互斥值
    + 已在 RSection 中新增 user_managed 旗標，指出環境是由使用者或 AzureML 管理。
    + 資料集：如果資料路徑包含 unicode 字元，則固定資料集下載失敗。
    + 資料集：改良的資料集掛接快取機制，以遵循 Azure Machine Learning 計算中的最小磁碟空間需求，這可避免讓節點無法使用，並導致取消作業。
    + 資料集：當您存取時間序列資料集作為 pandas 資料框架時，我們會加入時間序列資料行的索引，用來加速存取以時間序列為基礎的資料存取。  在過去，索引的名稱與時間戳記資料行相同，會讓使用者感到困惑，這是實際的時間戳記資料行，而是索引。 我們現在不會提供任何特定的名稱給索引，因為它不能當做資料行使用。 
    + 資料集：已修正主權 cloud 中的資料集驗證問題。
    + 資料集：已修正 `Dataset.to_spark_dataframe` 從 Azure 于 postgresql 資料存放區建立的資料集失敗。
  + **azureml-interpret**
    + 如果本機重要性值為稀疏，則將全域分數新增至視覺效果
    + 已更新 azureml-解讀以使用解讀-社區 0.9. *
    + 已修正下載含有 sparse 評估資料之說明的問題
    + 已在 AutoML 中新增對說明物件的 sparse 格式支援
  + **azureml-pipeline-core**
    + 支援 ComputeInstance 作為管線中的計算目標
  + **azureml-train-automl-client**
    + 已在定型後作業前後新增額外的遙測。
    + 已修正提早停止的回歸
    + 已取代的 iris.dprep 作為輸入資料的有效類型。
    +  將預設的 AutoML 實驗時間變更為六天。
  + **azureml-train-automl-runtime**
    + 已在定型後作業前後新增額外的遙測。
    + 已新增 sparse AutoML e2e 支援
  + **azureml-opendatasets**
    + 已新增服務監視器的額外遙測。
    + 啟用 blob 的前端以增加穩定性 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>適用于 Python 的 Azure Machine Learning SDK 1.2。0

+ **重大變更**
  + 捨棄 python 2.7 的支援

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 將 "--訂用帳戶識別碼" 新增至 `az ml model/computetarget/service` CLI 中的命令
    + 新增支援以傳遞客戶管理的金鑰（CMK） vault_url、key_name 和 key_version 進行 ACI 部署
  + **azureml-automl-core** 
    + 已針對 X 和 y 資料預測工作啟用具有常數值的自訂插補。
    + 已修正中的問題，並向使用者顯示錯誤訊息。    
  + **azureml-automl-runtime**
    + 已修正對資料集進行預測時的問題，其中包含只有一列的粒紋
    + 減少預測工作所需的記憶體數量。
    + 當時間資料行的格式不正確時，新增更好的錯誤訊息。
    + 已針對 X 和 y 資料預測工作啟用具有常數值的自訂插補。
  + **azureml-core**
    + 已新增從環境變數載入 ServicePrincipal 的支援： AZUREML_SERVICE_PRINCIPAL_ID、AZUREML_SERVICE_PRINCIPAL_TENANT_ID 和 AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + 引進了新的 `support_multi_line` 參數 `Dataset.Tabular.from_delimited_files` ：根據預設（ `support_multi_line=False` ），所有分行符號（包括以引號括住的域值）都會被視為記錄中斷。 以這種方式讀取資料比在多個 CPU 核心上平行執行更快速且更優化。 不過，它可能會導致以無訊息方式產生具有未對齊域值的更多記錄。 `True`當已知分隔的檔案包含引號的分行符號時，應該將此設定為。
    + 已新增在 Azure Machine Learning CLI 中註冊 ADLS Gen2 的功能
    + 已將 TabularDataset 中 with_timestamp_columns （）方法的參數 ' fine_grain_timestamp ' 重新命名為 ' timestamp '，並將參數 ' coarse_grain_timestamp ' 重新命名為 ' partition_timestamp '，以進一步反映參數的使用方式。
    + 將最大實驗名稱長度增加至255。
  + **azureml-interpret**
    + 已更新 azureml-解讀以解讀-社區 0.7. *
  + **azureml-sdk**
    + 變更為具有相容版本波狀符號的相依性，以支援在發行前版本和穩定版本中進行修補。


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>適用于 Python 的 Azure Machine Learning SDK 1.1。5

+ **功能取代**
  + **Python 2.7**
    + 支援 python 2.7 的最後版本

+ **重大變更**
  + **語意化版本控制系統 2.0.0**
    + 從1.1 版開始，Azure ML Python SDK 會採用語義版本設定2.0.0。 如需[詳細資訊，請參閱這裡](https://semver.org/)。 所有後續版本將遵循新的編號配置和語義版本設定合約。 

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 將 [端點 CLI 命令名稱] 從 [az ml endpoint aks] 變更為 [az ml endpoint real time] 以保持一致性。
    + 更新適用于穩定和實驗性分支 CLI 的 CLI 安裝指示
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-automl-core**
    + 已針對 AutoML ONNX 模型啟用批次模式推斷（只接受多個資料列）
    + 改善資料集的頻率偵測、缺少資料或包含異常資料點
    + 新增了移除不符合主要頻率之資料點的功能。
    + 已變更函式的輸入，以取得選項清單來套用對應資料行的插補選項。
    + 已改善錯誤記錄。
  + **azureml-automl-runtime**
    + 已修正當定型集出現在測試集內時，所擲回錯誤的問題
    + 已移除預測服務評分期間的 y_query 需求
    + 已修正資料集包含長時間間距的簡短粒紋時的預測問題。
    + 已修正 [自動最大水準] 開啟時的問題，而 [日期] 資料行包含格式為字串的日期。 不可能轉換成日期時，已新增適當的轉換和錯誤訊息
    + 使用原生 NumPy 和 SciPy 序列化和還原序列化 FileCacheStore 的中繼資料（用於本機 AutoML 執行）
    + 已修正失敗的子系執行可能會停滯在執行中狀態的 bug。
    + 增加特徵化的速度。
    + 已修正評分期間的頻率檢查，現在預測工作在定型和測試集之間不需要嚴格的頻率相等。
    + 已變更函式的輸入，以取得選項清單來套用對應資料行的插補選項。
    + 已修正與 lag 類型選取專案相關的錯誤。
    + 已修正在資料集上引發的未分類錯誤，其中包含具有單一資料列的粒紋
    + 已修正頻率偵測緩慢的問題。
    + 修正 AutoML 例外狀況處理中的 bug，這會造成 AttributeError 取代定型失敗的實際原因。
  + **azureml-cli-通用**
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-contrib-mir**
    + 在 MirWebservice 類別中新增功能，以取得存取權杖
    + 在 MirWebservice 期間，預設會使用 MirWebservice 的權杖驗證。執行（）僅限呼叫會在呼叫失敗時重新整理
    + Mir webservice 部署現在需要適當的 Sku [Standard_DS2_v2、Standard_F16、Standard_A2_v2]，而不是分別是 [Ds2v2、A2v2 和 F16]。
  + **azureml-contrib-pipeline-steps**
    + 選擇性參數 side_inputs 新增至 ParallelRunStep。 這個參數可以用來在容器上掛接資料夾。 目前支援的類型為 DataReference 和 PipelineData。
    + 現在傳遞管線參數可以覆寫 ParallelRunConfig 中傳遞的參數。 支援 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout （aml_node_count 和 aml_process_count_per_node 的新管線參數已是舊版的一部分）。
  + **azureml-core**
    + 已部署的 AzureML Webservices 現在會預設為 `INFO` 記錄。 這可以藉由 `AZUREML_LOG_LEVEL` 在已部署的服務中設定環境變數來加以控制。
    + Python sdk 使用探索服務來使用「api」端點，而不是「管線」。
    + 交換至所有 SDK 呼叫中的新路由。
    + 已將 ModelManagementService 的呼叫路由傳送至新的整合結構。
      + 讓工作區更新方法可公開使用。
      + 已在工作區更新方法中新增 image_build_compute 參數，以允許使用者更新映射組建的計算。
    + 已將取代訊息新增至舊的分析工作流程。 已修正分析 cpu 和記憶體限制。
    + 已新增 RSection 做為執行 R 作業的環境的一部分。
    + 已將驗證新增至， `Dataset.mount` 以在資料集的來源無法存取或未包含任何資料時引發錯誤。
    + 已新增 `--grant-workspace-msi-access` 作為資料存放區 CLI 的額外參數，用於註冊 Azure Blob 容器，可讓您註冊位於 VNet 後方的 Blob 容器。
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
    + 已修正 aks.py _deploy 中的問題。
    + 驗證所上傳模型的完整性以避免無訊息的儲存失敗。
    + 使用者現在可以在重新產生 webservices 的金鑰時，為驗證金鑰指定一個值。
    + 已修正無法使用大寫字母做為資料集之輸入名稱的 bug。
  + **azureml-預設值**
    + `azureml-dataprep`現在會安裝為的一部分 `azureml-defaults` 。 您不再需要在計算目標上手動安裝資料準備 [保險絲] 來裝載資料集。
  + **azureml-interpret**
    + 已更新 azureml-解讀以解讀-社區 0.6. *
    + 已更新 azureml-解讀以相依于解讀-社區0.5。0
    + 已將 azureml 樣式的例外狀況新增至 azureml-解讀
    + 已修正 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-mlflow**
    + 將對主權雲端的支援新增至 azureml. mlflow
  + **azureml-pipeline-core**
    + 管線批次計分筆記本現在會使用 ParallelRunStep
    + 已修正 PythonScriptStep 結果可能會不正確地重複使用的錯誤，儘管變更引數清單
    + 已新增在呼叫上的 parse_ * 方法時，設定資料行類型的功能`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + 已將移動 `AutoMLStep` 到 `azureml-pipeline-steps` 封裝。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。
    + 已新增資料集的檔範例做為 PythonScriptStep 輸入
  + **azureml-tensorboard**
    + 已更新 azureml-tensorboard 以支援 tensorflow 2。0
    + 在計算實例上使用自訂 Tensorboard 埠時顯示正確的埠號碼
  + **azureml-train-automl-client**
    + 已修正可能會在遠端執行的版本不正確的情況下安裝特定套件的問題。
    + 修正了篩選自訂特徵化 config 的 FeaturizationConfig 覆寫問題。
  + **azureml-train-automl-runtime**
    + 已修正遠端執行中頻率偵測的問題
    + 已移動 `AutoMLStep` 封裝中的 `azureml-pipeline-steps` 。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 支援 PyTorch 估計工具中的 PyTorch 1.4 版
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.2 rc0 （發行前版本）

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已針對 AutoML ONNX 模型啟用批次模式推斷（只接受多個資料列）
    + 改善資料集的頻率偵測、缺少資料或包含異常資料點
    + 新增了移除不符合主要頻率之資料點的功能。
  + **azureml-automl-runtime**
    + 已修正當定型集出現在測試集內時，所擲回錯誤的問題
    + 已移除預測服務評分期間的 y_query 需求
  + **azureml-contrib-mir**
    + 在 MirWebservice 類別中新增功能，以取得存取權杖
  + **azureml-core**
    + 已部署的 AzureML Webservices 現在會預設為 `INFO` 記錄。 這可以藉由 `AZUREML_LOG_LEVEL` 在已部署的服務中設定環境變數來加以控制。
    + 修正逐一查看 `Dataset.get_all` 以傳回已向工作區註冊的所有資料集。
    + 當傳遞不正確類型給 `path` 資料集建立 api 的引數時，改善錯誤訊息。
    + Python sdk 使用探索服務來使用「api」端點，而不是「管線」。
    + 結算所有 SDK 呼叫中的新路由
    + 將 ModelManagementService 呼叫的路由變更為新的整合結構
      + 讓工作區更新方法可公開使用。
      + 已在工作區更新方法中新增 image_build_compute 參數，以允許使用者更新映射組建的計算
    +  已將取代訊息新增至舊的分析工作流程。 已修正分析 cpu 和記憶體限制
  + **azureml-interpret**
    + 更新 azureml-解讀以解讀-社區 0.6. *
  + **azureml-mlflow**
    + 將對主權雲端的支援新增至 azureml. mlflow
  + **azureml-pipeline-steps**
    + 將移 `AutoMLStep` 至 `azureml-pipeline-steps package` 。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-automl-client**
    + 已修正可能會在遠端執行的版本不正確的情況下安裝特定套件的問題。
  + **azureml-train-automl-runtime**
    + 已修正遠端執行中頻率偵測的問題
    + 將移 `AutoMLStep` 至 `azureml-pipeline-steps package` 。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 將移 `AutoMLStep` 至 `azureml-pipeline-steps package` 。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>適用于 Python 的 Azure Machine Learning SDK rc0 （發行前版本）

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-automl-core**
    + 已改善錯誤記錄。
  + **azureml-automl-runtime**
    + 已修正資料集包含長時間間距的簡短粒紋時的預測問題。
    + 已修正 [自動最大水準] 開啟時的問題，而 [日期] 資料行包含格式為字串的日期。 我們已新增適當的轉換，並在無法轉換成日期時有合理的錯誤
    + 使用原生 NumPy 和 SciPy 序列化和還原序列化 FileCacheStore 的中繼資料（用於本機 AutoML 執行）
    + 已修正失敗的子系執行可能會停滯在執行中狀態的 bug。
  + **azureml-cli-通用**
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-core**
    + 已新增 `--grant-workspace-msi-access` 作為資料存放區 CLI 的額外參數，用於註冊 Azure Blob 容器，可讓您註冊位於 VNet 後方的 Blob 容器
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
    + 已修正 aks.py 中的問題 _deploy
    + 驗證所上傳模型的完整性以避免無訊息的儲存失敗。
  + **azureml-interpret**
    + 已將 azureml 樣式的例外狀況新增至 azureml-解讀
    + 已修正 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-pipeline-core**
    + 管線批次計分筆記本現在會使用 ParallelRunStep
  + **azureml-pipeline-steps**
    + 已移動 `AutoMLStep` 封裝中的 `azureml-pipeline-steps` 。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-contrib-pipeline-steps**
    + 選擇性參數 side_inputs 新增至 ParallelRunStep。 這個參數可以用來在容器上掛接資料夾。 目前支援的類型為 DataReference 和 PipelineData。
  + **azureml-tensorboard**
    + 已更新 azureml-tensorboard 以支援 tensorflow 2。0
  + **azureml-train-automl-client**
    + 修正了篩選自訂特徵化 config 的 FeaturizationConfig 覆寫問題。
  + **azureml-train-automl-runtime**
    + 已移動 `AutoMLStep` 封裝中的 `azureml-pipeline-steps` 。 在中已被取代 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 支援 PyTorch 估計工具中的 PyTorch 1.4 版
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.0 rc0 （發行前版本）

+ **重大變更**
  + **語意化版本控制系統 2.0.0**
    + 從1.1 版開始，Azure ML Python SDK 會採用語義版本設定2.0.0。 如需[詳細資訊，請參閱這裡](https://semver.org/)。 所有後續版本將遵循新的編號配置和語義版本設定合約。 
  
+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 增加特徵化的速度。
    + 已修正評分期間的頻率檢查，現在在預測工作中，我們不需要在定型和測試集之間進行嚴格的頻率相等。
  + **azureml-core**
    + 使用者現在可以在重新產生 webservices 的金鑰時，為驗證金鑰指定一個值。
  + **azureml-interpret**
    + 已更新 azureml-解讀以相依于解讀-社區0.5。0
  + **azureml-pipeline-core**
    + 已修正 PythonScriptStep 結果可能會不正確地重複使用的錯誤，儘管變更引數清單
  + **azureml-pipeline-steps**
    + 已新增資料集的檔範例做為 PythonScriptStep 輸入
  + **azureml-contrib-pipeline-steps**
    + 現在傳遞管線參數可以覆寫 ParallelRunConfig 中傳遞的參數。 支援 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout （aml_node_count 和 aml_process_count_per_node 的新管線參數已是舊版的一部分）。
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.85

+ **新功能**
  + **azureml-core**
    + 取得指定工作區和訂用帳戶中 AmlCompute 資源的目前核心使用量和配額限制
  
  + **azureml-contrib-pipeline-steps**
    + 讓使用者將表格式資料集當做上一個步驟的中繼結果傳遞至 parallelrunstep

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 已移除對已部署預測服務要求中 y_query 資料行的需求。 
    + 已從 Dominick 的橙色 Juice 筆記本服務要求區段移除 ' y_query '。
    + 已修正 bug，以防止在已部署的模型上進行預測，並在具有日期時間資料行的資料集上操作。
    + 已針對二元和多元分類，新增 Matthews 相互關聯係數做為分類度量。
  + **azureml-contrib-interpret**
    + 已移除從 azureml explainers 的文字-contrib-解讀為文字說明已移至即將發行的解讀文字存放庫。
  + **azureml-core**
    + Dataset：檔案資料集的使用方式不再相依于要安裝在 python env 中的 numpy 和 pandas。
    + 已變更 wait_for_deployment LocalWebservice （），以檢查本機 Docker 容器的狀態，然後再嘗試 ping 其健康情況端點，大幅減少報告失敗部署所需的時間量。
    + 已修正在使用 LocalWebservice （）函式從現有的部署建立服務物件時，LocalWebservice 中使用的內部屬性初始化（）。
    + 已編輯錯誤訊息以供澄清。
    + 將名為 get_access_token （）的新方法新增至 AksWebservice，它會傳回 AksServiceAccessToken 物件，其中包含存取權杖、在時間戳記之後重新整理、時間戳記和權杖類型到期。 
    + 已取代 AksWebservice 中的現有 get_token （）方法，因為新方法會傳回這個方法傳回的所有資訊。
    + 已修改 az ml service 取得存取權杖命令的輸出。 已將 token 重新命名為 accessToken，並 refreshBy 為 refreshAfter。 已新增 expiryOn 和 tokenType 屬性。
    + 已修正 get_active_runs
  + **azureml-說明-模型**
    + 已將 shap 更新至0.33.0，並將-社區轉譯為0.4。 *
  + **azureml-interpret**
    + 已將 shap 更新至0.33.0，並將-社區轉譯為0.4。 *
  + **azureml-train-automl-runtime**
    + 已針對二元和多元分類，新增 Matthews 相互關聯係數做為分類度量。
    + 取代程式碼中的前置處理旗標，並取代為特徵化-特徵化預設為開啟

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.83

+ **新功能**
  + 資料集：加入兩個選項 `on_error` ， `out_of_range_datetime` `to_pandas_dataframe` 如果資料有錯誤值，而不是用來填滿，則會失敗 `None` 。
  + 工作區：新增 `hbi_workspace` 具有敏感性資料之工作區的旗標，以啟用進一步的加密並停用工作區上的 advanced diagnostics。 我們也新增了將您自己的金鑰帶入相關聯 Cosmos DB 實例的支援，方法 `cmk_keyvault` 是 `resource_cmk_uri` 在建立工作區時指定和參數，在布建您的工作區時，于訂用帳戶中建立 Cosmos DB 實例。 [請於此處深入了解。](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 已修正在 Python 版本的3.5.4 上執行 AutoML 時，造成 TypeError 引發的回歸。
  + **azureml-core**
    + 已修正無法 `datastore.upload_files` 使用以開頭的相對路徑的錯誤（bug） `./` 。
    + 已新增所有映射類別 codepaths 的取代訊息
    + 適用于 Azure 中國世紀地區的固定模型管理 URL 結構。
    + 已修正無法封裝使用 source_dir 的模型以供 Azure Functions 的問題。    
    + 已將選項新增至[環境。 build_local （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)以將映射推送至 AzureML 工作區容器登錄
    + 已更新 SDK，以後向相容的方式在 Azure synapse 上使用新的權杖程式庫。
  + **azureml-interpret**
    + 已修正未提供可供下載的說明時，未傳回任何內容的錯誤（bug）。 現在會引發例外狀況，並在其他地方比對行為。
  + **azureml-pipeline-steps**
    + `DatasetConsumptionConfig` `Estimator` `inputs` 當將用於時，不允許傳遞至的參數 `Estimator` `EstimatorStep` 。
  + **azureml-sdk**
    + 已將 AutoML 用戶端新增至 azureml-sdk 套件，讓遠端 AutoML 執行提交，而不需要安裝完整的 AutoML 套件。
  + **azureml-train-automl-client**
    + 已更正 AutoML 執行的主控台輸出對齊
    + 已修正遠端 amlcompute 上可能會安裝不正確版本 pandas 的錯誤。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.81

+ **Bug 修正和改善**
  + **azureml-contrib-interpret**
    + 延遲 shap 相依性以從 azureml 解讀
  + **azureml-core**
    + 計算目標現在可以指定為對應部署設定物件的參數。 這是要部署至的計算目標名稱，而不是 SDK 物件。
    + 已將 CreatedBy 資訊新增至模型和服務物件。 可透過存取 <var> 。 created_by
    + 已修正 Install-containerimage （），其未正確設定 Docker 容器的 HTTP 埠。
    + `azureml-dataprep`為 CLI 命令設為選用 `az ml dataset register`
    + 已修正錯誤，其中 `TabularDataset.to_pandas_dataframe` 會錯誤地切換回替代的讀取器並印出警告。
  + **azureml-說明-模型**
    + 延遲 shap 相依性以從 azureml 解讀
  + **azureml-pipeline-core**
    + 已新增管線步驟 `NotebookRunnerStep` ，以在管線中以步驟執行本機筆記本。
    + 已移除 PublishedPipelines、排程和 PipelineEndpoints 的已淘汰 get_all 函式
  + **azureml-train-automl-client**
    + 已開始將 data_script 取代為 AutoML 的輸入。


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.79

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已移除要記錄的 featurizationConfig
      + 已將記錄更新為僅記錄「自動」/「關閉」/「自訂」。
  + **azureml-automl-runtime**
    + 已新增對 pandas 的支援。 數列和 pandas。 用於偵測資料行資料類型的類別。 先前只有支援的 numpy。 ndarray
      + 已新增相關的程式碼變更，以正確地處理類別 dtype。
    + 已改善預測函數介面： y_pred 參數設為選用。 -已改善 docstrings。
  + **azureml-contrib-dataset**
    + 已修正無法裝載已標記資料集的 bug。
  + **azureml-core**
    + 修正的錯誤 `Environment.from_existing_conda_environment(name, conda_environment_name)` 。 使用者可以建立環境的實例，這是本機環境的確切複本
    + 根據預設，將時間序列相關的資料集方法變更為 `include_boundary=True` 。
  + **azureml-train-automl-client**
    + 已修正 [顯示輸出] 設定為 false 時，不會列印驗證結果的問題。


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.76

+ **重大變更**
  + Azureml-訓練-AutoML 升級問題
    + 升級至 azureml-automl>= 1.0.76 from azureml-定型-automl<1.0.76 可能會導致部分安裝，導致部分 AutoML 匯入失敗。 若要解決這個問題，您可以執行中找到的安裝腳本 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd 。 或者，如果您直接使用 pip，您可以：
      + 「pip 安裝--升級 azureml-訓練-automl」
      + 「pip 安裝--忽略-已安裝的 azureml-訓練-automl-用戶端」
    + 或者，您也可以在升級之前先卸載舊版本
      + 「pip 卸載 azureml-訓練-automl」
      + 「pip 安裝 azureml-訓練-automl」

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 在計算二元分類工作的平均純量計量時，AutoML 現在會考慮 true 和 false 類別。
    + 已將 AutoML-Core 中的機器學習和訓練程式碼移至新的套件 AzureML-AutoML-Runtime。
  + **azureml-contrib-dataset**
    + `to_pandas_dataframe`使用下載選項在加上標籤的資料集上呼叫時，您現在可以指定是否要覆寫現有的檔案。
    + 當呼叫 `keep_columns` 或 `drop_columns` 會導致卸載時間序列、標籤或影像資料行時，也會卸載 dataset 的對應功能。
    + 已修正物件偵測工作的 pytorch 載入器問題。
  + **azureml-contrib-interpret**
    + 已從 azureml 移除說明儀表板 widget-contrib-解讀、變更的封裝，以參考中的新元件 interpret_community
    + 將解讀-社區更新為0.2.0 的版本
  + **azureml-core**
    + 改善的效能 `workspace.datasets` 。
    + 已新增使用使用者名稱和密碼驗證來註冊 Azure SQL Database 資料存放區的功能
    + 從相對路徑載入 RunConfigurations 的修正。
    + 當呼叫 `keep_columns` 或 `drop_columns` 導致卸載時間序列資料行時，也會針對資料集卸載對應的功能。
  + **azureml-interpret**
    + 將解讀-社區更新為0.2.0 的版本
  + **azureml-pipeline-steps**
    + 已記載 `runconfig_pipeline_params` Azure machine learning 管線步驟的支援值。
  + **azureml-pipeline-core**
    + 已新增 CLI 選項，以針對管線命令下載 json 格式的輸出。
  + **azureml-train-automl**
    + 將 AzureML AutoML 分成兩個套件，用戶端封裝 AzureML-定型-AutoML-用戶端和 ML 訓練套件 AzureML-定型-AutoML-執行時間
  + **azureml-train-automl-client**
    + 已新增瘦用戶端來提交 AutoML 實驗，而不需要在本機安裝任何機器學習服務相依性。
    + 已修正遠端執行中自動偵測到的延遲、滾動視窗大小和最大視野的記錄。
  + **azureml-train-automl-runtime**
    + 已新增新的 AutoML 套件，以隔離機器學習服務和用戶端的執行時間元件。
  + **azureml-contrib-定型-rl**
    + 已新增 SDK 中的增強式學習支援。
    + 已在 RL SDK 中新增 AmlWindowsCompute 支援。


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.74

  + **預覽功能**
    + **azureml-contrib-dataset**
      + 匯入 contrib 資料集之後，您可以呼叫 `Dataset.Labeled.from_json_lines` 而不是 `._Labeled` 來建立加上標籤的資料集。
      + `to_pandas_dataframe`使用下載選項在加上標籤的資料集上呼叫時，您現在可以指定是否要覆寫現有的檔案。
      + 當呼叫 `keep_columns` 或 `drop_columns` 會導致卸載時間序列、標籤或影像資料行時，也會卸載 dataset 的對應功能。
      + 已修正呼叫時 PyTorch 載入器的問題 `dataset.to_torchvision()` 。

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 已將模型分析新增至 preview CLI。
    + 修正 Azure 儲存體中的重大變更，導致 AzureML CLI 失敗。
    + 已針對 AKS 類型將 Load Balancer 類型新增至 LIP.MLC
  + **azureml-automl-core**
    + 已修正偵測到最大水準時間序列的問題，具有遺漏值和多個粒紋。
    + 已修正產生交叉驗證分割期間失敗的問題。
    + 將此區段取代為 markdown 格式的訊息，以顯示在版本資訊中：-改善預測資料集中較短粒紋的處理方式。
    + 已修正在記錄期間遮罩部分使用者資訊的問題。 -改善預測執行期間的錯誤記錄。
    + 將 psutil 當做 conda 相依性新增至自動產生的 yml 部署檔案。
  + **azureml-contrib-mir**
    + 修正 Azure 儲存體中的重大變更，導致 AzureML CLI 失敗。
  + **azureml-core**
    + 修正導致 Azure Functions 上部署的模型產生財500公司的 bug。
    + 已修正 amlignore 檔案未套用至快照集的問題。
    + 已加入新的 API amlcompute。 get_active_runs，它會傳回在指定 amlcompute 上執行和佇列執行的產生器。
    + 已針對 AKS 類型將 Load Balancer 類型新增至 LIP.MLC。
    + 已將 append_prefix bool 參數新增至 artifacts_client 中 run.py 和 download_artifacts_from_prefix 中的 download_files。 此旗標是用來選擇性地壓平合併原始 filepath，因此只會將檔案或資料夾名稱新增至 output_directory
    + 修正 `run_config.yml` 具有資料集使用方式的還原序列化問題。
    + 當呼叫 `keep_columns` 或 `drop_columns` 導致卸載時間序列資料行時，也會針對資料集卸載對應的功能。
  + **azureml-interpret**
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

中的 [共同作業] 工作區登陸頁面已 [https://ml.azure.com](https://ml.azure.com) 增強並更名為 Azure Machine Learning studio （預覽）。

在 studio 中，您可以定型、測試、部署和管理 Azure Machine Learning 的資產，例如資料集、管線、模型、端點等等。

從 studio 存取下列以 web 為基礎的撰寫工具：

| 以 Web 為基礎的工具 | Description | 版本 |
|-|-|-|
| 筆記本 VM （預覽） | 完全受控的雲端式工作站 | 基本 & 企業 |
| [自動化機器學習](tutorial-first-experiment-automated-ml.md)服務（預覽） | 自動化機器學習模型開發的程式碼體驗 | Enterprise |
| [設計](concept-designer.md)工具（預覽） | 拖放機器學習模型化工具，先前稱為「設計師」 | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning 設計工具增強功能

+ 先前稱為視覺化介面 
+    11個新[模組](algorithm-module-reference/module-reference.md)，包括 recommender、分類器和定型公用程式，包括功能工程、交叉驗證和資料轉換。

### <a name="r-sdk"></a>R SDK 
 
資料科學家和 AI 開發人員會使用[適用于 R 的 AZURE MACHINE LEARNING SDK](tutorial-1st-r-experiment.md) ，透過 Azure Machine Learning 來建立及執行機器學習工作流程。

適用于 R 的 Azure Machine Learning SDK 會使用 `reticulate` 套件來系結至 PYTHON SDK。 藉由直接系結至 Python，適用于 R 的 SDK 可讓您從所選的任何 R 環境，存取在 Python SDK 中執行的核心物件和方法。

SDK 的主要功能包括：

+    管理用來對您的機器學習實驗進行監視、記錄及組織的雲端資源。
+    使用雲端資源（包括 GPU 加速模型訓練）來定型模型。
+    將您的模型部署為 Azure 容器實例（ACI）上的 webservices，並 Azure Kubernetes Service （AKS）。

如需完整的檔，請參閱[套件網站](https://azure.github.io/azureml-sdk-for-r)。

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning 與事件方格整合 

Azure Machine Learning 現在是事件方格的資源提供者，您可以透過 Azure 入口網站或 Azure CLI 設定機器學習事件。 使用者可以建立執行完成、模型註冊、模型部署和偵測到資料漂移的事件。 這些事件可以路由傳送至事件方格支援的事件處理常式以供取用。 如需詳細資訊，請參閱機器學習服務事件[架構](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)和[教學](how-to-use-event-grid.md)課程文章。

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.72

+ **新功能**
  + 透過[**datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)套件新增資料集監視器，讓您監視時間序列資料集，以便在一段時間後進行資料漂移或其他統計變更。 如果偵測到漂移或符合資料的其他條件，就可以觸發警示和事件。 如需詳細資訊，請參閱[我們的檔](https://aka.ms/datadrift)。
  + 在 Azure Machine Learning 中宣佈兩個新版本（也稱為 SKU）。 在此版本中，您現在可以建立基本或企業 Azure Machine Learning 的工作區。 所有現有的工作區都會預設為基本版本，您可以移至 Azure 入口網站或至 studio，隨時升級工作區。 您可以從 Azure 入口網站建立基本或企業工作區。 若要深入瞭解，請參閱[我們的檔](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)。 從 SDK 中，您可以使用工作區物件的「sku」屬性來判斷您的工作區版本。
  + 我們也增強了 Azure Machine Learning 計算的功能-您現在可以在 Azure 監視器中，查看叢集的計量（例如節點總計、執行中節點、總核心配額），除了查看診斷記錄以進行偵錯工具以外。 此外，您也可以在叢集上查看目前正在執行或已佇列的回合，以及詳細資料，例如叢集上各種節點的 Ip。 您可以在入口網站中，或使用 SDK 或 CLI 中的對應函式來查看這些功能。

  + **預覽功能**
    + 我們將在 Azure Machine Learning 計算中發行本機 SSD 磁片加密的預覽支援。 請提出技術支援票證，讓您的訂用帳戶列入允許清單，以使用此功能。
    + Azure Machine Learning 批次推斷的公開預覽。 Azure Machine Learning 批次推斷是以不區分時間的大型推斷作業為目標。 批次推斷提供符合成本效益的推斷計算調整，並具有非同步應用程式的無可匹敵輸送量。 它已針對大型資料集合的高輸送量、火災和忘的推斷進行優化。
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
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
    + 已新增資料集 CLI。 如需詳細資訊：`az ml dataset --help`
    + 新增支援部署和封裝支援的模型（ONNX、scikit-learn、學習和 TensorFlow），而不含 InferenceConfig 實例。
    + 已在 SDK 和 CLI 中新增服務部署（ACI 和 AKS）的覆寫旗標。 如果有提供，將會覆寫現有的服務，前提是已有同名的服務。 如果服務不存在，將會建立新的服務。
    + 您可以使用兩個新的架構 Onnx 和 Tensorflow 來註冊模型。 -模型註冊可接受範例輸入資料、範例輸出資料，以及模型的資源設定。
  + **azureml-automl-core**
    + 只有在設定執行時間條件約束時，才會在子進程中執行定型。
    + 已新增預測工作的 guardrail，以檢查指定的 max_horizon 是否會在給定的電腦上造成記憶體問題。 如果是，將會顯示 guardrail 訊息。
    + 新增了兩年和一個月等複雜頻率的支援。 -如果無法判斷頻率，則新增理解錯誤訊息。
    + 將 azureml-預設值新增至自動產生的 conda env，以解決模型部署失敗
    + 允許 Azure Machine Learning 管線中的中繼資料轉換成表格式資料集，並在中使用 `AutoMLStep` 。
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
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI 現在支援模型封裝。
    + 為已淘汰的資料集 Api 新增取代警告。 請參閱中的資料集 API 變更通知 https://aka.ms/tabular-dataset 。
    + [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)如果已註冊資料集，請變更以傳回註冊名稱和版本。
    + 修正以 dataset 做為引數 ScriptRunConfig 的 bug，無法重複使用來提交實驗執行。
    + 執行期間所抓取的資料集會受到追蹤，並可在執行詳細資料頁面中看到，或在 [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) 執行完成後呼叫。
    + 允許 Azure Machine Learning 管線中的中繼資料轉換成表格式資料集，並在中使用 [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) 。
    + 新增支援部署和封裝支援的模型（ONNX、scikit-learn、學習和 TensorFlow），而不含 InferenceConfig 實例。
    + 已在 SDK 和 CLI 中新增服務部署（ACI 和 AKS）的覆寫旗標。 如果有提供，將會覆寫現有的服務，前提是已有同名的服務。 如果服務不存在，將會建立新的服務。
    +  您可以使用兩個新的架構 Onnx 和 Tensorflow 來註冊模型。 模型註冊可接受範例輸入資料、範例輸出資料，以及模型的資源設定。
    + 已新增適用於 MySQL 的 Azure 資料庫的新資料存放區。 已新增在 Azure Machine Learning 管線中的 DataTransferStep 中使用適用於 MySQL 的 Azure 資料庫的範例。
    + 新增功能以從實驗中新增和移除標記從執行移除標記的功能
    + 已在 SDK 和 CLI 中新增服務部署（ACI 和 AKS）的覆寫旗標。 如果有提供，將會覆寫現有的服務，前提是已有同名的服務。 如果服務不存在，將會建立新的服務。
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 已從移 `azureml-contrib-datadrift` 至`azureml-datadrift`
    + 已新增監視漂移和其他統計量值之時間序列資料集的支援
    + 新方法 `create_from_model()` 和 `create_from_dataset()` [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) 類別。 `create()`方法將會被取代。
    + Azure Machine Learning studio 中 Python 和 UI 中的視覺效果調整。
    + 除了資料集監視器的每日，還支援每週和每月監視排程。
    + 支援資料監視器計量的回填，以分析資料集監視器的歷程記錄資料。
    + 各種 Bug 修正
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + dataprep 不再需要從管線檔案提交 Azure Machine Learning 管線執行 `yaml` 。
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + 將 azureml-預設值新增至自動產生的 conda env，以解決模型部署失敗
    + AutoML 遠端訓練現在包含 azureml-預設值，以允許重複使用定型 env 進行推斷。
  + **azureml-train-core**
    + 已在估計工具中新增 PyTorch 1.3 支援 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)

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
  + 從 SQL Database 匯入

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.69

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 將模型說明限制為最佳執行，而不是每次執行的計算說明。 對本機、遠端和 ADB 進行此行為變更。
    + 新增 UI 的隨選模型說明支援
    + 已新增 psutil 做為相依性 `automl` ，並包含 psutil 作為 amlcompute 中的 conda 相依性。
    + 已修正預測資料集上的啟發式延遲和滾動視窗大小的問題，這可能會造成線性代數錯誤
      + 已針對預測執行中啟發式決定的參數新增 print out。
  + **azureml-contrib-datadrift**
    + 如果資料集層級漂移不在第一節中，則會在建立輸出計量時新增保護。
  + **azureml-contrib-interpret**
    + azureml-contrib-說明-模型套件已重新命名為 azureml-contrib-解讀
  + **azureml-core**
    + 已新增 API 以取消註冊資料集。 `dataset.unregister_all_versions()`
    + azureml-contrib-說明-模型套件已重新命名為 azureml-contrib-解讀。
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + 已新增 API 以取消註冊資料集。 集中.[unregister_all_versions （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)。
    + 已新增資料集 API 以檢查資料變更時間。 `dataset.data_changed_time`.
    + 能夠 `FileDataset` `TabularDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` 在 Azure Machine Learning 管線中使用和作為、和的輸入
    + 已 `FileDataset.mount` 針對具有大量檔案的資料夾改善的效能
    + 能夠在 Azure Machine Learning 管線中，使用[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset)和[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset)作為[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)、 [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)和[HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep)的輸入。
    + FileDataset 的效能。已針對具有大量檔案的資料夾改進[掛接（）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-)
    + 已在 [執行詳細資料] 中新增已知錯誤建議的 URL。
    + 已修正 run 中的錯誤（bug）。如果執行有太多子系，則要求會失敗 get_metrics
    + 已修正 run 中的錯誤（bug）。如果執行有太多子系，則要求會失敗[get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-)
    + 已新增 Arcadia 叢集上的驗證支援。
    + 建立實驗物件會取得或建立 Azure Machine Learning 工作區中的實驗，以執行歷程記錄追蹤。 實驗識別碼和封存時間會在建立時填入實驗物件中。 範例：實驗 = 實驗（工作區、「新實驗」） experiment_id = experiment.id archive （）和重新開機（）是可在實驗上呼叫的函式，以隱藏和還原實驗，使其不會顯示在 UX 中，或依預設在呼叫清單實驗時傳回。 如果使用與封存實驗相同的名稱建立新的實驗，您可以藉由傳遞新名稱來重新開機已封存的實驗。 只能有一個具有指定名稱的作用中實驗。 範例： experiment1 = 實驗（工作區，"Active 實驗"） experiment1. archive （） # 建立新的作用中實驗，其名稱與封存的相同。 experiment2. = 實驗（工作區、「作用中實驗」） experiment1。重新開機（new_name = 「先前的作用中實驗」）實驗上的靜態方法清單（）可以接受名稱篩選和 ViewType 篩選。 ViewType 值為 "ACTIVE_ONLY"、"ARCHIVED_ONLY" 和 "ALL" 範例： archived_experiments = 實驗。 list （workspace，view_type = "ARCHIVED_ONLY"） all_first_experiments = 實驗。 list （workspace，name = "First 實驗"，view_type = "ALL"）
    + 支援使用環境進行模型部署和服務更新
  + **azureml-datadrift**
    + DataDriftDector 類別的 show 屬性不再支援選擇性引數 ' with_details '。 Show 屬性只會呈現資料漂移係數和特徵資料行的資料漂移比重。
    + DataDriftDetector 屬性 ' get_output ' 行為變更：
      + 輸入參數 start_time，end_time 是選擇性的，而不是強制性;
      + 在相同的叫用中使用特定 run_id 輸入特定 start_time 和/或 end_time，將會導致值錯誤例外狀況，因為它們互斥
      + 藉由輸入特定 start_time 和/或 end_time，只會傳回排程執行的結果;
      + 參數 ' daily_latest_only ' 已被取代。
    + 支援抓取以資料集為基礎的資料漂移輸出。
  + **azureml-說明-模型**
    + 將 AzureML-說明模型套件重新命名為 AzureML-解讀，保留舊的封裝以提供回溯相容性
    + 已修正 `automl` 將原始解說設定為分類工作的 bug，而不是根據預設從 ExplanationClient 下載的回歸
    + 新增的支援 `ScoringExplainer` ，以直接使用來建立`MimicWrapper`
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
    + [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)類別的 show 屬性不再支援選擇性引數 ' with_details '。 Show 屬性只會呈現資料漂移係數和特徵資料行的資料漂移比重。
    + DataDriftDetector 函數 [get_output] https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) 行為變更：
      + 輸入參數 start_time，end_time 是選擇性的，而不是強制性;
      + 在相同叫用中使用特定 run_id 輸入特定 start_time 和/或 end_time，會導致值錯誤例外狀況，因為它們互斥。
      + 藉由輸入特定 start_time 和/或 end_time，只會傳回排程執行的結果;
      + 參數 ' daily_latest_only ' 已被取代。
    + 支援抓取以資料集為基礎的資料漂移輸出。
  + **azureml-說明-模型**
    + 將 AzureML-說明模型套件重新命名為 AzureML-解讀，並保留舊的封裝以提供回溯相容性。
    + 已修正 AutoML bug，其中原始說明已設定為分類工作，而不是預設從 ExplanationClient 下載的回歸。
    + 新增使用 MimicWrapper 直接建立[ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py)的支援
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 改善大型管線建立的效能。
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + 已在[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow)估計工具中新增 TensorFlow 2.0 支援。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
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
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 已新增 ADB 和 HDI 的 ONNX 轉換支援

+ **預覽功能**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援的經理 BERT 和 BiLSTM 做為文字 featurizer （僅限預覽）
    + 針對資料行用途和轉換器參數支援的特徵化自訂（僅限預覽）
    + 當使用者在定型期間啟用模型說明時支援的原始說明（僅限預覽）
    + 已將預測的 Prophet 新增 `timeseries` 為可訓練管線（僅限預覽）

  + **azureml-contrib-datadrift**
    + 從 azureml-contrib-datadrift 重新置放至 azureml-datadrift 的套件;`contrib`未來的版本將移除此封裝

+ **Bug 修正和改善**
  + **azureml-automl-core**
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
    + 引進 `run.get_details()['datasets']` 以取得與已提交執行相關聯的資料集
    + 新增 API `Dataset.Tabular.from_json_lines_files` 以從 JSON 行檔案建立 TabularDataset。 若要瞭解 TabularDataset 上 JSON 行檔案中的此表格式資料，請流覽檔 https://aka.ms/azureml-data 。
    + 已將額外的 VM 大小欄位（OS 磁片、Gpu 數目）新增至 supported_vmsizes （）函數
    + 已將其他欄位新增至 list_nodes （）函式，以顯示執行、私用和公用 IP、埠等。
    + 在叢集布建期間指定新欄位的能力--remotelogin_port_public_access 可以設定為 [啟用] 或 [停用]，視您是否想要在建立叢集時讓 SSH 埠保持開啟或關閉。 如果您未指定，服務會聰明地開啟或關閉埠，視您是否在 VNet 內部署叢集而定。
  + **azureml-說明-模型**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 已新增透過模型物件將 SAS URL 取出至儲存體中模型的功能。 例如： model。[get_sas_url （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 引進執行。[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' dataset '] 取得與已提交執行相關聯的資料集
    + 新增 API `Dataset.Tabular` 。[from_json_lines_files （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) ，以從 Json 行檔案建立 TabularDataset。 若要瞭解 TabularDataset 上 JSON 行檔案中的此表格式資料，請流覽檔 https://aka.ms/azureml-data 。
    + 已將額外的 VM 大小欄位（OS 磁片、Gpu 數目）新增至[supported_vmsizes （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)函數
    + 已將其他欄位新增至[list_nodes （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--)函式，以顯示執行、私用和公用 IP、埠等。
    + 在叢集布建期間指定新欄位的[能力，](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` 可以設定為啟用或停用，視您是否想要在建立叢集時讓 SSH 埠保持開啟或關閉。 如果您未指定，服務會聰明地開啟或關閉埠，視您是否在 VNet 內部署叢集而定。
  + **azureml-說明-模型**
    + 已改善分類案例中說明輸出的檔。
    + 已在評估範例的說明上，新增上傳預測 y 值的功能。 解除鎖定更有用的視覺效果。
    + 已將說明屬性新增至 MimicWrapper，以允許取得基礎 MimicExplainer。
  + **azureml-pipeline-core**
    + 已新增筆記本來描述 Module、ModuleVersion 和 ModuleStep
  + **azureml-pipeline-steps**
    + 已新增 RScriptStep 以支援透過 AML 管線執行的 R 腳本。
    + 已修正」已 azurebatchstep 中的中繼資料參數剖析，這會導致「未指定參數 SubscriptionId 的指派」錯誤訊息。
  + **azureml-train-automl**
    + 支援的 training_data、validation_data、label_column_name、weight_column_name 做為資料輸入格式
    + 已新增 explain_model （）和 retrieve_model_explanations （）的取代訊息
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 已新增[筆記本](https://aka.ms/pl-modulestep)來描述[Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class))、[ModuleVersion 和[ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)。
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + 已新增[RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep)以支援透過 AML 管線執行的 R 腳本。
    + 已修正在 [」已 azurebatchstep 中剖析的中繼資料參數，這會造成錯誤訊息「未指定參數 SubscriptionId 的指派」。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援的 training_data、validation_data、label_column_name、weight_column_name 做為資料輸入格式。
    + 已新增[explain_model （）](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-)和[retrieve_model_explanations （）](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)的取代訊息。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.62

+ **新功能**
  + `timeseries`在 TabularDataset 上引進了特性。 這項特性可讓您輕鬆地對資料 TabularDataset 進行時間戳記篩選，例如在一段時間內取得所有資料，或在最新的資料之間進行。 若要瞭解 `timeseries` TabularDataset 上的特性，請流覽 https://aka.ms/azureml-data 檔或 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 範例筆記本。
  + 已啟用 TabularDataset 和 FileDataset 的訓練。 如需 https://aka.ms/dataset-tutorial 範例筆記本，請造訪。

  + **azureml-train-core**
      + 已 `Nccl` `Gloo` 在 PyTorch 估計工具中新增和支援

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已淘汰 AutoML 設定 ' lag_length ' 和 LaggingTransformer。
    + 修正輸入資料以資料流程格式指定時的正確驗證
    + 修改 fit_pipeline .py，以產生圖形 json 並上傳至成品。
    + 使用在下轉譯 `userrun` 圖形 `Cytoscape` 。
  + **azureml-core**
    + 在 ADB 程式碼中再次執行例外狀況處理，並根據新的錯誤處理將變更為。
    + 已新增筆記本 Vm 的自動 MSI 驗證。
    + 修正因重試失敗而無法上傳損毀或空模型的 bug。
    + 修正 `DataReference` 當 `DataReference` 模式變更（例如呼叫 `as_upload` 、或）時，名稱變更的 bug `as_download` `as_mount` 。
    + `mount_point` `target_path` 針對和，請選擇和 `FileDataset.mount` `FileDataset.download` 。
    + 找不到時間戳記資料行的例外狀況將會擲回，如果呼叫 serials 相關的 API 時未指派微調時間戳記資料行，或是已卸載指派的時間戳記資料行。
    + 應該使用類型為 Date 的資料行來指派 Time serials 資料行，否則應為例外狀況
    + 指派 API ' with_timestamp_columns ' 的時間 serials 資料行可接受無值的微調/粗略時間戳記資料行名稱，這將會清除先前指派的時間戳記資料行。
    + 當您捨棄了粗略細微性或精細的時間戳記資料行時，將會擲回例外狀況，並指出可以在卸載清單中排除 timestamp 資料行或呼叫不含任何值的 with_time_stamp，以釋放時間戳記資料行。
    + 當 [保留資料行] 清單中未包含 [粗略細微性] 或 [精細的時間戳記] 資料行時，將會擲回例外狀況，並指示使用者在 [保留資料行] 清單中包含時間戳記資料行，或使用 [無值] with_time_stamp 呼叫 [釋放時間戳記] 資料行來進行保留。
    + 新增已註冊模型大小的記錄。
  + **azureml-說明-模型**
    + 已修正未安裝「封裝」 python 套件時，列印到主控台的警告：「使用比支援的 lightgbm 版本還舊的版本，請升級至大於2.2.1 的版本」
    + 已修正使用分區化的下載模型說明與許多功能的全域說明
    + 已修正模擬說明遺漏輸出說明的初始化範例
    + 已修正使用具有兩種不同類型之模型的說明用戶端上傳時，set 屬性上的不可變錯誤
    + 已將 get_raw param 加入評分說明。說明（），讓一個評分說明可以同時傳回工程化和原始值。
  + **azureml-train-automl**
    + 已從 AutoML 引進公用 Api，以提供支援說明，從 `automl` 說明 sdk-較新的支援 AutoML 說明的方式（藉由將 AutoML 特徵化分離）並說明 Sdk 整合的原始說明支援（來自 azureml 說明 sdk For AutoML 模型）。
    + 從遠端訓練環境中移除 azureml-預設值。
    + 已將預設快取存放區位置從 FileCacheStore 為基礎變更為 AzureFileCacheStore，以在 Azure Databricks 的程式碼路徑上 AutoML。
    + 修正輸入資料以資料流程格式指定時的正確驗證
  + **azureml-train-core**
    + 已還原 source_directory_data_store 取代。
    + 已新增覆寫已安裝之 azureml 套件版本的功能。
    + 已在估算器的參數中新增 dockerfile 支援 `environment_definition` 。
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

1. 無法刪除或重新命名筆記本和檔案中的檔案。 在公開預覽期間，您可以在筆記本 VM 中使用 Jupyter UI 或終端機來執行更新檔案作業。 由於這是已掛接的網路檔案系統，因此您在筆記本 VM 上進行的所有變更都會立即反映在 [筆記本] 工作區中。

1. 若要透過 SSH 連線到筆記本 VM：
   1. 尋找在 VM 設定期間所建立的 SSH 金鑰。 或者，在 [Azure Machine Learning] 工作區中尋找金鑰，> 開啟 [計算] 索引標籤，> 在清單中找出 [筆記本 VM] > 開啟其屬性：從對話方塊複製金鑰。
   1. 將這些公開和私人 SSH 金鑰匯入到您的本機電腦。
   1. 使用它們來透過 SSH 連線到筆記本 VM。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.60

+ **新功能**
  + 引進了 FileDataset，它會參考您資料存放區或公用 url 中的單一或多個檔案。 檔案可以是任何格式。 FileDataset 可讓您將檔案下載或掛接至您的計算。 若要深入瞭解 FileDataset，請造訪 https://aka.ms/file-dataset 。
  + 已新增 PythonScript 步驟、Adla 步驟、Databricks 步驟、DataTransferStep 和 AzureBatch 步驟的管線 Yaml 支援

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + AutoArima 現在是僅供預覽的 suggestable 管線。
    + 改善預測的錯誤報表。
    + 在預測工作中使用自訂例外狀況，而不是泛型來改善記錄。
    + 已移除 max_concurrent_iterations 的檢查，小於反覆運算總數。
    + AutoML 模型現在會傳回 AutoMLExceptions
    + 此版本可改善自動化機器學習本機執行的執行效能。
  + **azureml-core**
    + 引進資料集。 get_all （工作區），其會傳回 `TabularDataset` 以 `FileDataset` 其註冊名稱做為索引鍵之和物件的字典。

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + 引進 `parition_format` 做為和的引數 `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` 。 每個資料路徑的分割區資訊將會根據指定的格式，解壓縮到資料行中。 ' {column_name} ' 會建立字串資料行，而 ' {column_name： yyyy/MM/dd/HH/mm/ss} ' 會建立 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來針對日期時間類型來解壓縮年、月、日、小時、分鐘和秒 Partition_format 應該從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設路徑為 '.。/USA/2019/01/01/data.csv '，其中分割區是依據國家和時間，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data.csv ' 會建立字串資料行 ' Country '，其值為 ' USA '，而 datetime 資料行 ' PartitionDate ' 的值為 ' 2019-01-01 '。
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + 引進 `partition_format` 做為和的引數 `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` 。 每個資料路徑的分割區資訊將會根據指定的格式，解壓縮到資料行中。 ' {column_name} ' 會建立字串資料行，而 ' {column_name： yyyy/MM/dd/HH/mm/ss} ' 會建立 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來針對日期時間類型來解壓縮年、月、日、小時、分鐘和秒 Partition_format 應該從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設路徑為 '.。/USA/2019/01/01/data.csv '，其中分割區是依據國家和時間，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data.csv ' 會建立字串資料行 ' Country '，其值為 ' USA '，而 datetime 資料行 ' PartitionDate ' 的值為 ' 2019-01-01 '。
    + `to_csv_files`和 `to_parquet_files` 方法已加入至 `TabularDataset` 。 這些方法會將 `TabularDataset` `FileDataset` 資料轉換成指定格式的檔案，以啟用與之間的轉換。
    + 儲存 Model. package （）所產生的 Dockerfile 時，會自動登入基底映射登錄。
    + 不再需要 ' gpu_support ';AML 現在會自動偵測並使用 nvidia docker 擴充功能（如果有的話）。 未來的版本將予以移除。
    + 已新增建立、更新和使用 PipelineDrafts 的支援。
    + 此版本可改善自動化機器學習本機執行的執行效能。
    + 使用者可以依名稱查詢執行歷程記錄中的計量。
    + 在預測工作中使用自訂例外狀況，而不是泛型來改善記錄。
  + **azureml-說明-模型**
    + 已將 feature_maps 參數新增至新的 MimicWrapper，讓使用者可以取得原始功能的說明。
    + [資料集上傳] 預設會針對 [上傳說明] 關閉，而且可以使用 [upload_datasets = True] 重新啟用。
    + 已將 "is_law" 篩選參數新增至說明清單和下載功能。
    + 將方法新增 `get_raw_explanation(feature_maps)` 至全域和本機說明物件。
    + 已將版本檢查新增至具有列印警告的 lightgbm （如果支援的版本低於）
    + 已優化批次處理說明時的記憶體使用量
    + AutoML 模型現在會傳回 AutoMLExceptions
  + **azureml-pipeline-core**
    + 已新增建立、更新和使用 PipelineDrafts 的支援-可用來維護可變的管線定義，並以互動方式使用它們來執行
  + **azureml-train-automl**
    + 已建立功能以安裝支援 gpu 的特定版本 pytorch v 1.1.0、 :::no-loc text="cuda"::: 工具組9.0、pytorch-轉換器，這是在遠端 python 執行時間環境中啟用經理 bert/XLNet 所需的元件。
  + **azureml-train-core**
    + 直接在 sdk 中（而不是伺服器端）發生某些超參數空間定義錯誤的早期失敗。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning 資料準備 SDK v 1.1.14
+ **Bug 修正和改善**
  + 已使用原始路徑和認證啟用寫入 ADLS/ADLSGen2。
  + 已修正導致 `include_path=True` 無法處理的 bug `read_parquet` 。
  + 已修正因例外狀況「 `to_pandas_dataframe()` 不正確屬性值： hostSecret」而造成的失敗。
  + 已修正無法在 Spark 模式的 DBFS 上讀取檔案的錯誤。

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.57
+ **新功能**
  + 已啟用 `TabularDataset` ，可供 AutomatedML 使用。 若要深入瞭解 `TabularDataset` ，請造訪 https://aka.ms/azureml/howto/createdatasets 。

+ **Bug 修正和改善**
  + **automl-用戶端-核心-nativeclient-android**
    + 已修正在以 pandas 資料框架的形式提供定型和/或驗證標籤（y 和 y_valid）時引發的錯誤，但不是 numpy 陣列。
    + 已更新介面來建立 `RawDataContext` ，只需要資料和 `AutoMLBaseSettings` 物件。
    +  允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。 -允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
  + **azure-cli-ml**
    + 您現在可以針對 Microsoft 產生的和客戶憑證，更新部署在 AKS 叢集上的評分端點的 TLS/SSL 憑證。
  + **azureml-automl-core**
    + 已修正 AutoML 中的問題，其中遺漏標籤的資料列不會正確移除。
    + 已改善 AutoML 中的錯誤記錄;完整的錯誤訊息現在一律會寫入記錄檔。
    + AutoML 已將其封裝釘選更新為包含 `azureml-defaults` 、 `azureml-explain-model` 和 `azureml-dataprep` 。 AutoML 不會再警告套件不符（ `azureml-train-automl` 套件除外）。
    + 修正了 `timeseries` cv 分割的大小不相等的問題，導致 bin 計算失敗。
    + 針對交叉驗證定型類型執行集團反復專案時，如果我們最終無法下載在整個資料集上定型的模型，我們就會在模型權數和正在送入投票集團的模型之間產生不一致的情況。
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
    + 已新增環境。 add_private_pip_wheel （），可讓您將私人自訂 python 封裝上傳 `whl` 至工作區，並安全地使用它們來建立/具體化環境。
    + 您現在可以針對 Microsoft 產生的和客戶憑證，更新部署在 AKS 叢集上的評分端點的 TLS/SSL 憑證。
  + **azureml-說明-模型**
    + 已新增參數，以將模型識別碼新增到上傳的說明。
    + 已 `is_raw` 將標記新增至記憶體中的說明並上傳。
    + 已新增 azureml-說明模型套件的 pytorch 支援和測試。
  + **azureml-opendatasets**
    + 支援偵測和記錄自動測試環境。
    + 已新增類別，讓我們依縣市和郵遞區號取得人口。
  + **azureml-pipeline-core**
    + 已將標籤屬性新增至輸入和輸出埠定義。
  + **azureml-telemetry**
    + 已修正不正確的遙測設定。
  + **azureml-train-automl**
    + 修正安裝程式失敗時的錯誤、安裝程式執行的「錯誤」欄位中未記錄錯誤，因此不會儲存在父執行「錯誤」中。
    + 已修正 AutoML 中的問題，其中遺漏標籤的資料列不會正確移除。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。
    + 允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
    + 現在 AutoMLStep 會將設定傳遞 `automl` 給後端，以避免變更或新增新的設定參數時發生任何問題。
    + AutoML 資料 Guardrail 現已開放公開預覽。 使用者會在定型之後看到資料 Guardrail 報表（適用于分類/回歸工作），而且也可以透過 SDK API 來存取它。
  + **azureml-train-core**
    + 已在 PyTorch 估計工具中新增 torch 1.2 支援。
  + **azureml-widgets**
    + 改善分類定型的混淆矩陣圖表。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning 資料準備 SDK v 1.1.12
+ **新功能**
  + 現在可以將字串清單當做方法的輸入傳入 `read_*` 。

+ **Bug 修正和改善**
  + 在 Spark 中執行時，的效能 `read_parquet` 已大幅改善。
  + 已修正 `column_type_builder` 在單一資料行具有不明確日期格式的情況下失敗的問題。

### <a name="azure-portal"></a>Azure 入口網站
+ **預覽功能**
  + [執行詳細資料] 頁面現在提供記錄檔和輸出檔案串流。 開啟預覽切換時，檔案會即時串流更新。
  + 在工作區層級設定配額的功能會以預覽形式發行。 AmlCompute 配額會在訂用帳戶層級配置，但我們現在可讓您在工作區之間散發該配額，並將其配置給公平共用和治理。 只要按一下工作區左側導覽列中的 [**使用方式 + 配額**] 分頁，然後選取 [**設定配額**] 索引標籤。請注意，您必須是訂用帳戶管理員，才能在工作區層級設定配額，因為這是跨工作區的作業。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.55

+ **新功能**
  + 以權杖為基礎的驗證現在支援對 AKS 上部署之評分端點的呼叫。 我們會繼續支援目前以金鑰為基礎的驗證，而且使用者可以一次使用其中一種驗證機制。
  + 能夠將虛擬網路（VNet）後方的 blob 儲存體註冊為數據存放區。

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 修正 CV 分割的驗證大小較小的 bug，並導致預測性和預測的事實與 true 圖表不正確。
    + 遠端執行預測工作的記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已修正如果前置處理旗標為 True 時的失敗 `Timeseries` 。
    + 讓一些預測資料驗證錯誤訊息更容易採取動作。
    + 藉由卸載和/或資料集的消極式載入來減少 AutoML 執行的記憶體耗用量，特別是在處理常式產生之間
  + **azureml-contrib-說明-模型**
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯
    + 小工具變更：自動安裝 `contrib` ，且不 `nbextension` 支援任何安裝/啟用-僅包含全域功能重要性的說明（例如 Permutative）
    + 儀表板變更：除了 [摘要] 頁面上的繪圖之外，也會顯示盒狀圖和 violin 繪圖 `beeswarm` -以較快的 rerendering 繪製在「 `beeswarm` 上-k」滑杆變更-有用的訊息，說明如何計算前 k 個可自訂的訊息，以取代未提供資料時的圖表
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
    + 已將 blob_cache_timeout 參數新增至 `Datastore.register_azure_blob_container` 。
    + 已將 save_to_directory 和 load_from_directory 方法新增至 azureml. 環境。
    + 已將「az ml 環境下載」和「az ml 環境 register」命令新增至 CLI。
    + 已新增環境。 add_private_pip_wheel 方法。
  + **azureml-說明-模型**
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
    + 已 `docstrings` 針對集團相關檔案新增。
    + 已將檔更新為適用于和的更適當語言 `max_cores_per_iteration``max_concurrent_iterations`
    + 遠端執行預測工作的記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已從管線筆記本移除 get_data `automlstep` 。
    + 已 `dataprep` 在中開始支援 `automlstep` 。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning 資料準備 SDK v 1.1.10

+ **新功能**
  + 您現在可以要求在特定資料行上執行特定的檢查器（例如長條圖、散佈圖等）。
  + 已將平行處理引數加入至 `append_columns` 。 若為 True，則會將資料載入記憶體中，但執行將會平行執行;如果為 False，則執行會串流處理，但會進行單一執行緒。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.53

+ **新功能**
  + 自動化 Machine Learning 現在支援在遠端計算目標上定型 ONNX 模型
  + Azure Machine Learning 現在提供從先前的執行、檢查點或模型檔案繼續定型的功能。
    + 瞭解如何[使用估算器從上一次執行繼續定型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/training/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug 修正和改善**
  + **automl-用戶端-核心-nativeclient-android**
    + 修正轉換（bug 連結）之後遺失資料行類型的 bug;
    + 允許 y_query 為在開頭（#459519）包含無（s）的物件類型。
  + **azure-cli-ml**
    + CLI 命令「模型部署」和「服務更新」現在接受參數、設定檔或兩者的組合。 參數的優先順序高於檔案中的屬性。
    + 現在可以在註冊之後更新模型描述
  + **azureml-automl-core**
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
    + 新增要在 AutoML 估算器中使用之 NimbusML 估算器 & 管線的支援。
    + 修正集團選取程式中不必要地增加所產生集團的 bug，即使分數仍然不變也一樣。
    + 允許跨 CV 分割重複使用一些 featurizations，以進行預測工作。 這可加快執行安裝的執行時間，其方式是 featurizations 延遲和滾動時段等昂貴 n_cross_validations 的。
    + 解決時間超出 pandas 支援的時間範圍時的問題。 如果時間小於 pd，我們現在會引發 DataException。Timestamp. min 或大於 pd。時間戳記。最大值
    + 預測現在會在定型和測試集中允許不同的頻率（如果可以對齊的話）。 例如，「每季從一月開始」和「10月開始的季度」可以對齊。
    + 屬性 "parameters" 已加入至 TimeSeriesTransformer。
    + 移除舊的例外狀況類別。
    + 在預測工作中， `target_lags` 參數現在會接受單一整數值或整數清單。 如果已提供整數，則只會建立一個 lag。 如果提供了清單，將會採用延遲的唯一值。 target_lags = [1，2，2，4] 將建立延遲1、2和4個期間。
    + 修正轉換（bug 連結）之後遺失資料行類型的 bug;
    + 在中 `model.forecast(X, y_query)` ，允許 y_query 是一種物件類型，其在開頭（#459519）包含無（s）。
    + 將預期的值新增至 `automl` 輸出
  + **azureml-contrib-datadrift**
    +  範例筆記本的改良功能，包括切換至 azureml-opendatasets，而不是 azureml-contrib opendatasets 和效能改善（在充實資料時）
  + **azureml-contrib-說明-模型**
    + Azureml-contrib 中原始功能重要性的淺轉換引數固定說明-說明-模型套件
    + 已針對 AzureML-contrib-說明模型套件，在影像說明中新增 segmentations 至影像說明
    + 新增 LimeExplainer 的 scipy sparse 支援
    + 已新增至模擬說明的時機 `batch_size` `include_local=False` ，適用于批次中的串流全域解釋，以改善 DecisionTreeExplainableModel 的執行時間
  + **azureml-contrib-featureengineering**
    + 呼叫 set_featurizer_timeseries_params （）的修正： dict 數值型別變更和 null 檢查-新增 featurizer 的筆記本 `timeseries`
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能
    + 已修正資料存放區上傳的錯誤（如果 `target_path` 以開頭的位置建立空白資料夾）`/`
    + 已修正 `deepcopy` ServicePrincipalAuthentication 中的問題。
    + 已將「az ml 環境 show」和「az ml 環境 list」命令新增至 CLI。
    + 環境現在支援將 base_dockerfile 指定為已經建立 base_image 的替代方法。
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已淘汰。
    + 現在可以在註冊之後更新模型描述
    + 錯誤修正：模型和映射刪除現在提供有關抓取上游物件的詳細資訊（如果因為上游相依性而刪除失敗）。
    + 已修正為某些環境建立工作區時所發生部署的空白持續時間列印的 bug。
    + 改良的工作區建立失敗例外狀況。 使用者看不到 [無法建立工作區]。 找不到 ...」作為訊息，並改為查看實際的建立失敗。
    + 在 AKS webservices 中新增權杖驗證的支援。
    + 將 `get_token()` 方法新增至 `Webservice` 物件。
    + 已新增 CLI 支援來管理機器學習資料集。
    + `Datastore.register_azure_blob_container`現在可以選擇採用 `blob_cache_timeout` 值（以秒為單位），以設定 blobfuse 的掛接參數，以啟用此資料存放區的快取到期時間。 預設值為 [沒有超時]，也就是讀取 blob 時，它會保留在本機快取中，直到作業完成為止。 大部分的作業會偏好此設定，但某些作業需要從大型資料集讀取更多資料，而不能容納其節點。 針對這些作業，微調此參數將有助於成功完成。 微調此參數時請注意：設定值過低可能會導致效能不佳，因為 epoch 中使用的資料可能會在再次使用之前過期。 這表示所有讀取作業都是從 blob 儲存體（也就是網路）而不是本機快取完成，這會對定型時間造成負面影響。
    + 現在可以在註冊之後正確地更新模型描述
    + 模型和影像刪除現在提供相依于它們的上游物件詳細資訊，導致刪除失敗
    + 使用 mlflow 來改善遠端執行的資源使用率。
  + **azureml-說明-模型**
    + Azureml-contrib 中原始功能重要性的淺轉換引數固定說明-說明-模型套件
    + 新增 LimeExplainer 的 scipy sparse 支援
    + 已新增圖形線性說明包裝函式，以及另一個層級至表格式說明以說明線性模型
    + 如需說明模型程式庫中的模擬說明，請修正當 sparse 資料輸入 include_local = False 時的錯誤
    + 將預期的值新增至 `automl` 輸出
    + 已修正提供轉換引數以取得原始功能重要性時的排列功能重要性
    + 已新增至模擬說明的時機 `batch_size` `include_local=False` ，適用于批次中的串流全域解釋，以改善 DecisionTreeExplainableModel 的執行時間
    + 針對模型可解釋性程式庫，已修正黑箱 explainers，其中 pandas 資料框架輸入是預測的必要項
    + 已修正 `explanation.expected_values` 有時會傳回 float，而不是包含浮點數的清單的錯誤（bug）。
  + **azureml-mlflow**
    + 改善 mlflow 的效能 set_experiment （experiment_name）
    + 修正使用 InteractiveLoginAuthentication for mlflow tracking_uri 的 bug
    + 使用 mlflow 來改善遠端執行的資源使用率。
    + 改善 azureml mlflow 套件的檔
    + Patch bug，其中 mlflow log_artifacts （"my_dir"）會將成品儲存在 "my_dir/<成品路徑>" 之下，而不是 "<成品路徑>"
  + **azureml-opendatasets**
    + `pyarrow` `opendatasets` 因為新引進的記憶體問題，所以將的釘選到舊版本（<0.14.0）。
    + 將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開啟的資料集類別註冊到 Azure Machine Learning 的工作區，並順暢地利用 AML 資料集功能。
    + 大幅提升非 SPARK 版本的 NoaaIsdWeather 豐富效能。
  + **azureml-pipeline-steps**
    + DatabricksStep 中的輸入和輸出現在支援 DBFS 資料存放區。
    + 已更新有關輸入/輸出之 Azure Batch 步驟的檔。
    + 在」已 azurebatchstep 中，將*delete_batch_job_after_finish*預設值變更為*true*。
  + **azureml-telemetry**
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
  + **azureml-說明-模型**
    + 已更新 interpretability 物件的線上檔。
    + 已新增至模擬說明的時機 `batch_size` `include_local=False` ，適用于批次中的串流全域說明，以改善模型可解釋性程式庫的 DecisionTreeExplainableModel 執行時間。
    + 修正了 `explanation.expected_values` 有時候會傳回浮點數的問題，而不是包含浮點數的清單。
    + 在說明模型程式庫中，將預期的值新增至模擬 `automl` 說明的輸出。
    + 已修正提供轉換引數以取得原始功能重要性時的排列功能重要性。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能。
    + 已修正資料存放區上傳的問題，如果以啟動，則會建立空的資料夾 `target_path` `/` 。
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
    + 已取代 `conda_dependencies_file_path` 的參數，並 `pip_requirements_file_path` `conda_dependencies_file` 分別支援和 `pip_requirements_file` 。
  + **azureml-opendatasets**
    + 大幅提升非 SPARK 版本的 NoaaIsdWeather 豐富效能。

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning 資料準備 SDK v 1.1。8

+ **新功能**
 + 現在可以反復查看資料流程物件，產生一連串的記錄。 請參閱的檔 `Dataflow.to_record_iterator` 。
  + 現在可以反復查看資料流程物件，產生一連串的記錄。 請參閱的檔 `Dataflow.to_record_iterator` 。

+ **Bug 修正和改善**
 + 提升 DataPrep SDK 的穩定性。
 + 改善 pandas 資料框架與非字串資料行索引的處理。
 + 改善 `to_pandas_dataframe` 資料集中的效能。
 + 修正了在多節點環境中執行時，Spark 執行資料集失敗的錯誤。
  + 提升 DataPrep SDK 的穩定性。
  + 改善 pandas 資料框架與非字串資料行索引的處理。
  + 改善 `to_pandas_dataframe` 資料集中的效能。
  + 修正了在多節點環境中執行時，Spark 執行資料集失敗的錯誤。

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning 資料準備 SDK v 1.1。7

我們已還原改善效能的變更，因為這會導致某些客戶使用 Azure Databricks 的問題。 如果您在 Azure Databricks 上遇到問題，可以使用下列其中一種方法升級至版本1.1.7：
1. 執行此腳本進行升級：`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
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
  + Model。 register 現在支援使用參數，將多個個別檔案註冊為單一模型 `child_paths` 。

+ **預覽功能**
    + 計分 explainers 現在可以選擇性地儲存 conda 和 pip 資訊，以提供更可靠的序列化和還原序列化。
    + 自動功能選取器的錯誤修正。
    + 已更新 build_image mlflow 至新的 api，並修補新的執行所公開的 bug。

+ **Bug 修正和改善**
  + 已 `paramiko` 從 azureml 核心移除相依性。 已新增舊版計算目標附加方法的取代警告。
  + 提升執行效能。 create_children
  + 在 [模擬具有二元分類器的說明] 中，修正當教師機率用於縮放圖形值時的機率順序。
  + 已改善自動化機器學習的錯誤處理和訊息。
  + 已修正自動化機器學習的反復專案超時問題。
  + 已改善自動化機器學習的時間序列轉換效能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning 資料準備 SDK v 1.1。6

+ **新功能**
  + 已新增 top 值（ `SummaryFunction.TOPVALUES` ）和底端值（）的摘要函數 `SummaryFunction.BOTTOMVALUES` 。

+ **Bug 修正和改善**
  + 大幅改善的效能 `read_pandas_dataframe` 。
  + 已修正會導致 `get_profile()` 指向二進位檔案的資料流程失敗的錯誤。
  + 公開 `set_diagnostics_collection()` 以允許以程式設計方式啟用/停用遙測集合。
  + 已變更的行為 `get_profile()` 。 現在會忽略 Min、Mean、Std 和 Sum 的 NaN 值，這與 Pandas 的行為一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.43

+ **新功能**
  + Azure Machine Learning 現在提供熱門機器學習和資料分析架構 Scikit-learn 的一流支援。 使用者可以使用[ `SKLearn` 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)，輕鬆地訓練和部署 scikit-learn 學習模型。
    + 瞭解如何使用[scikit-learn 執行超參數微調-瞭解如何使用 HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 已新增在管線中建立 ModuleStep 以及模組和 ModuleVersion 類別的支援，以管理可重複使用的計算單位。
  + ACI webservices 現在支援透過更新的持續性 scoring_uri。 Scoring_uri 將從 IP 變更為 FQDN。 您可以藉由在 deploy_configuration 上設定 dns_name_label，來設定 FQDN 的 Dns 名稱標籤。
  + 自動化機器學習服務的新功能：
    + 適用于預測的 STL featurizer
    + 已啟用 Kmeans] 叢集以進行功能掃描
  + AmlCompute 配額核准的速度變快了！ 我們現在已自動化在閾值內核准配額要求的程式。 如需有關配額如何工作的詳細資訊，請瞭解[如何管理配額](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)。

+ **預覽功能**
    + 透過 azureml MLflow 套件（[範例筆記本](https://aka.ms/azureml-mlflow-examples)）與[MLflow](https://mlflow.org) 1.0.0 追蹤進行整合。
    + 提交 Jupyter 筆記本作為執行。 [API 參考檔](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 透過 azureml-contrib-datadrift package （[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/monitor-models/data-drift)）公開預覽[資料漂移](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class))偵測器。 資料漂移是模型精確度隨著時間而下降的其中一個主要原因。 在生產環境中提供模型的資料與定型模型的資料不同時，就會發生此情況。 AML 資料漂移偵測器可協助客戶監視資料漂移，並在偵測到漂移時傳送警示。

+ **重大變更**

+ **Bug 修正和改善**
  + RunConfiguration 載入和儲存支援針對先前的行為指定具有完整回溯相容性的完整檔案路徑。
  + 已在 ServicePrincipalAuthentication 中新增快取，預設為關閉。
  + 以相同的計量名稱來啟用多個繪圖的記錄功能。
  + 模型類別現在已從 azureml 核心（）正確地匯入 `from azureml.core import Model` 。
  + 在管線步驟中， `hash_path` 參數現在已被取代。 新的行為是雜湊完成 source_directory，但 amlignore 或. .gitignore 中列出的檔案除外。
  + 在管線封裝中， `get_all` 不同 `get_all_*` 的和方法分別被取代為 `list` 和 `list_*` 。
  + get_run 不再需要匯入類別，再傳回原始的執行類型。
  + 已修正某些 WebService 更新呼叫並未觸發更新的問題。
  + AKS webservices 的計分超時時間應介於5毫秒到300000毫秒之間。 評分要求的允許 scoring_timeout_ms 上限已從1分鐘 i 藉為5分鐘。
  + LocalWebservice 物件現在具有 `scoring_uri` 和 `swagger_uri` 屬性。
  + 已移動輸出目錄建立，並從使用者進程輸出目錄上傳。 已啟用執行歷程記錄 SDK，以在每個使用者進程中執行。 這應該會解決分散式定型執行所遇到的一些同步處理問題。
  + 從使用者進程名稱寫入的 azureml 記錄檔名稱現在會包含進程名稱（僅適用于分散式訓練）和 PID。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning 資料準備 SDK v 1.1。5

+ **Bug 修正和改善**
  + 針對具有2位數年份格式的已轉譯日期時間值，有效年份的範圍已更新為符合 Windows 可能發行的版本。 範圍已從1930-2029 變更為1950-2049。
  + 當讀取檔案和設定時 `handleQuotedLineBreaks=True` ，將會被 `\r` 視為新行。
  + 已修正 `read_pandas_dataframe` 在某些情況下導致失敗的錯誤（bug）。
  + 改善的效能 `get_profile` 。
  + 改善的錯誤訊息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning 資料準備 SDK 1.1。4

+ **新功能**
  + 您現在可以使用下列運算式語言函式，將日期時間值解壓縮並剖析成新的資料行。
    + `RegEx.extract_record()`將 datetime 元素解壓縮至新的資料行。
    + `create_datetime()`從個別的 datetime 元素建立 datetime 物件。
  + 呼叫時 `get_profile()` ，您現在可以看到分量資料行標示為（est.），以清楚指出這些值是近似值。
  + 從 Azure Blob 儲存體讀取時，您現在可以使用 * * 萬用字元。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Bug 修正**
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

### <a name="azure-portal"></a>Azure 入口網站

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
+ 已在 [Azure Machine Learning] 工作區的 [總覽] 頁面上，新增從檔案下載 config.js的功能。
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

使用筆記本 VM 做為適用于 Jupyter 筆記本的安全、符合企業需求的裝載環境，您可以在其中編寫機器學習服務實驗、將模型部署為 web 端點，以及使用 Python Azure Machine Learning SDK 來執行其他所有作業。它提供數個功能：
+ [快速加速預先設定的筆記本 VM](tutorial-1st-experiment-sdk-setup.md)  具有最新版本的 Azure Machine Learning SDK 和相關套件。
+ 存取是透過經過驗證的技術（例如 HTTPS、Azure Active Directory 驗證和授權）來保護。
+ 在 Azure Machine Learning 工作區 blob 儲存體帳戶中，筆記本和程式碼的可靠雲端儲存體。您可以安全地刪除您的筆記本 VM，而不會遺失您的工作。
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

+ 功能優化，可動態加入以 :::no-loc text="featurizers"::: 進行效能優化。 新增 :::no-loc text="featurizers"::: ：工作內嵌、辨識項權數、目標編碼、文字目標編碼、叢集距離
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

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)引進了以加入新版本的已發行管線，同時維持相同的端點。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 資料準備 SDK v 1.1。2

注意：資料準備 Python SDK 將不再安裝 `numpy` 和 `pandas` 封裝。 請參閱[更新的安裝指示](https://github.com/Microsoft/AMLDataPrepDocs)。

+ **新功能**
  + 您現在可以使用 [資料透視轉換]。
    + 操作指南：[資料透視筆記本](https://aka.ms/aml-data-prep-pivot-nb)
  + 您現在可以在原生函式中使用正則運算式。
    + 範例：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 您現在可以 `to_upper`   `to_lower`   在運算式語言中使用和函數。
  + 您現在可以看到資料設定檔中每個資料行的唯一值數目。
  + 對於一些常用的讀取器步驟，您現在可以傳入 `infer_column_types` 引數。 如果設定為，則 `True` 資料準備會嘗試偵測並自動轉換資料行類型。
    + `inference_arguments`現在已被取代。
  + 您現在可以呼叫 `Dataflow.shape` 。

+ **Bug 修正和改善**
  + `keep_columns` 現在會接受額外的選擇性引數 `validate_column_exists` ，以檢查的結果是否 `keep_columns` 會包含任何資料行。
  + 所有讀取器步驟（讀取自檔案）現在都接受額外的選擇性引數 `verify_exists` 。
  + 改善從 pandas 資料框架讀取及取得資料設定檔的效能。
  + 已修正從資料流程切割單一步驟失敗且具有單一索引的 bug。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 入口網站
  + 您現在可以在現有的遠端計算叢集上重新提交現有的腳本執行。
  + 您現在可以在 [管線] 索引標籤上，使用新的參數來執行已發佈的管線。
  + [執行詳細資料] 現在支援新的快照集檔案檢視器。 當您提交特定執行時，可以查看目錄的快照集。 您也可以下載已提交的筆記本以開始執行。
  + 您現在可以從 Azure 入口網站取消父系執行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.23

+ **新功能**
  + Azure Machine Learning SDK 現在支援 Python 3.7。
  + Azure Machine Learning DNN 估算器現在提供內建的多版本支援。 例如， `TensorFlow`   估計工具現在接受 `framework_version` 參數，而使用者可以指定 ' 1.10 ' 或 ' 1.12 ' 的版本。 如需目前 SDK 版本所支援的版本清單，請在所要 `get_supported_versions()` 的 framework 類別上呼叫（例如， `TensorFlow.get_supported_versions()` ）。
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
  + 您可以 `verify_has_data()` 在資料流程上呼叫，以檢查資料流程是否會在執行時產生記錄。

+ **Bug 修正和改善**
  + 您現在可以在數值資料行設定檔的長條圖中，指定要使用的 bin 數目。
  + `read_pandas_dataframe`轉換現在需要資料框架具有字串或位元組類型的資料行名稱。
  + 已修正轉換中的錯誤 `fill_nulls` ，如果遺漏資料行，就不會正確填入值。

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

+ **錯誤 (bug) 修正**
  + 已修正 API 變更所造成的服務主體驗證問題。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.17

+ **新功能**

  + Azure Machine Learning 現在為熱門的 DNN 架構 Chainer 提供了一流的支援。 使用 [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 類別使用者可以輕鬆地定型和部署 Chainer 模型。
    + 瞭解如何[使用 ChainerMN 執行分散式訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/training/distributed-chainer/distributed-chainer.ipynb)
    + 瞭解如何[使用 HyperDrive 搭配 Chainer 執行超參數微調](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning 管線已新增根據資料存放區修改來觸發管線執行的功能。 管線[排程筆記本](https://aka.ms/pl-schedule)會更新以展示這項功能。

+ **Bug 修正和改善**
  + 我們已在 Azure Machine Learning 管線中新增支援，以便在提供給[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)的[RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)上，將 source_directory_data_store 屬性設定為所需的資料存放區（例如 blob 儲存體）。 根據預設，步驟會使用 Azure 檔案儲存體作為備份資料存放區，而當同時執行大量步驟時，可能會遇到節流問題。

### <a name="azure-portal"></a>Azure 入口網站

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
  +  DataTranferStep 已更新，可與 Azure SQL Database 和適用于于 postgresql 的 Azure 資料庫（[筆記本](https://aka.ms/pl-data-trans)）搭配使用。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 資料準備現在支援使用資料存放區讀取 Azure SQL Database。

+ **變更**
  + 改善大型資料上某些作業的記憶體效能。
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**：
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
      + 如果您正在執行「az login」，然後使用 azureml-sdk，則 SDK 將再執行一次瀏覽器或裝置程式碼登入。 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您要使用服務主體進行「az login」以進行自動化，我們建議使用_ServicePrincipalAuthentication_類別，因為 azureml sdk 不會使用 Azure CLI 所建立的認證狀態。

+ **Bug 修正**：此版本大多包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Bug 修正**
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

+ **Bug 修正**
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

+ **Bug 修正**
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


+ **已**
  + 針對機器學習管線：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) \(英文\) 現在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) \(英文\) 現在可從 SQL 資料來源複製或複製到其中
    + 排程 SDK 中的功能，以建立和更新執行已發佈管線的排程

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning 資料準備 SDK v0.5.2
+ **重大變更**
  * `SummaryFunction.N` 已重新命名為 `SummaryFunction.Count`。

+ **Bug 修正**
  * 從遠端執行讀取和寫入資料存放區時，請使用最新的 AML 執行權杖。 以前，如果在 Python 中更新 AML 執行權杖，將不會使用已更新的 AML 執行權杖來更新資料準備執行階段。
  * 其他更清楚的錯誤訊息
  * 當 Spark 使用 `Kryo` 序列化時，to_spark_dataframe() 將不再損毀
  * 值計數偵測器現在可以顯示 1000 個以上的唯一值
  * 如果原始資料流程沒有名稱，隨機分割就不會再失敗

+ **詳細資訊**
  * [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文件和 Notebook
+ ML 管線
  + 適用於開始使用管線、批次範圍和樣式移轉範例之全新和更新的 Notebook：https://aka.ms/aml-pipeline-notebooks
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
  * azureml.train.widgets** 命名空間已移至 azureml.widgets**。
  * azureml.core.compute.AmlCompute** 取代了下列類別 - azureml.core.compute.BatchAICompute** 和 azureml.core.compute.DSVMCompute**。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

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

+ **Bug 修正**
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

### <a name="azure-portal"></a>Azure 入口網站
Azure Machine Learning 的 Azure 入口網站具有下列更新：
  * 用於已發佈管線的新 [管線]**** 索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更**
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets()** 取代為 Workspace.compute_targets**。
  * *Run.get_context* 取代 *Run.get_submitted_run*。 第二個方法將會在後續版本中移除。
  * *PipelineData* 類別現在預期將 datastore 物件而不是 datastore_name 當作參數。 同樣地，*管線* 接受 default_datastore，而不是 default_datastore_name。

+ **新功能**
  * Azure Machine Learning 管線[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines) 現在使用 MPI 步驟。
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

+ **Bug 已修正**
  * 64 位元不帶正負號的整數溢位現在會在 Linux 上正確處理
  * 已在 smart_read 中修正純文字檔案不正確的文字標籤
  * 字串資料行類型現在會出現在 [計量] 檢視中
  * 類型計數現在已修正，可顯示對應至單一 FieldType，而不是個別項目的 ValueKinds
  * 當路徑以字串方式提供時，Write_to_csv 不會再失敗
  * 使用 Replace 時，保留「尋找」空白將不會再失敗

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新功能**
  * 建立新工作區時的多個租用戶支援。

+ **修正的 Bug**
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
  * 已修正造成系統停止回應的 macOS 上的定型 bug。
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

+ **修正錯誤：**
  * 適用於任何 Tornado 版本，不必降級 Tornado 版本
  * 值計數代表所有值，不只是前三個值

## <a name="2018-09-public-preview-refresh"></a>2018-09 (公開預覽刷新版)

Azure Machine Learning 的新重新整理版本：深入瞭解此版本：https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning](overview-what-is-azure-ml.md) 概觀。
