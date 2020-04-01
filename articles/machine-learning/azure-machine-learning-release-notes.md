---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習的最新更新以及機器學習和數據準備 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: b55c351927a56afce697d07f41bfbe668144d68d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475512"
---
# <a name="azure-machine-learning-release-notes"></a>Azure 機器學習發行說明

在本文中,瞭解 Azure 機器學習版本。  有關完整的 SDK 參考內容,請造訪 Azure 機器學習[**的主要 SDK 以造訪 Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)參考頁。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>適用於 Python v1.2.0 的 Azure 機器學習 SDK

+ **重大變更**
  + 移除對 python 2.7 的支援

+ **錯誤修復與改進**
  + **azure-cli-ml**
    + 將「--訂閱 id」新增`az ml model/computetarget/service`到 CLI 中的指令
    + 新增對傳遞客戶託管金鑰 (CMK) vault_url、key_name和 Key_version的 ACI 部署的支援
  + **azureml-automl-core** 
    + 為 X 和 y 數據預測任務啟用具有恆定值的自定義歸給。
    + 修復了向用戶顯示錯誤消息的問題。    
  + **azureml-自動ml-執行時間**
    + 修復了資料集上的預測問題,其中僅包含一行顆粒
    + 減少了預測任務所需的記憶體。
    + 如果時間列的格式不正確,則添加更好的錯誤消息。
    + 為 X 和 y 數據預測任務啟用具有恆定值的自定義歸給。
  + **azureml-core**
    + 新增從環境變數載入 Service 主要服務的支援:AZUREML_SERVICE_PRINCIPAL_ID、AZUREML_SERVICE_PRINCIPAL_TENANT_ID和AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + 引入了一個新參數`support_multi_line``Dataset.Tabular.from_delimited_files`: 預設情況下`support_multi_line=False`(), 所有換行元(包括引用的欄位值中的斷線)將解釋為記錄中斷。 以這種方式讀取數據的速度更快,並且更優化,以便對多個 CPU 內核進行並行執行。 但是,它可能導致默默生成更多欄位值不對齊的記錄。 當已知分隔檔包含`True`引號換行符時,應將此設置為。
    + 新增了在 Azure 機器學習 CLI 中註冊 ADLS Gen2 的能力
    + 將參數"fine_grain_timestamp"重新命名為"時間戳",將參數"coarse_grain_timestamp"重新命名為"partition_timestamp",用於表格資料集中的with_timestamp_columns()方法,以便更好地反映參數的用法。
    + 將實驗名稱的最大長度增加到 255。
  + **azureml-interpret**
    + 更新的 azureml 解釋到解釋社區 0.7.*
  + **azureml-sdk**
    + 更改為具有相容版本 Tilde 的依賴項,以支援在預發佈和穩定版本中進行修補。


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>適用於 Python v1.1.5 的 Azure 機器學習 SDK

+ **功能棄用**
  + **Python 2.7**
    + 支援 python 2.7 的最後版本

