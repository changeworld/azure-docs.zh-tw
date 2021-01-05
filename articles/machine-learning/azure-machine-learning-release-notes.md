---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 的最新更新，以及機器學習和資料準備 Python Sdk。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 09/10/2020
ms.openlocfilehash: 695702c04e2eeb74ee27b7d4276a3be94d9d1cf7
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881815"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning 版本資訊

在本文中，您將瞭解 Azure Machine Learning 版本。  如需完整的 SDK 參考內容，請造訪 Azure Machine Learning 的 [**適用于 Python 的主要 SDK**](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 參考頁面。

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio 筆記本體驗 (12 月更新) 
+ **新功能**
  + 使用者名稱搜尋。 使用者現在可以搜尋儲存在工作區中的所有檔案。
  + Markdown 每個筆記本的並列支援資料格。 在筆記本資料格中，使用者現在可以選擇並排查看轉譯的 markdown 和 markdown 語法。
  + 儲存格狀態列。 狀態列會指出程式碼資料格的狀態、資料格執行是否成功，以及執行所花的時間。 
   
+ **Bug 修正和改善**
  + 改善的頁面載入時間
  + 提升效能 
  + 提升速度和核心可靠性
  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK for Python v 1.19。0
+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已將測試資料的實驗性支援新增至 AutoMLStep。
    + 已新增測試集內嵌功能的初始核心執行。
    + 已將參考移至 sklearn，以直接相依于 joblib。
    + 引進新的 AutoML 工作類型「映射-實例分割」。
  + **azureml-automl-runtime**
    + 已新增測試集內嵌功能的初始核心執行。
    + 當文字資料行中的所有字串長度剛好為1個字元時，TfIdf 的字組語法 featurizer 將無法運作，因為它的 tokenizer 會忽略少於2個字元的字串。 目前的程式碼變更將允許 AutoML 處理此使用案例。
    + 引進新的 AutoML 工作類型「映射-實例分割」。
  + **azureml-contrib-automl-dnn-nlp**
    + 新 dnn-nlp 封裝的初始 PR
  + **azureml-contrib-automl-dnn-願景**
    + 引進新的 AutoML 工作類型「映射-實例分割」。
  + **azureml-contrib-automl-管線-步驟**
    + 這個新的封裝負責建立許多模型定型/推斷案例所需的步驟。 -它也會將定型/推斷程式碼移至 azureml. automl 套件，以便任何未來的修正都會透過策劃環境版本自動提供。
  + **azureml-contrib-dataset**
    + 引進新的 AutoML 工作類型「映射-實例分割」。
  + **azureml-core**
    + 已新增測試集內嵌功能的初始核心執行。
    + 修正 azureml 核心封裝中檔的 x 警告
    + SDK 中命令支援功能的 Doc 字串修正
    + 正在將命令屬性加入至 RunConfiguration。 這項功能可讓使用者透過 AzureML SDK 在計算上執行實際的命令或可執行檔。
    + 使用者可以在指定該實驗的識別碼時，刪除空的實驗。
  + **azureml-dataprep**
    + 已新增使用 Scala 2.12 建立之 Spark 的資料集支援。 這會新增至現有的2.11 支援。
  + **azureml-mlflow**
    + AzureML-MLflow 新增遠端腳本的安全防護，以避免提早終止提交的執行。
  + **azureml-pipeline-core**
    + 修正針對透過 UI 所建立的管線端點設定預設管線時的錯誤（bug）
  + **azureml-pipeline-steps**
    + 已將測試資料的實驗性支援新增至 AutoMLStep。
  + **azureml-tensorboard**
    + 修正 azureml 核心封裝中檔的 x 警告
  + **azureml-train-automl-client**
    + 已將測試資料的實驗性支援新增至 AutoMLStep。
    + 已新增測試集內嵌功能的初始核心執行。
    + 引進新的 AutoML 工作類型「映射-實例分割」。
  + **azureml-train-automl-runtime**
    + 已新增測試集內嵌功能的初始核心執行。
    + 如果使用 validation_size 設定來定型 AutoML 模型，請修正最佳 AutoML 模型的原始說明的計算。
    + 已將參考移至 sklearn，以直接相依于 joblib。
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric ( # A1 現在應更快速完成
    + 改進 HyperDrive SDK 中的錯誤處理。
    +  已淘汰所有的估算器類別，以利使用 ScriptRunConfig 來設定實驗執行。 已淘汰的類別包括：
        + MMLBaseEstimator
        + 評估工具
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + 已淘汰使用 Nccl 和 Gloo 做為估算器類別的有效輸入類型，以配合使用 PyTorchConfiguration 與 ScriptRunConfig。
    + 已淘汰使用 Mpi 作為估算器類別的有效輸入類型，以配合搭配使用 MpiConfiguration 與 ScriptRunConfig。
    + 正在將命令屬性加入至 runconfiguration。 這項功能可讓使用者透過 AzureML SDK 在計算上執行實際的命令或可執行檔。

    +  已淘汰所有的估算器類別，以利使用 ScriptRunConfig 來設定實驗執行。 已淘汰的類別包括： + MMLBaseEstimator + 估算器 + PyTorch + TensorFlow + Chainer + SKLearn
    + 已淘汰使用 Nccl 和 Gloo 做為估算器類別的有效輸入類型，以配合使用 PyTorchConfiguration 與 ScriptRunConfig。 
    + 已淘汰使用 Mpi 作為估算器類別的有效輸入類型，以配合搭配使用 MpiConfiguration 與 ScriptRunConfig。

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio 筆記本體驗 (11 月更新) 
+ **新功能**
   + 原生終端機。 使用者現在可以透過[整合式終端](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#terminal)機存取整合式終端機，以及 Git 操作。
  + 複製資料夾 
  + 計算下拉式清單的成本 
  + 離線計算 Pylance 

+ **Bug 修正和改善**
  + 改善的頁面載入時間
  + 提升效能 
  + 提升速度和核心可靠性
  + 大型檔案上傳。 您現在可以上傳檔案 >95mb

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK for Python v 1.18。0
+ **Bug 修正和改善**
  + **azureml-automl-core**
    +  藉由允許以高斯雜訊填補，來改善短時間序列的處理。
  + **azureml-automl-runtime**
    + 如果 DateTime 資料行具有 OutOfBoundsDatetime 值，則擲回 ConfigException
    + 藉由允許以高斯雜訊填補，來改善短時間序列的處理。
    + 確定每個文字資料行都可以根據該文字資料行中字串的長度，利用含有 n 個語法的字元轉換
    + 針對在使用者的本機計算上執行的 AutoML 實驗，提供最佳模式的原始功能說明
  + **azureml-core**
    + 釘選套件： pyjwt，以避免在即將推出的版本中提取中斷的版本。
    + 如果這類實驗存在或新的實驗，建立實驗將會傳回具有相同指定名稱的作用中或上次封存的實驗。
    + 依名稱呼叫 get_experiment 會傳回使用該指定名稱的作用中或上次封存的實驗。
    + 使用者無法在重新開機時重新命名實驗。
    + 改進的錯誤訊息，以在資料集不正確地傳遞至實驗 (例如 ScriptRunConfig) 時，包含可能的修正程式。 
    + 的改進檔， `OutputDatasetConfig.register_on_complete` 包括名稱已存在時所發生的行為。
    + 指定可能與常見環境變數衝突的資料集輸入和輸出名稱，現在會產生警告
    + `grant_workspace_access`註冊資料存放區時的重新用途參數。 將它設定為 `True` ，以從 Machine Learning Studio 存取虛擬網路背後的資料。
      [深入了解](./how-to-enable-studio-virtual-network.md)
    + 已調整連結服務 API。 我們不會提供資源識別碼，而是在設定中定義3個不同的參數 sub_id、rg 和名稱。
    + 為了讓客戶能夠自行解決權杖損毀問題，請啟用工作區權杖同步處理成為公用方法。
    + 這種變更可讓您使用空字串做為 script_param 的值
  + **azureml-train-automl-client**
    +  藉由允許以高斯雜訊填補，來改善短時間序列的處理。
  + **azureml-train-automl-runtime**
    + 如果 DateTime 資料行具有 OutOfBoundsDatetime 值，則擲回 ConfigException
    + 已新增支援，以針對在使用者的本機計算上執行的 AutoML 實驗提供最佳模型的原始功能說明
    + 藉由允許以高斯雜訊填補，來改善短時間序列的處理。
  + **azureml-train-core**
    + 這種變更可讓您使用空字串做為 script_param 的值
  + **azureml-定型-restclients-hyperdrive**
    + 讀我檔案已變更為提供更多內容
  + **azureml-widgets**
    + 針對 widget 將字串支援新增至圖表/平行座標程式庫。

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>影像實例分割的資料標記 (多邊形注釋)  (preview) 

現在可以使用資料標籤中的影像實例分割 (多邊形注釋) 專案類型，讓使用者可以在影像中物件的輪廓周圍繪製和標注多邊形。 使用者將能夠將某個類別和多邊形指派給影像內感興趣的每個物件。

深入瞭解 [影像實例分割標記](how-to-label-images.md)。



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK for Python v 1.17.0 或
+ **新範例**
  + 提供範例的新社區導向存放庫，網址為： https://github.com/Azure/azureml-examples
+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 修正 get_output 可能引發 XGBoostError 的問題。
  + **azureml-automl-runtime**
    + AutoML 所建立的時間/行事曆功能現在會有前置詞。
    + 已修正在針對分類資料集定型 StackEnsemble 時，已啟用大量類別和多取樣的 IndexError 時發生的。
    + 修正 VotingRegressor 預測在 refitting 模型之後可能不正確的問題。
  + **azureml-core**
    + 已新增有關 AKS 部署設定和 Azure Kubernetes Service 概念之間關聯性的額外詳細資料。
    + 支援環境用戶端標籤。 使用者可以加上標籤，並依標籤參考環境。
  + **azureml-dataprep**
    + 使用目前不支援的 Spark 搭配 Scala 2.12 時，有更好的錯誤訊息。
  + **azureml-explain-model**
    + Azureml-說明模型套件已正式淘汰
  + **azureml-mlflow**
    + 已解決 mlflow 中的錯誤。請針對未正確處理完成狀態的 azureml 後端執行。
  + **azureml-pipeline-core**
    + 新增支援以建立、列出及取得以一個管線端點為基礎的管線排程。
    +  使用不正確使用範例改善了 PipelineData.as_dataset 的檔-使用 PipelineData.as_dataset 不正確，現在會導致擲回 ValueException
    + 已將 HyperDriveStep 管線筆記本變更為直接在 HyperDriveStep 執行之後，在 PipelineStep 中註冊最佳模型。
  + **azureml-pipeline-steps**
    + 已將 HyperDriveStep 管線筆記本變更為直接在 HyperDriveStep 執行之後，在 PipelineStep 中註冊最佳模型。
  + **azureml-train-automl-client**
    + 修正 get_output 可能引發 XGBoostError 的問題。

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio 筆記本體驗 (10 月更新) 
+ **新功能**
  + [完整的虛擬網路支援](./how-to-enable-studio-virtual-network.md)
  + [焦點模式](./how-to-run-jupyter-notebooks.md#focus-mode)
  + 儲存筆記本 Ctrl-S
  + 行號

+ **Bug 修正和改善**
  + 改進速度和核心可靠性
  + Jupyter 小工具 UI 更新

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK for Python v 1.16。0
+ **Bug 修正和改善**
  + **azure-cli-ml**
    + >akswebservice 和 AKSEndpoints 現在支援 pod 層級的 CPU 和記憶體資源限制。 `--cpu-cores-limit` `--memory-gb-limit` 在適用的 CLI 呼叫中，設定和旗標可以使用這些選擇性限制
  + **azureml-core**
    + 釘選 azureml 核心直接相依性的主要版本
    + >akswebservice 和 AKSEndpoints 現在支援 pod 層級的 CPU 和記憶體資源限制。 [Kubernetes 資源和限制](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)的詳細資訊
    + 已更新 run.log_table，以允許記錄個別資料列。
    + 已新增靜態方法 `Run.get(workspace, run_id)` ，以只使用工作區取出執行 
    + 已新增實例方法 `Workspace.get_run(run_id)` 以取得工作區中的執行
    + 執行設定中的命令屬性，可讓使用者提交命令，而不是腳本 & 引數。
  + **azureml-interpret**
    + 修正了 azureml 中用戶端 is_raw 旗標行為的說明-解讀
  + **azureml-sdk**
    + `azureml-sdk` 正式支援 Python 3.8。
  + **azureml-train-core**
    + 新增 TensorFlow 2.3 策劃環境
    + 執行設定中的命令屬性，可讓使用者提交命令，而不是腳本 & 引數。
  + **azureml-widgets**
    + 腳本執行 widget 的重新設計介面。


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK for Python v 1.15。0
+ **Bug 修正和改善**
  + **azureml-contrib-interpret**
    + 從 azureml 移出的說明-contrib-解讀至解讀套件和從 azureml 移除的影像說明-contrib-解讀套件
    + 從 azureml 移除的視覺效果儀表板-contrib-解讀套件、說明用戶端移至 azureml-解讀套件，並在 azureml 中淘汰-contrib-解讀套件和筆記本已更新以反映改良的 API
    + 修正 azureml 的 pypi 套件描述-解讀、azureml-說明-contrib-解讀和 azureml-tensorboard
  + **azureml-contrib-notebook**
    + 將 nbcovert 相依性釘選到 < 6，讓 papermill 1.x 繼續運作。
  + **azureml-core**
    + 已將參數新增至 TensorflowConfiguration 和 MpiConfiguration 的函式，以啟用更簡化的類別屬性初始化，而不需要使用者設定每個個別的屬性。 已新增 PyTorchConfiguration 類別，以便在 ScriptRunConfig 中設定分散式 PyTorch 工作。
    + 釘選 azure 管理資源的版本，以修正驗證錯誤。
    + 支援 Triton 無程式碼部署
    + 當使用在互動式案例中執行時，將會追蹤 Run.start_logging ( # A1 中指定的輸出目錄。 在呼叫執行時，會在 ML Studio 上顯示追蹤的檔案。完成 ( # A1
    + 檔案編碼現在可在使用 `Dataset.Tabular.from_delimited_files` 和 `Dataset.Tabular.from_json_lines_files` 傳遞引數的資料集建立期間指定 `encoding` 。 支援的編碼為 ' utf8 '、' iso88591 '、' 採用 latin1-general '、' ascii '、utf16 '、' utf32 '、' utf8bom ' 和 ' windows1252 '。
    + 當環境物件未傳遞至 ScriptRunConfig 的函式時，修正錯誤。
    + 已更新執行。取消 ( # A1 以允許從另一部電腦取消本機執行。
  + **azureml-dataprep**
    +  修正資料集掛接超時問題。
  + **azureml-explain-model**
    + 修正 azureml 的 pypi 套件描述-解讀、azureml-說明-contrib-解讀和 azureml-tensorboard
  + **azureml-interpret**
    + 從 azureml 移除的視覺效果儀表板-contrib-解讀套件、說明用戶端移至 azureml-解讀套件，並在 azureml 中淘汰-contrib-解讀套件和筆記本已更新以反映改良的 API
    + azureml-解讀已更新的套件以相依于解讀-社區0.15。0
    + 修正 azureml 的 pypi 套件描述-解讀、azureml-說明-contrib-解讀和 azureml-tensorboard
  + **azureml-pipeline-core**
    +  修正 `OutputFileDatasetConfig` 當呼叫時，系統可能會停止回應的管線問題， `register_on_complete` 並將 `name` 參數設定為預先存在的資料集名稱。
  + **azureml-pipeline-steps**
    + 已移除過時的 databricks 筆記本。
  + **azureml-tensorboard**
    + 修正 azureml 的 pypi 套件描述-解讀、azureml-說明-contrib-解讀和 azureml-tensorboard
  + **azureml-train-automl-runtime**
    + 從 azureml 移除的視覺效果儀表板-contrib-解讀套件、說明用戶端移至 azureml-解讀套件，並在 azureml 中淘汰-contrib-解讀套件和筆記本已更新以反映改良的 API
  + **azureml-widgets**
    + 從 azureml 移除的視覺效果儀表板-contrib-解讀套件、說明用戶端移至 azureml-解讀套件，並在 azureml 中淘汰-contrib-解讀套件和筆記本已更新以反映改良的 API

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK for Python v 1.14。0
+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 格線分析已從 SDK 中移除，且不再受到支援。
  + **azureml-accel-models**
    + azureml-accel-模型套件現在支援 Tensorflow 2。x
  + **azureml-automl-core**
    + 針對 pandas/sklearn 本機版本不符合定型期間使用的情況，在 get_output 中新增錯誤處理
  + **azureml-automl-runtime**
    + 修正了 AutoArima 反復專案會失敗並出現 PredictionException 和訊息：「預測期間發生無訊息失敗」的 bug。
  + **azureml-cli-通用**
    + 格線分析已從 SDK 中移除，且不再受到支援。
  + **azureml-contrib-伺服器**
    + 更新 pypi 的封裝描述頁面的描述。
  + **azureml-core**
    + 已從 SDK 移除格線分析，且已不再支援。
    + 減少工作區抓取失敗時的錯誤訊息數目。
    + 提取中繼資料失敗時不要顯示警告
    + 新的 Kusto 步驟和 Kusto 計算目標。
    + 更新 sku 參數的檔。 在 CLI 和 SDK 的工作區更新功能中移除 sku。
    + 更新 pypi 的封裝描述頁面的描述。
    + AzureML 環境的更新檔。
    + 公開 SDK 中 AML 工作區的服務受控資源設定。
  + **azureml-dataprep**
    + 啟用資料集裝載之檔案的 execute 許可權。
  + **azureml-mlflow**
    + 已更新 AzureML MLflow 檔和筆記本範例 
    + 使用 AzureML 後端 MLflow 專案的新支援
    + MLflow 模型登錄支援
    + 已新增 AzureML-MLflow 作業的 Azure RBAC 支援 
    
  + **azureml-pipeline-core**
    + 改良 PipelineOutputFileDataset.parse_ * 方法的檔。
    + 新的 Kusto 步驟和 Kusto 計算目標。
    + 透過該使用者提供的管線端點實體的 Swaggerurl 屬性，可以看到已發佈管線端點的架構定義。
  + **azureml-pipeline-steps**
    + 新的 Kusto 步驟和 Kusto 計算目標。
  + **azureml-telemetry**
    + 更新 pypi 的封裝描述頁面的描述。
  + **azureml-定型**
    + 更新 pypi 的封裝描述頁面的描述。
  + **azureml-train-automl-client**
    + 針對 pandas/sklearn 本機版本不符合定型期間使用的情況，在 get_output 中新增錯誤處理
  + **azureml-train-core**
    + 更新 pypi 的封裝描述頁面的描述。
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK for Python v 1.13。0
+ **預覽功能**
  + **azureml-核心** 有了新的輸出資料集功能，您可以回寫到雲端儲存體，包括 Blob、ADLS Gen 1、ADLS Gen 2 和檔案共用。 您可以設定輸出資料的位置、如何透過裝載或上傳) 來輸出資料 (、是否註冊輸出資料以供未來重複使用，以及在管線步驟之間順暢地傳遞中繼資料。 這可讓重現性、共用、防止資料重複，並產生成本效益和生產力的提升。 [了解其使用方式](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py)
    
+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已新增 validated_ {platform} _requirements.txt 檔，以釘選 AutoML 的所有 pip 相依性。
    + 此版本支援大於 4 Gb 的模型。
    + 升級的 AutoML 相依性： `scikit-learn` 現在 (0.22.1) ， `pandas` (現在 0.25.1) ， `numpy` (現在 1.18.2) 。
  + **azureml-automl-runtime**
    + 將文字 DNN 的 horovod 設定為一律使用 fp16 壓縮。
    + 此版本支援大於 4 Gb 的模型。
    + 已修正下列問題： AutoML 失敗並出現 ImportError：無法匯入名稱 `RollingOriginValidator` 。
    + 升級的 AutoML 相依性： `scikit-learn` 現在 (0.22.1) ， `pandas` (現在 0.25.1) ， `numpy` (現在 1.18.2) 。
  + **azureml-contrib-automl-dnn-預測**
    + 升級的 AutoML 相依性： `scikit-learn` 現在 (0.22.1) ， `pandas` (現在 0.25.1) ， `numpy` (現在 1.18.2) 。
  + **azureml-contrib-fairness**
    + 提供 azureml-contrib-公平的簡短描述。
  + **azureml-contrib-pipeline-steps**
    + 已新增訊息，指出此封裝已被取代，而使用者應該改用 azureml 管線步驟。
  + **azureml-core**
    + 已新增工作區的清單金鑰命令。
    + 在工作區 SDK 和 CLI 中新增標記參數。
    + 修正了使用資料集提交子系執行的錯誤（bug），將會導致失敗 `TypeError: can't pickle _thread.RLock objects` 。
    + 新增模型清單的 page_count 預設/檔 ( # A1。
    + 修改 CLI&SDK 以採用 adbworkspace 參數，並新增工作區 adb lin/取消連結執行器。
    + 修正資料集中的錯誤（bug），這會更新導致最新資料集版本更新的更新，而不是呼叫資料集更新的版本。 
    + 修正 Dataset.get_by_name 中的 bug，以顯示最新資料集版本的標記，即使已抓取特定的舊版本。
  + **azureml-interpret**
    + 已將機率輸出新增至 azureml 中的 shap 計分 explainers-根據來自原始說明的 shap_values_output 參數來解讀。
  + **azureml-pipeline-core**
    + 改良 `PipelineOutputAbstractDataset.register` 的檔。
  + **azureml-train-automl-client**
    + 升級的 AutoML 相依性： `scikit-learn` 現在 (0.22.1) ， `pandas` (現在 0.25.1) ， `numpy` (現在 1.18.2) 。
  + **azureml-train-automl-runtime**
    + 升級的 AutoML 相依性： `scikit-learn` 現在 (0.22.1) ， `pandas` (現在 0.25.1) ， `numpy` (現在 1.18.2) 。
  + **azureml-train-core**
    + 建立 HyperDriveConfig 時，使用者現在必須提供有效的 hyperparameter_sampling arg。 此外，也已編輯 HyperDriveRunConfig 的檔，以通知使用者已淘汰 HyperDriveRunConfig。
    + 將 PyTorch 預設版本還原為1.4。
    + 新增 PyTorch 1.6 & Tensorflow 2.2 映射和策劃環境。

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio 筆記本體驗 (8 月更新) 
+ **新功能**
  + 新增開始使用登陸頁面 
  
+ **預覽功能**
    + 在筆記本中收集功能。 透過 [收集](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) 功能，使用者現在可以輕鬆地清除筆記本，收集會使用您筆記本的自動化相依性分析，確保會保留必要的程式碼，但移除任何不相關的部分。

+ **Bug 修正和改善**
  + 改進速度和可靠性
  + 已修正深色模式錯誤
  + 已修正輸出滾動 Bug
  + 範例搜尋現在會搜尋 Azure Machine Learning 範例筆記本存放庫中所有檔案的所有內容
  + 現在可執行多行 R 儲存格
  + 「我信任這個檔案的內容」現在是在第一次後自動檢查
  + 改進衝突解決對話方塊，並提供新的「製作複本」選項
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK for Python v 1.12。0

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 將 image_name 和 image_label 參數加入至模型。封裝 ( # A1 以啟用重新命名建立的封裝映射。
  + **azureml-automl-core**
    + 當內容在讀取期間遭到修改時，AutoML 會從 dataprep 引發新的錯誤碼。
  + **azureml-automl-runtime**
    + 當資料包含遺漏值但關閉特徵化時，新增使用者的警示。
    + 修正當資料包含 nan 和特徵化關閉時的子執行失敗。
    + 當內容在讀取期間遭到修改時，AutoML 會從 dataprep 引發新的錯誤碼。
    + 已更新正規化，以依資料細微性進行預測度量。
    + 改進預測分量在回顧功能停用時的計算。
    + 修正 AutoML 之後計算說明時的布林疏鬆陣列處理。
  + **azureml-core**
    + 新的方法 `run.get_detailed_status()` 現在會顯示目前執行狀態的詳細說明。 目前只會顯示狀態的說明 `Queued` 。
    + 將 image_name 和 image_label 參數加入至模型。封裝 ( # A1 以啟用重新命名建立的封裝映射。
    + `set_pip_requirements()`一次設定整個 pip 區段的新方法 [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py) 。
    + 啟用註冊無認證 ADLS Gen2 資料存放區。
    + 改善嘗試下載或掛接不正確的資料集類型時的錯誤訊息。
    + 更新時間序列資料集篩選範例筆記本，並提供更多可提供篩選優化的 partition_timestamp 範例。
    + 在刪除私人端點連線時，變更 sdk 和 CLI 以接受 subscriptionId、resourceGroup、workspaceName、peConnectionName 作為參數，而不是 ArmResourceId。
    + 實驗性裝飾專案會顯示類別名稱，以方便識別。
    + 模型內的資產描述不會再根據執行自動產生。
  + **azureml-datadrift**
    + 將 DataDriftDetector 中的 create_from_model API 標記為即將淘汰。
  + **azureml-dataprep**
    + 改善嘗試下載或掛接不正確的資料集類型時的錯誤訊息。
  + **azureml-pipeline-core**
    + 修正還原序列化包含已註冊資料集的管線圖形時的 bug。
  + **azureml-pipeline-steps**
    + RScriptStep 支援從 azureml. 核心環境進行 RSection。
    + 從公用 API 中移除 passthru_automl_config 參數 `AutoMLStep` ，並將其轉換為僅供內部使用的參數。
  + **azureml-train-automl-client**
    + 從 AutoML 中移除本機非同步 managed 環境。 所有本機執行都將在執行啟動的環境中執行。
    + 修正提交 AutoML 執行時沒有使用者提供之腳本的快照集問題。
    + 修正當資料包含 nan 和特徵化關閉時的子執行失敗。
  + **azureml-train-automl-runtime**
    + 當內容在讀取期間遭到修改時，AutoML 會從 dataprep 引發新的錯誤碼。
    + 修正提交 AutoML 執行時沒有使用者提供之腳本的快照集問題。
    + 修正當資料包含 nan 和特徵化關閉時的子執行失敗。
  + **azureml-train-core**
    + 已新增在 pip 需求檔案中指定 pip 選項的支援 (例如--) --------------------- [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator?preserve-view=true&view=azure-ml-py) `pip_requirements_file`


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK for Python v 1.11。0

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 修正在 CLI 模型登錄路徑中未傳遞至執行物件的模型架構和模型架構
    + 修正 CLI amlcompute identity show 命令以顯示租使用者識別碼和主體識別碼 
  + **azureml-train-automl-client**
    + 已將 get_best_child ( # A1 新增至 AutoMLRun，以取得 AutoML 執行的最佳子執行，而不需要下載相關聯的模型。
    + 新增 ModelProxy 物件，允許在遠端定型環境上執行預測或預測，而不需要在本機下載模型。
    + AutoML 中未處理的例外狀況現在會指向已知問題的 HTTP 頁面，而您可以在其中找到錯誤的詳細資訊。
  + **azureml-core**
    + 模型名稱長度可以是255個字元。
    + Environment.get_image_details ( # A1 傳回物件類型已變更。 `DockerImageDetails` 已取代類別 `dict` ，可從新的類別屬性取得影像詳細資料。 變更與舊版相容。
    + 修正 Environment.from_pip_requirements ( # A1 的 bug 以保留相依性結構
    + 修正當相同清單中包含了 int 和 double 時，log_list 會失敗的 bug。
    + 在現有的工作區上啟用 private link 時，請注意，如果有與工作區相關聯的計算目標，如果它們不在與工作區私人端點相同的虛擬網路後方，這些目標將無法運作。
    + 在 `as_named_input` 實驗中使用資料集，並將和加入至時，會成為選擇性的 `as_mount` `as_download` `FileDataset` 。 如果 `as_mount` 呼叫或，就會自動產生輸入名稱 `as_download` 。
  + **azureml-automl-core**
    + AutoML 中未處理的例外狀況現在會指向已知問題的 HTTP 頁面，而您可以在其中找到錯誤的詳細資訊。
    + 已將 get_best_child ( # A1 新增至 AutoMLRun，以取得 AutoML 執行的最佳子執行，而不需要下載相關聯的模型。
    + 已新增 ModelProxy 物件，可讓預測或預測在遠端定型環境中執行，而不需要在本機下載模型。
  + **azureml-pipeline-steps**
    + 新增 `enable_default_model_output` 和 `enable_default_metrics_output` 旗標至 `AutoMLStep` 。 這些旗標可用來啟用/停用預設輸出。


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK for Python v 1.10。0

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 使用 AutoML 時，如果將路徑傳遞至 AutoMLConfig 物件，而且它還不存在，就會自動建立。
    + 使用者現在可以使用參數來指定預測工作的時間序列頻率 `freq` 。
  + **azureml-automl-runtime**
    + 使用 AutoML 時，如果將路徑傳遞至 AutoMLConfig 物件，而且它還不存在，就會自動建立。
    + 使用者現在可以使用參數來指定預測工作的時間序列頻率 `freq` 。
    + AutoML 預測現在支援輪流評估，其適用于測試或驗證集長度超過輸入範圍的使用案例，而且已知的 y_pred 值會作為預測內容使用。
  + **azureml-core**
    + 如果在執行的資料存放區中沒有下載任何檔案，則會列印警告訊息。
    + 已將的檔新增 `skip_validation` 至 `Datastore.register_azure_sql_database method` 。
    + 使用者必須升級至 sdk v 1.10.0 或更新版本，才能建立自動核准的私人端點。 這包括可在 VNet 後方使用的筆記本資源。
    + 在 get 工作區的回應中公開 NotebookInfo。
    + 變更以列出計算目標，並在遠端執行成功取得計算目標。 取得計算目標和清單工作區計算目標的 Sdk 函式現在將可在遠端執行中運作。
    + 將取代訊息新增至 azureml. 映射類別的類別描述。
    + 如果工作區私人端點建立失敗，則擲回例外狀況，並清除工作區和相依的資源。
    + 在工作區更新方法中支援工作區 sku 升級。
  + **azureml-datadrift**
    + 將 matplotlib 版本從3.0.2 更新為3.2.1 以支援 python 3.8。
  + **azureml-dataprep**
    + 已新增或要求的 web url 資料來源支援 `Range` `Head` 。 
    + 改進檔案資料集掛接和下載的穩定性。
  + **azureml-train-automl-client**
    + 修正從 setuptools 移除的相關問題 `RequirementParseError` 。
    + 針對使用 "compute_target = ' local '" 提交的本機執行，使用 docker 而非 conda
    + 已更正列印到主控台的反復專案持續時間。 先前反覆運算的持續時間有時會列印為執行結束時間減去執行建立時間。 它已更正為等於執行結束時間減去執行開始時間。
    + 使用 AutoML 時，如果將路徑傳遞至 AutoMLConfig 物件，而且它還不存在，就會自動建立。
    + 使用者現在可以使用參數來指定預測工作的時間序列頻率 `freq` 。
  + **azureml-train-automl-runtime**
    + 改進了最佳模型說明失敗時的主控台輸出。
    + 已將 "backlist_models" 輸入參數重新命名為 "blocked_models"。
      + 已將 "whitelist_models" 輸入參數重新命名為 "allowed_models"。
    + 使用者現在可以使用參數來指定預測工作的時間序列頻率 `freq` 。

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK for Python v 1.9。0

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 在 AutoML 自動產生的評分腳本中，以 AZUREML_MODEL_DIR 環境變數取代 get_model_path ( # A1。 此外，也新增了在 init ( # A1 期間追蹤失敗的遙測。
    + 已移除在 AutoMLConfig 中指定的功能 `enable_cache`
    + 修正在特定預測執行期間，執行可能會失敗併發生服務錯誤的 bug
    + 改進在中的特定模型處理 `get_output`
    + 已修正 fitted_model 的呼叫。使用 y 轉換程式將 (X、y) 配合分類
    + 啟用預測工作的自訂向前填滿 imputer
    + 將使用新的 ForecastingParameters 類別，而不是 dict 格式的預測參數
    + 改善的目標延隔時間偵測
    + 使用 BERT 新增了多重 noded、多重 gpu 分散式特徵化的有限可用性
  + **azureml-automl-runtime**
    + 先知現在會執行加法季節性模型，而不是乘法。
    + 修正了簡短粒紋時，與長粒紋的頻率不同的頻率會導致執行失敗的問題。
  + **azureml-contrib-automl-dnn-願景**
    + 收集定型和評分的系統/gpu 統計資料和記錄平均值
  + **azureml-contrib-mir**
    + 在 ManagedInferencing 中新增了啟用-app insights 旗標的支援
  + **azureml-core**
    + 當無法從目前的計算存取資料來源時，允許略過驗證，以驗證這些 Api 的參數。
      + TabularDataset.time_before (end_time，include_boundary = True，validate = True) 
      + TabularDataset.time_after (start_time，include_boundary = True，validate = True) 
      + TabularDataset.time_recent (time_delta，include_boundary = True，validate = True) 
      + TabularDataset.time_between (start_time、end_time、include_boundary = True、validate = True) 
    + 已新增模型清單的架構篩選支援，並在筆記本中新增 NCD AutoML 範例
    + 針對 Datastore.register_azure_blob_container 和 Datastore.register_azure_file_share (只有支援 SAS 權杖) 的選項，我們已更新欄位的 doc 字串， `sas_token` 以包含一般讀取和寫入案例的最低許可權需求。
    + Ws.get_mlflow_tracking_uri 中的淘汰 _with_auth param ( # A1
  + **azureml-mlflow**
    + 新增使用 AzureML-MLflow 部署本機 file://模型的支援
    + Ws.get_mlflow_tracking_uri 中的淘汰 _with_auth param ( # A1
  + **azureml-opendatasets**
    + 最近發佈的 Covid-19-19 追蹤資料集現在可透過 SDK 使用
  + **azureml-pipeline-core**
    + 當「azureml-預設值」未包含為 pip 相依性的一部分時，即登出警告
    + 改善便箋轉譯。
    + 已新增將分隔的檔案剖析為 PipelineOutputFileDataset 時，對引號換行的支援。
    + PipelineDataset 類別已被取代。 如需詳細資訊，請參閱 https://aka.ms/dataset-deprecation 。 瞭解如何使用具有管線的資料集，請參閱 https://aka.ms/pipeline-with-dataset 。
  + **azureml-pipeline-steps**
    + Azureml-管線-步驟的更新。
    +  已新增 ParallelRunConfig 的支援， `load_yaml()` 可讓使用者定義以 rest 設定或個別檔案內嵌的環境
  + **azureml-定型-automl-client**。
    + 已移除在 AutoMLConfig 中指定的功能 `enable_cache`
  + **azureml-train-automl-runtime**
    + 新增了具有 BERT 的多重 noded、多重 gpu 分散式特徵化的有限可用性。
    + 在以 ADB 為基礎的自動化機器學習回合中，新增了不相容套件的錯誤處理。
  + **azureml-widgets**
    + Azureml 的檔更新。

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK for Python v 1.8。0
  
  + **預覽功能**
    + **azureml-contrib-公平** 此 `azureml-contrib-fairness` 封裝提供開放原始碼公平評定與不公平性緩和套件 [Fairlearn](https://fairlearn.github.io) 和 Azure Machine Learning studio 之間的整合。 尤其是，此套件可讓模型公平評估儀表板上傳作為 AzureML 執行的一部分，並顯示在 Azure Machine Learning studio 中

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 支援取得 init 容器的記錄。
    + 已新增 CLI 命令來管理 ComputeInstance
  + **azureml-automl-core**
    + 使用者現在可以對時間序列工作啟用 stack 集團反復專案，但有可能會過度學習的警告。
    + 加入新的使用者例外狀況類型，如果快取存放區內容已遭修改，則會引發
  + **azureml-automl-runtime**
    + 如果使用者停用特徵化，將無法再啟用類別平衡清除。  
  + **azureml-contrib-itp**
    + 支援 CmAks 計算類型。 您可以將自己的 AKS 叢集附加至工作區，以進行定型作業。
  + **azureml-contrib-notebook**
    + Azureml-contrib-筆記本套件的檔改進。
  + **azureml-contrib-pipeline-steps**
    + Azureml-contrib--管線-步驟套件的檔改進。
  + **azureml-core**
    + 新增 set_connection、get_connection list_connections、delete_connection 函式以供客戶在工作區連線資源上運作
    + Azureml-coore/azureml 的檔更新。例外狀況套件。
    + Azureml 核心封裝的檔更新。
    + ComputeInstance 類別的 Doc 更新。
    + Azureml-core/azureml 封裝的檔改進。
    + Azureml 核心中 webservice 相關類別的檔改進。
    + 支援使用者選取的資料存放區來儲存程式碼剖析資料
    + 已新增模型清單 API 的展開和 page_count 屬性
    + 修正移除覆寫屬性將導致提交的執行失敗併發生還原序列化錯誤的 bug。
    + 修正當下載或掛接參考單一檔案的 FileDataset 時，不一致的資料夾結構。
    + 現在 to_spark_dataframe 載入 parquet 檔案的資料集，並支援所有 parquet 和 Spark SQL 資料類型。
    + 支援取得 init 容器的記錄。
    + AutoML 回合現在會標示為執行平行執行步驟的子回合。
  + **azureml-datadrift**
    + Azureml-contrib-筆記本套件的檔改進。
  + **azureml-dataprep**
    + 現在 to_spark_dataframe 載入 parquet 檔案的資料集，並支援所有 parquet 和 Spark SQL 資料類型。
    + To_pandas_dataframe 的 OutOfMemory 問題更好的記憶體處理。
  + **azureml-interpret**
    + 已升級 azureml-解讀以使用解讀-社區版本0.12。 *
  + **azureml-mlflow**
    + Azureml 的檔改進-mlflow。
    + 新增對 AML 模型登錄與 MLFlow 的支援。
  + **azureml-opendatasets**
    + 新增了 Python 3.8 的支援
  + **azureml-pipeline-core**
    + 更新 `PipelineDataset` 的檔，讓它清楚地成為內部類別。
    + ParallelRunStep 更新以接受一個引數的多個值，例如： "--group_column_names"、"Col1"、"Col2"、"Col3"
    + 已移除管線中 AutoMLStep 中繼資料使用量的 passthru_automl_config 需求。
  + **azureml-pipeline-steps**
    + Azureml-管線-步驟套件的檔改進。
    + 已移除管線中 AutoMLStep 中繼資料使用量的 passthru_automl_config 需求。
  + **azureml-telemetry**
    + Azureml 遙測的檔改進。
  + **azureml-train-automl-client**
    + 修正在 `experiment.submit()` 物件上呼叫兩次的 bug，會 `AutoMLConfig` 導致不同的行為。
    + 使用者現在可以對時間序列工作啟用 stack 集團反復專案，但有可能會過度學習的警告。
    + 已變更 AutoML 執行行為，以在服務擲回使用者錯誤時引發 UserErrorException
    + 修正在遠端計算目標上執行 AutoML 實驗時，azureml_automl 導致無法產生或遺失記錄檔的錯誤（bug）。
    + 針對具有不平衡類別的分類資料集，我們會套用權數平衡，如果功能 sweeper 決定 subsampled 資料，加權平衡會依據特定閾值來改善分類工作的效能。
    + AutoML 回合現在會標示為執行平行執行步驟的子回合。
  + **azureml-train-automl-runtime**
    + 已變更 AutoML 執行行為，以在服務擲回使用者錯誤時引發 UserErrorException
    + AutoML 回合現在會標示為執行平行執行步驟的子回合。

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK for Python v 1.7。0

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 藉由清除 CLI 命令和套件相依性來完成從 mir contrib 中移除模型分析，可在核心中取得模型分析。
    + 將最小 Azure CLI 版本升級至2.3。0
  + **azureml-automl-core**
    + 由於自訂轉換器參數的緣故，特徵化步驟 fit_transform ( # A1 的較佳例外狀況訊息。
    + 針對深度學習轉換器模型（例如自動化 ML 中的 BERT）新增多種語言的支援。
    + 從檔中移除已淘汰的 lag_length 參數。
    + 預測參數檔已獲得改善。 Lag_length 的參數已被取代。
  + **azureml-automl-runtime**
    + 修正當預測/測試時間中的其中一個類別目錄資料行是空的時，所引發的錯誤。
    + 修正在啟用回顧功能且資料包含短粒紋時所發生的執行失敗。
    + 修正當延後或滾動視窗設定為「自動」時，重複的時間索引錯誤訊息的問題。
    + 修正了先知和 Arima 模型在包含回顧功能的資料集上的問題。
    + 已新增在1677-09-21 之前或2262-04-11 之後資料行中的日期，在預測工作中的日期時間之前的資料行支援。 改進的錯誤訊息。
    + 預測參數檔已獲得改善。 Lag_length 的參數已被取代。
    + 由於自訂轉換器參數的緣故，特徵化步驟 fit_transform ( # A1 的較佳例外狀況訊息。
    + 針對深度學習轉換器模型（例如自動化 ML 中的 BERT）新增多種語言的支援。
    + 導致某些 OSErrors 的快取作業會引發使用者錯誤。
    + 新增檢查以確保定型和驗證資料具有相同的資料行數目和集合
    + 修正當資料包含引號時，自動產生的 AutoML 評分腳本問題
    + 啟用包含先知模型的 AutoML 先知和 ensembled 模型的說明。
    + 最新的客戶問題揭示了即時網站的錯誤，即使類別平衡邏輯未正確啟用，我們仍會在類別平衡的情況下記錄訊息。 使用此 PR 移除這些記錄檔/訊息。
  + **azureml-cli-通用**
    + 藉由清除 CLI 命令和套件相依性來完成從 mir contrib 中移除模型分析，可在核心中取得模型分析。
  + **azureml-contrib-reinforcementlearning**
    + 負載測試工具
  + **azureml-core**
    + Script_run_config 上的檔變更。 .py
    + 修正列印執行提交-管線 CLI 輸出的 bug
    + Azureml-core/azureml 的檔改進。資料
    + 修正使用 hdfs getconf 命令抓取儲存體帳戶的問題
    + 改良的 register_azure_blob_container 和 register_azure_file_share 檔
  + **azureml-datadrift**
    + 改善停用和啟用資料集漂移監視器的執行
  + **azureml-interpret**
    + 在 [說明用戶端] 中，從成品上傳後，移除 json 序列化之前的 Nan 或 Inf
    + 更新為最新版本的解讀-社區，以改善具有許多功能和類別的全域說明的記憶體不足錯誤
    + 新增 true_ys 選擇性參數來說明上傳，以啟用 studio UI 中的其他功能
    + 改善 download_model_explanations ( # A1 和 list_model_explanations ( # A3 效能
    + 小型的筆記本調整，以協助進行調試
  + **azureml-opendatasets**
    + azureml-opendatasets 需要 dataprep 1.4.0 版或更高版本。 已在偵測到較低版本時新增警告
  + **azureml-pipeline-core**
    + 這種變更可讓使用者在呼叫模組時，提供選擇性的 runconfig 給 moduleVersion。Publish_python_script。
    + 在 azureml 的 ParallelRunStep 中，啟用節點帳戶可以是管線參數。
  + **azureml-pipeline-steps**
    + 這種變更可讓使用者在呼叫模組時，提供選擇性的 runconfig 給 moduleVersion。Publish_python_script。
  + **azureml-train-automl-client**
    + 針對深度學習轉換器模型（例如自動化 ML 中的 BERT）新增多種語言的支援。
    + 從檔中移除已淘汰的 lag_length 參數。
    + 預測參數檔已獲得改善。 Lag_length 的參數已被取代。
  + **azureml-train-automl-runtime**
    + 啟用包含先知模型的 AutoML 先知和 ensembled 模型的說明。
    + Azureml-定型-automl-* 套件的檔更新。
  + **azureml-train-core**
    + 在 PyTorch 估算器中支援 TensorFlow 2.1 版
    + Azureml-定型核心套件的增強功能。
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK for Python v 1.6。0

+ **新功能**
  + **azureml-automl-runtime**
    + AutoML 預測現在可支援客戶在預先指定的最大範圍之外進行預測，而不需要重新定型模型。 當預測目的地比指定的最大範圍更多時，預測 ( # A1 函式仍會使用遞迴作業模式，讓點預測在未來的日期。 如需新功能的圖例，請參閱「預測-預測-函式」筆記本 [資料夾](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)中的「在最大範圍內進行預測」一節。
  
  + **azureml-pipeline-steps**
    + ParallelRunStep 現已發行，而且是 **azureml 管線步驟** 套件的一部分。 Azureml 中的現有 ParallelRunStep **-contrib-pipeline-步驟** 套件已淘汰。 公開預覽版本的變更：
      + 已新增 `run_max_try` 選擇性的可設定參數，以控制任何指定批次的最大呼叫執行方法，預設值為3。
      + 不會再自動產生任何 PipelineParameters。 可以明確地將下列可設定的值設定為 >pipelineparameter。
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Process_count_per_node 的預設值已變更為1。 使用者應調整此值，以獲得更好的效能。 最佳做法是將設定為 GPU 或 CPU 節點的數目。
      + ParallelRunStep 不會插入任何套件，使用者需要在環境定義中包含 **azureml 核心** 和 **azureml dataprep [pandas，保險絲]** 套件。 如果搭配 user_managed_dependencies 使用自訂 docker 映射，則使用者必須在映射上安裝 conda。
      
+ **重大變更**
  + **azureml-pipeline-steps**
    + 已淘汰使用 >iris.dprep 做為 AutoMLConfig 的有效輸入類型
  + **azureml-train-automl-client**
    + 已淘汰使用 >iris.dprep 做為 AutoMLConfig 的有效輸入類型

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 修正在 `get_output` 要求使用者降級用戶端時，可能會列印警告的錯誤（bug）。
    + 更新 Mac 以依賴 cudatoolkit = 9.0，因為它在第10版尚未提供。
    + 在遠端計算上定型時，移除對先知和 xgboost 模型的限制。
    + 改進 AutoML 中的記錄
    + 已改善預測工作中自訂特徵化的錯誤處理。
    + 已新增功能，可讓使用者包含延隔功能來產生預測。
    + 錯誤訊息的更新，以正確顯示使用者錯誤。
    + 支援搭配使用 cv_split_column_names training_data
    + 更新記錄例外狀況訊息和追溯。
  + **azureml-automl-runtime**
    + 啟用護欄來預測遺漏值 imputations。
    + 改進 AutoML 中的記錄
    + 新增資料準備例外狀況的細微錯誤處理
    + 在遠端計算上定型時，移除對 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime` 和 `azureml-automl-runtime` 已更新、和的相依性 `pytorch` `scipy` `cudatoolkit` 。 我們現在支援 `pytorch==1.4.0` 、 `scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243` 。
    + 已改善預測工作中自訂特徵化的錯誤處理。
    + 已改善預測資料集頻率偵測機制。
    + 修正了某些資料集上先知模型定型的問題。
    + 預測期間的最大範圍自動偵測已獲得改善。
    + 已新增功能，可讓使用者包含延隔功能來產生預測。
    +  在預測函數中新增功能，以在未重新定型預測模型的情況下，提供超過定型範圍的預測。
    + 支援搭配使用 cv_split_column_names training_data
  + **azureml-contrib-automl-dnn-預測**
    + 改進 AutoML 中的記錄
  + **azureml-contrib-mir**
    + 已在 ManagedInferencing 中新增對 Windows 服務的支援
    + 移除舊的 MIR 工作流程，例如附加 MIR 計算、SingleModelMirWebservice 類別-清除模型分析放置於 contrib-MIR 封裝中
  + **azureml-contrib-pipeline-steps**
    + YAML 支援的次要修正
    + ParallelRunStep 已發行至正式運作-azureml. contrib。步驟有淘汰通知，即將移至 azureml。管線。步驟
  + **azureml-contrib-reinforcementlearning**
    + RL 負載測試工具
    + RL 估算器具有智慧型預設值
  + **azureml-core**
    + 移除舊的 MIR 工作流程，例如附加 MIR 計算、SingleModelMirWebservice 類別-清除模型分析放置於 contrib-MIR 封裝中
    + 修正在分析失敗時提供給使用者的資訊：包含的要求識別碼，以及改寫要更有意義的訊息。 已將新的分析工作流程新增至分析進行程式碼剖析
    + 改善資料集執行失敗時的錯誤文字。
    + 已新增工作區 private link CLI 支援。
    + 在中加入選擇性 `invalid_lines` 參數 `Dataset.Tabular.from_json_lines_files` ，可讓您指定如何處理包含無效 JSON 的程式程式碼。
    + 在下一版中，我們將淘汰以執行為基礎的計算建立。 建議您建立實際的 Amlcompute 叢集做為持續性計算目標，並使用叢集名稱作為執行設定中的計算目標。 請參閱這裡的範例筆記本： aka.ms/amlcomputenb
    + 改善資料集執行失敗時的錯誤訊息。
  + **azureml-dataprep**
    + 發出警告以更明確地升級 pyarrow 版本。
    + 改善錯誤處理，以及在執行資料流程失敗時傳回的訊息。
  + **azureml-interpret**
    + Azureml 的檔更新-解讀套件。
    + 已修正可解譯性套件和筆記本以與最新的 sklearn 更新相容
  + **azureml-opendatasets**
    + 沒有傳回任何資料時，就會傳回 None。
    + 提升 to_pandas_dataframe 的效能。
  + **azureml-pipeline-core**
    + ParallelRunStep 從 YAML 載入中斷的快速修正
    + ParallelRunStep 已發行至正式運作。 contrib。步驟具有取代通知，即將移至 azureml。管線。步驟-新功能包括：1。 >pipelineparameter 2 的資料集。 Run_max_retry 3 的新參數。 可設定的 append_row 輸出檔名稱
  + **azureml-pipeline-steps**
    + 已淘汰的 azureml. >iris.dprep 是輸入資料的有效類型。
    + ParallelRunStep 從 YAML 載入中斷的快速修正
    + ParallelRunStep 已發行至正式運作-azureml. contrib。步驟具有淘汰通知，即將移至 azureml。管線。步驟-新功能包括：
      + >pipelineparameter 的資料集
      + 新參數 run_max_retry
      + 可設定的 append_row 輸出檔名稱
  + **azureml-telemetry**
    + 更新記錄例外狀況訊息和追溯。
  + **azureml-train-automl-client**
    + 改進 AutoML 中的記錄
    + 錯誤訊息的更新，以正確顯示使用者錯誤。
    + 支援搭配使用 cv_split_column_names training_data
    + 已淘汰的 azureml. >iris.dprep 是輸入資料的有效類型。
    + 更新 Mac 以依賴 cudatoolkit = 9.0，因為它在第10版尚未提供。
    + 在遠端計算上定型時，移除對 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime` 和 `azureml-automl-runtime` 已更新、和的相依性 `pytorch` `scipy` `cudatoolkit` 。 我們現在支援 `pytorch==1.4.0` 、 `scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243` 。
    + 已新增功能，可讓使用者包含延隔功能來產生預測。
  + **azureml-train-automl-runtime**
    + 改進 AutoML 中的記錄
    + 新增資料準備例外狀況的細微錯誤處理
    + 在遠端計算上定型時，移除對 phrophet 和 xgboost 模型的限制。
    + `azureml-train-automl-runtime` 和 `azureml-automl-runtime` 已更新、和的相依性 `pytorch` `scipy` `cudatoolkit` 。 我們現在支援 `pytorch==1.4.0` 、 `scipy>=1.0.0,<=1.3.1` 和 `cudatoolkit==10.1.243` 。
    + 錯誤訊息的更新，以正確顯示使用者錯誤。
    + 支援搭配使用 cv_split_column_names training_data
  + **azureml-train-core**
    + 新增一組新的 HyperDrive 特定例外狀況。 hyperdrive 現在會擲回詳細的例外狀況。
  + **azureml-widgets**
    + AzureML 小工具未顯示在 JupyterLab 中
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>適用于 Python 的 Azure Machine Learning SDK v1。1

+ **新功能**
  + **預覽功能**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning 正在使用 [光線](https://ray.io) 架構來發行增強式學習的預覽支援。 `ReinforcementLearningEstimator`可在 Azure Machine Learning 的 GPU 和 CPU 計算目標上訓練增強式學習代理程式。

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 修正先前的 PR 中不小心留下的警告記錄。 記錄檔是用來進行偵錯工具，並意外留下。
    + Bug 修正：通知用戶端在分析期間發生部分失敗
  + **azureml-automl-core**
    + 當資料集有多個時間序列時，可啟用時間序列的平行管，以加速 AutoML 預測中的先知/AutoArima 模型。 為了受益于這項新功能，建議您設定「max_cores_per_iteration =-1」 (也就是使用 AutoMLConfig 中所有可用的 cpu 核心) 。
    + 修正主控台介面中的列印護欄 KeyError
    + 已修正 experimentation_timeout_hours 的錯誤訊息
    + 適用于 AutoML 的已淘汰 Tensorflow 模型。
  + **azureml-automl-runtime**
    + 已修正 experimentation_timeout_hours 的錯誤訊息
    + 修正嘗試從快取存放區還原序列化時發生的非分類例外狀況
    + 當資料集有多個時間序列時，可啟用時間序列的平行管，以加速 AutoML 預測中的先知/AutoArima 模型。
    + 修正了在資料集上已啟用滾動視窗的預測，其中測試/預測集不包含定型集中的其中一個粒紋。
    + 改進遺失資料的處理
    + 已修正在預測資料集（包含時間序列）時預測間隔的問題，這些資料集不會在時間內對齊。
    + 針對預測工作新增更好的資料圖形驗證。
    + 已改善頻率偵測。
    + 如果無法產生預測工作的交叉驗證折迭，就會建立更好的錯誤訊息。
    + 修正主控台介面，以正確地列印遺漏的值 guardrail。
    + 針對 AutoMLConfig 中的 cv_split_indices 輸入強制執行資料類型檢查。
  + **azureml-cli-通用**
    + Bug 修正：通知用戶端在分析期間發生部分失敗
  + **azureml-contrib-mir**
    + 新增類別 azureml. contrib. mir. RevisionStatus，以轉送目前已部署 MIR 修訂的相關資訊，以及使用者指定的最新版本。 這個類別包含在 MirWebservice 物件的 ' deployment_status ' 屬性下。
    + 在 Webservices 類型的 MirWebservice 及其子類別 SingleModelMirWebservice 上啟用更新。
  + **azureml-contrib-reinforcementlearning**
    + 新增了對光線0.8.3 的支援
    + AmlWindowsCompute 只支援以掛接的儲存空間 Azure 檔案儲存體
    + 將 health_check_timeout 重新命名為 health_check_timeout_seconds
    + 修正了一些類別/方法的描述。
  + **azureml-core**
    + 已在 Azure Government 和中國雲端啟用 WASB > Blob 轉換。
    + 修正錯誤，以允許讀者角色使用 az ml run CLI 命令來取得執行資訊
    + 在使用輸入資料集的 Azure ML 遠端執行期間，移除不必要的記錄。
    + RCranPackage 現在支援 CRAN 套件版本的 "version" 參數。
    + Bug 修正：通知用戶端在分析期間發生部分失敗
    + 已新增適用于 azureml 核心的歐洲樣式浮點數處理。
    + Azure ml sdk 中已啟用的工作區私用連結功能。
    + 使用建立 TabularDataset 時 `from_delimited_files` ，您可以設定布林值引數，以指定空值是否應該載入為 None 或空字串 `empty_as_string` 。
    + 新增資料集的歐洲樣式浮點數處理。
    + 改進資料集掛接失敗的錯誤訊息。
  + **azureml-datadrift**
    + 從 SDK 產生的資料漂移結果查詢有一個錯誤，不區分最小值、最大值和平均特徵度量，而導致重複的值。 我們已藉由在計量名稱前面加上目標或基準來修正此錯誤。 之前：重複的最小值、最大值、平均值。 After： target_min、target_max、target_mean、baseline_min、baseline_max、baseline_mean。
  + **azureml-dataprep**
    + 改善寫入受限制 python 環境的處理，以確保資料傳遞所需的 .NET 相依性。
    + 已修正具有前置空白記錄之檔案的資料流程建立。
    + 已新增類似的錯誤處理選項 `to_partition_iterator` `to_pandas_dataframe` 。
  + **azureml-interpret**
    + 減少說明路徑長度限制，以降低超越 Windows 限制的可能性
    + 錯誤修正使用線性代理模型，利用模仿說明建立的稀疏說明。
  + **azureml-opendatasets**
    + MNIST 的資料行修正問題會以字串的形式剖析，這應該是 int。
  + **azureml-pipeline-core**
    + 使用內嵌在 ModuleStep 中的模組時，允許選項 regenerate_outputs。
  + **azureml-train-automl-client**
    + 適用于 AutoML 的已淘汰 Tensorflow 模型。
    + 修正使用者允許在原生模式中列出不支援的演算法
    + AutoMLConfig 的 Doc 修正。
    + 針對 AutoMLConfig 中的 cv_split_indices 輸入強制執行資料類型檢查。
    + 修正 show_output 中的 AutoML 執行失敗的問題
  + **azureml-train-automl-runtime**
    + 修正集團反覆運算中的錯誤，這會導致無法成功地從開始模型下載超時。
  + **azureml-train-core**
    + 修正 azureml 中的打字錯誤。 dnn. Nccl 類別。
    + 在 PyTorch 估算器中支援 PyTorch 1.5 版
    + 修正使用定型架構估算器時，無法在 Azure Government 區域中提取 framework 映射的問題

  
## <a name="2020-05-04"></a>2020-05-04
**新的筆記本體驗**

您現在可以直接在 Azure Machine Learning 的 studio web 體驗內建立、編輯和共用機器學習筆記本和檔案。 您可以從這些筆記本中使用[Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)提供的所有類別和方法，從[這裡](./how-to-run-jupyter-notebooks.md)開始著手

**引進的新功能：**

+ 改良的編輯器 (摩納哥編輯器) 使用 VS Code 
+ UI/UX 改進
+ 儲存格工具列
+ 新筆記本工具列和計算控制項
+ 筆記本狀態列 
+ 內嵌核心切換
+ R 支援
+ 協助工具和當地語系化的改進
+ 命令選擇區
+ 其他鍵盤快速鍵
+ 自動儲存
+ 改進的效能和可靠性

從 studio 存取下列以網頁為基礎的編寫工具：
    
| 以 Web 為基礎的工具  |     描述  |
|---|---|
| Azure ML Studio 筆記本   |     第一次針對筆記本檔案進行類別撰寫，並支援 Azure ML Python SDK 中所有可用的作業。 | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK for Python v 1.4。0

+ **新功能**
  + AmlCompute 叢集現在支援在布建時于叢集上設定受控識別。 只要指定您是否要使用系統指派的身分識別或使用者指派的身分識別，並針對後者傳遞 identityId。 然後，您可以設定許可權來存取各種資源（例如儲存體或 ACR），以使用計算的身分識別來安全地存取資料，而不是 AmlCompute 目前採用的權杖型方法。 如需參數的詳細資訊，請參閱我們的 SDK 參考。
  

+ **重大變更**
  + AmlCompute 叢集支援以執行為基礎之建立的預覽功能，我們將在兩周內規劃淘汰。 您可以繼續使用 Amlcompute 類別繼續建立持續性計算目標，但不久的未來將不會支援指定識別碼為 "Amlcompute" 的特定方法作為執行設定中的計算目標。 

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 在計算資料行中的唯一值數目時，啟用 unhashable 類型的支援。
  + **azureml-core**
    + 改善使用 TabularDataset 從 Azure Blob 儲存體讀取時的穩定性。
    + 改進參數的檔 `grant_workspace_msi` `Datastore.register_azure_blob_store` 。
    + 修正了錯誤， `datastore.upload` 以支援 `src_dir` 結束于或的引數 `/` `\` 。
    + 在嘗試上傳至沒有存取金鑰或 SAS 權杖的 Azure Blob 儲存體資料存放區時，已新增可採取動作的錯誤訊息。
  + **azureml-interpret**
    + 已針對上傳的說明，將視覺效果資料的上限新增至檔案大小。
  + **azureml-train-automl-client**
    + 明確檢查 label_column_name & weight_column_name 參數，讓 AutoMLConfig 成為字串類型。
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep 現在支援資料集做為管線參數。 使用者可以使用範例資料集來建立管線，並且可以變更相同類型的輸入資料集 (檔案或表格式) 來執行新的管線。

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK for Python v 1.3。0

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 在訓練後作業方面新增額外的遙測。
    + 使用平方的條件加總來加速自動 ARIMA 訓練， (CSS) 長度超過100的系列定型。 使用的長度會儲存為常數 ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/in TimeSeriesInternal 類別，位於/src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + 已改善使用者記錄的預測執行，現在記錄中會顯示目前正在執行之階段的詳細資訊
    + 不允許的 target_rolling_window_size 設定為小於2的值
  + **azureml-automl-runtime**
    + 已改善在找到重複的時間戳記時所顯示的錯誤訊息。
    + 不允許的 target_rolling_window_size 設定為小於2的值。
    + 修正延遲插補失敗。 此問題是因為季節性分解數列所需的觀察數目不足所造成。 「Seasonalized」資料可用來計算部分 autocorrelation 函數 (PACF) 來判斷延隔時間長度。
    + 啟用的資料行目的特徵化由特徵化設定進行預測工作的自訂。現在支援以數值和類別作為預測工作的資料行用途。
    + 啟用的 drop column 特徵化自訂依特徵化設定的預測工作。
    + 藉由特徵化設定為預測工作啟用插補自訂。現在支援目標資料行的常數值插補，以及定型資料的 mean、中位數、most_frequent 和常數值插補。
  + **azureml-contrib-pipeline-steps**
    + 接受要傳遞給 ParallelRunConfig 的字串計算名稱
  + **azureml-core**
    +  已新增環境。複製 (new_name) API 以建立環境物件的複本
    +  Environment.docker.base_dockerfile 接受 filepath。 如果能夠解析檔案，內容將會讀入 base_dockerfile 環境屬性中
    + 當使用者在 Environment.docker 中手動設定值時，會自動重設 base_image 和 base_dockerfile 的互斥值
    + 已在 RSection 中新增 user_managed 旗標，指出環境是否由使用者或 AzureML 管理。
    + 資料集：如果資料路徑包含 unicode 字元，則修正資料集下載失敗。
    + 資料集：改良的資料集裝載快取機制，以尊重 Azure Machine Learning 計算中的最小磁碟空間需求，這可避免讓節點無法使用，並導致取消作業。
    + Dataset：當您存取時間序列資料集做為 pandas 資料框架時，我們會加入時間序列資料行的索引，用來加速存取以時間序列為基礎的資料存取。  先前，索引的名稱與時間戳記資料行的名稱相同，因此混淆的使用者是實際的時間戳記資料行，而是索引。 我們現在不會將任何特定名稱提供給索引，因為它不應該當做資料行使用。 
    + 資料集：已修正主權雲端中的資料集驗證問題。
    + 資料集：已修正 `Dataset.to_spark_dataframe` 從 Azure 于 postgresql 資料存放區建立的資料集失敗。
  + **azureml-interpret**
    + 在本機重要性值為稀疏時，將全域分數新增至視覺效果
    + 已更新 azureml-解讀以使用解讀-社區0.9。 *
    + 修正了下載具有稀疏評估資料之說明的問題
    + 在 AutoML 中新增了說明物件的稀疏格式支援
  + **azureml-pipeline-core**
    + 在管線中支援 ComputeInstance 作為計算目標
  + **azureml-train-automl-client**
    + 在訓練後作業方面新增額外的遙測。
    + 修正提早停止的回歸
    + 已淘汰的 azureml. >iris.dprep 是輸入資料的有效類型。
    +  將預設的 AutoML 實驗時間變更為六天。
  + **azureml-train-automl-runtime**
    + 在訓練後作業方面新增額外的遙測。
    + 已新增稀疏 AutoML 端對端支援
  + **azureml-opendatasets**
    + 已新增服務監視的其他遙測。
    + 啟用 blob 的前門以提高穩定性 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK for Python v 1.2。0

+ **重大變更**
  + 捨棄對 python 2.7 的支援

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 將 "--訂用帳戶識別碼" 新增至 `az ml model/computetarget/service` CLI 中的命令
    + 新增支援將客戶管理的金鑰傳遞 (CMK) vault_url、key_name 和 key_version 進行 ACI 部署
  + **azureml-automl-core** 
    + 為 X 和 y 資料預測工作啟用了具有常數值的自訂插補。
    + 修正中的問題，並向使用者顯示錯誤訊息。    
  + **azureml-automl-runtime**
    + 修正了在資料集上進行預測的問題，其中包含只有一個資料列的粒紋
    + 減少預測工作所需的記憶體數量。
    + 如果時間資料行的格式不正確，則新增更好的錯誤訊息。
    + 為 X 和 y 資料預測工作啟用了具有常數值的自訂插補。
  + **azureml-core**
    + 已新增從環境變數載入 ServicePrincipal 的支援： AZUREML_SERVICE_PRINCIPAL_ID、AZUREML_SERVICE_PRINCIPAL_TENANT_ID 和 AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + 導入了新的參數 `support_multi_line` `Dataset.Tabular.from_delimited_files` ：根據預設 (`support_multi_line=False`) ，所有分行符號（包括以引號括住的域值）都會被視為記錄中斷。 以這種方式讀取資料，更快且更適合在多個 CPU 核心上平行執行。 不過，它可能會導致以無訊息模式產生更多具有未對齊域值的記錄。 `True`如果有分隔的檔案包含加上引號的分行符號，則應該設定為。
    + 已新增在 Azure Machine Learning CLI 中註冊 ADLS Gen2 的功能
    + 已將參數 ' fine_grain_timestamp ' 重新命名為 ' timestamp '，並將參數 ' coarse_grain_timestamp ' 重新命名為 TabularDataset 中 with_timestamp_columns ( # A1 方法的 ' partition_timestamp '，以更清楚地反映參數的使用方式。
    + 將最大實驗名稱長度增加至255。
  + **azureml-interpret**
    + 已更新 azureml-解讀以解讀0.7。 *
  + **azureml-sdk**
    + 變更為具有相容版本的相依性，以支援發行前版本和穩定版本中的修補。


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK for Python v 1.1。5

+ **功能淘汰**
  + **Python 2.7**
    + 支援 python 2.7 的最後版本

+ **重大變更**
  + **語意化版本控制系統 2.0.0** \(英文\)
    + 從1.1 版開始，Azure ML Python SDK 會採用語義版本控制2.0.0。 請[在這裡閱讀更多資訊](https://semver.org/)。 所有後續版本都將遵循新的編號配置和語義版本設定合約。 

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 將端點 CLI 命令名稱從 [az ml endpoint aks] 變更為 [az ml endpoint real time] 以保持一致性。
    + 更新穩定和實驗性分支 CLI 的 CLI 安裝指示
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-automl-core**
    + 已啟用批次模式推斷 (在 AutoML ONNX 模型中) 一次就接受多個資料列
    + 改善資料集的頻率偵測、缺少資料或包含異常資料點
    + 已新增移除資料點但不符合主要頻率的功能。
    + 變更函式的輸入，以取得套用對應資料行之插補選項的選項清單。
    + 錯誤記錄已獲得改善。
  + **azureml-automl-runtime**
    + 修正當測試集內出現不在定型集中的細微性時，所擲回錯誤的問題
    + 在預測服務的評分期間移除 y_query 需求
    + 修正當資料集包含長時間間距的短粒紋時的預測問題。
    + 修正自動大範圍開啟時的問題，以及日期資料行以字串形式包含日期。 不可能轉換為日期時，已新增適當的轉換和錯誤訊息
    + 使用原生 NumPy 和 SciPy 來序列化和還原序列化用於本機 AutoML 執行之 FileCacheStore (的中繼資料) 
    + 修正失敗的子回合可能停滯在執行中狀態的 bug。
    + 提升特徵化速度。
    + 修正評分期間的頻率檢查，現在預測工作不需要定型和測試集之間嚴格的頻率相等。
    + 變更函式的輸入，以取得套用對應資料行之插補選項的選項清單。
    + 修正與 lag 類型選取相關的錯誤。
    + 修正資料集上引發的非分類錯誤，具有單一資料列的粒紋
    + 修正了頻率偵測緩慢的問題。
    + 修正 AutoML 例外狀況處理中的錯誤，此錯誤會造成定型失敗的實際原因被 AttributeError 取代。
  + **azureml-cli-通用**
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-contrib-mir**
    + 在 MirWebservice 類別中新增功能以取得存取權杖
    + 在 MirWebservice 期間，預設會使用 MirWebservice 的 token 驗證。如果呼叫失敗，請執行 ( # A1 僅呼叫更新
    + Mir webservice 部署現在需要適當的 Sku [Standard_DS2_v2、Standard_F16 Standard_A2_v2]，而不是分別是 [Ds2v2、A2v2 和 F16]。
  + **azureml-contrib-pipeline-steps**
    + 選擇性參數 side_inputs 新增至 ParallelRunStep。 此參數可用於在容器上掛接資料夾。 目前支援的類型為 DataReference 和 PipelineData。
    + 現在傳遞管線參數可以覆寫 ParallelRunConfig 中傳遞的參數。 支援 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout (aml_node_count 和 aml_process_count_per_node 的新管線參數已是舊版) 的一部分。
  + **azureml-core**
    + 現在已部署的 AzureML Webservices 會預設為 `INFO` 記錄。 這可以藉由在已 `AZUREML_LOG_LEVEL` 部署的服務中設定環境變數來控制。
    + Python sdk 使用探索服務來使用「api」端點，而不是「管線」。
    + 切換至所有 SDK 呼叫中的新路由。
    + 將 ModelManagementService 呼叫的路由變更為新的整合結構。
      + 讓工作區更新方法可公開使用。
      + 在工作區更新方法中新增 image_build_compute 參數，以允許使用者更新映射組建的計算。
    + 已將取代訊息新增至舊的分析工作流程。 修正分析 cpu 和記憶體限制。
    + 新增 RSection 作為環境的一部分，以執行 R 作業。
    + 已將驗證新增至， `Dataset.mount` 以在資料集的來源無法存取或未包含任何資料時引發錯誤。
    + 新增 `--grant-workspace-msi-access` 為數據存放區 CLI 的額外參數，用來註冊 Azure Blob 容器，可讓您註冊位於 VNet 後方的 Blob 容器。
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
    + 修正了 aks.py _deploy 的問題。
    + 驗證所上傳模型的完整性，以避免無訊息儲存失敗。
    + 使用者現在可以在重新產生 webservices 的金鑰時，為驗證金鑰指定一個值。
    + 修正無法使用大寫字母作為資料集之輸入名稱的 bug。
  + **azureml-預設值**
    + `azureml-dataprep` 現在將會安裝為的一部分 `azureml-defaults` 。 您不再需要在計算目標上手動安裝資料準備 [保險絲] 來裝載資料集。
  + **azureml-interpret**
    + 已更新 azureml-解讀以解讀0.6。 *
    + 已更新 azureml-解讀以相依于解讀-社區0.5。0
    + 已將 azureml 樣式的例外狀況新增至 azureml-解讀
    + 已修正 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-mlflow**
    + 將主權 cloud 的支援新增至 azureml. mlflow
  + **azureml-pipeline-core**
    + 管線批次評分筆記本現在使用 ParallelRunStep
    + 修正儘管變更引數清單，PythonScriptStep 結果可能不正確重複使用的 bug
    + 新增在上呼叫 parse_ * 方法時，設定資料行類型的功能 `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + 已將移 `AutoMLStep` 至 `azureml-pipeline-steps` 封裝。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。
    + 已新增資料集的檔範例作為 PythonScriptStep 輸入
  + **azureml-tensorboard**
    + 已更新 azureml-tensorboard 以支援 tensorflow 2。0
    + 使用計算實例上的自訂 Tensorboard 埠時，顯示正確的埠號碼
  + **azureml-train-automl-client**
    + 修正了某些封裝可能在遠端執行上的版本不正確的問題。
    + 已修正 FeaturizationConfig 覆寫篩選自訂特徵化設定的問題。
  + **azureml-train-automl-runtime**
    + 修正遠端執行中的頻率偵測問題
    + 已將移至 `AutoMLStep` `azureml-pipeline-steps` 封裝中。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 在 PyTorch 估算器中支援 PyTorch 1.4 版
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.2 rc0 (發行前版本) 

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已啟用批次模式推斷 (在 AutoML ONNX 模型中) 一次就接受多個資料列
    + 改善資料集的頻率偵測、缺少資料或包含異常資料點
    + 已新增移除資料點但不符合主要頻率的功能。
  + **azureml-automl-runtime**
    + 修正當測試集內出現不在定型集中的細微性時，所擲回錯誤的問題
    + 在預測服務的評分期間移除 y_query 需求
  + **azureml-contrib-mir**
    + 在 MirWebservice 類別中新增功能以取得存取權杖
  + **azureml-core**
    + 現在已部署的 AzureML Webservices 會預設為 `INFO` 記錄。 這可以藉由在已 `AZUREML_LOG_LEVEL` 部署的服務中設定環境變數來控制。
    + 修正反覆運算 `Dataset.get_all` 以傳回所有已向工作區註冊的資料集。
    + 當傳遞不正確類型給 `path` 資料集建立 api 的引數時，改善錯誤訊息。
    + Python sdk 使用探索服務來使用「api」端點，而不是「管線」。
    + 在所有 SDK 呼叫中交換至新的路由
    + 將 ModelManagementService 呼叫的路由變更為新的整合結構
      + 讓工作區更新方法可公開使用。
      + 在工作區更新方法中新增 image_build_compute 參數，以允許使用者更新映射組建的計算
    +  已將取代訊息新增至舊的分析工作流程。 修正分析 cpu 和記憶體限制
  + **azureml-interpret**
    + 更新 azureml-解讀以解讀0.6。 *
  + **azureml-mlflow**
    + 將主權 cloud 的支援新增至 azureml. mlflow
  + **azureml-pipeline-steps**
    + 已將移 `AutoMLStep` 至 `azureml-pipeline-steps package` 。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-automl-client**
    + 修正了某些封裝可能在遠端執行上的版本不正確的問題。
  + **azureml-train-automl-runtime**
    + 修正遠端執行中的頻率偵測問題
    + 已將移 `AutoMLStep` 至 `azureml-pipeline-steps package` 。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 已將移 `AutoMLStep` 至 `azureml-pipeline-steps package` 。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>適用于 Python 的 Azure Machine Learning SDK rc0 (發行前版本) 

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-automl-core**
    + 錯誤記錄已獲得改善。
  + **azureml-automl-runtime**
    + 修正當資料集包含長時間間距的短粒紋時的預測問題。
    + 修正自動大範圍開啟時的問題，以及日期資料行以字串形式包含日期。 如果無法轉換為日期，我們新增了適當的轉換和合理的錯誤
    + 使用原生 NumPy 和 SciPy 來序列化和還原序列化用於本機 AutoML 執行之 FileCacheStore (的中繼資料) 
    + 修正失敗的子回合可能停滯在執行中狀態的 bug。
  + **azureml-cli-通用**
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
  + **azureml-core**
    + 新增 `--grant-workspace-msi-access` 為數據存放區 CLI 的額外參數，以註冊 Azure Blob 容器，可讓您註冊位於 VNet 後方的 Blob 容器
    + 已修正單一實例分析以產生建議，並可在 core sdk 中取得。
    + 修正了 aks.py _deploy 的問題
    + 驗證所上傳模型的完整性，以避免無訊息儲存失敗。
  + **azureml-interpret**
    + 已將 azureml 樣式的例外狀況新增至 azureml-解讀
    + 已修正 keras 模型的 DeepScoringExplainer 序列化
  + **azureml-pipeline-core**
    + 管線批次評分筆記本現在使用 ParallelRunStep
  + **azureml-pipeline-steps**
    + 已將移至 `AutoMLStep` `azureml-pipeline-steps` 封裝中。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-contrib-pipeline-steps**
    + 選擇性參數 side_inputs 新增至 ParallelRunStep。 此參數可用於在容器上掛接資料夾。 目前支援的類型為 DataReference 和 PipelineData。
  + **azureml-tensorboard**
    + 已更新 azureml-tensorboard 以支援 tensorflow 2。0
  + **azureml-train-automl-client**
    + 已修正 FeaturizationConfig 覆寫篩選自訂特徵化設定的問題。
  + **azureml-train-automl-runtime**
    + 已將移至 `AutoMLStep` `azureml-pipeline-steps` 封裝中。 中的已淘汰 `AutoMLStep` `azureml-train-automl-runtime` 。
  + **azureml-train-core**
    + 在 PyTorch 估算器中支援 PyTorch 1.4 版
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.0 rc0 (發行前版本) 

+ **重大變更**
  + **語意化版本控制系統 2.0.0** \(英文\)
    + 從1.1 版開始，Azure ML Python SDK 會採用語義版本控制2.0.0。 請[在這裡閱讀更多資訊](https://semver.org/)。 所有後續版本都將遵循新的編號配置和語義版本設定合約。 
  
+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 提升特徵化速度。
    + 修正評分期間的頻率檢查，現在在預測工作中，我們不需要定型和測試集之間嚴格的頻率相等。
  + **azureml-core**
    + 使用者現在可以在重新產生 webservices 的金鑰時，為驗證金鑰指定一個值。
  + **azureml-interpret**
    + 已更新 azureml-解讀以相依于解讀-社區0.5。0
  + **azureml-pipeline-core**
    + 修正儘管變更引數清單，PythonScriptStep 結果可能不正確重複使用的 bug
  + **azureml-pipeline-steps**
    + 已新增資料集的檔範例作為 PythonScriptStep 輸入
  + **azureml-contrib-pipeline-steps**
    + 現在傳遞管線參數可以覆寫 ParallelRunConfig 中傳遞的參數。 支援 aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout (aml_node_count 和 aml_process_count_per_node 的新管線參數已是舊版) 的一部分。
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK for Python v 1.0.85

+ **新功能**
  + **azureml-core**
    + 取得在指定的工作區和訂用帳戶中 AmlCompute 資源目前的核心使用量和配額限制
  
  + **azureml-contrib-pipeline-steps**
    + 讓使用者將表格式資料集作為前一個步驟的中繼結果傳遞給 parallelrunstep

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 已將要求中 y_query 資料行的需求，移除至已部署的預測服務。 
    + 已從 Dominick 的橙色柳橙汁筆記本服務要求區段中移除 ' y_query '。
    + 修正 bug：在具有日期時間資料行的資料集上操作的已部署模型上，無法進行預測。
    + 將馬修斯相互關聯係數新增為二元和多元分類的分類度量。
  + **azureml-contrib-interpret**
    + 移除 explainers 自 azureml 的文字-contrib-解讀為文字說明已移至即將發行的解讀文字存放庫。
  + **azureml-core**
    + 資料集：檔案資料集的使用量不再相依于要安裝在 python env 中的 numpy 和 pandas。
    + 已變更 LocalWebservice.wait_for_deployment ( # A1 以檢查本機 Docker 容器的狀態，然後再嘗試 ping 其健康情況端點，大幅減少回報失敗部署所需的時間量。
    + 修正了 LocalWebservice 中使用的內部屬性初始化。當使用 LocalWebservice ( # A3 函式從現有的部署建立服務物件時，就會重載 ( # A1。
    + 已編輯錯誤訊息以供澄清。
    + 將名為 get_access_token ( # A1 的新方法新增至 >akswebservice，此方法會傳回 AksServiceAccessToken 物件，其中包含存取權杖、時間戳記之後的重新整理、時間戳記的到期日和權杖類型。 
    + 因為新方法會傳回這個方法所傳回的所有資訊，所以 >akswebservice 中已取代現有的 get_token ( # A1 方法。
    + 已修改 az ml 服務取得存取權杖命令的輸出。 將 token 重新命名為 accessToken，並將 refreshBy 重新命名為 refreshAfter。 已新增 expiryOn 和 tokenType 屬性。
    + 已修正 get_active_runs
  + **azureml-explain-model**
    + 已將 shap 更新為0.33.0，並將-社區轉譯為0.4。 *
  + **azureml-interpret**
    + 已將 shap 更新為0.33.0，並將-社區轉譯為0.4。 *
  + **azureml-train-automl-runtime**
    + 將馬修斯相互關聯係數新增為二元和多元分類的分類度量。
    + 預設會開啟程式碼中的取代前置處理旗標，並取代為特徵化-特徵化

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK for Python v 1.0.83

+ **新功能**
  + Dataset：加入兩個 `on_error` 選項 `out_of_range_datetime` ， `to_pandas_dataframe` 如果資料有錯誤值而不是填滿，則會失敗 `None` 。
  + 工作區：新增 `hbi_workspace` 具有機密資料的工作區旗標，可進一步加密並停用工作區上的 advanced diagnostics。 此外，我們也新增了在建立工作區時，藉由指定和參數來為相關聯的 Cosmos DB 實例提供您自己的金鑰的支援，這會在布 `cmk_keyvault` `resource_cmk_uri` 建您的工作區時，在訂用帳戶中建立 Cosmos DB 實例。 [請於此處深入了解。](./concept-data-encryption.md#azure-cosmos-db)

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 修正在 Python 版本下方執行 AutoML 的3.5.4 時，造成 TypeError 引發的回歸。
  + **azureml-core**
    + 中的修正錯誤 `datastore.upload_files` 是無法使用開頭為的相對路徑 `./` 。
    + 已新增所有映射類別程式碼路徑的取代訊息
    + 已修正 Azure 中國世紀地區的模型管理 URL 結構。
    + 已修正使用 source_dir 的模型無法封裝 Azure Functions 的問題。    
    + 已新增選項，可 [Environment.build_local ( # B1 ](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) 將映射推送至 AzureML 工作區 container registry
    + 已更新 SDK，以便在 Azure synapse 上以回溯相容的方式使用新的權杖程式庫。
  + **azureml-interpret**
    + 修正未提供任何說明可供下載時，未傳回任何的 bug。 現在會引發例外狀況，並在其他地方比對行為。
  + **azureml-pipeline-steps**
    + `DatasetConsumptionConfig` `Estimator` `inputs` 當 `Estimator` 將在中使用時，不允許將 s 參數傳遞至的參數 `EstimatorStep` 。
  + **azureml-sdk**
    + 已將 AutoML 用戶端新增至 azureml sdk 套件，以在不安裝完整 AutoML 套件的情況下提交遠端 AutoML 執行。
  + **azureml-train-automl-client**
    + 更正 AutoML 執行的主控台輸出對齊
    + 修正了可能會在遠端 amlcompute 上安裝不正確版本之 pandas 的錯誤（bug）。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK for Python v 1.0.81

+ **Bug 修正和改善**
  + **azureml-contrib-interpret**
    + 將 shap 相依性延遲至 azureml 的解讀-解讀
  + **azureml-core**
    + 您現在可以將計算目標指定為對應部署設定物件的參數。 這是要部署的計算目標名稱，而不是 SDK 物件。
    + 將 CreatedBy 資訊新增至模型和服務物件。 可以存取 through.created_by
    + 已修正 Save-containerimage，請執行未正確設定 Docker 容器的 HTTP 埠 ( # A1。
    + `azureml-dataprep`針對 CLI 命令設為選用 `az ml dataset register`
    + 修正 `TabularDataset.to_pandas_dataframe` 不正確切換回替代讀取器並列印警告的 bug。
  + **azureml-explain-model**
    + 將 shap 相依性延遲至 azureml 的解讀-解讀
  + **azureml-pipeline-core**
    + 已加入新的管線步驟 `NotebookRunnerStep` ，以在管線中執行本機筆記本。
    + 已移除 PublishedPipelines、排程和 PipelineEndpoints 的已淘汰 get_all 函式
  + **azureml-train-automl-client**
    + 已開始將 data_script 取代為 AutoML 的輸入。


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK for Python v 1.0.79

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已移除要記錄的 featurizationConfig
      + 已將記錄更新為只記錄「自動」/「關閉」/「自訂」。
  + **azureml-automl-runtime**
    + 新增對 pandas 的支援。 數列和 pandas。 用於偵測資料行資料類型的類別。 先前只支援 numpy. ndarray
      + 已新增相關的程式碼變更，以正確地處理類別 dtype。
    + 已改善預測函數介面： y_pred 參數設為選擇性。 -Docstrings 已改善。
  + **azureml-contrib-dataset**
    + 修正無法裝載標記資料集的 bug。
  + **azureml-core**
    + 修正的錯誤 `Environment.from_existing_conda_environment(name, conda_environment_name)` 。 使用者可以建立環境的實例，該實例是本機環境的精確複本
    + 預設會將時間序列相關的資料集方法變更為 `include_boundary=True` 。
  + **azureml-train-automl-client**
    + 修正當 [顯示輸出] 設定為 [false] 時，不會列印驗證結果的問題。


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK for Python v 1.0.76

+ **重大變更**
  + Azureml-定型-AutoML 升級問題
    + 升級至 azureml-定型-automl>= 1.0.76 從 azureml 定型-automl<1.0.76 可能會導致部分安裝，導致某些 AutoML 匯入失敗。 若要解決此問題，您可以執行在中找到的安裝腳本 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd 。 或者，如果您直接使用 pip，您可以：
      + "pip install--upgrade azureml-automl"
      + 「pip 安裝--忽略已安裝的 azureml-automl-用戶端」
    + 或者，您可以在升級之前先卸載舊版本
      + 「pip 卸載 azureml-定型-automl」
      + "pip install azureml-automl"

+ **Bug 修正和改善**
  + **azureml-automl-runtime**
    + 在計算二元分類工作的平均純量計量時，AutoML 現在會考慮 true 和 false 類別。
    + 將 AutoML 核心中的機器學習和定型程式碼移至新的套件 AzureML-AutoML-Runtime。
  + **azureml-contrib-dataset**
    + 在 `to_pandas_dataframe` 具有下載選項的已加上標籤資料集上呼叫時，您現在可以指定是否要覆寫現有的檔案。
    + 當呼叫 `keep_columns` 或 `drop_columns` 產生要卸載的時間序列、標籤或影像資料行時，也會針對資料集卸載對應的功能。
    + 修正了物件偵測工作的 pytorch 載入器問題。
  + **azureml-contrib-interpret**
    + 已從 azureml 移除說明儀表板 widget-contrib-解讀、變更的套件，以參考 interpret_community 中的新套件
    + 將解讀-社區更新為0.2.0 的版本
  + **azureml-core**
    + 改善的效能 `workspace.datasets` 。
    + 新增了利用使用者名稱和密碼驗證註冊 Azure SQL Database 資料存放區的功能
    + 從相對路徑載入 RunConfigurations 的修正。
    + 當呼叫 `keep_columns` 或 `drop_columns` 導致卸載時間序列資料行時，也會針對資料集卸載對應的功能。
  + **azureml-interpret**
    + 將解讀-社區更新為0.2.0 的版本
  + **azureml-pipeline-steps**
    + `runconfig_pipeline_params`針對 Azure machine learning 管線步驟所記載的支援值。
  + **azureml-pipeline-core**
    + 已新增 CLI 選項，以下載管線命令的 json 格式輸出。
  + **azureml-定型-automl**
    + 將 AzureML 定型-AutoML 分成兩個套件：用戶端套件 AzureML-定型-AutoML-用戶端和 ML 訓練套件 AzureML-AutoML-Runtime
  + **azureml-train-automl-client**
    + 已新增瘦用戶端來提交 AutoML 實驗，而不需要在本機安裝任何機器學習相依性。
    + 修正在遠端執行中自動偵測延遲、滾動視窗大小和最大 horizons 的記錄。
  + **azureml-train-automl-runtime**
    + 新增了新的 AutoML 套件，以將機器學習服務和執行時間元件與用戶端隔離。
  + **azureml-contrib-訓練-rl**
    + SDK 中已新增增強式學習支援。
    + 已新增 RL SDK 中的 AmlWindowsCompute 支援。


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK for Python v 1.0.74

  + **預覽功能**
    + **azureml-contrib-dataset**
      + 匯入 azureml-contrib-資料集之後，您可以呼叫 `Dataset.Labeled.from_json_lines` 而不是建立已加上 `._Labeled` 標籤的資料集。
      + 在 `to_pandas_dataframe` 具有下載選項的已加上標籤資料集上呼叫時，您現在可以指定是否要覆寫現有的檔案。
      + 當呼叫 `keep_columns` 或 `drop_columns` 產生要卸載的時間序列、標籤或影像資料行時，也會針對資料集卸載對應的功能。
      + 修正呼叫時 PyTorch 載入器的問題 `dataset.to_torchvision()` 。

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 已將模型分析新增至預覽 CLI。
    + 修正 Azure 儲存體造成 AzureML CLI 失敗的中斷性變更。
    + 已將 Load Balancer 類型新增至 AKS 類型的 LIP.MLC
  + **azureml-automl-core**
    + 修正了在時間序列上偵測到最大範圍、具有遺漏值和多個粒紋的問題。
    + 修正在產生跨驗證分割時失敗的問題。
    + 將此區段取代為 markdown 格式的訊息，以顯示在版本資訊中：-改善預測資料集中短粒紋的處理。
    + 修正在記錄期間遮罩某些使用者資訊的問題。 -改進預測執行期間的錯誤記錄。
    + 將 psutil 做為 conda 相依性新增至自動產生的 yml 部署檔案。
  + **azureml-contrib-mir**
    + 修正 Azure 儲存體造成 AzureML CLI 失敗的中斷性變更。
  + **azureml-core**
    + 修正導致 Azure Functions 部署模型的 bug，以產生500s。
    + 修正了 amlignore 檔案未套用在快照集上的問題。
    + 已加入新的 API amlcompute.get_active_runs，其會傳回在指定的 amlcompute 上執行和已排入佇列的產生器。
    + 已將 Load Balancer 類型新增至 AKS 類型的 LIP.MLC。
    + 已將 append_prefix bool 參數新增至 artifacts_client 中的 run.py 和 download_artifacts_from_prefix download_files。 此旗標是用來選擇性地壓平合併原始檔案路徑，因此只會將檔案或資料夾名稱新增至 output_directory
    + 修正 `run_config.yml` 資料集使用方式的還原序列化問題。
    + 當呼叫 `keep_columns` 或 `drop_columns` 導致卸載時間序列資料行時，也會針對資料集卸載對應的功能。
  + **azureml-interpret**
    + 將解讀-社區版本更新為0.1.0。3
  + **azureml-定型-automl**
    + 修正 automl_step 可能無法列印驗證問題的問題。
    + 已修正 register_model 即使模型的環境在本機遺失相依性，也會成功。
    + 修正了某些遠端執行未啟用 docker 的問題。
    + 加入導致本機執行失敗的例外狀況記錄。
  + **azureml-train-core**
    + 請考慮 resume_from 在自動超參數微調最佳子執行的計算中執行。
  + **azureml-pipeline-core**
    + 修正管線引數結構中的參數處理。
    + 已新增管線描述和步驟類型 yaml 參數。
    + 新的管線步驟 yaml 格式，並新增舊格式的淘汰警告。



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Web 體驗

中的共同作業工作區登陸頁面已 [https://ml.azure.com](https://ml.azure.com) 增強，並更名為 Azure Machine Learning studio。

您可以從 studio 訓練、測試、部署和管理 Azure Machine Learning 的資產，例如資料集、管線、模型、端點等等。

從 studio 存取下列以網頁為基礎的編寫工具：

| 以 Web 為基礎的工具 | 描述 | 
|-|-|-|
| 筆記本 VM (預覽)  | 完全受控的雲端式工作站 | 
| [自動化機器學習](tutorial-first-experiment-automated-ml.md) (預覽)  | 自動化機器學習模型開發的程式碼體驗 | 
| [設計工具](concept-designer.md) | 拖放機器學習模型工具先前稱為視覺化介面 | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning 設計工具增強功能

+ 先前稱為視覺化介面 
+    11個新 [模組](algorithm-module-reference/module-reference.md) ，包括 recommender、分類器和定型公用程式，包括特徵工程、交叉驗證和資料轉換。

### <a name="r-sdk"></a>R SDK 
 
資料科學家和 AI 開發人員使用 [適用于 R 的 AZURE MACHINE LEARNING SDK](tutorial-1st-r-experiment.md) ，以 Azure Machine Learning 建立及執行機器學習工作流程。

適用于 R 的 Azure Machine Learning SDK 會使用 `reticulate` 套件來系結至 PYTHON SDK。 藉由直接系結至 Python，適用于 R 的 SDK 可讓您從任何您選擇的 R 環境，存取 Python SDK 中所執行的核心物件和方法。

SDK 的主要功能包括：

+    管理用來對您的機器學習實驗進行監視、記錄及組織的雲端資源。
+    使用雲端資源（包括 GPU 加速模型定型）來定型模型。
+    將您的模型部署為 Azure 容器實例上的 webservices (ACI) 和 Azure Kubernetes Service (AKS) 。

如需完整檔，請參閱 [套件網站](https://azure.github.io/azureml-sdk-for-r) 。

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning 與事件方格整合 

Azure Machine Learning 現在是事件方格的資源提供者，您可以透過 Azure 入口網站或 Azure CLI 來設定機器學習事件。 使用者可以建立執行完成的事件、模型註冊、模型部署，以及偵測到的資料漂移。 這些事件可以路由傳送至事件方格所支援的事件處理常式，以供取用。 如需詳細資訊，請參閱機器學習事件 [架構](../event-grid/event-schema-machine-learning.md) 和 [教學](how-to-use-event-grid.md) 課程文章。

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK for Python v 1.0.72 版

+ **新功能**
  + 透過 [**azureml datadrift**](/python/api/azureml-datadrift) 套件新增了資料集監視器，可讓您監視時間序列資料集的資料漂移或其他統計變更。 如果偵測到漂移或符合其他條件的資料，就可以觸發警示和事件。 請參閱 [我們的檔](how-to-monitor-datasets.md) 以取得詳細資料。
  + 宣佈兩個新版本 (也稱為可在 Azure Machine Learning 交換) 的 SKU。 在此版本中，您現在可以建立基本或企業 Azure Machine Learning 工作區。 所有現有的工作區都會預設為基本版本，您可以移至 Azure 入口網站或 studio 以隨時升級工作區。 您可以從 Azure 入口網站建立基本或企業工作區。 若要深入瞭解，請參閱 [我們的檔](./how-to-manage-workspace.md) 。 從 SDK 中，您可以使用工作區物件的 "sku" 屬性來決定您的工作區版本。
  + 我們也增強了 Azure Machine Learning 計算-您現在可以在 Azure 監視器中查看叢集的計量 (例如節點總計、執行中節點、核心配額) 總計，除了可讓您流覽診斷記錄以進行偵測。 此外，您也可以在叢集上查看目前執行中或已排入佇列的回合，以及詳細資料，例如叢集中各種節點的 Ip。 您可以在入口網站中或使用 SDK 或 CLI 中的對應函式來查看這些功能。

  + **預覽功能**
    + 在 Azure Machine Learning 計算中，我們會發行您本機 SSD 的磁片加密預覽支援。 提出技術支援票證，讓您的訂用帳戶允許清單使用此功能。
    + Azure Machine Learning 批次推斷的公開預覽。 Azure Machine Learning 批次推斷會以不區分時間的大型推斷作業為目標。 批次推斷可提供符合成本效益的推斷計算規模，並具有適用于非同步應用程式的無與倫比輸送量。 它最適用于大型資料集合的高輸送量、火災和遺忘推斷。
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + 已標記資料集的已啟用功能
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
    + 已新增支援部署和封裝支援的模型 (ONNX、scikit-learn-學習和 TensorFlow) ，而不需要 InferenceConfig 實例。
    + 已新增在 SDK 和 CLI (ACI 和 AKS) 的服務部署覆寫旗標。 如果有提供，將會覆寫現有的服務（如果已有同名的服務）。 如果服務不存在，將會建立新的服務。
    + 您可以使用兩個新架構（Onnx 和 Tensorflow）來註冊模型。 -模型註冊可接受範例輸入資料、範例輸出資料和模型的資源設定。
  + **azureml-automl-core**
    + 只有在設定執行時間條件約束時，定型反覆運算才會在子進程中執行。
    + 已新增預測工作的 guardrail，以檢查指定的 max_horizon 是否會在指定的電腦上造成記憶體問題。 如果有的話，將會顯示 guardrail 訊息。
    + 新增複雜頻率的支援，例如兩年和一個月。 -如果無法判斷頻率，則新增了理解錯誤訊息。
    + 新增 azureml-預設為自動產生的 conda env，以解決模型部署失敗
    + 允許 Azure Machine Learning 管線中的中繼資料轉換成表格式資料集，並在中使用 `AutoMLStep` 。
    + 已針對串流處理資料行目的更新。
    + 針對串流處理 Imputer 和 HashOneHotEncoder 所執行的轉換器參數更新。
    + 將目前的資料大小和所需的最小資料大小新增至驗證錯誤訊息。
    + 已更新交叉驗證所需的最小資料大小，以保證每個驗證折迭中至少有兩個樣本。
  + **azureml-cli-通用**
    + CLI 現在支援模型封裝。
    + 您可以使用兩個新架構（Onnx 和 Tensorflow）來註冊模型。
    + 模型註冊可接受範例輸入資料、範例輸出資料和模型的資源設定。
  + **azureml-contrib-gbdt**
    + 已修正筆記本的發行頻道
    + 針對不支援的非 AmlCompute 計算目標新增警告
    + 已將 LightGMB 估算器新增至 azureml-contrib-gbdt 套件
  + [**azureml-core**](/python/api/azureml-core)
    + CLI 現在支援模型封裝。
    + 針對已淘汰的資料集 Api 新增淘汰警告。 請參閱中的資料集 API 變更通知 https://aka.ms/tabular-dataset 。
    + [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)如果已註冊資料集，變更為傳回註冊名稱和版本。
    + 修正無法重複使用資料集做為引數來提交實驗執行的 bug。
    + 在執行期間抓取的資料集會進行追蹤，並可在執行詳細資料頁面中看到，或在 [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) 執行完成之後呼叫。
    + 允許 Azure Machine Learning 管線中的中繼資料轉換成表格式資料集，並在中使用 [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) 。
    + 已新增支援部署和封裝支援的模型 (ONNX、scikit-learn-學習和 TensorFlow) ，而不需要 InferenceConfig 實例。
    + 已新增在 SDK 和 CLI (ACI 和 AKS) 的服務部署覆寫旗標。 如果有提供，將會覆寫現有的服務（如果已有同名的服務）。 如果服務不存在，將會建立新的服務。
    +  您可以使用兩個新架構（Onnx 和 Tensorflow）來註冊模型。 模型註冊可接受範例輸入資料、範例輸出資料和模型的資源設定。
    + 已新增適用於 MySQL 的 Azure 資料庫的新資料存放區。 已新增在 Azure Machine Learning 管線的 DataTransferStep 中使用適用於 MySQL 的 Azure 資料庫的範例。
    + 新增了新增和移除實驗標記的功能，從執行中移除標記
    + 已新增在 SDK 和 CLI (ACI 和 AKS) 的服務部署覆寫旗標。 如果有提供，將會覆寫現有的服務（如果已有同名的服務）。 如果服務不存在，將會建立新的服務。
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + 移 `azureml-contrib-datadrift` 至 `azureml-datadrift`
    + 已針對漂移和其他統計量值新增監視時間序列資料集的支援
    + 新方法 `create_from_model()` 以及 `create_from_dataset()` [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) 類別。 `create()`方法將被取代。
    + 調整 Azure Machine Learning studio 中 Python 和 UI 的視覺效果。
    + 除了每日資料集監視器之外，還支援每週和每月監視排程。
    + 支援資料監視器度量的回填，以分析資料集監視器的歷程記錄資料。
    + 各種 Bug 修正
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-dataprep 不再需要從管線檔案提交 Azure Machine Learning 管線執行 `yaml` 。
  + [**azureml-定型-automl**](/python/api/azureml-train-automl-runtime/)
    + 新增 azureml-預設為自動產生的 conda env，以解決模型部署失敗
    + AutoML 遠端定型現在包含 azureml-預設值，以允許重複使用定型環境進行推斷。
  + **azureml-train-core**
    + 在估算器中新增 PyTorch 1.3 支援 [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch)

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>視覺化介面 (預覽) 

+ Azure Machine Learning 的視覺化介面 (預覽) 已翻修了在 [Azure Machine Learning 管線](concept-ml-pipelines.md)上執行。 在視覺化介面中撰寫 (先前稱為實驗) 的管線，現已與核心 Azure Machine Learning 體驗完全整合。
  + 使用 SDK 資產的整合管理體驗
  + 視覺化介面模型、管線和端點的版本控制和追蹤
  + 重新設計的 UI
  + 已新增批次推斷部署
  + 已新增 Azure Kubernetes Service (AKS，) 推斷計算目標的支援
  + 新的 Python-步驟管線撰寫工作流程
  + Visual authoring tools 的新[登陸頁面](https://ml.azure.com)

+ **新模組**
  + 套用數學運算
  + 套用 SQL 轉換
  + 剪輯值
  + 摘要資料
  + 從 SQL Database 匯入

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK for Python v 1.0.69

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 將模型說明限制為最佳執行，而不是計算每次執行的說明。 進行本機、遠端和 ADB 的這種行為變更。
    + 新增 UI 的隨選模型說明支援
    + 將 psutil 新增為 `automl` amlcompute 中的 conda 相依性，並將其納入 psutil。
    + 修正預測資料集上的啟發式延遲和滾動時間範圍大小的問題，其中某些系列可能會導致線性代數錯誤
      + 針對在預測執行中所決定的啟發式地參數，新增了 print out。
  + **azureml-contrib-datadrift**
    + 如果資料集層級漂移不在第一節中，則會在建立輸出計量時新增保護。
  + **azureml-contrib-interpret**
    + azureml-contrib-說明模型套件已重新命名為 azureml-contrib-解讀
  + **azureml-core**
    + 已新增用來取消註冊資料集的 API。 `dataset.unregister_all_versions()`
    + azureml-contrib-說明模型套件已重新命名為 azureml-contrib-解讀。
  + **[azureml-core](/python/api/azureml-core)**
    + 已新增用來取消註冊資料集的 API。 資料。[unregister_all_versions ( # B1 ](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)。
    + 已新增資料集 API 以檢查資料變更時間。 `dataset.data_changed_time`.
    + 能夠 `FileDataset` `TabularDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` 在 Azure Machine Learning 管線中使用和做為、和的輸入
    + 的效能已 `FileDataset.mount` 針對具有大量檔案的資料夾改善
    + 能夠使用 [FileDataset](/python/api/azureml-core/azureml.data.filedataset) 和 [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) 做為 Azure Machine Learning 管線中 [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)、 [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)和 [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) 的輸入。
    + FileDataset 的效能。已針對具有大量檔案的資料夾改善[掛接 ( # B1](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-)
    + 在 [執行詳細資料] 中，將 URL 新增至已知的錯誤建議。
    + 修正 run.get_metrics 中，如果執行的子系太多，要求會失敗的錯誤（bug）
    + 修正 [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) 中，如果執行的子系太多，要求會失敗的錯誤（bug）
    + 在 Arcadia 叢集上新增了驗證支援。
    + 建立實驗物件會取得或建立 Azure Machine Learning 工作區中的實驗，以進行執行歷程記錄追蹤。 實驗識別碼和封存時間會在建立時填入實驗物件中。 範例：實驗 = 實驗 (工作區，[新實驗] ) experiment_id = experiment.id 封存 ( # A3 和重新開機 ( # A5 是可在實驗上呼叫的函式，以隱藏和還原實驗，使其不會顯示在 UX 中，或根據預設在清單實驗的呼叫中傳回。 如果使用與封存實驗相同的名稱建立新的實驗，您可以藉由傳遞新名稱重新啟用封存的實驗。 只能有一個使用中的實驗具有指定的名稱。 範例： experiment1 = 實驗 (工作區，"Active 實驗" ) experiment1. archive ( # A3 # 使用與封存的相同名稱建立新的作用中實驗。 experiment2. = 實驗 (工作區、「使用中實驗」 ) experiment1。重新開機 (new_name = 「先前使用中的實驗」 ) 靜態方法清單 ( # A5 on 實驗可採用名稱篩選和 ViewType 篩選。 ViewType 值為 "ACTIVE_ONLY"、"ARCHIVED_ONLY" 和 "ALL" 範例： archived_experiments = 實驗。清單 (工作區，view_type = "ARCHIVED_ONLY" ) all_first_experiments = 實驗。清單 (工作區，名稱 = "第一個實驗"，view_type = "全部" ) 
    + 支援使用環境進行模型部署和服務更新
  + **azureml-datadrift**
    + DataDriftDector 類別的 show 屬性不再支援選擇性引數 ' with_details '。 Show 屬性只會顯示資料漂移係數和特徵資料行的資料漂移比重。
    + DataDriftDetector 屬性 ' get_output ' 行為變更：
      + 輸入參數 start_time，end_time 是選擇性的，而不是強制性。
      + 輸入特定 start_time 及/或在相同叫用中具有特定 run_id 的 end_time 將會導致值錯誤例外狀況，因為它們是互斥的
      + 藉由輸入特定 start_time 及/或 end_time，只會傳回已排程執行的結果;
      + 參數 ' daily_latest_only ' 已被取代。
    + 支援抓取以資料集為基礎的資料漂移輸出。
  + **azureml-explain-model**
    + 將 AzureML 解釋模型套件重新命名為 AzureML-解讀，讓舊的封裝立即提供回溯相容性
    + 已修正 `automl` 從 ExplanationClient 下載時，原始說明設定為分類工作而非回歸的 bug （預設值）
    + 新增 `ScoringExplainer` 可直接使用建立的支援 `MimicWrapper`
  + **azureml-pipeline-core**
    + 改善大型管線建立的效能
  + **azureml-train-core**
    + 已在 TensorFlow 估算器中新增 TensorFlow 2.0 支援
  + **azureml-定型-automl**
    + 建立 [實驗](/python/api/azureml-core/azureml.core.experiment.experiment) 物件會取得或建立 Azure Machine Learning 工作區中的實驗，以進行執行歷程記錄追蹤。 實驗識別碼和封存時間會在建立時填入實驗物件中。 範例：

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        封存[ ( # B1](/python/api/azureml-core/azureml.core.experiment.experiment#archive--)和[重新開機 ( # B3](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-)是可在實驗上呼叫的函式，可隱藏和還原實驗，使其不會顯示在 UX 中，或依預設在清單實驗的呼叫中傳回。 如果使用與封存實驗相同的名稱建立新的實驗，您可以藉由傳遞新名稱重新啟用封存的實驗。 只能有一個使用中的實驗具有指定的名稱。 範例：

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        實驗上 [ ( # B1 ](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) 的靜態方法清單可以採用名稱篩選和 ViewType 篩選。 ViewType 值為 "ACTIVE_ONLY"、"ARCHIVED_ONLY" 和 "ALL"。 範例：

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 支援使用環境進行模型部署和服務更新。
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)類別的 show 屬性不再支援選擇性引數 ' with_details '。 Show 屬性只會顯示資料漂移係數和特徵資料行的資料漂移比重。
    + DataDriftDetector 函式 [get_output] python/api/azureml-datadrift/azureml. datadrift. DataDriftDetector # 取得-輸出-開始時間-無--結束時間-無--執行-識別碼-無-) 行為變更：
      + 輸入參數 start_time，end_time 是選擇性的，而不是強制性。
      + 輸入特定的 start_time 及/或在相同叫用中具有特定 run_id 的 end_time 將會導致值錯誤例外狀況，因為它們是互斥的;
      + 藉由輸入特定 start_time 及/或 end_time，只會傳回已排程執行的結果;
      + 參數 ' daily_latest_only ' 已被取代。
    + 支援抓取以資料集為基礎的資料漂移輸出。
  + **azureml-explain-model**
    + 新增使用 MimicWrapper 直接建立的 [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py&preserve-view=true) 支援
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + 改善大型管線建立的效能。
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + 已在 [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) 估算器中新增 TensorFlow 2.0 支援。
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 當安裝程式反復專案失敗時，父執行將不再失敗，因為協調流程已經負責處理。
    + 已新增 conda AutoML 實驗的本機 docker 和本地支援
    + 已新增 conda AutoML 實驗的本機 docker 和本地支援。


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>新的 web 體驗 (預覽) 適用于 Azure Machine Learning 工作區

[新工作區入口網站](https://ml.azure.com)中的 [實驗] 索引標籤已更新，因此資料科學家可以更有效地監視實驗。 您可以探索下列功能：
+ 實驗中繼資料來輕鬆篩選和排序您的實驗清單
+ 簡化且高效能的實驗詳細資料頁面，可讓您將執行視覺化並加以比較
+ 執行詳細資料頁面的新設計，以瞭解及監視定型回合

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK for Python v 1.0.65

  + **新功能**
    + 已新增策劃環境。 這些環境已預先設定適用于一般機器學習工作的程式庫，並已預先建立並快取為 Docker 映射，以加快執行速度。 預設會顯示在工作區的環境清單中，首碼為 "AzureML"。
    + 已新增策劃環境。 這些環境已預先設定適用于一般機器學習工作的程式庫，並已預先建立並快取為 Docker 映射，以加快執行速度。 預設會顯示在 [工作區](/python/api/azureml-core/azureml.core.workspace%28class%29)的環境清單中，首碼為 "AzureML"。

  + **azureml-定型-automl**
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 已新增 ADB 和 HDI 的 ONNX 轉換支援

+ **預覽功能**
  + **azureml-定型-automl**
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援 BERT 和 BiLSTM 做為文字 featurizer (僅限預覽) 
    + 資料行用途和轉換器參數的支援特徵化自訂 (僅限預覽) 
    + 當使用者在訓練 (預覽期間僅啟用模型說明時，所支援的原始說明) 
    + 已將預測的先知新增 `timeseries` 為可訓練管線 (僅限預覽) 

  + **azureml-contrib-datadrift**
    + 封裝從 azureml-contrib-datadrift 重新置放至 azureml-datadrift; `contrib` 未來的版本將移除此套件

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 引進 FeaturizationConfig 至 AutoMLConfig 和 AutoMLBaseSettings
    + 引進 FeaturizationConfig 至 [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 和 AutoMLBaseSettings
      + 使用指定的資料行和功能類型覆寫特徵化的資料行用途
      + 覆寫轉換器參數
    + 已新增 explain_model ( # A1 和 retrieve_model_explanations ( # A3 的取代訊息
    + 已將先知新增為可訓練管線 (僅限預覽) 
    + 已新增 explain_model ( # A1 和 retrieve_model_explanations ( # A3 的取代訊息。
    + 已將先知新增為可訓練管線 (僅限預覽) 。
    + 新增了自動偵測目標延隔、滾動視窗大小及最大範圍的支援。 如果 target_lags、target_rolling_window_size 或 max_horizon 的其中一個設定為 ' auto '，則會套用啟發學習法，以根據定型資料來估計對應參數的值。
    + 修正當資料集包含一個資料細微性資料行時，此資料細微性為數數值型別，而且定型和測試集之間有間距的情況
    + 修正了遠端執行的預測工作中重複索引的錯誤訊息
    + 修正當資料集包含一個資料細微性資料行時，此資料細微性為數數值型別，而且定型和測試集之間有間距的情況。
    + 修正了遠端執行的預測工作中，重複索引的相關錯誤訊息。
    + 已新增 guardrail 來檢查資料集是否為不平衡。 如果是，則會將 guardrail 訊息寫入主控台。
  + **azureml-core**
    + 已新增可透過模型物件，在儲存體中將 SAS URL 取出至模型的功能。 例如： model.get_sas_url ( # A1
    + 引進 `run.get_details()['datasets']` 以取得與已提交執行相關聯的資料集
    + 新增 API `Dataset.Tabular.from_json_lines_files` ，以從 JSON 行檔案建立 TabularDataset。 若要瞭解 TabularDataset 上的 JSON 行檔案中的表格式資料，請造訪 [這篇文章](how-to-create-register-datasets.md) 以取得檔。
    + 已將額外的 VM 大小欄位新增 (OS 磁片、supported_vmsizes ( # A3 函式) 的 Gpu 數目
    + 將其他欄位新增至 list_nodes ( # A1 函式，以顯示執行、私用和公用 IP、埠等。
    + 在叢集布建期間指定新欄位的能力--remotelogin_port_public_access 可設定為 [啟用] 或 [停用]，取決於您是否要在建立叢集時讓 SSH 埠保持開啟或關閉。 如果您未指定，服務會聰明地開啟或關閉埠，這取決於您是否要在 VNet 內部署叢集。
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + 已新增可透過模型物件，在儲存體中將 SAS URL 取出至模型的功能。 例如： model。[get_sas_url ( # B1 ](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 介紹執行。[get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' dataset '] 以取得與已提交執行相關聯的資料集
    + 新增 API `Dataset.Tabular` 。[from_json_lines_files ( # B1 ](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) ，從 Json 行檔案建立 TabularDataset。 若要瞭解 TabularDataset 上的 JSON 行檔案中的表格式資料，請流覽檔 https://aka.ms/azureml-data 。
    + 已將額外的 VM 大小欄位新增 (OS 磁片、 [supported_vmsizes ( # B3 ](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 函式) 的 gpu 數目
    + 將其他欄位新增至 [list_nodes ( # B1 ](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 函式，以顯示執行、私用和公用 IP、埠等。
    + 在叢集布建期間指定新欄位的能力，可以設定為啟用或停用 [，這取決](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  于您是否要在建立叢集時讓 SSH 埠保持開啟或關閉。 如果您未指定，服務會聰明地開啟或關閉埠，這取決於您是否要在 VNet 內部署叢集。
  + **azureml-explain-model**
    + 改進了分類案例中的說明輸出檔案。
    + 已新增在評估範例的說明上傳預測 y 值的功能。 解除鎖定更有用的視覺效果。
    + 已將說明屬性新增至 MimicWrapper，以允許取得基礎 MimicExplainer。
  + **azureml-pipeline-core**
    + 新增筆記本以描述 Module、ModuleVersion 和 ModuleStep
  + **azureml-pipeline-steps**
    + 已新增 RScriptStep，以支援透過 AML 管線執行 R 腳本。
    + 修正」已 azurebatchstep 中的已修正中繼資料參數剖析，這會導致錯誤訊息「未指定參數 SubscriptionId 的指派。」
  + **azureml-定型-automl**
    + 支援的 training_data、validation_data、label_column_name weight_column_name 作為資料輸入格式
    + 已新增 explain_model ( # A1 和 retrieve_model_explanations ( # A3 的取代訊息
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + 新增 [筆記本](https://aka.ms/pl-modulestep) 以描述 [模組](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29)： [ModuleVersion] 和 [ [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)]。
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + 已新增 [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) ，以支援透過 AML 管線執行 R 腳本。
    + 修正了 [」已 azurebatchstep 中剖析的中繼資料參數，而導致錯誤訊息「未指定參數 SubscriptionId 的指派」。
  + **[azureml-定型-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援的 training_data、validation_data、label_column_name weight_column_name 作為資料輸入格式。
    + 已新增 [explain_model ( # B1 ](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) 和 [Retrieve_model_explanations ( # B3 ](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)的取代訊息。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK for Python v 1.0.62

+ **新功能**
  + `timeseries`在 TabularDataset 上引進了特性。 這項特性可讓您輕鬆地篩選資料的 TabularDataset，例如在一段時間內或最新的資料之間取得所有資料。  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 以取得範例筆記本。
  + 使用 TabularDataset 和 FileDataset 啟用定型。 

  + **azureml-train-core**
      + `Nccl` `Gloo` 在 PyTorch 估算器中新增和支援

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 已淘汰 AutoML 設定 ' lag_length ' 和 LaggingTransformer。
    + 修正輸入資料以資料流程格式指定時的正確驗證
    + 修改了 fit_pipeline .py，以產生圖形 json 並上傳至成品。
    + 使用將圖形轉譯 `userrun` `Cytoscape` 。
  + **azureml-core**
    + 修改 ADB 程式碼中的例外狀況處理，並根據新的錯誤處理進行變更
    + 已新增筆記本 Vm 的自動 MSI 驗證。
    + 修正因重試失敗而無法上傳損毀或空白模型的 bug。
    + 修正 `DataReference` 當 `DataReference` 模式變更 (例如，呼叫 `as_upload` 、或) 時，名稱變更的 bug `as_download` `as_mount` 。
    + `mount_point` `target_path` 適用于和的 `FileDataset.mount` 選擇性 `FileDataset.download` 。
    + 如果呼叫 serials 相關的 API，但未指派精確的時間戳記資料行，或卸載指派的時間戳記資料行，就會擲回時間戳記資料行的例外狀況。
    + 應使用類型為日期的資料行來指派時間 serials 資料行，否則應為例外狀況
    + 指派 API ' with_timestamp_columns ' 的時間 serials 資料行，不能採用任何值的微調/粗略時間戳記資料行名稱，這會清除先前指派的時間戳記資料行。
    + 捨棄粗略的資料細微性或精細的時間戳記資料行時，將會擲回例外狀況，並指出使用者可以在卸載清單中排除時間戳記資料行或呼叫 with_time_stamp （沒有值以釋出時間戳記資料行）之後完成刪除
    + 當 [保留資料行] 清單中未包含粗略的 [時間戳記] 或 [微調時間戳記] 資料行時，將會擲回例外狀況，並指出使用者必須在 [保留資料行] 清單中包含時間戳記資料行或呼叫 with_time_stamp （沒有值）來釋放時間戳記資料行。
    + 已針對已註冊模型的大小新增記錄。
  + **azureml-explain-model**
    + 修正「封裝」 python 套件未安裝時，已將警告列印到主控台：「使用早于支援的 lightgbm 版本，請升級至大於2.2.1 的版本」
    + 修正了具有許多功能的全域說明的分區化下載模型說明
    + 修正了說明輸出說明時遺漏初始化範例
    + 使用兩種不同類型的模型，以說明用戶端上傳時，修正 set 屬性上的不可變錯誤
    + 已將 get_raw param 新增至計分說明。說明 ( # A1，讓一個評分說明可以傳回工程和原始值。
  + **azureml-定型-automl**
    + 從 AutoML 導入了公用 Api，以透過將 `automl` AutoML 特徵化分離，以說明 sdk-較新的支援 AutoML 說明的方式，並說明來自 azureml 說明 sdk For AutoML 模型的 Sdk 整合式原始說明支援。
    + 從遠端定型環境中移除 azureml-預設值。
    + 已將預設快取存放區位置從以 FileCacheStore 為基礎的位置變更為 AzureFileCacheStore，Azure Databricks 程式碼路徑上的 AutoML。
    + 修正輸入資料以資料流程格式指定時的正確驗證
  + **azureml-train-core**
    + 已還原 source_directory_data_store 淘汰。
    + 已新增覆寫 azureml 已安裝套件版本的功能。
    + 已在估算器中新增參數的 dockerfile 支援 `environment_definition` 。
    + 簡化了估算器中的分散式訓練參數。

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>新的 web 體驗 (預覽) 適用于 Azure Machine Learning 工作區
新的 web 體驗可讓資料科學家和資料工程師完成其端對端機器學習服務生命週期，從準備並將資料視覺化，以在單一位置定型及部署模型。

![Azure Machine Learning 工作區 UI (預覽) ](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**主要功能：**

使用這個新的 Azure Machine Learning 介面，您現在可以：
+ 管理您的筆記本或連結到 Jupyter
+ [執行自動化 ML 實驗](tutorial-first-experiment-automated-ml.md)
+ [從本機檔案、資料存放區 & web 檔案建立資料集](how-to-create-register-datasets.md)
+ 探索 & 準備建立模型的資料集
+ 監視模型的資料漂移
+ 從儀表板查看最近的資源

在此版本中，支援下列瀏覽器： Chrome、Firefox、Safari 和 Microsoft Edge 預覽。

**已知問題**

1. 如果您看到「發生問題，請重新整理您的瀏覽器！ 部署正在進行時載入區塊檔案時發生錯誤。

1. 無法刪除或重新命名筆記本和檔案中的檔案。 在公開預覽期間，您可以在筆記本 VM 中使用 Jupyter UI 或終端機來執行更新檔案作業。 因為這是已掛接的網路檔案系統所有變更，所以您在筆記本 VM 上進行的變更會立即反映在 [筆記本] 工作區中。

1. 若要透過 SSH 連線到筆記本 VM：
   1. 尋找在 VM 設定期間所建立的 SSH 金鑰。 或者，在 [Azure Machine Learning] 工作區中尋找金鑰 > 開啟 [計算] 索引標籤，> 在清單中尋找筆記本 VM > 開啟其屬性：從對話方塊複製金鑰。
   1. 將這些公用和私人 SSH 金鑰匯入本機電腦。
   1. 使用它們來透過 SSH 連線到筆記本 VM。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK for Python v 1.0.60

+ **新功能**
  + 引進了 FileDataset，它會參考資料存放區或公用 url 中的單一或多個檔案。 檔案可以是任何格式。 FileDataset 可讓您將檔案下載或掛接至您的計算。 
  + 已新增 PythonScript 步驟、Adla 步驟、Databricks 步驟、DataTransferStep 和 AzureBatch 步驟的管線 Yaml 支援

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + AutoArima 現在是僅供預覽的 suggestable 管線。
    + 改進了預測的錯誤報表。
    + 使用自訂例外狀況（而不是預測工作中的泛型）來改善記錄。
    + 已移除 max_concurrent_iterations 的檢查，以減少反覆運算總數。
    + AutoML 模型現在會傳回 AutoMLExceptions
    + 此版本可改善自動化機器學習本機執行的執行效能。
  + **azureml-core**
    + 引進 Dataset.get_all (工作區) ，它會傳回以 `TabularDataset` 其註冊名稱做為索引鍵的和物件的字典 `FileDataset` 。

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + 引進 `parition_format` 和的自 `Dataset.Tabular.from_delimited_files` 變數 `Dataset.Tabular.from_parquet.files` 。 系統會根據指定的格式，將每個資料路徑的分割區資訊解壓縮至資料行中。 ' {column_name} ' 建立了字串資料行，而 ' {column_name： yyyy/MM/dd/HH/MM/ss} ' 建立了 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來將 datetime 類型的年、月、日、小時、分鐘和秒解壓縮。 Partition_format 應從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設有路徑 '。/USA/2019/01/01/data.csv '，其中的資料分割依 country 和 time，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data.csv ' 會以值 ' USA ' 和值為 ' 2019-01-01 ' 的日期時間資料行 ' PartitionDate ' 建立字串資料行 ' Country '。
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + 引進 `partition_format` 和的自 `Dataset.Tabular.from_delimited_files` 變數 `Dataset.Tabular.from_parquet.files` 。 系統會根據指定的格式，將每個資料路徑的分割區資訊解壓縮至資料行中。 ' {column_name} ' 建立了字串資料行，而 ' {column_name： yyyy/MM/dd/HH/MM/ss} ' 建立了 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來將 datetime 類型的年、月、日、小時、分鐘和秒解壓縮。 Partition_format 應從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設有路徑 '。/USA/2019/01/01/data.csv '，其中的資料分割依 country 和 time，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data.csv ' 會以值 ' USA ' 和值為 ' 2019-01-01 ' 的日期時間資料行 ' PartitionDate ' 建立字串資料行 ' Country '。
    + `to_csv_files` 和 `to_parquet_files` 方法已加入至 `TabularDataset` 。 這些方法會將 `TabularDataset` `FileDataset` 資料轉換成指定格式的檔案，以啟用與之間的轉換。
    + 儲存模型所產生的 Dockerfile 時，自動登入基底映射登錄。套件 ( # A1。
    + 不再需要 ' gpu_support ';AML 現在會自動偵測並使用 nvidia docker 延伸模組（如果有的話）。 未來的版本將予以移除。
    + 已新增建立、更新和使用 PipelineDrafts 的支援。
    + 此版本可改善自動化機器學習本機執行的執行效能。
    + 使用者可以依名稱查詢執行歷程記錄中的計量。
    + 使用自訂例外狀況（而不是預測工作中的泛型）來改善記錄。
  + **azureml-explain-model**
    + 已將 feature_maps 參數新增至新的 MimicWrapper，可讓使用者取得原始的功能說明。
    + 資料集上傳現在預設為關閉以進行說明上傳，而且可以使用 upload_datasets = True 重新啟用
    + 已新增「is_law」篩選參數來說明清單和下載功能。
    + 將方法新增 `get_raw_explanation(feature_maps)` 至全域和本機說明物件。
    + 已將版本檢查新增至 lightgbm，並在低於支援的版本時進行列印警告
    + 批次處理說明時的優化記憶體使用量
    + AutoML 模型現在會傳回 AutoMLExceptions
  + **azureml-pipeline-core**
    + 新增了建立、更新和使用 PipelineDrafts 的支援-可用來維護可變動的管線定義，並以互動方式使用它們來執行
  + **azureml-定型-automl**
    + 已建立功能來安裝具有 gpu 功能的特定版本 pytorch v 1.1.0、 :::no-loc text="cuda"::: 工具組9.0、pytorch 轉換器，這是在遠端 python 執行時間環境中啟用 BERT/XLNet 所需的功能。
  + **azureml-train-core**
    + 直接在 sdk 中（而不是伺服器端）發生某些超參數空間定義錯誤的早期失敗。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning 資料準備 SDK v 1.1.14
+ **Bug 修正和改善**
  + 啟用使用原始路徑和認證寫入至 ADLS/ADLSGen2。
  + 修正導致 `include_path=True` 無法運作的 bug `read_parquet` 。
  + 修正 `to_pandas_dataframe()` 例外狀況「不正確屬性值： hostSecret」所造成的失敗。
  + 修正了在 Spark 模式的 DBFS 上無法讀取檔案的 bug。

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK for Python v 1.0.57
+ **新功能**
  + 啟用 `TabularDataset` 以供 AutomatedML 使用。 若要深入瞭解 `TabularDataset` ，請造訪 https://aka.ms/azureml/howto/createdatasets 。

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + 您現在可以針對 Microsoft 所產生和客戶憑證的 AKS 叢集上部署的評分端點，更新 TLS/SSL 憑證。
  + **azureml-automl-core**
    + 修正了 AutoML 中有遺漏標籤的資料列未正確移除的問題。
    + 改進了 AutoML 中的錯誤記錄;完整的錯誤訊息現在一律會寫入記錄檔中。
    + AutoML 已更新其封裝釘選，以包含 `azureml-defaults` 、 `azureml-explain-model` 和 `azureml-dataprep` 。 AutoML 不會再對套件不符 (發出警告，除非 `azureml-train-automl` 封裝) 。
    + 修正 `timeseries`  cv 分割的大小不相等，導致 bin 計算失敗的問題。
    + 針對交叉驗證定型類型執行集團反復專案時，如果我們在下載針對整個資料集定型的模型時遇到問題，我們會在模型加權和正在送至投票集團的模型之間產生不一致的情況。
    + 修正了定型和/或驗證標籤 (y 和 y_valid) 以 pandas 資料框架的形式提供，而不是 numpy 陣列時，所引發的錯誤。
    + 修正了在輸入資料表的布林資料行中找不到任何情況時，預測工作的問題。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。 -允許 AutoML 使用者從測試集中卸載不存在於定型集中的粒紋。
  + **azureml-core**
    + 修正了 blob_cache_timeout 參數順序的問題。
    + 已將外部大小和轉換例外狀況類型新增至系統錯誤。
    + 新增遠端執行 Key Vault 秘密的支援。 新增 keyvault Keyvault 類別，以新增、取得及列出與您工作區相關聯之 keyvault 的秘密。 支援的作業為：
      + azureml.core.workspace.Workspace.get_default_keyvault ( # A1
      + azureml.core.keyvault.Keyvault.set_secret (名稱、值) 
      + azureml.core.keyvault.Keyvault.set_secrets (secrets_dict) 
      + azureml.core.keyvault.Keyvault.get_secret (名稱) 
      + azureml.core.keyvault.Keyvault.get_secrets (secrets_list) 
      + azureml.core.keyvault.Keyvault.list_secrets ( # A1
    + 在遠端執行期間取得預設 keyvault 和取得秘密的其他方法：
      + azureml.core.workspace.Workspace.get_default_keyvault ( # A1
      + azureml.core.run.Run.get_secret (名稱) 
      + azureml.core.run.Run.get_secrets (secrets_list) 
    + 已將其他覆寫參數新增至 submit hyperdrive CLI 命令。
    + 提高 API 呼叫的可靠性，將重試擴充至常見的要求程式庫例外狀況。
    + 新增從提交的執行提交執行的支援。
    + 已修正 FileWatcher 中即將到期的 SAS 權杖問題，這會導致檔案在初始權杖到期後停止上傳。
    + 支援匯入資料集 python SDK 中的 HTTP csv/tsv 檔案。
    + 已淘汰工作區。安裝程式 ( # A1 方法。 向使用者顯示的警告訊息會建議您改用 create ( # A1 或 get ( # B3/from_config ( # A5。
    + 已新增 Environment.add_private_pip_wheel ( # A1，可讓您將私人自訂 python 套件上傳 `whl` 到工作區，並安全地使用它們來建立/具體化環境。
    + 您現在可以針對 Microsoft 所產生和客戶憑證的 AKS 叢集上部署的評分端點，更新 TLS/SSL 憑證。
  + **azureml-explain-model**
    + 已新增參數，將模型識別碼新增至上傳的說明。
    + 已 `is_raw` 將標記新增至記憶體中的說明並上傳。
    + 已新增 azureml-說明模型封裝的 pytorch 支援和測試。
  + **azureml-opendatasets**
    + 支援偵測和記錄自動測試環境。
    + 已新增類別，以依縣市和郵遞區號取得美國人口。
  + **azureml-pipeline-core**
    + 已將標籤屬性新增至輸入和輸出埠定義。
  + **azureml-telemetry**
    + 已修正不正確的遙測設定。
  + **azureml-定型-automl**
    + 修正安裝程式失敗時的錯誤（錯誤未記錄在安裝程式執行的「錯誤」欄位中），因此不會儲存在父代執行「錯誤」中。
    + 修正了 AutoML 中有遺漏標籤的資料列未正確移除的問題。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。
    + 在預測時，允許 AutoML 使用者從不存在於定型集中的測試集卸載粒紋。
    + 現在 AutoMLStep 會將 `automl` config 傳遞給後端，以避免變更或新增設定參數時發生任何問題。
    + AutoML 資料 Guardrail 現已進入公開預覽階段。 使用者會看到「資料 Guardrail 報表」 (分類/回歸工作) 在定型之後，也可以透過 SDK API 來存取。
  + **azureml-train-core**
    + 已在 PyTorch 估算器中新增 torch 1.2 支援。
  + **azureml-widgets**
    + 改善分類定型的混淆矩陣圖表。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning 資料準備 SDK v 1.1.12
+ **新功能**
  + 您現在可以將字串清單當作輸入傳遞給 `read_*` 方法。

+ **Bug 修正和改善**
  + 在 Spark 中執行時，的效能已 `read_parquet` 獲得改善。
  + 修正 `column_type_builder` 在具有不明確日期格式的單一資料行時失敗的問題。

### <a name="azure-portal"></a>Azure 入口網站
+ **預覽功能**
  + 記錄檔和輸出檔案串流現在可用於執行詳細資料頁面。 開啟預覽切換時，檔案會即時串流更新。
  + 在工作區層級設定配額的功能會以預覽形式發行。 AmlCompute 配額是在訂用帳戶層級進行配置，但我們現在可讓您在工作區之間散發該配額，並將其配置給公平的共用和治理。 只需按一下工作區左側導覽列中的 [ **使用方式 + 配額** ] 分頁，然後選取 [ **設定配額** ] 索引標籤。您必須是訂用帳戶管理員，才能在工作區層級設定配額，因為這是跨工作區的操作。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK for Python v 1.0.55

+ **新功能**
  + 針對部署在 AKS 上的評分端點進行的呼叫，現在支援以權杖為基礎的驗證。 我們將繼續支援目前的金鑰型驗證，而且使用者可以一次使用這些驗證機制的其中一種。
  + 可以在虛擬網路後方登錄 blob 儲存體 (VNet) 作為資料存放區。

+ **Bug 修正和改善**
  + **azureml-automl-core**
    + 修正 CV 分割驗證大小很小的 bug，並產生不正確的預測與實際回歸和預測圖表。
    + 遠端執行上的預測工作記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 修正 `Timeseries` 了如果前置處理旗標為 True 時失敗。
    + 使某些預測資料驗證錯誤訊息更具可採取動作。
    + 減少 AutoML 執行的記憶體耗用量，方法是卸載和/或消極式載入資料集，特別是在進程產生之間
  + **azureml-contrib-說明-模型**
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯
    + Widget 變更：自動安裝時 `contrib` ，不會有 `nbextension` 全域功能重要性的安裝/啟用支援說明 (例如 Permutative) 
    + 儀表板變更：-方塊繪圖和小提琴圖，除了 `beeswarm` 繪製在摘要頁面上，更快速轉譯資料流程繪圖的「上到 `beeswarm` k」滑杆變更-說明如何在未提供資料的情況下，可自訂的自訂訊息，以取代圖表
  + **azureml-core**
    + 已新增模型。封裝 ( # A1 方法，以建立可封裝模型及其相依性的 Docker 映射和 Dockerfile。
    + 已更新本機 webservices，以接受包含環境物件的 InferenceConfigs。
    + 已修正模型。當目前目錄) 的 '. ' (作為 model_path 參數傳遞時，請註冊 ( # A1 產生不正確模型。
    + 新增 Run.submit_child，這項功能會鏡像實驗。將執行指定為已提交子執行的父系時，請提交。
    + 從模型支援設定選項。在 Run.register_model 中註冊。
    + 在現有的叢集上執行 JAR 作業的能力。
    + 現在支援 instance_pool_id 和 cluster_log_dbfs_path 參數。
    + 已新增在將模型部署至 Webservice 時使用環境物件的支援。 現在可以提供環境物件做為 InferenceConfig 物件的一部分。
    + 為新區域新增 appinsifht 對應-centralus-westus-northcentralus
    + 已針對所有資料存放區類別中的所有屬性新增檔。
    + 已將 blob_cache_timeout 參數加入至 `Datastore.register_azure_blob_container` 。
    + 已將 save_to_directory 和 load_from_directory 方法新增至 azureml.. a. 環境。
    + 已將 "az ml 環境下載" 和 "az ml 環境 register" 命令新增至 CLI。
    + 已新增 Environment.add_private_pip_wheel 方法。
  + **azureml-explain-model**
    + 將資料集追蹤新增至使用資料集服務 (預覽) 的說明。
    + 從1到100串流全域說明時，已減少預設批次大小。
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯。
  + **azureml-mlflow**
    + 已修正 mlflow.azureml.build_image 中會忽略嵌套目錄的 bug。
  + **azureml-pipeline-steps**
    + 已新增在現有 Azure Databricks 叢集上執行 JAR 作業的功能。
    + 已新增 DatabricksStep 步驟的支援 instance_pool_id 和 cluster_log_dbfs_path 參數。
    + 在 DatabricksStep 步驟中新增管線參數的支援。
  + **azureml-定型-automl**
    + `docstrings`針對集團相關檔案加入。
    + 已將檔更新為適用于和的更適當語言 `max_cores_per_iteration``max_concurrent_iterations`
    + 遠端執行上的預測工作記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已從管線筆記本中移除 get_data `automlstep` 。
    + 開始支援 `dataprep` `automlstep` 。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning 資料準備 SDK v 1.1.10

+ **新功能**
  + 您現在可以要求在特定資料行上執行特定的偵測器 (例如，長條圖、散佈圖等 ) 。
  + 已將平行處理引數加入至 `append_columns` 。 若為 True，將會將資料載入記憶體中，但會以平行方式執行。如果為 False，則會將執行串流處理，但會進行單一執行緒。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK for Python v 1.0.53

+ **新功能**
  + 自動 Machine Learning 現在支援在遠端計算目標上定型 ONNX 模型
  + Azure Machine Learning 現在可讓您從先前的執行、檢查點或模型檔案繼續定型。
    + 瞭解如何 [使用估算器從先前的執行繼續定型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug 修正和改善**
  + **azure-cli-ml**
    + CLI 命令「模型部署」和「服務更新」現在接受參數、設定檔或這兩者的組合。 參數的優先順序高於檔案中的屬性。
    + 現在可在註冊之後更新模型描述
  + **azureml-automl-core**
    + 將 NimbusML 相依性更新為目前最新)  (的1.2.0 版本。
    + 新增 NimbusML 估算器 & 管線的支援，以便在 AutoML 估算器中使用。
    + 修正集團選取程式中的 bug，即使分數保持不變，也不必要地成長產生的集團。
    + 啟用跨 CV 分割的部分 featurizations，以進行預測工作。 如此一來，就能加速安裝程式執行時間，方法是將成本 featurizations （例如延隔和變換視窗）視為 n_cross_validations 的因素。
    + 當時間超出 pandas 支援的時間範圍時，解決問題。 如果時間小於 pd，我們現在會引發 DataException。Timestamp. min 或大於 pd。時間戳記。最大值
    + 預測現在可讓定型和測試集中有不同的頻率（如果可以對齊）。 例如，「每季開始于一月」和「每季從10月開始」都可以對齊。
    + 已將屬性 "parameters" 新增至 TimeSeriesTransformer。
    + 移除舊的例外狀況類別。
    + 在預測工作中， `target_lags` 參數現在接受單一整數值或整數清單。 如果提供整數，則只會建立一個延遲。 如果有提供清單，將會取得延遲的唯一值。 target_lags = [1，2，2，4] 將建立一、二和四個句點的延隔時間。
    + 修正轉換 (錯誤連結之後遺失資料行類型的錯誤;) ;
    + 在中，您可以在 `model.forecast(X, y_query)` begin ( # 459519) ，讓 y_query 成為包含 None (s) 的物件類型。
    + 將預期值新增至 `automl` 輸出
  + **azureml-contrib-datadrift**
    +  增強了範例筆記本的功能，包括切換至 azureml-opendatasets，而不是 azureml-contrib-opendatasets，以及在擴充資料時的效能改進
  + **azureml-contrib-說明-模型**
    + 已修正 azureml 的轉換引數（說明），以取得 azureml 中的原始特徵重要性-contrib-說明模型套件
    + 已將 segmentations 新增至說明中的影像說明，以進行 AzureML-contrib-說明模型套件
    + 新增 LimeExplainer 的 scipy 稀疏支援
    + 已新增 `batch_size` 至模仿說明的時機 `include_local=False` ，以串流全域說明以改善 DecisionTreeExplainableModel 的執行時間
  + **azureml-contrib-featureengineering**
    + 修正呼叫 set_featurizer_timeseries_params ( # A1： dict 數值型別變更和 null 檢查-新增 featurizer 的筆記本 `timeseries`
    + 將 NimbusML 相依性更新為目前最新)  (的1.2.0 版本。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能
    + 修正了資料存放區上傳的錯誤，其中建立了空的資料夾（若 `target_path` 其開頭為 `/`
    + 修正了 `deepcopy` ServicePrincipalAuthentication 中的問題。
    + 已將 "az ml 環境 show" 和 "az ml 環境 list" 命令新增至 CLI。
    + 環境現在支援將 base_dockerfile 指定為已建立的 base_image 的替代方案。
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已淘汰。
    + 現在可在註冊之後更新模型描述
    + 錯誤修正：模型和映射刪除現在提供詳細資訊，說明如何在由於上游相依性而刪除失敗時，取得相依于這些物件的上游物件。
    + 修正針對在某些環境中建立工作區時所發生的部署，所列印的空白持續時間的 bug。
    + 改善工作區建立的失敗例外狀況。 如此一來，使用者就看不到「無法建立工作區。 找不到 ...」做為訊息，並改為查看實際的建立失敗。
    + 在 AKS webservices 中新增對權杖驗證的支援。
    + 將 `get_token()` 方法新增至 `Webservice` 物件。
    + 已新增 CLI 支援來管理 machine learning 資料集。
    + `Datastore.register_azure_blob_container` 現在可選擇性地接受 `blob_cache_timeout` 值 (（秒）) 這會設定 blobfuse 的掛接參數，以啟用此資料存放區的快取到期時間。 預設值為 no timeout，例如讀取 blob 時，它會保留在本機快取中，直到作業完成為止。 大部分的作業會偏好使用此設定，但某些作業需要從大型資料集讀取更多資料，而不是其節點的大小。 針對這些作業，調整此參數將有助於成功。 微調此參數時請小心：將值設得太低可能會導致效能不佳，因為 epoch 中使用的資料可能會在重新使用之前過期。 所有讀取都會從 blob 儲存體/網路完成，而不是本機快取，這會對定型時間造成負面影響。
    + 您現在可以在註冊後正確地更新模型描述
    + 模型和映射刪除現在提供相依于這些物件的詳細資訊，導致刪除失敗
    + 使用 azureml mlflow 來改善遠端執行的資源使用率。
  + **azureml-explain-model**
    + 已修正 azureml 的轉換引數（說明），以取得 azureml 中的原始特徵重要性-contrib-說明模型套件
    + 新增 LimeExplainer 的 scipy 稀疏支援
    + 新增了圖形線性說明包裝函式，以及另一個可說明線性模型的層級至表格式說明
    + 如需模擬模型程式庫中的模仿說明，請修正針對稀疏資料輸入 include_local = False 時的錯誤
    + 將預期值新增至 `automl` 輸出
    + 當提供轉換引數以取得原始功能重要性時，已修正排列功能重要性
    + 已新增 `batch_size` 至模仿說明的時機 `include_local=False` ，以串流全域說明以改善 DecisionTreeExplainableModel 的執行時間
    + 針對模型可解釋性程式庫，已修正黑箱 explainers，其中需要 pandas 資料框架輸入以進行預測
    + 修正了 `explanation.expected_values` 有時會傳回浮點數的錯誤，而不是其中包含 float 的清單。
  + **azureml-mlflow**
    + 改善 mlflow.set_experiment (experiment_name 的效能) 
    + 修正使用 InteractiveLoginAuthentication 進行 mlflow tracking_uri 的 bug
    + 使用 azureml mlflow 來改善遠端執行的資源使用率。
    + 改善 azureml mlflow 套件的檔
    + 修補 bug，其中 mlflow.log_artifacts ( "my_dir" ) 會將成品儲存在 "my_dir/<成品-路徑>" 下，而不是 "<成品-路徑>"
  + **azureml-opendatasets**
    + `pyarrow` `opendatasets` 由於新引進的記憶體問題， ( # B0 0.14.0) 的釘選到舊版本。
    + 將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開放式資料集類別註冊到 Azure Machine Learning 工作區，並順暢地利用 AML 資料集功能。
    + 大幅改善非 SPARK 版本的 NoaaIsdWeather 效能。
  + **azureml-pipeline-steps**
    + DatabricksStep 中的輸入和輸出現在支援 DBFS 資料存放區。
    + 針對輸入/輸出的 Azure Batch 步驟更新檔。
    + 在」已 azurebatchstep 中， *delete_batch_job_after_finish* 預設值變更為 *true*。
  + **azureml-telemetry**
    +  將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開放式資料集類別註冊到 Azure Machine Learning 工作區，並順暢地利用 AML 資料集功能。
    + 大幅改善非 SPARK 版本的 NoaaIsdWeather 效能。
  + **azureml-定型-automl**
    + 更新 get_output 上的檔以反映實際的傳回型別，並提供有關如何抓取索引鍵屬性的其他注意事項。
    + 將 NimbusML 相依性更新為目前最新)  (的1.2.0 版本。
    + 將預期值新增至 `automl` 輸出
  + **azureml-train-core**
    + 現在接受字串作為自動超參數微調的計算目標
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已淘汰。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning 資料準備 SDK v 1.1。9

+ **新功能**
  + 已新增直接從 HTTP 或 HTTPs url 讀取檔案的支援。

+ **Bug 修正和改善**
  + 在嘗試從遠端來源讀取 Parquet 資料集時，已改善的錯誤訊息 (目前) 不支援此功能。
  + 修正在 ADLS Gen 2 中寫入 Parquet 檔案格式的錯誤，並在路徑中更新 ADLS Gen 2 容器名稱。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>視覺化介面
+ **預覽功能**
  + 在視覺化介面中新增了 [執行 R 腳本] 模組。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v 1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** 現在以 **azureml-opendatasets** 的形式提供。 舊的封裝仍可運作，但我們建議您使用 **azureml opendatasets** ，以取得更豐富的功能和增強功能。
    + 這個新的封裝可讓您將開啟的資料集註冊為 Azure Machine Learning 工作區中的資料集，並利用資料集所提供的任何功能。
    + 它也包含現有的功能，例如取用開啟的資料集做為 Pandas/SPARK 資料框架，以及某些資料集（例如天氣）的位置聯結。

+ **預覽功能**
    + HyperDriveConfig 現在可以接受管線物件做為參數，以支援使用管線的超參數微調。

+ **Bug 修正和改善**
  + **azureml-定型-automl**
    + 修正在轉換之後遺失資料行類型的錯誤。
    + 修正 bug，讓 y_query 成為一開始不包含 (s) 的物件類型。
    + 修正集團選擇程式中，即使分數保持不變，也不必要地成長產生的集團的問題。
    + 修正了在 AutoMLStep 中允許 list_models 和封鎖 list_models 設定的問題。
    + 修正在 Azure ML 管線的內容中使用 AutoML 時，無法使用前置處理的問題。
  + **azureml-opendatasets**
    + 已將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開啟的資料集類別註冊到 Azure Machine Learning 工作區，並順暢地利用 AML 資料集功能。
    + 改善的 NoaaIsdWeather 大幅提升非 SPARK 版本的效能。
  + **azureml-explain-model**
    + 已更新可解譯性物件的線上檔。
    + 新增 `batch_size` 至模仿說明的時間 `include_local=False` ，以批次方式串流全域說明，以改善模型可解釋性程式庫的 DecisionTreeExplainableModel 執行時間。
    + 修正了 `explanation.expected_values` 有時會傳回浮點數的問題，而不是其中包含 float 的清單。
    + 在說明模型程式庫中，將預期值新增至 `automl` 模仿說明的輸出。
    + 修正提供轉換引數以取得原始特徵重要性時的排列功能重要性。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能。
    + 修正了資料存放區上傳的問題，如果在啟動時，就會建立空的資料夾 `target_path` `/` 。
    + 啟用兩個資料集的比較。
    + 模型和映射刪除現在提供有關如何在刪除因上游相依性而失敗時，取得相依于這些物件的上游物件的詳細資訊。
    + 已淘汰 auto_prepare_environment 中未使用的 RunConfiguration 設定。
  + **azureml-mlflow**
    + 改善使用 azureml. mlflow 的遠端執行的資源使用率。
    + 已改進 azureml mlflow 套件的檔。
    + 修正 mlflow.log_artifacts ( "my_dir" ) 會將成品儲存在 "my_dir/artifact-paths" 下，而不是「成品路徑」的問題。
  + **azureml-pipeline-core**
    + 所有管線步驟的參數 hash_paths 已被取代，未來將會移除。 根據預設，source_directory 的內容會雜湊 (除了 `.amlignore` 或) 中列出的檔案之外 `.gitignore`
    + 持續改善模組和 ModuleStep，以支援計算類型專屬的模組，以準備進行 RunConfiguration 整合和其他變更，以在管線中解除鎖定計算類型特定的模組使用。
  + **azureml-pipeline-steps**
    + 」已 azurebatchstep：已改善輸入/輸出的相關檔。
    + 」已 azurebatchstep：已將 delete_batch_job_after_finish 預設值變更為 true。
  + **azureml-train-core**
    + 現在可接受字串作為自動超參數微調的計算目標。
    + 已淘汰 auto_prepare_environment 中未使用的 RunConfiguration 設定。
    + 已淘汰 `conda_dependencies_file_path` 的參數，而且也會 `pip_requirements_file_path` 優先使用 `conda_dependencies_file` `pip_requirements_file` 。
  + **azureml-opendatasets**
    + 大幅改善非 SPARK 版本的 NoaaIsdWeather 效能。

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>已發行 Azure Machine Learning 1.0.33 SDK for Python v。

+ [Fpga](how-to-deploy-fpga-web-service.md)上的 Azure ML 硬體加速模型已正式推出。
  + 您現在可以 [使用 azureml accel 模型套件](how-to-deploy-fpga-web-service.md) ：
    + 定型受支援深度類神經網路的權數 (ResNet 50、ResNet 152、Densenet-121-121、VGG-16-16 和 SSD VGG-16) 
    + 搭配支援的 DNN 使用傳輸學習
    + 向模型管理服務註冊模型，並將模型
    + 使用 Azure Kubernetes Service (AKS) 叢集中的 FPGA，將模型部署至 Azure VM
  + 將容器部署到 [Azure 資料箱 Edge](../databox-online/azure-stack-edge-overview.md) server 裝置
  + 使用此[範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)以 gRPC 端點為您的資料評分

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ 功能整理可動態新增以 :::no-loc text="featurizers"::: 進行效能優化。 新 :::no-loc text="featurizers"::: ：工作內嵌、辨識項權數、目標編碼、文字目標編碼、叢集距離
+ 智慧型 CV 可處理自動化 ML 內的訓練/有效分割
+ 少數記憶體優化變更和執行時間效能改進
+ 效能改善模型說明
+ 本機執行的 ONNX 模型轉換
+ 已新增次取樣支援
+ 未定義結束準則時的智慧型停止
+ 堆疊整體

+ 時間序列預測
  + 新的預測預測函數
  + 您現在可以使用時間序列資料的輪流來源交叉驗證
  + 新增至設定時間序列延遲的新功能
  + 新增功能以支援滾動時間範圍匯總功能
  + 在實驗設定中定義國家/地區代碼時的新假日偵測和 featurizer

+ Azure Databricks
  + 啟用時間序列預測和模型 explainabilty/可解譯性功能
  + 您現在可以取消並繼續 (繼續) 自動化 ML 實驗
  + 已新增多核心處理的支援

### <a name="mlops"></a>MLOps
+ **評分容器的本機部署 & 偵錯工具**<br/> 您現在可以在本機部署 ML 模型，並快速地逐一查看評分檔案和相依性，以確保其行為如預期般運作。

+ **已推出 InferenceConfig & 模型。部署 ( # B1**<br/> 模型部署現在支援指定具有輸入腳本的源資料夾，與 RunConfig 相同。  此外，模型部署已簡化為單一命令。

+ **Git 參考追蹤**<br/> 客戶已要求基本的 Git 整合功能一段時間，因為它有助於維護完整的審核記錄。 我們已在 Azure ML 中針對 Git 相關的中繼資料執行追蹤， (存放庫、認可、清除狀態) 。 SDK 和 CLI 會自動收集此資訊。

+ **模型分析 & 驗證服務**<br/> 客戶通常會抱怨如何適當地調整與推斷服務相關聯的計算。 使用我們的模型分析服務時，客戶可以提供範例輸入，我們會在16個不同的 CPU/記憶體設定之間進行分析，以判斷部署的最佳大小。

+ **攜帶您自己的基底映射以進行推斷**<br/> 另一個常見的問題是，從實驗移至推斷時，不會重新共用相依性。 有了新的基底映射共用功能，您現在可以重複使用您的實驗基礎映射、相依性和全部來進行推斷。 這應該會加速部署，並減少從內部到外部迴圈的間隔。

+ **改良的 Swagger 架構產生體驗**<br/> 先前的 swagger 產生方法發生錯誤，且無法自動化。 我們有新的逐行方式，可透過裝飾專案從任何 Python 函數產生 swagger 架構。 我們有開放原始碼的程式碼，而我們的架構產生通訊協定不會與 Azure ML 平臺結合。

+ **Azure ML CLI 正式推出 (GA)**<br/> 現在可以使用單一 CLI 命令來部署模型。 我們提供了一項常見的客戶意見反應，讓您無法從 Jupyter 筆記本部署 ML 模型。 [**CLI 參考檔**](./reference-azure-machine-learning-cli.md)已更新。


## <a name="2019-04-22"></a>2019-04-22

已發行 Azure Machine Learning 1.0.30 SDK for Python v。

引進了在 [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?preserve-view=true&view=azure-ml-py) 維護相同端點的情況下新增發行管線的新版本。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 入口網站
  + 您現在可以在現有的遠端計算叢集上重新提交現有的腳本執行。
  + 您現在可以在 [管線] 索引標籤上，使用新的參數來執行已發佈的管線。
  + [執行詳細資料] 現在支援新的快照集檔案檢視器。 您可以在提交特定執行時，查看目錄的快照集。 您也可以下載提交來開始執行的筆記本。
  + 您現在可以從 Azure 入口網站取消父執行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v 1.0.23

+ **新功能**
  + Azure Machine Learning SDK 現在支援 Python 3.7。
  + Azure Machine Learning DNN 估算器現在提供內建的多版本支援。 例如， `TensorFlow` 估算器現在接受 `framework_version` 參數，使用者可以指定 ' 1.10 ' 或 ' 1.12 ' 版。 如需目前 SDK 版本所支援的版本清單，請在所 `get_supported_versions()` 需的架構類別上呼叫 (例如 `TensorFlow.get_supported_versions()`) 。
  如需最新 SDK 版本所支援的版本清單，請參閱 [DNN 估算器檔](/python/api/azureml-train-core/azureml.train.dnn?preserve-view=true&view=azure-ml-py)。

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v 1.0.21

+ **新功能**
  + *Azureml.core.Run.create_children* 方法可讓您透過單一呼叫，以低延遲的方式建立多個子執行。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v 1.0.18

 + **變更**
   + Azureml tensorboard 套件會取代 azureml-contrib-tensorboard。
   + 在此版本中，您可以在您的受控計算叢集 (amlcompute) 上設定使用者帳戶，並建立該帳戶。 這可以藉由在布建設定中傳遞這些屬性來完成。 您可以在 [SDK 參考檔](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)中找到更多詳細資料。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 資料準備 SDK v 1.0.17

+ **新功能**
  + 現在支援加入兩個數值資料行，以使用運算式語言來產生結果資料行。

+ **Bug 修正和改善**
  + 改進了 random_split 的檔和參數檢查。

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 資料準備 SDK v 1.0.16

+ **Bug 修正**
  + 修正了 API 變更所造成的服務主體驗證問題。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v 1.0.17

+ **新功能**
  + Azure Machine Learning 現在提供熱門 DNN framework Chainer 的一流支援。 使用 [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py) 類別使用者可以輕鬆地定型及部署 Chainer 模型。
    + 瞭解如何 [使用 ChainerMN 執行分散式訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + 瞭解如何 [使用 HyperDrive 以 Chainer 執行超參數微調](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning 管線已新增可根據資料存放區修改來觸發管線執行的功能。 管線 [排程筆記本](https://aka.ms/pl-schedule) 會更新以展示這項功能。

+ **Bug 修正和改善**
  + 我們已在 Azure Machine Learning 管線中新增了支援，以將 source_directory_data_store 屬性設定為所需的資料存放區 (例如提供給[PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py)之[RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration?preserve-view=true&view=azure-ml-py)上的 blob 儲存體) 。 依預設，步驟會使用 Azure 檔案存放區做為備份資料存放區，這可能會在多個步驟同時執行時遇到節流問題。

### <a name="azure-portal"></a>Azure 入口網站

+ **新功能**
  + 報表的新拖放表編輯器體驗。 使用者可以將資料行從適當的資料行拖曳到資料表區域，以顯示資料表的預覽。 您可以重新排列資料行。
  + 新記錄檔檢視器
  + [活動] 索引標籤中的實驗執行、計算、模型、映射和部署的連結

## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning](overview-what-is-azure-ml.md) 概觀。