+ **重大變更**
  + **語意化版本控制系統 2.0.0**
    + 從版本 1.1 開始,Azure ML Python SDK 採用語義版本 2.0.0。 [請在這裡閱讀更多資訊](https://semver.org/)。 所有後續版本都將遵循新的編號方案和語義版本控制協定。 

+ **錯誤修復與改進**
  + **azure-cli-ml**
    + 將終結點 CLI 命令名稱從「az ml 終結點 aks」更改為「az ml 即時」,以確保一致性。
    + 更新穩定與實驗分支 CLI 的 CLI 安裝說明
    + 單個實例分析已修復以生成建議,並在核心 sdk 中提供。
  + **azureml-automl-core**
    + 為自動ml ONNX 型號啟用批次模式推理(一次多行)
    + 改進對資料集上頻率的偵測,缺乏資料或包含不規則資料點
    + 添加了刪除不符合主導頻率的數據點的能力。
    + 更改建構函數的輸入以獲取選項列表以應用相應列的歸因選項。
    + 錯誤日誌記錄已得到改進。
  + **azureml-自動ml-執行時間**
    + 修復了如果定型集中中不存在的顆粒出現在測試集中時引發的錯誤問題
    + 在預測服務評分期間刪除了y_query要求
    + 修復了當數據集包含具有長時間間隙的短粒時的預測問題。
    + 當自動最大視距打開且日期列以字串形式包含日期時,修復了問題。 無法將轉換到日期時新增正確的轉換和錯誤訊息
    + 使用本機 NumPy 和 SciPy 對 FileCacheStore 的中間資料進行序列化和反序列化(用於本地自動ML執行)
    + 修復了失敗的子運行可能卡在"正在運行"狀態的 Bug。
    + 提高壯舉速度。
    + 修復了評分期間的頻率檢查,現在預測任務不需要列車和測試組之間的嚴格頻率等價。
    + 更改建構函數的輸入以獲取選項列表以應用相應列的歸因選項。
    + 修復了與滯後類型選擇相關的錯誤。
    + 修復了資料集上引發的未分類錯誤,具有具有單行的顆粒
    + 修復了頻率檢測速度慢的問題。
    + 修復了 AutoML 異常處理中的 Bug,該 Bug 會導致訓練失敗的真正原因被屬性錯誤替換。
  + **azureml-cli 常見**
    + 單個實例分析已修復以生成建議,並在核心 sdk 中提供。
  + **azureml-contrib-mir**
    + 在 MirWeb 服務類別新增功能以檢索存取權杖
    + 預設情況下,在 MirWebservice.run() 呼叫期間,對 MirWeb 服務使用權杖身份驗證 - 僅當呼叫失敗時刷新
    + Mir Web 服務部署現在需要適當的 Skus [Standard_DS2_v2、Standard_F16、Standard_A2_v2]而不是 [Ds2v2、A2v2 和 F16]。
  + **azureml-contrib-管道步驟**
    + 可選參數side_inputs添加到 ParallelRunStep。 此參數可用於在容器上安裝資料夾。 當前支援的類型是數據參考和管道數據。
    + 現在可以通過傳遞管道參數來覆蓋在 ParallelRunConfig 中傳遞的參數。 支援aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout(aml_node_count和aml_process_count_per_node)支援的新管道參數已經是早期版本的一部分。
  + **azureml-core**
    + 已部署的 AzureML`INFO`Web 服務現在預設為日誌記錄。 這可以通過在已部署的服務`AZUREML_LOG_LEVEL`中 設置環境變數來控制。
    + Python sdk 使用發現服務使用"api"終結點而不是"管道"。
    + 交換到所有 SDK 呼叫中的新路由
    + 將模型管理服務的通話由變更為新的統一結構
      + 使工作區更新方法公開可用。
      + 在工作區更新方法中新增了image_build_compute參數,以允許使用者更新影像產生的計算
    +  將棄用消息添加到舊分析工作流。 修復分析 CPU 和記憶體限制
    + 將 R 節作為環境的一部分新增到執行 R 作業
    +  添加驗證以`Dataset.mount`在數據集源無法訪問或不包含任何數據時引發錯誤。
    + `--grant-workspace-msi-access`新增為資料儲存 CLI 用於註冊 Azure Blob 容器的附加參數,該參數允許您註冊 VNet 後面的 Blob 容器
    + 單個實例分析已修復以生成建議,並在核心 sdk 中提供。
    + aks.py_deploy修復問題
    + 驗證要上載的模型的完整性,以避免靜默存儲故障。
    + 現在,當為 Web 服務重新生成密鑰時,用戶可以為 auth 鍵指定值。
    + 大寫字母不能用作資料集的輸入名稱的修復錯誤
  + **azureml 預設值**
    + `azureml-dataprep`現在將作為的一部分`azureml-defaults`安裝。 不再需要在計算目標上手動安裝數據準備[fuse]以裝載數據集。
  + **azureml-interpret**
    + 更新的 azureml 解釋到解釋社區 0.6.*
    + 更新的 azureml 解釋,以依賴於解釋社區 0.5.0
    + 將 azureml 樣式異常新增到 azureml 解釋
    + 修復了角質模型的深度評分直譯器序列化
  + **azureml-ml 串流**
    + 加入 azureml.mlflow 新增對主權雲的支援
  + **azureml-pipeline-core**
    + 管管批次處理評分筆記現在使用並行 RunStep
    + 修復了儘管更改參數清單,但 PythonScriptStep 結果可能被錯誤地重用的 Bug
    + 添加了在調用 parse_* 方法時設置列類型的能力。`PipelineOutputFileDataset`
  + **azureml-管道步驟**
    + 將`AutoMLStep`移動到`azureml-pipeline-steps`包。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。
    + 新增資料集的文件範例為 PythonScript 步驟輸入
  + **azureml-tensor板**
    + 更新了 azureml-tensor 板以支援張量流 2.0
    + 在計算實體使用自訂 Tensorboard 連接埠時顯示正確的連接埠號
  + **azureml-train-automl-client**
    + 修復了某些包在遠端運行時可能以不正確的版本安裝的問題。
    + 修復了自定義完成配置的重寫問題,可篩選自定義壯舉配置。
  + **azureml-火車-自動-運行時間**
    + 修復遠端執行中頻率偵測的問題
    + 在`azureml-pipeline-steps``AutoMLStep`套件中移 。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。
  + **azureml-train-core**
    + 在 PyTorch 估計器中支援 PyTorch 版本 1.4
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>適用於 Python v1.1.2rc0 的 Azure 機器學習 SDK(預先發佈)

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + 為自動ml ONNX 型號啟用批次模式推理(一次多行)
    + 改進對資料集上頻率的偵測,缺乏資料或包含不規則資料點
    + 添加了刪除不符合主導性數據點的數據點的能力。
  + **azureml-自動ml-執行時間**
    + 修復了如果定型集中中不存在的顆粒出現在測試集中時引發的錯誤問題
    + 在預測服務評分期間刪除了y_query要求
  + **azureml-contrib-mir**
    + 在 MirWeb 服務類別新增功能以檢索存取權杖
  + **azureml-core**
    + 已部署的 AzureML`INFO`Web 服務現在預設為日誌記錄。 這可以通過在已部署的服務`AZUREML_LOG_LEVEL`中 設置環境變數來控制。
    + 修復反覆運算以`Dataset.get_all`返回在工作區中註冊的所有數據集。
    + 當無效類型傳遞給`path`資料集創建API的參數時,改進錯誤訊息。
    + Python sdk 使用發現服務使用"api"終結點而不是"管道"。
    + 交換到所有 SDK 呼叫中的新路由
    + 將模型管理服務的通話由變更為新的統一結構
      + 使工作區更新方法公開可用。
      + 在工作區更新方法中新增了image_build_compute參數,以允許使用者更新影像產生的計算
    +  將棄用消息添加到舊分析工作流。 修復分析 CPU 和記憶體限制
  + **azureml-interpret**
    + 將 azureml 解釋更新為解釋社區 0.6.*
  + **azureml-ml 串流**
    + 加入 azureml.mlflow 新增對主權雲的支援
  + **azureml-管道步驟**
    + 移動`AutoMLStep`到`azureml-pipeline-steps package`。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。
  + **azureml-train-automl-client**
    + 修復了某些包在遠端運行時可能以不正確的版本安裝的問題。
  + **azureml-火車-自動-運行時間**
    + 修復遠端執行中頻率偵測的問題
    + 移動`AutoMLStep`到`azureml-pipeline-steps package`。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。
  + **azureml-train-core**
    + 移動`AutoMLStep`到`azureml-pipeline-steps package`。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>適用於 Python v1.1.1rc0 的 Azure 機器學習 SDK(預先發佈)

+ **錯誤修復與改進**
  + **azure-cli-ml**
    + 單個實例分析已修復以生成建議,並在核心 sdk 中提供。
  + **azureml-automl-core**
    + 錯誤日誌記錄已得到改進。
  + **azureml-自動ml-執行時間**
    + 修復了當數據集包含具有長時間間隙的短粒時的預測問題。
    + 當自動最大視距打開且日期列以字串形式包含日期時,修復了問題。 如果無法將轉換到日期,我們添加了正確的轉換和合理的錯誤
    + 使用本機 NumPy 和 SciPy 對 FileCacheStore 的中間資料進行序列化和反序列化(用於本地自動ML執行)
    + 修復了失敗的子運行可能卡在"正在運行"狀態的 Bug。
  + **azureml-cli 常見**
    + 單個實例分析已修復以生成建議,並在核心 sdk 中提供。
  + **azureml-core**
    + `--grant-workspace-msi-access`新增為資料儲存 CLI 用於註冊 Azure Blob 容器的附加參數,該參數允許您註冊 VNet 後面的 Blob 容器
    + 單個實例分析已修復以生成建議,並在核心 sdk 中提供。
    + aks.py_deploy修復問題
    + 驗證要上載的模型的完整性,以避免靜默存儲故障。
  + **azureml-interpret**
    + 將 azureml 樣式異常新增到 azureml 解釋
    + 以角質模型的固定深度評分直譯器序列化
  + **azureml-pipeline-core**
    + 管管批次處理評分筆記現在使用並行 RunStep
  + **azureml-管道步驟**
    + 在`azureml-pipeline-steps``AutoMLStep`套件中移 。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。
  + **azureml-contrib-管道步驟**
    + 可選參數side_inputs添加到 ParallelRunStep。 此參數可用於在容器上安裝資料夾。 當前支援的類型是數據參考和管道數據。
  + **azureml-tensor板**
    + 更新了 azureml-tensor 板以支援張量流 2.0
  + **azureml-train-automl-client**
    + 修復了自定義完成配置的重寫問題,可篩選自定義壯舉配置。
  + **azureml-火車-自動-運行時間**
    + 在`azureml-pipeline-steps``AutoMLStep`套件中移 。 棄用內部`AutoMLStep``azureml-train-automl-runtime`。
  + **azureml-train-core**
    + 在 PyTorch 估計器中支援 PyTorch 版本 1.4
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>適用於 Python v1.1.0rc0 的 Azure 機器學習 SDK(預先發佈)

+ **重大變更**
  + **語意化版本控制系統 2.0.0**
    + 從版本 1.1 開始,Azure ML Python SDK 採用語義版本 2.0.0。 [請在這裡閱讀更多資訊](https://semver.org/)。 所有後續版本都將遵循新的編號方案和語義版本控制協定。 
  
+ **錯誤修復與改進**
  + **azureml-自動ml-執行時間**
    + 提高壯舉速度。
    + 修復了評分期間的頻率檢查,現在在預測任務中,我們不需要列車和測試集之間的嚴格頻率等價。
  + **azureml-core**
    + 現在,當為 Web 服務重新生成密鑰時,用戶可以為 auth 鍵指定值。
  + **azureml-interpret**
    + 更新的 azureml 解釋,以依賴於解釋社區 0.5.0
  + **azureml-pipeline-core**
    + 修復了儘管更改參數清單,但 PythonScriptStep 結果可能被錯誤地重用的 Bug
  + **azureml-管道步驟**
    + 新增資料集的文件範例為 PythonScript 步驟輸入
  + **azureml-contrib-管道步驟**
    + 現在可以通過傳遞管道參數來覆蓋在 ParallelRunConfig 中傳遞的參數。 支援aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout(aml_node_count和aml_process_count_per_node)支援的新管道參數已經是早期版本的一部分。
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>適用於 Python v1.0.85 的 Azure 機器學習 SDK

+ **新功能**
  + **azureml-core**
    + 取得給定工作區與訂閱中 AmlCompute 資源的目前核心使用和配額限制
  
  + **azureml-contrib-管道步驟**
    + 使用戶能夠將表格資料集作為從上一步到並行潤步的中間結果傳遞

+ **錯誤修復與改進**
  + **azureml-自動ml-執行時間**
    + 刪除了對已部署預測服務的請求中y_query列的要求。 
    + "y_query"從多米尼克的橙汁筆記本服務請求部分中刪除。
    + 修復了阻止在已部署的模型上預測的錯誤,在具有日期時間列的數據集上運行。
    + 添加了 Matthews 相關係數作為分類指標,用於二進位和多類分類。
  + **azureml-contrib-解釋**
    + 從 azureml-contrib 解釋中刪除的文本解釋器作為文本解釋已移動到即將發佈的解釋文本回購。
  + **azureml-core**
    + 數據集:文件數據集的用法不再依賴於要安裝在 python env 中的數位和熊貓。
    + 更改了 LocalWebservice.wait_for_deployment() 以在嘗試 ping 其運行狀況終結點之前檢查本地 Docker 容器的狀態,從而大大減少了報告失敗部署所需的時間。
    + 修復了使用 LocalWebservice() 建構函數從現有部署創建服務物件時本地 Webservice.reload() 中使用的內部屬性的初始化。
    + 已編輯的錯誤消息以進行澄清。
    + 向 AksWeb 服務添加了一種名為 get_access_token() 的新方法,該方法將返回 AksServiceAccessToken 物件,該物件包含訪問令牌、時間戳後刷新、時間戳到期和令牌類型。 
    + 在新方法返回此方法返回的所有資訊時,在 AksWebservice 服務中棄用的現有get_token() 方法。
    + 修改的 az ml 服務獲取權杖命令的輸出。 重新命名的權杖以造訪權杖和刷新。 添加了過期On 和令牌類型屬性。
    + 固定get_active_runs
  + **azureml 解釋模型**
    + 將 shap 更新為 0.33.0,解釋社區更新為 0.4.*
  + **azureml-interpret**
    + 將 shap 更新為 0.33.0,解釋社區更新為 0.4.*
  + **azureml-火車-自動-運行時間**
    + 添加了 Matthews 相關係數作為分類指標,用於二進位和多類分類。
    + 從代碼棄用預處理標誌,並替換為「壯舉化」-預設情況下處於打開狀態

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>適用於 Python v1.0.83 的 Azure 機器學習 SDK

+ **新功能**
  + 數據集:添加兩個選項`on_error``out_of_range_datetime`,`to_pandas_dataframe`當 資料具有錯誤值`None`而不是填充 它們時,將失敗。
  + 工作區:為具有`hbi_workspace`敏感數據的工作區添加了標誌,從而可進一步加密並禁用工作區上的高級診斷。 我們還通過在創建工作區時指定`cmk_keyvault``resource_cmk_uri`和 參數來添加對為關聯的Cosmos DB實例自帶密鑰的支持,該工作區在預配工作區時在訂閱中創建Cosmos DB實例。 [在此處閱讀更多內容。](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **錯誤修復與改進**
  + **azureml-自動ml-執行時間**
    + 修復了在 3.5.4 以下的 Python 版本上運行 AutoML 時引發 TypeError 的回歸。
  + **azureml-core**
    + `datastore.upload_files`修復了無法`./`使用相對路徑的位置。
    + 為所有影像類別碼路徑加入了棄用訊息
    + 月餅區域固定模型管理 URL 構造。
    + 修復了無法使用source_dir模型打包用於 Azure 函數的問題。    
    + 向[Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)新增了一個選項,用於將映射推送到 AzureML 工作區容器註冊表
    + 更新了 SDK 以在 Azure 突觸上以與背面相容的方式使用新的權杖庫。
  + **azureml-interpret**
    + 修復了在無可下載說明時返回無的修復錯誤。 現在引發一個異常,匹配其他位置的行為。
  + **azureml-管道步驟**
    + 不要允許在`DatasetConsumptionConfig`中使用`Estimator`時`inputs`會`Estimator`將 s`EstimatorStep`傳回的參數 。
  + **azureml-sdk**
    + 將 AutoML 用戶端添加到 azureml-sdk 包中,使遠端自動ML 運行無需安裝完整的 AutoML 包即可提交。
  + **azureml-train-automl-client**
    + 主控台輸出上為自動ml 執行而更正的對齊方式
    + 修復了在遠端 ml 計算上可能安裝錯誤版本的熊貓的錯誤。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>適用於 Python v1.0.81 的 Azure 機器學習 SDK

+ **錯誤修復與改進**
  + **azureml-contrib-解釋**
    + 從 azureml 解釋中順從到解釋社區
  + **azureml-core**
    + 計算目標現在可以指定為相應部署配置物件的參數。 這是要部署到的計算目標的名稱,而不是 SDK 物件。
    + 將「已創建」資訊添加到模型和服務物件。 可通過<var>.created_by存取
    + 修復了容器映像.run(),該埠未正確設置 Docker 容器的 HTTP 埠。
    + 為`azureml-dataprep``az ml dataset register`cli 指令提供可選功能
    + 修復了錯誤,其中`TabularDataset.to_pandas_dataframe`錯誤會回退到備用讀取器,並列印出警告。
  + **azureml 解釋模型**
    + 從 azureml 解釋中順從到解釋社區
  + **azureml-pipeline-core**
    + 添加了新的管道步驟`NotebookRunnerStep`,以在管道中運行本地筆記本作為步驟。
    + 已棄用get_all已發佈管道、計畫和管道終結點的功能已刪除
  + **azureml-train-automl-client**
    + 開始棄用data_script作為自動ML的輸入。


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>適用於 Python v1.0.79 的 Azure 機器學習 SDK

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + 要記錄的已移除的壯舉設定
      + 更新的日誌記錄以僅記錄"自動"/"關閉"/"自定義"。
  + **azureml-自動ml-執行時間**
    + 增加了對熊貓的支援。 系列和熊貓。 用於檢測列數據類型的分類。 以前僅支援 numpy.ndarray
      + 添加了相關代碼更改以正確處理分類類型。
    + 改進了預測功能介面:y_pred參數是可選的。 -文檔字串得到了改進。
  + **azureml-contrib-dataset**
    + 修復了無法裝載標記數據集的錯誤。
  + **azureml-core**
    + 的`Environment.from_existing_conda_environment(name, conda_environment_name)`Bug 修復。 使用者可以建立環境實例,該實體是本地環境的精確副本
    + 默認情況下,將與時間序列相關的數據集方法`include_boundary=True`更改為。
  + **azureml-train-automl-client**
    + 修復了當顯示輸出設置為 false 時未列印驗證結果的問題。


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>適用於 Python v1.0.76 的 Azure 機器學習 SDK

+ **重大變更**
  + Azureml-火車-自動ML升級問題
    + 從 azureml-train-automl<1.0.76 升級到 azureml-train-automl>±1.0.76 可能會導致部分安裝,從而導致某些自動ml 導入失敗。 要解決此問題,可以運行 在中https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd找到 的設置腳本。 或者,如果您直接使用點子,您可以:
      + "點安裝 -升級 azureml-火車-自動ml"
      + "pip 安裝 -- 忽略安裝的 azureml-火車-自動ml-用戶端"
    + 或者您可以在升級之前卸載舊版本
      + "點卸載 azureml-火車自動ml"
      + "點安裝 azureml-火車自動ml"

+ **錯誤修復與改進**
  + **azureml-自動ml-執行時間**
    + AutoML 現在將在計算二進位分類任務的平均標量指標時考慮真類和假類。
    + 將 AzureML-自動ML-Core 中的機器學習和培訓代碼移動到新的包 AzureML-自動ML-運行時。
  + **azureml-contrib-dataset**
    + 使用下載`to_pandas_dataframe`選項調用標記的數據集時,現在可以指定是否覆蓋現有檔。
    + 當調用`keep_columns``drop_columns`或 導致時間序列、標籤或圖像列被刪除時,也會刪除數據集的相應功能。
    + 修復了物件檢測任務的 pytorch 載入器問題。
  + **azureml-contrib-解釋**
    + 從 azureml-contrib 解釋中刪除說明儀表板小元件,更改了套件以參考interpret_community中的新元件
    + 解釋社區的更新版本到 0.2.0
  + **azureml-core**
    + 提高的效能`workspace.datasets`。
    + 新增使用使用者名稱和密碼認證註冊 Azure SQL 資料庫資料儲存的功能
    + 修復了從相對路徑載入 Run 配置的修復程式。
    + 呼叫`keep_columns``drop_columns`或 導致刪除時間序列欄時,也會刪除資料集的相應功能。
  + **azureml-interpret**
    + 解釋社區的更新版本到 0.2.0
  + **azureml-管道步驟**
    + 為`runconfig_pipeline_params`Azure 機器學習管道步驟記錄支援的值。
  + **azureml-pipeline-core**
    + 添加了 CLI 選項,用於下載管道命令的 json 格式的輸出。
  + **azureml-train-automl**
    + 將 AzureML-火車-自動ML 拆分為 2 個包、用戶端包 AzureML-培訓-自動ML-用戶端和 ML 培訓包 AzureML-培訓-自動ML-運行時
  + **azureml-train-automl-client**
    + 添加了一個用於提交 AutoML 實驗的瘦用戶端,而無需在本地安裝任何機器學習依賴項。
    + 修復了遠端運行中自動檢測到的延遲、滾動視窗大小和最大視野的日誌記錄。
  + **azureml-火車-自動-運行時間**
    + 添加了新的 AutoML 包,用於將機器學習和運行時元件與用戶端隔離開來。
  + **azureml-contrib-火車-rl**
    + 在 SDK 中添加了增強學習支援。
    + 在 RL SDK 中添加了 AmlWindows 計算支援。


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>適用於 Python v1.0.74 的 Azure 機器學習 SDK

  + **預覽功能**
    + **azureml-contrib-dataset**
      + 導入 azureml-contrib 資料集後,可以`Dataset.Labeled.from_json_lines`調`._Labeled`用 而不是創建標記的數據集。
      + 使用下載`to_pandas_dataframe`選項調用標記的數據集時,現在可以指定是否覆蓋現有檔。
      + 當調用`keep_columns``drop_columns`或 導致時間序列、標籤或圖像列被刪除時,也會刪除數據集的相應功能。
      + 修復了 PyTorch 載入`dataset.to_torchvision()`程式在 調用 時的問題。

+ **錯誤修復與改進**
  + **azure-cli-ml**
    + 將模型分析添加到預覽 CLI。
    + 修復了 Azure 儲存中的重大更改,導致 AzureML CLI 失敗。
    + 為 AKS 類型將負載均衡器類型加入 MLC
  + **azureml-automl-core**
    + 修復了時間序列上最大視界(缺少值和多個顆粒)的問題。
    + 修復了生成交叉驗證拆分期間發生故障的問題。
    + 將此部分替換為標記格式的消息,以顯示在發行說明中: -改進預測數據集中短粒的處理。
    + 修復了在日誌記錄期間遮罩某些使用者信息的問題。 -改進預測運行期間錯誤的日誌記錄。
    + 將 psutil 作為 conda 依賴項添加到自動生成的 yml 部署檔。
  + **azureml-contrib-mir**
    + 修復了 Azure 儲存中的重大更改,導致 AzureML CLI 失敗。
  + **azureml-core**
    + 修復了導致 Azure 函數上部署的模型生成 500 的 Bug。
    + 修復了未在快照上應用 amlignore 文件的問題。
    + 添加了一個新的 API amlcompute.get_active_runs,該get_active_runs返回用於在給定的 aml 計算上運行和排隊運行的生成器。
    + 將負載均衡器類型添加到 AKC 類型的 MLC。
    + 在run.py中添加了append_prefix bool 參數,並在artifacts_client中download_artifacts_from_prefixdownload_files。 此旗標用於選擇地拼平來源路徑,以便僅將檔案或資料夾名稱加入到output_directory
    + 修復數據集使用方式的`run_config.yml`去序列化問題。
    + 呼叫`keep_columns``drop_columns`或 導致時間序列列被刪除時,資料集的相應功能也將被刪除。
  + **azureml-interpret**
    + 將解釋社區版本更新為 0.1.0.3
  + **azureml-train-automl**
    + 修復了automl_step可能無法列印驗證問題的問題。
    + 修復了register_model成功,即使模型的環境在本地缺少依賴項也是如此。
    + 修復了某些遠端運行未啟用 Docker 的問題。
    + 添加導致本地運行過早失敗異常的日誌記錄。
  + **azureml-train-core**
    + 考慮在計算自動超參數優化最佳子運行時運行resume_from。
  + **azureml-pipeline-core**
    + 管道參數建構中的固定參數處理。
    + 添加了管道描述和步驟類型 yaml 參數。
    + 管道步驟的新 yaml 格式,並為舊格式添加了棄用警告。



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>網路體驗

協作[https://ml.azure.com](https://ml.azure.com)工作區登錄頁已增強,並重命名為 Azure 機器學習工作室(預覽)。

在工作室中,您可以訓練、測試、部署和管理 Azure 機器學習資產,如數據集、管道、模型、終結點等。

從工作室存取以下基於 Web 的創作工具:

| 基於 Web 的工具 | 描述 | 版本 |
|-|-|-|
| 筆記本 VM(預覽版) | 完全管理的基於雲的工作站 | 基本&企業 |
| [自動機器學習](tutorial-first-experiment-automated-ml.md)(預覽) | 無需自動化機器學習模型開發的代碼經驗 | Enterprise |
| [設計器](concept-designer.md)(預覽版) | 拖放機器學習建模工具,以前稱為設計器 | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure 機器學習設計器增強功能

+ 以前稱為視覺化介面 
+    11 個新[模組](algorithm-module-reference/module-reference.md),包括推薦者、分類器和培訓實用程式,包括功能工程、交叉驗證和數據轉換。

### <a name="r-sdk"></a>R SDK 
 
數據科學家和 AI 開發人員使用[Azure 機器學習 SDK 進行 R](tutorial-1st-r-experiment.md)構建和運行機器學習工作流。

Azure 機器學習 SDK`reticulate`用於 R 使用包綁定到 Python SDK。 以直接結合 Python,R 的 SDK 允許您從您選擇的任何 R 環境存取 Python SDK 中實現的核心物件和方法。

SDK 的主要功能包括:

+    管理用來對您的機器學習實驗進行監視、記錄及組織的雲端資源。
+    使用雲資源對模型進行培訓,包括 GPU 加速模型培訓。
+    在 Azure 容器實例 (ACI) 和 Azure 庫伯奈斯服務 (AKS) 上將模型部署為 Web 服務。

有關完整文件,請參閱[套件網站](https://azure.github.io/azureml-sdk-for-r)。

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure 機器學習整合事件網格 

Azure 機器學習現在是事件網格的資源提供程式,您可以通過 Azure 門戶或 Azure CLI 配置機器學習事件。 用戶可以為運行完成、模型註冊、模型部署和檢測到的數據漂移創建事件。 這些事件可以路由到事件網格支援的事件處理程式以供使用。 有關詳細資訊,請參閱機器學習事件[架構](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)、[概念](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration)和[教程](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid)文章。

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>適用於 Python v1.0.72 的 Azure 機器學習 SDK

+ **新功能**
  + 通過[**azureml 資料漂移**](https://docs.microsoft.com/python/api/azureml-datadrift)包添加數據集監視器,允許監視時間序列數據集的數據漂移或隨時間變化的其他統計變化。 如果檢測到漂移或滿足數據上的其他條件,則可以觸發警報和事件。 有關詳細資訊,請參閱[我們的文件](https://aka.ms/datadrift)。
  + 在 Azure 機器學習中發佈兩個新版本(也稱為 SKU 可互換)。 使用此版本,您現在可以創建基本或企業 Azure 機器學習工作區。 所有現有工作區將預設為基本版,您可以隨時轉到 Azure 門戶或工作室升級工作區。 可以從 Azure 門戶創建基本工作區或企業工作區。 請閱讀[我們的文檔](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)以瞭解更多資訊。 在 SDK 中,可以使用工作區物件的"sku"屬性確定工作區的版本。
  + 我們還對 Azure 機器學習計算進行了增強 - 現在,除了查看診斷日誌以進行調試外,您還可以在 Azure 監視器中查看群集的指標(如總節點、正在運行的節點、總核心配額)。 此外,您還可以查看群集上當前正在運行的或排隊運行,以及群集上各種節點的 IP 等詳細資訊。 您可以在門戶中或通過在 SDK 或 CLI 中使用相應的函數來查看這些功能。

  + **預覽功能**
    + 我們將在 Azure 機器學習計算中發表對本地 SSD 磁碟加密的預覽支援。 請提出技術支援票證,使您的訂閱白名單,以使用此功能。
    + Azure 機器學習批處理推理的公共預覽。 Azure 機器學習批處理推理面向不分時間的大型推理作業。 批處理推理提供經濟高效的推理計算擴展,為非同步應用程式提供無與倫比的輸送量。 它針對大數據集合的高通量、防火和忘記推理進行了優化。
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + 開啟標記資料集的功能
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

+ **錯誤修復與改進**
  + **azure-cli-ml**
    + CLI 現在支援模型打包。
    + 添加了數據集 CLI。 有關詳細資訊,`az ml dataset --help`
    + 添加了對部署和打包支援的模型(ONNX、scikit-學習和 TensorFlow)的支援,而無需使用推理配置實例。
    + 在 SDK 和 CLI 中添加了用於服務部署(ACI 和 AKS)的覆蓋標誌。 如果提供,將覆蓋現有服務,如果服務的名稱已存在。 如果服務不存在,將創建新服務。
    + 模型可以註冊兩個新的框架,Onnx 和 Tensorflow。 - 模型註冊接受模型的範例輸入數據、樣本輸出資料和資源配置。
  + **azureml-automl-core**
    + 僅當設置運行時約束時,訓練反覆運算才會在子進程中運行。
    + 添加了用於預測任務的保護欄,以檢查指定的max_horizon是否會在給定計算機上導致記憶體問題。 如果願意,將顯示護欄消息。
    + 增加了對複雜頻率的支援,如2年1個月。 -如果無法確定頻率,則添加可理解的錯誤消息。
    + 將 azureml 預設值新增到自動產生的 conda env 以解決模型部署失敗
    + 允許將 Azure 機器學習導管中的中間資料轉換為表格資料集`AutoMLStep`, 並使用 。
    + 為流式處理實現的列用途更新。
    + 為流式處理器和哈希OneHotEncoder實現了變壓器參數更新。
    + 將目前資料大小和所需的最小資料大小添加到驗證錯誤消息中。
    + 更新了交叉驗證所需的最小數據大小,以確保每個驗證摺疊中至少兩個樣本。
  + **azureml-cli 常見**
    + CLI 現在支援模型打包。
    + 模型可以註冊兩個新的框架,Onnx 和 Tensorflow。
    + 模型註冊接受模型的範例輸入數據、樣本輸出數據和資源配置。
  + **azureml-contrib-gbdt**
    + 修復了筆記本的釋放通道
    + 新增了不支援的非 Aml 計算計算目標的警告
    + 將 LightGMB 估計器加入 azureml-contrib-gbdt 套件
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI 現在支援模型打包。
    + 為棄用數據集 API 添加棄用警告。 請參閱 在https://aka.ms/tabular-dataset上的 數據集 API 更改通知。
    + 如果[`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)註冊了數據集,則更改為返回註冊名稱和版本。
    + 修復了腳本 Runfig 與數據集一起用作參數的錯誤,無法重複使用該 Bug 提交實驗運行。
    + 運行期間檢索的數據集將被跟蹤,並且可以在運行詳細資訊頁中看到,或在運行完成後通過調用[`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)來查看。
    + 允許將 Azure 機器學習導管中的中間資料轉換為表格資料集[`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep), 並使用 。
    + 添加了對部署和打包支援的模型(ONNX、scikit-學習和 TensorFlow)的支援,而無需使用推理配置實例。
    + 在 SDK 和 CLI 中添加了用於服務部署(ACI 和 AKS)的覆蓋標誌。 如果提供,將覆蓋現有服務,如果服務的名稱已存在。 如果服務不存在,將創建新服務。
    +  模型可以註冊兩個新的框架,Onnx 和 Tensorflow。 模型註冊接受模型的範例輸入數據、樣本輸出數據和資源配置。
    + 為 MySQL 為 Azure 資料庫添加了新的數據存儲。 添加了在 Azure 機器學習管道中使用 Azure 資料庫為 MySQL 的範例。
    + 新增從實驗中加入與移除標記的功能 新增功能以從執行中移除標記
    + 在 SDK 和 CLI 中添加了用於服務部署(ACI 和 AKS)的覆蓋標誌。 如果提供,將覆蓋現有服務,如果服務的名稱已存在。 如果服務不存在,將創建新服務。
  + [**azureml 資料漂移**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 從`azureml-contrib-datadrift`移動到`azureml-datadrift`
    + 新增對監控漂移和其他統計度量的時間序列資料集的支援
    + 新方法`create_from_model()``create_from_dataset()`與[`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class))類別 。 該方法`create()`將被棄用。
    + 調整 Azure 機器學習工作室中的 Python 和 UI 中的可視化效果。
    + 除了數據集監視器的每日計劃外,還支援每周和每月的監視器調度。
    + 支援數據監視器指標的回填,以分析數據集監視器的歷史數據。
    + 各種 Bug 修正
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + 不再需要 azureml 資料準備來提交`yaml`從管道 檔運行的 Azure 機器學習管道。
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + 將 azureml 預設值新增到自動產生的 conda env 以解決模型部署失敗
    + AutoML 遠端訓練現在包括 azureml 預設值,允許重用訓練 env 進行推理。
  + **azureml-train-core**
    + 新增 PyTorch 1.3[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)在估算器中的支援

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>視覺化介面 (預覽)

+ Azure 機器學習可視化介面(預覽)已過大修,以在[Azure 機器學習管道](concept-ml-pipelines.md)上運行。 可視化介面中創作的管道(以前稱為實驗)現在與核心 Azure 機器學習體驗完全整合。
  + 具有 SDK 資產的統一管理經驗
  + 視覺化介面模型、導管和端點的版本和追蹤
  + 重新設計的 UI
  + 新增批次處理推理部署
  + 新增 Azure 函貝恩斯服務 (AKS) 對推理計算目標的支援
  + 新的 Python 步驟導管創作工作流
  + 以視覺化創作工具的新[登入頁](https://ml.azure.com)

+ **新增模組**
  + 應用數學運算
  + 套用 SQL 轉換
  + 剪輯值
  + 匯總資料
  + 從 SQL 資料庫匯入

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>適用於 Python v1.0.69 的 Azure 機器學習 SDK

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + 將模型解釋限制為最佳運行,而不是計算每次運行的解釋。 使本地、遠端和亞行的此行為發生變化。
    + 新增對 UI 按一個支援軟體
    + 在 aml 計算中`automl`添加了 psutil 作為依賴項,並將 psutil 作為 conda 依賴項。
    + 修復了啟發式滯後和滾動視窗大小在預測數據集上的一些系列可能導致線性代數誤差的問題
      + 為預測運行中經測定的參數添加了列印。
  + **azureml-contrib-資料漂移**
    + 如果數據集級別漂移不在第一部分中,則在創建輸出指標時添加了保護。
  + **azureml-contrib-解釋**
    + azureml-contrib-解釋模型包已重新命名為 azureml-contrib-解釋
  + **azureml-core**
    + 將 API 添加到取消註冊數據集。 `dataset.unregister_all_versions()`
    + azureml-contrib-解釋模型包已重命名為 azureml-contrib-解釋。
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + 將 API 添加到取消註冊數據集。 數據。[unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + 添加了數據集 API 以檢查數據更改的時間。 `dataset.data_changed_time`.
    + `FileDataset`能夠使用`TabularDataset`並作為輸入`PythonScriptStep``EstimatorStep`,`HyperDriveStep`以及 Azure 機器學習管道中
    + 對於具有`FileDataset.mount`大量檔的資料夾,性能得到了提高
    + 能夠將[檔案資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset)和[表格資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset)用作 Azure 機器學習管道中[PythonScript 步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)、[估計步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)和[超驅動器步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep)的輸入。
    + 檔資料集的性能。對有許多檔案的資料夾,[已改進載入()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-)已改進
    + 在運行詳細資訊中向已知錯誤建議添加了 URL。
    + 修復了 run.get_metrics 中的 Bug,如果執行中子級太多,則請求將失敗
    + 修復了[run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-)中的 Bug,如果執行中子級太多,則請求將失敗
    + 添加了對 Arcadia 群集上身份驗證的支援。
    + 創建實驗物件可在 Azure 機器學習工作區中獲取或創建用於運行歷史記錄追蹤的實驗。 實驗 ID 和存檔時間在創建時填充在實驗物件中。 範例:實驗 = 實驗(工作區、"新實驗")experiment_id = experiment.id存檔()和重新啟動()是可在實驗中調用的函數,用於隱藏和恢復實驗,以便從 UX 中顯示或預設返回清單實驗的調用中。 如果創建與存檔實驗同名的新實驗,則可以通過傳遞新名稱來重新啟動存檔實驗。 只能有一個具有給定名稱的活動實驗。 範例:實驗1 = 實驗(工作區,"活動實驗")實驗1.存檔() = 使用與存檔的名稱創建新的活動實驗。 實驗2。 * 實驗(工作區,"活動實驗")實驗1.重新啟動(new_name="以前的活動實驗")實驗上的靜態方法清單()可以採用名稱篩選器和 ViewType 篩選器。 檢視類型值是"ACTIVE_ONLY"、"ARCHIVED_ONLY"和"ALL"示例:archived_experiments = 實驗.list(工作區,view_type="ARCHIVED_ONLY")all_first_experiments = 實驗.list(工作區,名稱="第一個實驗",view_type="ALL")
    + 支援使用環境進行模型部署和服務更新
  + **azureml 資料漂移**
    + DataDriftDector 類的顯示屬性不再支援可選參數"with_details"。 顯示屬性將僅顯示要素列的數據漂移係數和數據漂移貢獻。
    + 資料漂移檢測器屬性「get_output」行為更改:
      + 輸入參數start_time,end_time是可選的,而不是強制性的;
      + 輸入特定start_time和/或具有相同呼叫中特定run_idend_time將導致值錯誤異常,因為它們是互斥的
      + 通過輸入特定的start_time和/或end_time,將僅返回計劃運行的結果;
      + 參數"daily_latest_only"被棄用。
    + 支持檢索基於數據集的數據漂移輸出。
  + **azureml 解釋模型**
    + 將 AzureML 解釋模型包重命名為 AzureML 解釋,保持舊包的向後相容性。
    + 修復了`automl`錯誤,原始說明設定為分類任務,而不是預設從 ExplanationClient 下載時回歸
    + 新增直接使用`ScoringExplainer`建立支援`MimicWrapper`
  + **azureml-pipeline-core**
    + 提高大型導管建立的效能
  + **azureml-train-core**
    + 在 TensorFlow 估計器中新增 TensorFlow 2.0 支援
  + **azureml-train-automl**
    + 創建[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment)物件可在 Azure 機器學習工作區中獲取或創建用於運行歷史記錄追蹤的實驗。 實驗 ID 和存檔時間在創建時填充在實驗物件中。 範例：

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [存檔()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--)和[重新啟動()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-)是可在實驗中調用的函數,用於隱藏和恢復實驗,防止實驗在 UX 中顯示或預設返回到列表實驗的調用中。 如果創建與存檔實驗同名的新實驗,則可以通過傳遞新名稱來重新啟動存檔實驗。 只能有一個具有給定名稱的活動實驗。 範例：

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        實驗上的靜態方法[清單()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-)可以採用名稱篩選器和 ViewType 篩選器。 視圖類型值是"ACTIVE_ONLY"、"ARCHIVED_ONLY"和"ALL"。 範例：

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 支援使用環境進行模型部署和服務更新。
  + **[azureml 資料漂移](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)類的顯示屬性不再支援可選參數"with_details"。 顯示屬性將僅顯示要素列的數據漂移係數和數據漂移貢獻。
    + 資料漂移偵測器函數 [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-)行為變更:
      + 輸入參數start_time,end_time是可選的,而不是強制性的;
      + 輸入特定start_time和/或end_time,在相同的調用中具有特定run_id將導致值錯誤異常,因為它們是互斥的;因為它們是互斥的。
      + 通過輸入特定的start_time和/或end_time,將僅返回計劃運行的結果;
      + 參數"daily_latest_only"被棄用。
    + 支持檢索基於數據集的數據漂移輸出。
  + **[azureml 解釋模型](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + 將 AzureML 解釋模型包重命名為 AzureML 解釋,保持舊包的向後相容性。
    + 修復了 AutoML Bug,原始說明設置為分類任務,默認情況下從解釋用戶端下載時返回。
    + 新增對直接使用[模擬包裝器](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)建立[評分說明](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py)器的支援
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 提高了大型管道創建的性能。
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + 在 TensorFlow 估計器中添加了[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) 2.0 支援。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 當設置反覆運算失敗時,父運行將不再失敗,因為業務流程已經處理它。
    + 新增了對 AutoML 實驗的本地 docker 和本地 conda 支援
    + 添加了對 AutoML 實驗的本地 docker 和本地 conda 支援。


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure 機器學習工作區的新 Web 體驗(預覽)

[新工作區門戶](https://ml.azure.com)中的「實驗」選項卡已更新,以便數據科學家可以以更性能的方式監視實驗。 您可以瀏覽以下功能:
+ 實驗中繼資料,輕鬆篩選和排序實驗清單
+ 簡化和可執行的實驗詳細資訊頁面,允許您可視化和比較執行
+ 執行詳細資訊頁面以瞭解和監視培訓執行的新設計

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>適用於 Python v1.0.65 的 Azure 機器學習 SDK

  + **新功能**
    + 添加了精心策劃的環境。 這些環境已預先配置了用於常見機器學習任務的庫,並已預建並緩存為 Docker 映射,以便更快地執行。 默認情況下,它們出現在工作區的環境清單中,首碼為「AzureML」。。
    + 添加了精心策劃的環境。 這些環境已預先配置了用於常見機器學習任務的庫,並已預建並緩存為 Docker 映射,以便更快地執行。 預設情況下,它們顯示在[工作區](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)的環境清單中,首碼為「AzureML」。。

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 為亞行與 HDI 新增了 ONNX 轉換支援

+ **預覽功能**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援 BERT 與 BiLSTM 為文字輔助器(僅限預覽)
    + 支援用於列用途和變壓器參數的壯舉定制(僅限預覽)
    + 當使用者在培訓期間啟用模型說明時支援原始說明(僅限預覽)
    + 新增用於`timeseries`預測為可訓練管道的先知(僅限預覽)

  + **azureml-contrib-資料漂移**
    + 從 azureml-contrib 資料漂移重新置放到 azureml 資料漂移的包;此`contrib`套件將在將來的版本中移除

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + 引入自動ML設定與自動ML基礎設定
    + 引入自動[ML 設定](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)與自動 ML 基礎設定
      + 使用給定欄和元素類型覆寫欄用途
      + 覆寫變壓器參數
    + 為 explain_model() 和 retrieve_model_explanations() 增加了棄用訊息
    + 將先知新增為可訓練管道(僅限預覽)
    + 為explain_model()和 retrieve_model_explanations()添加了棄用消息。
    + 將先知添加為可訓練管道(僅限預覽)。
    + 增加了對自動檢測目標滯後、滾動視窗大小和最大視界的支援。 如果target_lags、target_rolling_window_size或max_horizon之一設置為"自動",則將應用啟發式方法,根據訓練數據估計相應參數的值。
    + 當資料集包含一個顆粒列時,修復了預測,此顆粒為數位類型,並且訓練與測試集之間存在間隙
    + 修復了預測工作中遠端執行中重複索引的錯誤訊息
    + 在數據集包含一個顆粒列的情況下,修復了預測,此顆粒為數字類型,並且訓練與測試集之間存在間隙。
    + 修復了預測任務中遠端運行中重複索引的錯誤消息。
    + 添加了一個保護欄,用於檢查數據集是否不平衡。 如果是,則守護軌消息將寫入主控台。
  + **azureml-core**
    + 添加了通過模型物件檢索 SAS URL 以在存儲中建模的能力。 例如模型.get_sas_url()
    + 介紹`run.get_details()['datasets']`取得與已提交執行關聯的資料集
    + 添加`Dataset.Tabular.from_json_lines_files`API 以從 JSON 行檔創建表格數據集。 要瞭解表格數據集中的 JSON 行檔中的此表格數據,請https://aka.ms/azureml-data造訪瞭解 文檔。
    + 將supported_vmsizes()功能新增了其他 VM 大小欄位(OS 磁碟、GPU 數)
    + 向list_nodes () 函數添加了其他欄位,以顯示運行、私有和公共 IP、埠等。
    + 能夠在群集預配期間指定新欄位 -- remotelogin_port_public_access,該欄位可以設置為啟用或禁用,具體取決於在創建群集時是保持 SSH 埠打開還是關閉。 如果不指定它,該服務將智慧地打開或關閉埠,具體取決於您是否在 VNet 中部署群集。
  + **azureml 解釋模型**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 添加了通過模型物件檢索 SAS URL 以在存儲中建模的能力。 例如:模型。[get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 引入運行。[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[資料集] 取得與已提交執行關聯的資料集
    + 新增`Dataset.Tabular`API 。[from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)從 JSON 行檔創建表格數據集。 要瞭解表格數據集中的 JSON 行檔中的此表格數據,請https://aka.ms/azureml-data造訪瞭解 文檔。
    + 將[supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)功能新增了其他 VM 大小欄位(OS 磁碟、GPU 數)
    + 向[list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--)函數添加了其他欄位,以顯示運行、私有和公共 IP、埠等。
    + 能夠在群集[預配](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)`--remotelogin_port_public_access`期間指定可設置為啟用或禁用的新欄位,具體取決於在創建群集時是保持 SSH 埠打開還是關閉。 如果不指定它,該服務將智慧地打開或關閉埠,具體取決於您是否在 VNet 中部署群集。
  + **[azureml 解釋模型](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + 改進了分類方案中解釋輸出的文檔。
    + 添加了在評估示例的解釋上上傳預測 y 值的能力。 解鎖更有用的可視化效果。
    + 向「模擬包裝器」添加了解釋器屬性,以啟用獲取基礎的「模擬解釋器」。
  + **azureml-pipeline-core**
    + 新增了筆記本來描述模組、模組版本和模組步驟
  + **azureml-管道步驟**
    + 添加了 RScript 步驟,以支援透過 AML 管道運行 R 文稿。
    + 在 AzureBatchStep 中解析的固定元資料參數,導致錯誤消息" 未指定參數訂閱 Id 的分配」。
  + **azureml-train-automl**
    + 支援training_data、validation_data、label_column_name、weight_column_name資料輸入格式
    + 為 explain_model() 和 retrieve_model_explanations() 增加了棄用訊息
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 新增一個[筆記](https://aka.ms/pl-modulestep)來描述[模組](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class))、[模組版本](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion)與[模組步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)。
  + **[azureml-管道步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + 添加了[RScript 步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep),以支援透過 AML 管道運行 R 文稿。
    + 在[AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep)中解析的固定元資料參數,導致錯誤消息" 未指定參數訂閱 Id 的分配」。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 支援training_data、validation_data、label_column_nameweight_column_name資料輸入格式。
    + 為[explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-)和[retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)添加了棄用消息。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>適用於 Python v1.0.62 的 Azure 機器學習 SDK

+ **新功能**
  + 介紹了`timeseries`表格數據集上的特性。 這種特性允許對表格數據集的數據進行簡單的時間戳篩選,例如在一系列時間或最新數據之間獲取所有數據。 要瞭解 TabularDataset 上的`timeseries`功能,https://aka.ms/azureml-data請造https://aka.ms/azureml-tsd-notebook訪說明 文件或範例筆記本。
  + 啟用了具有表格數據集和文件數據集的培訓。 請訪問https://aka.ms/dataset-tutorial示例筆記本。

  + **azureml-train-core**
      + 新增`Nccl``Gloo`PyTorch 的估算器加入及支援

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + 棄用自動ML設置"lag_length"和滯後變形金剛。
    + 修復輸入資料的正確驗證(如果輸入資料以資料串流格式指定)
    + 修改了fit_pipeline.py 以生成圖形 json 並上載到工件。
    + 使用渲染下`userrun`的`Cytoscape`圖形。
  + **azureml-core**
    + 重新存取 Afidid 的錯誤處理,並根據新的錯誤處理變更
    + 為筆記本 VM 添加了自動 MSI 身份驗證。
    + 修復了由於重試失敗而上載損壞或空模型的錯誤。
    + 修復了`DataReference``DataReference`模式更改時名稱更改的 Bug(例如,`as_upload`呼叫`as_download`時`as_mount`, 或 )。
    + 為`mount_point``target_path`和`FileDataset.mount`製作`FileDataset.download`和可選。
    + 如果在調用時間序列相關 API 而不分配精細時間戳列或刪除分配的時間戳列,則找不到時間戳列的異常將被丟棄。
    + 時間序列列應使用其類型為「日期」的列分配,否則應例外
    + 分配 API"with_timestamp_columns"的時間序列列可以採用"無"值精細/粗時間戳列名稱,這將清除以前分配的時間戳列。
    + 當粗粒或細粒度時間戳列被丟棄時,將引發異常,並指示用戶可以在刪除清單中排除時間戳列或調用「無」值with_time_stamp後執行丟棄,以釋放時間戳列
    + 當粗粒度或細粒度時間戳列未包含在保留列清單中時,將引發異常,該列指示用戶可以在保留列清單中的時間戳列或調用具有"無"值with_time_stamp後進行保留,以發佈時間戳列。
    + 添加了已註冊模型大小的日誌記錄。
  + **azureml 解釋模型**
    + 未安裝"打包"python 包時列印到控制台的固定警告:"使用早於支援的 lightgbm 版本,請升級到大於 2.2.1 的版本"
    + 修復了下載模型說明,並分片用於具有多種功能的全域解釋
    + 修復在輸出說明上缺少初始化範例模擬直譯器
    + 使用兩種不同類型的模型使用說明客戶端進行上傳時,修復了設定屬性上的不可變錯誤
    + 為評分解釋器.explain()添加了get_raw參數,以便一個評分解釋器可以返回工程值和原始值。
  + **azureml-train-automl**
    + 從 AutoML 引入公共 API 以支援`automl`解釋 SDK 的解釋 - 透過分離 AutoML 壯舉和解釋 SDK 來支援自動ML解釋的較新方法 - Azureml 針對 AutoML 模型的整合原始解釋支援。
    + 從遠端訓練環境中刪除 azureml 預設值。
    + 將預設緩存儲存位置從基於 FileCacheStore 的位置更改為 Azure FileCacheStore 一個,用於 Azure 資料磚塊代碼路徑上的自動ML。
    + 修復輸入資料的正確驗證(如果輸入資料以資料串流格式指定)
  + **azureml-train-core**
    + 還原source_directory_data_store棄用。
    + 增加了覆蓋 azureml 安裝的套件版本的功能。
    + 在估計器中添加了`environment_definition`參數中的 dockerfile 支援。
    + 簡化估算器中的分散式訓練參數。

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure 機器學習工作區的新 Web 體驗(預覽)
新的 Web 體驗使數據科學家和數據工程師能夠完成其端到端機器學習生命週期,從數據準備和可視化到在單個位置培訓和部署模型。

![Azure 機器學習工作區 UI(預覽版)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**主要功能:**

使用此新的 Azure 機器學習介面,您現在可以:
+ 管理筆記本或連結到 Jupyter
+ [執行自動 ML 實驗](tutorial-first-experiment-automated-ml.md)
+ [從本地檔案、資料儲存、& Web 檔案建立資料集](how-to-create-register-datasets.md)
+ 探索&準備資料集以進行模型建立
+ 監控模型的資料漂移
+ 從儀表板檢視最新資源

在發佈此版本時,支援以下瀏覽器:Chrome、火狐、Safari 和 Microsoft 邊緣預覽。

**已知問題:**

1. 如果您看到「出現問題,請刷新您的瀏覽器! 部署正在進行時載入區塊檔的錯誤。

1. 無法刪除或重新命名筆記和檔案中的檔案。 在公共預覽期間,您可以使用 Jupyter UI 或筆記本 VM 中的終端執行更新檔操作。 由於它是一個已安裝的網路文件系統,您在筆記本 VM 上所做的所有更改都立即反映在筆記本工作區中。

1. 要將 SSH 放入筆記的 VM:
   1. 尋找在 VM 設定期間創建的 SSH 金鑰。 或者,在 Azure 機器學習工作區中查找鍵>打開計算選項卡>在清單中找到筆記本 VM >打开它的属性:從對話框中複製鍵。
   1. 將這些公共和私有 SSH 金鑰導入本地電腦。
   1. 使用它們將 SSH 放入筆記本 VM 中。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>適用於 Python v1.0.60 的 Azure 機器學習 SDK

+ **新功能**
  + 引入了檔案資料集,它引用資料存儲或公共 URL 中的單個或多個檔。 檔案可以是任何格式。 FileDataset 讓您能夠下載檔案或將檔案載入到計算中。 要瞭解檔案資料集,請造https://aka.ms/file-dataset訪 。
  + 新增了導管 Yaml 對 PythonScript 步驟、Adla 步驟、資料塊步驟、資料傳輸步驟和 AzureBatch 步驟的支援

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + AutoArima 現在是僅供預覽的可建議管道。
    + 改進了用於預測的錯誤報告。
    + 在預測任務中使用自定義異常而不是泛型改進日誌記錄。
    + 刪除max_concurrent_iterations的檢查小於反覆運算總數。
    + 自動ML型號現在傳回自動ML例外
    + 此版本提高了自動機器學習本地運行的執行性能。
  + **azureml-core**
    + 引入 Dataset.get_all(工作區),該工作區返回`TabularDataset`由`FileDataset`其 註冊名稱摳的字典和物件。

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + 為`parition_format`參數`Dataset.Tabular.from_delimited_files``Dataset.Tabular.from_parquet.files`引入與 。 每個資料路徑的分區資訊將根據指定的格式提取到列中。 "{column_name}"創建字串列,並且"{column_name:yyyyy/MM/dd/HH/mm/ss}"創建日期時間列,其中"yyyy"、"MM"、"dd","HH","mm"和"s"用於提取日期時間類型的年份、月份、日期、小時、分鐘和第二個日期時間類型。 partition_format應從第一個分區鍵的位置開始,直到檔路徑結束。 例如,給定路徑 ../USA/2019/01/01/data.csv",其中分區按國家/地區和時間,partition_format\'/{國家/地區]/{分區日期:yyyy/MM/dd}/data.csv"創建字串列"國家",值"美國",日期時間列"分區日期",值"2019-01-01"。
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + 為`partition_format`參數`Dataset.Tabular.from_delimited_files``Dataset.Tabular.from_parquet.files`引入與 。 每個資料路徑的分區資訊將根據指定的格式提取到列中。 "{column_name}"創建字串列,並且"{column_name:yyyyy/MM/dd/HH/mm/ss}"創建日期時間列,其中"yyyy"、"MM"、"dd","HH","mm"和"s"用於提取日期時間類型的年份、月份、日期、小時、分鐘和第二個日期時間類型。 partition_format應從第一個分區鍵的位置開始,直到檔路徑結束。 例如,給定路徑 ../USA/2019/01/01/data.csv",其中分區按國家/地區和時間,partition_format\'/{國家/地區]/{分區日期:yyyy/MM/dd}/data.csv"創建字串列"國家",值"美國",日期時間列"分區日期",值"2019-01-01"。
    + `to_csv_files`並將`to_parquet_files`方法添加到`TabularDataset`中。 這些方法透過資料轉換為指定格式`TabularDataset`的檔案,`FileDataset`在和之間轉換。
    + 保存模型.package()生成的 Dockerfile 時,自動登錄到基本映射註冊表。
    + 不再需要「gpu_support」;AML 現在自動檢測並使用 nvidia docker 擴展器(當它可用時)。 未來的版本將予以移除。
    + 添加了創建、更新和使用管道草稿的支援。
    + 此版本提高了自動機器學習本地運行的執行性能。
    + 用戶可以按名稱查詢運行歷史記錄中指標。
    + 在預測任務中使用自定義異常而不是泛型改進日誌記錄。
  + **azureml 解釋模型**
    + 添加了feature_maps參數到新的模擬包裝器,允許用戶獲取原始功能說明。
    + 預設情況下,資料集上載已關閉,用於解釋上載,並且可以使用upload_datasets_True 重新啟用
    + 將「is_law」篩選參數添加到說明清單和下載函數。
    + 將方法`get_raw_explanation(feature_maps)`添加到全域和本地解釋物件。
    + 將版本檢查添加到 lightgbm,如果低於支援的版本,則帶有列印警告
    + 批次處理說明時優化記憶體使用
    + 自動ML型號現在傳回自動ML例外
  + **azureml-pipeline-core**
    + 新增了建立、更新和使用導管草稿的支援 - 可用於維護可變管定義,並互動使用它們來執行這些定義
  + **azureml-train-automl**
    + 建立功能以安裝支援 gpu 的 pytorch v1.1.0、:::no-loc text="cuda":::工具套件 9.0、pytorch 變壓器的特定版本,這是在遠端 python 執行時環境中啟用 BERT/XLNet 所必需的。
  + **azureml-train-core**
    + 某些超參數空間定義錯誤的早期失敗直接在 sdk 中,而不是伺服器端。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure 機器學習資料準備 SDK v1.1.14
+ **錯誤修復與改進**
  + 使用原始路徑和認證啟用寫入 ADLS/ADLSGen2。
  + 修復了無法`include_path=True`正常工作的錯誤`read_parquet`。
  + 由`to_pandas_dataframe()`異常「無效屬性值:主機機密」引起的修復故障。
  + 修復了在 Spark 模式下無法在 DBFS 上讀取檔的 Bug。

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>適用於 Python v1.0.57 的 Azure 機器學習 SDK
+ **新功能**
  + 啟用`TabularDataset`自動ML 使用。 要瞭解有關詳細資訊`TabularDataset`,請https://aka.ms/azureml/howto/createdatasets造訪 。

+ **錯誤修復與改進**
  + **自動ml-用戶端-核心-本機用戶端**
    + 修復了在以熊貓數據框形式提供訓練和/或驗證標籤(y 和y_valid)而不是作為數位陣列時引起的錯誤。
    + 更新了介面以創建`RawDataContext`僅需要數據和對`AutoMLBaseSettings`象的介面。
    +  允許 AutoML 使用者刪除預測時不夠長的訓練系列。 - 允許 AutoML 使用者在預測時從訓練集中不存在的測試集中丟棄顆粒。
  + **azure-cli-ml**
    + 現在,您可以更新為 Microsoft 生成的和客戶證書部署在 AKS 群集上評分終結點的 TLS/SSL 證書。
  + **azureml-automl-core**
    + 修復了 AutoML 中未正確刪除缺少標籤的行的問題。
    + 改進了自動ML中的錯誤日誌記錄;完整的錯誤消息現在將始終寫入日誌檔。
    + AutoML 已將目錄固定更新為`azureml-defaults``azureml-explain-model`包含`azureml-dataprep`。 AutoML 將不再對包不匹配發出警告(包除`azureml-train-automl`外)。
    + 修復了 cv`timeseries`拆分大小不相等導致 bin 計算失敗的問題。
    + 在為交叉驗證訓練類型運行集合反覆運算時,如果我們最終下載在整個數據集上訓練的模型時遇到問題,則模型權重與輸入到投票集中的模型之間不一致。
    + 修復了在以熊貓數據框形式提供訓練和/或驗證標籤(y 和y_valid)而不是作為數位陣列時引起的錯誤。
    + 修復了在輸入表的布爾列中遇到 None 時預測任務的問題。
    + 允許 AutoML 使用者刪除預測時不夠長的訓練系列。 - 允許 AutoML 使用者在預測時從訓練集中不存在的測試集中丟棄顆粒。
  + **azureml-core**
    + 修復了blob_cache_timeout參數排序的問題。
    + 將外部擬合和將異常類型轉換為系統錯誤。
    + 添加了對遠端運行金鑰保管庫機密的支援。 添加 azureml.core.keyvault.keyvault 類,以便從與工作區關聯的密鑰庫中添加、獲取和列出機密。 支援的操作包括:
      + azureml.core.工作區.工作區.get_default_keyvault()
      + azureml.core.keyvault.keyvault.set_secret(名稱、值)
      + azureml.core.keyvault.keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.keyvault.get_secret(名稱)
      + azureml.core.keyvault.keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.keyvault.list_secrets()
    + 在遠端執行期間取得預設金鑰庫和取得機密的其他方法:
      + azureml.core.工作區.工作區.get_default_keyvault()
      + azureml.core.run.run.run.get_secret(名稱)
      + azureml.core.run.run.run.get_secrets(secrets_list)
    + 向提交超驅動器 CLI 命令添加了其他覆蓋參數。
    + 提高 API 調用的可靠性是將重試擴展到常見請求庫異常。
    + 添加對從已提交運行提交運行的提交運行的支援。
    + 修復了 FileWatcher 中即將過期的 SAS 令牌問題,導致檔在初始權杖過期後停止上載。
    + 支援在資料集 python SDK 匯入 HTTP csv/tsv 檔案。
    + 棄用工作區.setup() 方法。 向使用者顯示的警告消息建議改用 create() 或獲取()/from_config()。
    + 添加了 Environment.add_private_pip_wheel(),它允許將私有自定義 Python`whl`包上載到工作區,並安全地使用它們來構建/實現環境。
    + 現在,您可以更新為 Microsoft 生成的和客戶證書部署在 AKS 群集上評分終結點的 TLS/SSL 證書。
  + **azureml 解釋模型**
    + 添加參數以在上載時將模型 ID 添加到說明中。
    + 在`is_raw`記憶體和上傳中添加了標記到說明。
    + 為 azureml 解釋模型包添加了 pytorch 支援和測試。
  + **azureml 開啟資料集**
    + 支持檢測和記錄自動測試環境。
    + 添加類,讓美國人口按縣和郵政編碼。
  + **azureml-pipeline-core**
    + 將標籤屬性添加到輸入和輸出埠定義。
  + **azureml-telemetry**
    + 修復了不正確的遙測配置。
  + **azureml-train-automl**
    + 修復了在設定失敗時錯誤未記錄在安裝程式運行的"錯誤"欄位中,因此未存儲在父運行"錯誤"中的錯誤。
    + 修復了 AutoML 中未正確刪除缺少標籤的行的問題。
    + 允許 AutoML 使用者刪除預測時不夠長的訓練系列。
    + 允許 AutoML 使用者在預測時從訓練集中不存在的測試集中丟棄顆粒。
    + 現在,AutoMLStep`automl`通過配置到後端,以避免更改或添加新配置參數時出現任何問題。
    + AutoML 數據保護軌現已處於公共預覽版。 培訓后,使用者將看到數據保護欄報告(用於分類/回歸任務),並且還可通過 SDK API 訪問該報告。
  + **azureml-train-core**
    + 在 PyTorch 估計器中添加了割炬 1.2 支援。
  + **azureml-widgets**
    + 改進了分類培訓的混淆矩陣圖表。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure 機器學習資料準備 SDK v1.1.12
+ **新功能**
  + 字串清單現在可以作為輸入傳遞給`read_*`方法。

+ **錯誤修復與改進**
  + 在 Spark`read_parquet`中運行時,其性能得到了顯著提高。
  + 修復了在單列`column_type_builder`具有不明確的日期格式的情況下失敗的問題。

### <a name="azure-portal"></a>Azure 入口網站
+ **預覽功能**
  + 日誌和輸出檔流現在可用於運行詳細資訊頁。 打開預覽切換時,檔將即時流式傳輸更新。
  + 在預覽版中釋放了在工作區級別設置配額的能力。 AmlCompute 配額在訂閱級別分配,但現在我們允許您在工作區之間分配該配額,並將其分配給公平共用和治理。 只需按一下工作區左側導航欄中的 **「使用方式+配額**」邊欄選項卡,然後選擇 **「配置配額」** 選項卡。請注意,您必須是訂閱管理員才能在工作區級別設置配額,因為這是跨工作區操作。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>適用於 Python v1.0.55 的 Azure 機器學習 SDK

+ **新功能**
  + 現在,對於對部署在 AKS 上的評分終結點進行的調用,現在支援基於權杖的身份驗證。 我們將繼續支援當前基於密鑰的身份驗證,使用者一次可以使用這些身份驗證機制之一。
  + 能夠將虛擬網路 (VNet) 後面的 Blob 儲存註冊為數據儲存。

+ **錯誤修復與改進**
  + **azureml-automl-core**
    + 修復了 CV 拆分的驗證大小較小,導致預測錯誤與回歸和預測的真實圖表的 Bug。
    + 遠端運行上預測任務的日誌記錄改進了,現在如果運行失敗,則向使用者提供全面的錯誤消息。
    + `Timeseries`修復了預進程標誌為 True 的故障。
    + 使一些預測數據驗證錯誤消息更具可操作性。
    + 通過丟棄和/或延遲載入資料集來減少 AutoML 執行的記憶體消耗,尤其是在進程生成之間
  + **azureml-contrib-解釋模型**
    + 向直譯器加入model_task標誌,以允許使用者覆蓋模型類型的預設自動推理邏輯
    + 小元件變更:自動安裝`contrib`,`nbextension`不再 安裝/啟用 - 支援僅具有全域功能重要性的解釋(例如,具有全域性)
    + 儀表板變更: - 框繪圖和小提琴繪`beeswarm`圖除了 在摘要頁面上繪圖 - 在"Top-k"滑`beeswarm`塊更改上重新成成繪圖的速度要快得多 - 解釋如何計算 Top-k 的有用消息 - 未提供資料時,有用的可自定義消息代替圖表
  + **azureml-core**
    + 添加了 Model.package() 方法來創建包含模型及其依賴項的 Docker 映射和 Dockerfile。
    + 更新了本地 Web 服務以接受包含環境物件的推理配置。
    + 固定模型.寄存器()在""時生成無效模型。(對於當前目錄)作為model_path參數傳遞。
    + 添加 Run.submit_child,功能將鏡像實驗.submit,同時將運行指定為已提交子運行的父級。
    + 支援在 Run.register_model 中註冊模型.註冊處的配置選項。
    + 能夠在現有群集上運行 JAR 作業。
    + 現在支援instance_pool_id和cluster_log_dbfs_path參數。
    + 在將模型部署到 Web 服務時添加了對使用環境物件的支援。 環境物件現在可以作為推理配置物件的一部分提供。
    + 為新區域加入映射 - 中心 - 西部 - 北中心
    + 為所有資料存儲類中的所有屬性添加了文檔。
    + blob_cache_timeout參數新增到`Datastore.register_azure_blob_container`。
    + 添加了 save_to_directory和load_from_directory 方法,以 azureml.core.環境.環境。
    + 將「az ml 環境下載」和「az ml 環境寄存器」命令添加到 CLI 中。
    + 添加了環境.add_private_pip_wheel方法。
  + **azureml 解釋模型**
    + 使用數據集服務(預覽)將數據集追蹤添加到說明。
    + 將全域解釋從 10k 流式傳輸到 100 時,將默認批處理大小減小。
    + 向解釋器添加了model_task標誌,以允許使用者覆蓋模型類型的預設自動推理邏輯。
  + **azureml-ml 串流**
    + 修復了 mlflow.azureml.build_image中忽略嵌套目錄的錯誤。
  + **azureml-管道步驟**
    + 添加了在現有 Azure 資料塊群集上運行 JAR 作業的能力。
    + 為 DatabricksStep 步驟添加了支援instance_pool_idcluster_log_dbfs_path參數。
    + 在 DatabricksStep 步驟中添加了對管道參數的支援。
  + **azureml-train-automl**
    + 已`docstrings`為"組合"相關文件添加。
    + 將文件更新為`max_cores_per_iteration``max_concurrent_iterations`
    + 遠端運行上預測任務的日誌記錄改進了,現在如果運行失敗,則向使用者提供全面的錯誤消息。
    + 從管道`automlstep`筆記本中刪除get_data。
    + 在`dataprep`中`automlstep`啟動支援。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure 機器學習資料準備 SDK v1.1.10

+ **新功能**
  + 現在,您可以請求在特定列上執行特定檢查器(例如直方圖、散點圖等)。
  + 新增並行化參數`append_columns`。 如果為 True,數據將載入到記憶體中,但執行將並行運行;如果為 True,則數據將載入到記憶體中,但執行將並行運行。如果為 False,則執行將是流式處理,但為單線程執行。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>適用於 Python v1.0.53 的 Azure 機器學習 SDK

+ **新功能**
  + 自動化機器學習現在支援遠端計算目標上訓練 ONNX 模型
  + Azure 機器學習現在提供了從以前的運行、檢查點或模型檔恢復訓練的功能。
    + 瞭解如何[使用估計器從上一次執行中恢復訓練](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **錯誤修復與改進**
  + **自動ml-用戶端-核心-本機用戶端**
    + 修復轉換後有關丟失列類型的 Bug(錯誤連結);
    + 允許y_query在開始時(#459519)為包含 None 的物件類型。
  + **azure-cli-ml**
    + CLI 命令"模型部署"和"服務更新"現在接受參數、設定檔或兩者的組合。 參數優先於檔中的屬性。
    + 模型描述現在可以在註冊後更新
  + **azureml-automl-core**
    + 將 NimbusML 依賴項更新為 1.2.0 版本(當前最新版本)。
    + 添加對 NimbusML 估計器的支援,&在自动ML估计器中使用的管道。
    + 修復 Ensemble 選擇過程中的錯誤,即使分數保持不變,該錯誤也會不必要地增加生成的集合。
    + 啟用在 CV 拆分中重用某些壯舉來預測任務。 這將加快設置的運行時間,大約一個n_cross_validations係數,用於昂貴的壯舉,如滯後和滾動視窗。
    + 如果時間沒有熊貓支持的時間範圍,則解決問題。 現在,如果時間小於 pd,則引發數據異常。時間戳.min 或大於 pd。時間戳.max
    + 如果列車和測試集中可以對齊,則預測現在允許它們的不同頻率。 例如,"從 1 月開始的季度"和"10 月開始的季度"可以對齊。
    + 屬性"參數"已添加到時間序列變形金剛中。
    + 刪除舊的異常類。
    + 在預測工作中,`target_lags`參數現在接受單個整數值或整數清單。 如果提供了整數,則只會創建一個滯後。 如果提供了清單,則將獲取滯後的唯一值。 target_lags[1,2,2,4]將創建一個、2 和 4 個週期的滯後。
    + 修復有關在轉換後丟失列類型的 Bug(錯誤連結);
    + 在`model.forecast(X, y_query)`中,允許y_query是一個對象類型,在開始 (#459519) 包含 Nones。
    + 將預期值加入`automl`輸出
  + **azureml-contrib-資料漂移**
    +  對範例筆記本的改進,包括切換到 azureml 開啟資料集,而不是 azureml-contrib 開放資料集,並在豐富資料時效能改進
  + **azureml-contrib-解釋模型**
    + 為 LIME 直譯器修復了在 azureml-contrib-解釋模型套件中的原始特徵重要性的轉換參數
    + 在 AzureML-contrib-解釋模型套件的影像直譯器中新增了對影像解釋的分段
    + 新增對 LimeExplainer 的稀疏支援
    + 新增到`batch_size`模擬直譯`include_local=False`器時 ,用於批量流式傳輸全域解釋,以改善決策樹可解釋模型的執行時間
  + **azureml-contrib 特徵工程**
    + 以呼叫 set_featurizer_timeseries_params()的修復:字形類型更改和空檢查`timeseries`- 新增筆記本用於 featurizer
    + 將 NimbusML 依賴項更新為 1.2.0 版本(當前最新版本)。
  + **azureml-core**
    + 新增 AzureML CLI 中附加 DBFS 資料儲存的能力
    + 修復了使用資料儲存上載的錯誤,如果`target_path`從該資料夾開始,則建立空資料夾`/`
    + 修復了`deepcopy`服務主體身份驗證中的問題。
    + 將「az ml 環境顯示」和「az ml 環境清單」命令添加到 CLI 中。
    + 環境現在支援指定base_dockerfile作為已構建base_image的替代方法。
    + 未使用的 Run 設定設定auto_prepare_environment已標記為已棄用。
    + 模型描述現在可以在註冊後更新
    + Bugfix:模型和圖像刪除現在提供有關檢索依賴於它們的上游對象的詳細資訊,如果由於上游依賴項而刪除失敗。
    + 修復了為為某些環境創建工作區時發生的部署列印空白持續時間的錯誤。
    + 改進的工作區會產生失敗異常。 因此,使用者看不到"無法創建工作區」。 找不到..."作為消息,而是看到實際創建失敗。
    + 添加對 AKS Web 服務中的權杖身份驗證的支援。
    + 將`get_token()`方法添加到`Webservice`物件。
    + 添加了用於管理機器學習數據集的 CLI 支援。
    + `Datastore.register_azure_blob_container`現在可以選擇使用一個`blob_cache_timeout`值(以秒為單位),該值配置 Blobfuse 的裝載參數,以便為此數據存儲啟用緩存過期。 默認值為無超時,即讀取 Blob 時,它將保留在本地緩存中,直到作業完成。 大多數作業都喜歡此設置,但某些作業需要從大型數據集中讀取的數據多於適合其節點的數據。 對於這些作業,調整此參數將幫助他們成功。 調整此參數時請小心:將值設置得過低可能會導致性能不佳,因為在紀元中使用的數據可能會過期,然後再再次使用。 這意味著所有讀取都將從 Blob 存儲(即網路)而不是本地緩存完成,從而對訓練時間產生負面影響。
    + 模型描述現在可以在註冊後正確更新
    + 模型和影像刪除現在提供有關依賴於它們的上游物件的詳細資訊,這些物件會導致刪除失敗
    + 使用 azureml.mlflow 提高遠端運行的資源利用率。
  + **azureml 解釋模型**
    + 為 LIME 直譯器修復了在 azureml-contrib-解釋模型套件中的原始特徵重要性的轉換參數
    + 新增對 LimeExplainer 的稀疏支援
    + 新增了元件線性直譯器包裝器,以及用於解釋線性模型的表格解釋器的另一個級別
    + 用於解釋模型庫中的模擬直譯器,include_local_稀疏資料輸入時發生錯誤
    + 將預期值加入`automl`輸出
    + 提供轉換參數以獲得原始要素重要性時,固定排列特徵重要性
    + 新增到`batch_size`模擬直譯`include_local=False`器時 ,用於批量流式傳輸全域解釋,以提高決策樹可解釋模型的執行時間
    + 用於模型解釋庫,固定黑匣說明器,其中熊貓數據幀輸入需要預測
    + 修復了有時`explanation.expected_values`返回浮點而不是帶有浮點的清單的 Bug。
  + **azureml-ml 串流**
    + 提高 mlflow 的性能。set_experiment(experiment_name)
    + 修復使用互動式登入認證進行 mlflow tracking_uri
    + 使用 azureml.mlflow 提高遠端運行的資源利用率。
    + 改進 azureml-ml 串套件的文件
    + 在 mlflow.log_artifacts("my_dir")下將專案保存在"my_dir/<工件路径>"而不是"<工件路径>"下,修補 bug
  + **azureml 開啟資料集**
    + 固定`pyarrow``opendatasets`到舊版本 (<0.14.0),因為記憶體問題新引入那裡。
    + 將 azureml-contrib 開放資料集移動到 azureml 打開數據集。
    + 允許將打開的數據集類註冊到 Azure 機器學習工作區,並無縫利用 AML 數據集功能。
    + 改進 NoaaIsdWeather 可顯著豐富非 SPARK 版本的性能。
  + **azureml-管道步驟**
    + 現在支援 DBFS 資料儲存用於數據磚塊步驟中的輸入和輸出。
    + 有關輸入/輸出的 Azure 批次處理步驟的更新文檔。
    + 在 AzureBatchStep 中,*將delete_batch_job_after_finish*預設值變更為*true*。
  + **azureml-telemetry**
    +  將 azureml-contrib 開放資料集移動到 azureml 打開數據集。
    + 允許將打開的數據集類註冊到 Azure 機器學習工作區,並無縫利用 AML 數據集功能。
    + 改進 NoaaIsdWeather 可顯著豐富非 SPARK 版本的性能。
  + **azureml-train-automl**
    + 更新了有關get_output的文檔以反映實際返回類型,並提供有關檢索密鑰屬性的其他註釋。
    + 將 NimbusML 依賴項更新為 1.2.0 版本(當前最新版本)。
    + 將預期值加入`automl`輸出
  + **azureml-train-core**
    + 字串現在被接受為自動超參數調優的計算目標
    + 未使用的 Run 設定設定auto_prepare_environment已標記為已棄用。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure 機器學習資料準備 SDK v1.1.9

+ **新功能**
  + 添加了對直接從 http 或 Hs url 讀取檔案的支援。

+ **錯誤修復與改進**
  + 嘗試從遠端源讀取鑲嵌數據集時改進了錯誤消息(當前不支援)。
  + 在 ADLS 第 2 代中寫入 Parquet 檔案格式並更新路徑中的 ADLS 第 2 代容器名稱時修復了錯誤。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>視覺介面
+ **預覽功能**
  + 在可視介面中添加了"執行 R 文本" 模組。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>適用於 Python v1.0.48 的 Azure 機器學習 SDK

+ **新功能**
  + **azureml 開啟資料集**
    + **azureml-contrib 開放資料集**現在可作為**azureml 開放資料集提供**。 舊包仍然有效,但我們建議您使用**Azureml 開放數據集**來改進更豐富的功能和改進。
    + 此新包允許您在 Azure 機器學習工作區中註冊打開的數據集作為數據集,並利用數據集提供的任何功能。
    + 它還包括現有功能,例如使用開放數據集(如熊貓/SPARK 數據幀)以及某些數據集(如天氣)的位置聯接。

+ **預覽功能**
    + HyperDriveConfig 現在可以接受管道物件作為參數,以支援使用管道進行超參數調優。

+ **錯誤修復與改進**
  + **azureml-train-automl**
    + 修復了有關在轉換后丟失列類型的 Bug。
    + 修復了 bug,以允許y_query在開頭包含 None 的物件類型。
    + 修復了 Ensemble 選擇過程中的問題,即使分數保持不變,該選擇程式也會不必要地增加生成的集合。
    + 修復了自動MLStep中whitelist_models和blacklist_models設置的問題。
    + 修復了在 Azure ML 管道上下文中使用 AutoML 時阻止使用預處理的問題。
  + **azureml 開啟資料集**
    + 將 azureml-contrib 開放資料集移動到 azureml 打開數據集。
    + 允許將打開的數據集類註冊到 Azure 機器學習工作區,並無縫利用 AML 數據集功能。
    + 改進的 NoaaIsdWeather 顯著豐富了非 SPARK 版本的性能。
  + **azureml 解釋模型**
    + 更新了可解釋性對象的在線文檔。
    + 添加到`batch_size`類比解釋`include_local=False`器時 ,用於批量流式傳輸全域解釋,以提高決策樹可解釋模型的可解釋性庫的執行時間。
    + 修復了有時`explanation.expected_values`返回浮點而不是帶有浮點的清單的問題。
    + 在解釋模型庫中`automl`為模擬解釋器添加預期值以輸出。
    + 當提供轉換參數以獲得原始要素重要性時,修復了排列要素重要性。
  + **azureml-core**
    + 添加了在 AzureML CLI 中附加 DBFS 資料存儲的能力。
    + 修復了在 其中創建空資料夾(`target_path`如果從 )`/`創建空資料夾 的數據存儲上載問題。
    + 啟用了兩個數據集的比較。
    + 模型和圖像刪除現在提供有關檢索依賴於它們的上游物件的詳細資訊,如果由於上游依賴項而刪除失敗。
    + 在auto_prepare_environment中棄用未使用的 Run 配置設置。
  + **azureml-ml 串流**
    + 改進了使用 azureml.mlflow 的遠端運行的資源利用率。
    + 改進了 azureml-mlflow 包的文檔。
    + 修復了 mlflow.log_artifacts("my_dir")將工件保存在"my_dir/工件路徑"下而不是"工件路徑"的問題。
  + **azureml-pipeline-core**
    + 所有管道步驟的參數hash_paths都將棄用,將來將被刪除。 預設情況下,source_directory的內容是哈希的(.amlignore 或 .gitignore 中列出的檔除外)
    + 繼續改進模組和模組步驟以支援計算類型特定的模組,以便為 Run 配置整合和其他更改做好準備,以解鎖管道中特定於計算類型的模組使用方式。
  + **azureml-管道步驟**
    + AzureBatch 步驟:改進了與輸入/輸出有關的文檔。
    + AzureBatch 步驟:將預設值delete_batch_job_after_finish更改為 true。
  + **azureml-train-core**
    + 字串現在被接受為自動超參數調優的計算目標。
    + 在auto_prepare_environment中棄用未使用的 Run 配置設置。
    + `conda_dependencies_file_path`棄用參數`pip_requirements_file_path`,分別贊`conda_dependencies_file`成`pip_requirements_file`和。
  + **azureml 開啟資料集**
    + 改進 NoaaIsdWeather 可顯著豐富非 SPARK 版本的性能。

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure 機器學習資料準備 SDK v1.1.8

+ **新功能**
 + 數據流物件現在可以反覆運算,生成一系列記錄。 請參考的文件`Dataflow.to_record_iterator`。
  + 數據流物件現在可以反覆運算,生成一系列記錄。 請參考的文件`Dataflow.to_record_iterator`。

+ **錯誤修復與改進**
 + 提高了 DataPrep SDK 的魯棒性。
 + 使用非字串列索引改進熊貓數據幀的處理。
 + 改進了數據集中`to_pandas_dataframe`的性能。
 + 修復了在多節點環境中運行時,Spark 執行數據集失敗的錯誤。
  + 提高了 DataPrep SDK 的魯棒性。
  + 使用非字串列索引改進熊貓數據幀的處理。
  + 改進了數據集中`to_pandas_dataframe`的性能。
  + 修復了在多節點環境中運行時,Spark 執行數據集失敗的錯誤。

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure 機器學習資料準備 SDK v1.1.7

我們還原了改進性能的更改,因為它導致某些客戶使用 Azure 數據塊出現問題。 如果在 Azure 資料塊上遇到問題,可以使用以下方法之一升級到版本 1.1.7:
1. 執行此文稿以升級:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 重新建立群集,這將安裝最新的數據準備 SDK 版本。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>適用於 Python v1.0.45 的 Azure 機器學習 SDK

+ **新功能**
  + 新增決策樹代理模型以在 azureml 解釋模型套件中模擬直譯器
  + 能夠指定要安裝在推斷映射上的 CUDA 版本。 支援 CUDA 9.0、9.1 和 10.0。
  + 有關 Azure ML 培訓基礎映像的資訊現在可在[Azure ML 容器 GitHub 儲存庫](https://github.com/Azure/AzureML-Containers)和[DockerHub 中提供](https://hub.docker.com/_/microsoft-azureml)
  + 添加了對管道計劃的 CLI 支援。 執行「az ml 管道 -h」以瞭解更多資訊
  + 將自訂 Kubernetes 命名空間參數添加到 AKS Web 服務部署配置和 CLI 中。
  + 所有導管步驟的已棄用hash_paths參數
  + Model.寄存器現在支援使用`child_paths`參數將多個單個檔註冊為單個模型。

+ **預覽功能**
    + 評分解釋者現在可以選擇保存 conda 和 pip 資訊,以便更可靠的序列化和反序列化。
    + 自動功能選擇器的 Bug 修復。
    + 更新了 mlflow.azureml.build_image到新實現公開的修補 Bug 的新 api。

+ **錯誤修復與改進**
  + 從`paramiko`azureml 核心中刪除依賴項。 為舊計算目標附加方法添加了棄用警告。
  + 提高執行性能.create_children
  + 在使用二進位分類器的模擬直譯器中,當教師概率用於縮放形狀值時,修復概率的順序。
  + 改進了自動機器學習的錯誤處理和消息。
  + 修復了自動化機器學習的反覆運算超時問題。
  + 改進了自動化機器學習的時序轉換性能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure 機器學習資料準備 SDK v1.1.6

+ **新功能**
  + 為最高值`SummaryFunction.TOPVALUES`( )`SummaryFunction.BOTTOMVALUES`與底部值 ( ) 新增了摘要函數 。

+ **錯誤修復與改進**
  + 顯著提高了的效能`read_pandas_dataframe`。
  + 修復了導致`get_profile()`指向二進位檔的數據流上的錯誤。
  + 公開`set_diagnostics_collection()`以允許對遙測集合進行程式設計啟用/禁用。
  + 變更了`get_profile()`的行為 。 NaN 值現在被忽略為最小值、平均值、Std 值和總和值,這與熊貓的行為一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>適用於 Python v1.0.43 的 Azure 機器學習 SDK

+ **新功能**
  + Azure 機器學習現在為流行的機器學習和數據分析框架 Scikit-learn 提供一流的支援。 使用[`SKLearn`估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py),用戶可以輕鬆地訓練和部署 Scikit 學習模型。
    + 瞭解如何使用[HyperDrive 使用 Scikit 學習執行超參數調優](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 添加了在管道中創建模組步驟以及模組和模組版本類以管理可重用計算單元的支援。
  + ACI Web 服務現在透過更新支援持久scoring_uri。 scoring_uri將從 IP 更改為 FQDN。 可以通過在deploy_configuration上設置dns_name_label來配置 FQDN 的 Dns 名稱標籤。
  + 自動機器學習新功能:
    + 用於預測的 STL 壯舉器
    + KMeans 叢集已啟用功能掃描
  + Aml計算配額審批速度更快! 現在,我們已經自動執行流程,以在閾值內批准您的配額請求。 有關配額的工作原理的詳細資訊,[瞭解如何管理配額](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)。

+ **預覽功能**
    + 通過 azureml-mlflow 包([範例筆記本](https://aka.ms/azureml-mlflow-examples))與[MLflow](https://mlflow.org) 1.0.0 跟蹤整合。
    + 將 Jupyter 筆記本作為運行方式提交。 [API 參考文件](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 通過 azureml-contrib-資料漂移包([範例筆記本](https://aka.ms/azureml-datadrift-example))對[資料漂移檢測器](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class))進行公共預覽。 數據漂移是模型精度隨時間而降低的主要原因之一。 當用於生產中建模的數據與訓練模型的數據不同時,就會發生這種情況。 AML 資料漂移檢測器幫助客戶監控數據漂移,並在檢測到漂移時發送警報。

+ **重大變更**

+ **錯誤修復與改進**
  + Run配置載入和保存支援為以前的行為指定具有完全背對功能的完整文件路徑。
  + 在服務主體身份驗證中添加緩存,預設情況下已關閉。
  + 啟用以同一指標名稱記錄多個繪圖。
  + 模型類現在可以從 azureml.core`from azureml.core import Model`() 正確導入。
  + 在管道步驟中`hash_path`,現在棄用參數。 新行為是哈希完成source_directory,但 .amlignore 或 .gitignore 中列出的檔除外。
  + 在管道包中,`get_all``get_all_*`各種 方法被棄用,`list`分別偏向`list_*`和。
  + azureml.core.get_run 不再需要在返回原始運行類型之前導入類。
  + 修復了某些對 Web 服務更新的調用未觸發更新的問題。
  + AKS Web 服務上的評分超時應在 5ms 和 300000ms 之間。 馬克斯允許scoring_timeout_ms的計分請求被撞了1分鐘到5分鐘。
  + 本地 Web`scoring_uri`服務`swagger_uri`物件 現在具有和 屬性。
  + 移動的輸出目錄創建和輸出目錄上載出使用者進程。 啟用執行歷史記錄 SDK 以在每個使用者程序中運行。 這應解決分散式訓練運行遇到的一些同步問題。
  + 從使用者進程名稱編寫的 azureml 日誌的名稱現在將包括進程名稱(僅適用於分散式培訓)和 PID。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure 機器學習資料準備 SDK v1.1.5

+ **錯誤修復與改進**
  + 對於具有 2 位年份格式的解釋日期時間值,已更新有效年份的範圍以匹配 Windows 可能發布。 範圍從 1930-2029 到 1950-2049。
  + 在檔案和設定`handleQuotedLineBreaks=True`中讀取時`\r`, 將被視為新行。
  + 修復了在某些情況下導致`read_pandas_dataframe`故障的錯誤。
  + 改進的效能`get_profile`。
  + 改進的錯誤消息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure 機器學習資料準備 SDK v1.1.4

+ **新功能**
  + 現在,可以使用以下表達式語言函數提取日期時間值並將其解析為新列。
    + `RegEx.extract_record()`將日期時間元素提取到新列中。
    + `create_datetime()`從單獨的日期時間元素創建日期時間物件。
  + 呼叫`get_profile()`時,您現在可以看到分位數列標記為 (est.),以明確指示值是近似值。
  + 現在,從 Azure Blob 儲存讀取時可以使用 _ globing。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **錯誤修復**
  + 修復了與從遠端源 (Azure Blob) 讀取 Parquet 文件相關的 Bug。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>適用於 Python v1.0.39 的 Azure 機器學習 SDK
+ **變更**
  + 正在棄用運行設定auto_prepare_environment選項,自動準備變為預設值。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure 機器學習資料準備 SDK v1.1.3

+ **新功能**
  + 通過調用read_postgresql或使用數據存儲,添加從 PostgresSQL 資料庫讀取的支援。
    + 請參考指南中的範例:
      + [數據攝取筆記本](https://aka.ms/aml-data-prep-ingestion-nb)
      + [資料儲存筆記本](https://aka.ms/aml-data-prep-datastore-nb)

+ **錯誤修復與改進**
  + 修復欄型態轉換的問題:
  + 現在,將布爾或數位列正確轉換為布爾列。
  + 現在,嘗試將日期列設置為日期類型時不會失敗。
  + 改進了聯接類型和隨附的參考文檔。 加入兩個資料流時,現在可以指定以下類型的聯接之一:
    + 無,匹配,內,不匹配左,左安蒂,左外,無配,右蒂蒂,右外,全安蒂,全。
  + 改進的數據類型推斷以識別更多日期格式。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 門戶中,現在可以:
+ 建立與執行自動 ML 實驗
+ 創建筆記本 VM 以嘗試 Jupyter 筆記本或您自己的示例。
+ Azure 機器學習工作區中的全新創作部分(預覽),包括自動機器學習、可視化介面和託管筆記本 VM
    + 使用自動機器學習自動建立模型
    + 使用拖放視覺介面執行實驗
    + 創建筆記本 VM 以瀏覽數據、創建模型和部署服務。
+ 執行報表中的即時圖表和指標更新與執行詳細資訊頁
+ 更新了運行詳細資訊頁中日誌、輸出和快照的檔檢視器。
+ 實驗"選項卡中新增和改進的報告創建體驗。
+ 增加了從 Azure 機器學習工作區的「概述」頁面下載 config.json 檔的能力。
+ 支援從 Azure 資料塊工作區創建 Azure 機器學習工作區。

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>適用於 Python v1.0.33 的 Azure 機器學習 SDK
+ **新功能**
  + _工作區.create_方法現在接受 CPU 和 GPU 群集的預設叢集配置。
  + 如果工作區創建失敗,則清除依賴資源。
  + 預設 Azure 容器註冊表 SKU 已切換到基本。
  + Azure 容器註冊表在運行或映射創建需要時以懶惰方式創建。
  + 支援"環境"以進行培訓運行。

### <a name="notebook-virtual-machine"></a>筆記型虛擬機器 

將筆記本 VM 用作 Jupyter 筆記型電腦的安全、企業就緒的託管環境,您可以在其中對機器學習實驗進行程式設計,將模型部署為 Web 終結點,並使用 Python 執行 Azure 機器學習 SDK 支援的所有其他操作。它提供多種功能:
+ [快速啟動預先配置的筆記型 VM,](tutorial-1st-experiment-sdk-setup.md) 該 VM 具有最新版本的 Azure 機器學習 SDK 和相關包。
+ 通過經過驗證的技術(如 HS、Azure 活動目錄身份驗證和授權)保護訪問。
+ Azure 機器學習工作區 Blob 儲存帳戶中筆記本和代碼的可靠雲端儲存。您可以安全地刪除筆記本 VM,而不會丟失工作。
+ 預裝示例筆記本,用於探索和試驗 Azure 機器學習功能。
+ Azure VM、任何 VM 類型、任何包、任何驅動程式的完整自定義功能。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>針對 Python v1.0.33 發佈的 Azure 機器學習 SDK。

+ [FPGA](how-to-deploy-fpga-web-service.md)上的 Azure ML 硬體加速模型通常可用。
  + 現在[,您可以使用 azureml-accel 模型套件](how-to-deploy-fpga-web-service.md)來:
    + 訓練支援的深層神經網路的權重(ResNet 50、ResNet 152、密集網-121、VGG-16 和 SSD-VGG)
    + 將轉移學習與支援的 DNN 一起使用
    + 將模型註冊到模型管理服務並容器化模型
    + 將模型部署到 Azure 庫伯內特服務 (AKS) 群集中具有 FPGA 的 Azure VM
  + 將容器部署到[Azure 資料框邊緣](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)伺服器裝置
  + [使用 此範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)對資料進行評分

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ 功能掃描,可動態添加:::no-loc text="featurizers":::性能優化。 新增:::no-loc text="featurizers":::: 工作嵌入、 證據重量、 目標編碼、 文字目標編碼、 叢集距離
+ 智慧 CV 處理自動 ML 內部的火車/有效分割
+ 很少變更記憶體和提高執行時效能
+ 模型解釋的效能改進
+ 本地端執行的 ONNX 模型轉換
+ 新增子採樣支援
+ 未定義離開條件時智慧停止
+ 堆疊合奏

+ 時間序列預測
  + 新的預測函數
  + 現在,您可以對時間序列資料使用捲動源交叉驗證
  + 為設定時序延遲與新功能
  + 支援捲動視窗聚合功能而新增的新功能
  + 在實驗設置中定義國家/地區代碼時,新的假日檢測和 featurizer

+ Azure Databricks
  + 啟用時序預測和模型解釋能力/可解釋性能力
  + 您現在可以取消與復原(繼續)自動 ML 實驗
  + 增加了對多核處理的支援

### <a name="mlops"></a>MLOps
+ **對評分容器的本地部署&除錯**<br/> 現在,您可以在本地部署 ML 模型,並在評分檔和依賴項上快速反覆運算,以確保它們按預期方式工作。

+ **引入推理設定&模型.部署()**<br/> 模型部署現在支援使用條目腳本指定源資料夾,與 RunConfig 相同。  此外,模型部署已簡化為單個命令。

+ **Git 參考追蹤**<br/> 客戶一直在請求基本的 Git 集成功能一段時間,因為它有助於維護端到端審核跟蹤。 我們已跨 Azure ML 中的主要實體實現了與 Git 相關的元數據(存儲庫、提交、乾淨狀態)的跟蹤。 此資訊將由 SDK 和 CLI 自動收集。

+ **模型分析&驗證服務**<br/> 客戶經常抱怨難以正確調整與其推理服務關聯的計算的大小。 通過我們的模型分析服務,客戶可以提供示例輸入,我們將對 16 種不同的 CPU/記憶體配置進行分析,以確定部署的最佳大小調整。

+ **自帶基本影像進行推理**<br/> 另一個常見的抱怨是難以從實驗轉移到推斷RE共享依賴項。 借助我們新的基本映射共用功能,您現在可以重用實驗基礎映射、依賴項和所有內容進行推理。 這將加快部署速度並縮小從內部迴圈到外部迴圈的差距。

+ **改進的斯瓦格架構產生體驗**<br/> 我們以前的誇張生成方法容易出錯,無法自動執行。 我們有一種新的內聯方式,通過修飾器從任何 Python 函數生成大搖大腳的架構。 我們有開源此代碼,我們的架構生成協定未耦合到 Azure ML 平臺。

+ **Azure ML CLI 通常可用 (GA)**<br/> 模型現在可以使用單個 CLI 命令進行部署。 我們得到常見的客戶反饋,沒有人從 Jupyter 筆記型電腦部署 ML 型號。 [**CLI 參考文檔**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

Azure 機器學習 SDK,適用於 Python v1.0.30 發佈。

引入[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)是為了添加已發佈管道的新版本,同時保持相同的終結點。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure 機器學習資料準備 SDK v1.1.2

注意:數據準備 Python SDK`numpy``pandas`將不再 安裝和打包。 請參考[更新的安裝說明](https://github.com/Microsoft/AMLDataPrepDocs)。

+ **新功能**
  + 您現在可以使用數據透視轉換。
    + 指南:[樞軸筆記本](https://aka.ms/aml-data-prep-pivot-nb)
  + 您現在可以在本機函數中使用正則運算式。
    + 範例：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 您`to_upper` 可以選擇式語言`to_lower` 中使用與函數。
  + 現在,您可以在資料配置檔中查看每列的唯一值數。
  + 對於一些常用的讀取器步驟,現在可以傳遞`infer_column_types`參數。 如果設置為`True`,數據準備將嘗試檢測並自動轉換列類型。
    + `inference_arguments`現在被棄用了。
  + 您現在可以呼叫`Dataflow.shape`。

+ **錯誤修復與改進**
  + `keep_columns` 現在接受一個附加的`validate_column_exists`可選參數 ,`keep_columns`它檢查 的結果是否包含任何列。
  + 所有讀取器步驟 (從檔案讀取)現在接受其他可選`verify_exists`參數 。
  + 提高從熊貓數據幀讀取和獲取數據配置檔的性能。
  + 修復了從數據流中分割單個步驟的 Bug 失敗,單個索引。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 入口網站
  + 您現在可以在現有遠端計算群集上重新提交現有的腳本運行。
  + 現在,您可以在「管道」選項卡上使用新參數運行已發布的管道。
  + 現在,運行詳細資訊支援新的快照檔查看器。 在提交特定運行時,您可以查看目錄的快照。 您還可以下載已提交的筆記本以開始運行。
  + 您現在可以取消 Azure 門戶的父級運行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>適用於 Python v1.0.23 的 Azure 機器學習 SDK

+ **新功能**
  + Azure 機器學習 SDK 現在支援 Python 3.7。
  + Azure 機器學習 DNN 估計器現在提供內置的多版本支援。 例如,`TensorFlow` 估計器現在接受`framework_version`參數 ,用戶可以指定版本"1.10"或"1.12"。 有關當前 SDK 版本支援的版本的清單,請`get_supported_versions()`調用 所需的框架類`TensorFlow.get_supported_versions()`(例如,
  有關最新 SDK 版本支援的版本的清單,請參閱[DNN 估計器文件](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure 機器學習資料準備 SDK v1.1.1

+ **新功能**
  + 您可以使用read_*轉換讀取多個資料存儲/資料路徑/資料參考源。
  + 您可以對列執行以下操作以創建新列:除法、樓層、蒙杜洛、功率、長度。
  + 數據準備現在是 Azure ML 診斷套件的一部分,默認情況下將記錄診斷資訊。
    + 要關閉這個選項,請將此環境變數設定為 true:DISABLE_DPREP_LOGGER

+ **錯誤修復與改進**
  + 改進了常用類和函數的代碼文檔。
  + 修復了auto_read_file無法讀取 Excel 檔的錯誤。
  + 添加了在read_pandas_dataframe中覆蓋資料夾的選項。
  + 提高了 dotnetcore2 依賴項安裝的性能,並增加了對 Fedora 27/28 和 Ubuntu 1804 的支援。
  + 提高了從 Azure Blob 讀取的性能。
  + 列類型檢測現在支援「長」類型的欄。
  + 修復了某些日期值顯示為時間戳而不是 Python 日期時間物件的錯誤。
  + 修復了某些類型計數顯示為雙精度值而不是整數的錯誤。


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>適用於 Python v1.0.21 的 Azure 機器學習 SDK

+ **新功能**
  + *azureml.core.Run.create_children*方法允許通過單個調用創建多個子運行的低延遲。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure 機器學習資料準備 SDK v1.1.0

+ **重大變更**
  + 數據準備包的概念已被棄用,不再受支援。 您可以單獨保留數據流,而不是在一個包中持久化多個數據流。
    + 如何指導:[開啟與儲存資料串流](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 數據準備現在可以識別與特定語義類型匹配的列,並相應地拆分。 當前支援的 SType 包括:電子郵件位址、地理座標(緯度&經度)、IPv4 和 IPv6 位址、美國電話號碼和美國郵政編碼。
    + 指南:[語意類型筆記本](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 數據準備現在支援以下操作,以便從兩個數位列生成生成的列:減去、乘法、除法和移動列。
  + 您可以調用`verify_has_data()`資料流來檢查如果執行數據流,數據流是否會生成記錄。

+ **錯誤修復與改進**
  + 現在,您可以指定用於數位列配置檔的直方圖中的條柱數。
  + 轉換`read_pandas_dataframe`現在要求 DataFrame 具有字串或位元組類型列名稱。
  + 修復了轉換中的`fill_nulls`Bug,如果缺少列,則無法正確填充值。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>適用於 Python v1.0.18 的 Azure 機器學習 SDK

 + **變更**
   + azureml-tensorboard 封裝替換 azureml-contrib-tensor板。
   + 使用此版本,您可以在託管計算群集(amlcompute)上設置使用者帳戶,同時創建該帳戶。 這可以通過在預配配置中傳遞這些屬性來實現。 您可以在[SDK 參考文件中](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)找到更多詳細資訊。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure 機器學習資料準備 SDK v1.0.17

+ **新功能**
  + 現在支援添加兩個數位列,以便使用表達式語言生成生成的列。

+ **錯誤修復與改進**
  + 改進了文檔和參數檢查random_split。

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure 機器學習資料準備 SDK v1.0.16

+ **錯誤修復**
  + 修復了由 API 更改引起的服務主體身份驗證問題。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>適用於 Python v1.0.17 的 Azure 機器學習 SDK

+ **新功能**

  + Azure 機器學習現在為流行的 DNN 框架鏈程式提供一流支援。 使用[`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)類用戶可以輕鬆地訓練和部署鏈器模型。
    + 瞭解如何[使用鏈式MN運行分散式培訓](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 瞭解如何使用[HyperDrive 使用鍊條執行超參數調優](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure 機器學習管道增加了根據數據存儲修改觸發管道運行的能力。 管道[計劃筆記本](https://aka.ms/pl-schedule)將更新以顯示此功能。

+ **錯誤修復與改進**
  + 我們在 Azure 機器學習管道中添加了支援,用於將source_directory_data_store屬性設置為提供給[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)的[Run配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)上的所需資料儲存(如 blob 儲存)。 默認情況下,步驟使用 Azure 檔存儲作為備份數據存儲,當同時執行大量步驟時,可能會遇到限制問題。

### <a name="azure-portal"></a>Azure 入口網站

+ **新功能**
  + 報表的新拖放表編輯器體驗。 用戶可以將列從井拖動到顯示表預覽的表區域。 可以重新排列列。
  + 新增紀錄檔案檢視器
  + 從作用選項卡到實驗執行、計算、模型、影像和部署的連結

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure 機器學習資料準備 SDK v1.0.15

+ **新功能**
  + 數據準備現在支援從資料流寫入檔流。 還提供了操作檔流名稱以創建新檔名的能力。
    + 指南:[使用檔案串流](https://aka.ms/aml-data-prep-file-stream-nb)

+ **錯誤修復與改進**
  + 提高了 t-Digest 在大型數據集上的性能。
  + 數據準備現在支援從數據路徑讀取數據。
  + 一個熱編碼現在適用於布林和數位列。
  + 其他雜項錯誤修復。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>適用於 Python 的 Azure Machine Learning SDK v1.0.15

+ **新功能**
  + Azure 機器學習管道添加了 AzureBatchStep ([筆記本](https://aka.ms/pl-azbatch))、超驅動器步驟(筆記本)和基於時間的調度功能 ([筆記本](https://aka.ms/pl-schedule))。
  +  DataTranferStep 已更新成可與 Azure SQL Server 和「適用於 PostgreSQL 的 Azure 資料庫」([otebook](https://aka.ms/pl-data-trans)) 搭配運作。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 「資料準備」現在支援使用「資料存放區」從 Azure SQL 資料庫讀取資料。

+ **變更**
  + 提高了對大數據的某些操作的記憶體性能。
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**:
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
      + 如果執行「az 登入」,然後使用 azureml-sdk,SDK 將再執行一次瀏覽器或設備代碼日誌。 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您正在使用服務主體執行「az login」以進行自動化，建議您使用 _azureml.core.authentication.ServicePrincipalAuthentication_ 類別，因為 azureml-sdk 不會使用 azure CLI 建立的認證狀態。

+ **錯誤修復**:此版本主要包含輕微的錯誤修復

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **錯誤修復**
  + 提高了獲取數據配置檔的性能。
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

+ **錯誤修復**:此版本主要包含輕微的錯誤修復

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **新功能**
  + 資料存放區改善 (記載於[資料存放區操作指南](https://aka.ms/aml-data-prep-datastore-nb))
    + 已新增以相應增加方式從中讀取和寫入至 Azure 檔案共用和 ADLS 資料存放區的功能。
    + 使用資料存放區時，Data Prep 現在支援使用服務主體驗證，而非互動式驗證。
    + 已新增 wasb 和 wasbs url 的支援。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning 資料準備 SDK v1.0.6

+ **錯誤修復**
  + 已修正與從 Spark 上公開可讀取的 Azure Blob 容器讀取相關的 Bug

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **錯誤修復**:此版本主要包含輕微的錯誤修復

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **新功能**
  + `to_bool` 函式現在允許將不相符的值轉換成 Error 值。 這是 `to_bool` 和 `set_column_types` 新的預設不相符行為，先前的預設行為會將不相符的值轉換為 False。
  + 在呼叫 `to_pandas_dataframe` 時，有新的選項可將數字資料行中的 null/遺失值解譯為 NaN。
  + 新增了功能，會檢查某些運算式的傳回類型，以確保類型一致性，並及早失敗。
  + 您現在可以呼叫 `parse_json`，以將資料行中的值剖析為 JSON 物件，並將其展開成多個資料行。

+ **錯誤修復**
  + 已修正錯誤，該錯誤會讓 Python 3.5.2 中的 `set_column_types` 損毀。
  + 已修正會在使用 AML 映像連線至資料存放區時當機的錯誤。

+ **更新**
  * 使用者入門教學課程、案例研究和操作指南的[範例 Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04: 一般供應

Azure 機器學習現在通常可用。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
在此版本中，我們透過 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 來宣布新的受控計算體驗。 這個計算目標會取代適用於 Azure Machine Learning 的 Azure Batch AI 計算。

這個計算目標：
+ 用於模型訓練和批次推理/評分
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


+ **更新**
  + 針對機器學習管線：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) \(英文\) 現在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) \(英文\) 現在可從 SQL 資料來源複製或複製到其中
    + 排程 SDK 中的功能，以建立和更新執行已發佈管線的排程

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning 資料準備 SDK v0.5.2
+ **重大變更**
  * `SummaryFunction.N` 已重新命名為 `SummaryFunction.Count`。

+ **錯誤修復**
  * 在遠端執行中讀取和寫入資料儲存時,請使用最新的 AML 執行權杖。 以前，如果在 Python 中更新 AML 執行權杖，將不會使用已更新的 AML 執行權杖來更新資料準備執行階段。
  * 其他更清楚的錯誤訊息
  * 當 Spark 使用 `Kryo` 序列化時，to_spark_dataframe() 將不再損毀
  * 值計數偵測器現在可以顯示 1000 個以上的唯一值
  * 如果原始資料串流沒有名稱,則隨機分割不再失敗

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
  * azureml.core.compute.AmlCompute** 取代了下列類別 - azureml.core.compute.BatchAICompute** 和 azureml.core.compute.DSVMCompute**。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

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

+ **錯誤修復**
   * 修正長時間的雙重轉換
   * 修正新增任何資料行之後的判斷提示
   * 修正 FuzzyGrouping 在某些情況下無法偵測到群組的問題
   * 修正排序函式以遵循多欄排列順序
   * 將 and/or 運算式修正為類似於 `pandas` 處理它們的方式
   * 修正從 dbfs 路徑讀取的方式
   * 讓錯誤訊息變得更容易了解
   * 現在,使用 AML 權取遠端計算目標時不再失敗
   * Linux DSVM 現在不會再有失敗狀況
   * 現在字串述詞中有非字串值時不會再發生損毀
   * 現在能在資料流程發生正常的失敗時，處理判斷提示錯誤
   * 現在支援 Azure Databricks 上掛接 dbutils 的儲存位置

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure 入口網站
Azure 機器學習的 Azure 門戶具有以下更新:
  * 用於已發佈管線的新 [管線]**** 索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更**
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets()** 取代為 Workspace.compute_targets**。
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

+ **錯誤已修復**
  * 64 位元不帶正負號的整數溢位現在會在 Linux 上正確處理
  * 已在 smart_read 中修正純文字檔案不正確的文字標籤
  * 字串資料行類型現在會出現在 [計量] 檢視中
  * 類型計數現在已修正，可顯示對應至單一 FieldType，而不是個別項目的 ValueKinds
  * 當路徑以字串方式提供時，Write_to_csv 不會再失敗
  * 使用「替換」時,將「尋找」留空將不再失敗

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

+ **自動化機器學習**
  * 改良錯誤處理和文件
  * 已修正 run 屬性擷取的效能問題。
  * 已修正繼續執行的問題。
  * 修復了:::no-loc text="ensembling":::反覆運算問題。
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

Azure 機器學習的一個新的刷新版本:閱讀有關此版本的更多內容:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning](overview-what-is-azure-ml.md) 概觀。
