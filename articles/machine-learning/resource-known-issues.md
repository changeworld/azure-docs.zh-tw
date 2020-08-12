---
title: 已知問題和疑難排解
titleSuffix: Azure Machine Learning
description: 取得在 Azure Machine Learning 中尋找和更正錯誤或失敗的協助。 瞭解已知問題、疑難排解和因應措施。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/06/2020
ms.openlocfilehash: 17d6137dd243c3bce011a1841ea9bca64e0b64ba
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120757"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Azure Machine Learning 中的已知問題和疑難排解

本文可協助您針對使用 Azure Machine Learning 時可能會遇到的已知問題進行疑難排解。 

如需有關疑難排解的詳細資訊，請參閱本文結尾處的[後續步驟](#next-steps)。

> [!TIP]
> 錯誤或其他問題可能是您使用 Azure Machine Learning 時所遇到的[資源配額](how-to-manage-quotas.md)所導致。 

## <a name="access-diagnostic-logs"></a>存取診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 若要查看一些記錄： 
1. 流覽[Azure Machine Learning studio](https://ml.azure.com)。 
1. 在左側選取 [**實驗**] 
1. 選取實驗。
1. 選取 [執行]。
1. 在頂端，選取 [**輸出 + 記錄**]。

> [!NOTE]
> Azure Machine Learning 會在定型期間記錄各種來源的資訊，例如 AutoML 或執行定型作業的 Docker 容器。 其中有許多記錄檔並未記載。 如果您遇到問題並聯繫 Microsoft 支援服務，他們在進行疑難排解時可能會使用這些記錄。


## <a name="installation-and-import"></a>安裝和匯入
                           
* **Pip 安裝：不保證相依性與單一程式列安裝一致：** 

   這是已知的 pip 限制，因為當您將安裝為單一行時，它沒有正常運作的相依性解析程式。 第一個唯一的相依性是它所查看的唯一相依性。 

   在下列程式碼中 `azureml-datadrift` ，和都 `azureml-train-automl` 是使用單行 pip 安裝進行安裝。 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   在此範例中，假設 `azureml-datadrift` 需要版本 > 1.0，而且 `azureml-train-automl` 需要版本 < 1.2。 如果的最新版本 `azureml-datadrift` 是1.3，則不論舊版的套件需求為何，這兩個封裝都會升級至 1.3 `azureml-train-automl` 。 

   若要確定已為您的套件安裝適當的版本，請使用多行進行安裝，如下列程式碼所示。 此處的順序不是問題，因為 pip 會在下一行呼叫中明確降級。 因此，會套用適當的版本相依性。
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **在安裝 azureml-automl-用戶端時，不保證會安裝說明套件：** 
   
   執行已啟用模型說明的遠端 AutoML 時，您會看到錯誤訊息：「請安裝 azureml-說明模型套件以進行模型說明」。 這是已知的問題。 如需因應措施，請遵循下列其中一個步驟：
  
  1. 在本機安裝 azureml-說明模型。
   ```
      pip install azureml-explain-model
   ```
  2. 藉由在 AutoML 設定中傳遞 model_explainability = False，完全停用可解釋性功能。
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Panda 錯誤：通常會在 AutoML 實驗期間看到：**
   
   使用 pip 手動設定您的環境時，您可能會注意到屬性錯誤 (特別是 pandas) ，因為安裝了不支援的套件版本。 為了避免這類錯誤，[請使用 automl_setup 安裝 AUTOML SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md)：
   
    1. 開啟 Anaconda 提示，並複製一組範例筆記本的 GitHub 存放庫。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd 至使用方法，其中會解壓縮範例筆記本並加以執行的作法/自動化機器學習資料夾：
    
    ```bash
    automl_setup
    ```
    
* **KeyError：在本機計算或 Azure Databricks 叢集上執行 AutoML 時的「品牌」**

    如果新環境是在2020年6月10日之後建立，使用 SDK 1.7.0 或更早版本，由於 .py-cpuinfo 套件中的更新，訓練可能會因此錯誤而失敗。 在2020年6月10日或之前建立的 (環境不會受到影響，因為在遠端計算上執行的是實驗，因為已使用快取的定型影像。 ) 若要解決此問題，請採取下列兩個步驟之一：
    
    * 將 SDK 版本更新為1.8.0 或更新版本 (這也會將 .py-cpuinfo 降級為 5.0.0) ：
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * 將已安裝的 .py-cpuinfo 版本降級為5.0.0：
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **錯誤訊息：無法解除安裝 'PyYAML'**

    適用于 Python 的 Azure Machine Learning SDK： PyYAML 是 `distutils` 已安裝的專案。 因此，在有部分解決安裝的情況下，我們無法精確判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **安裝封裝時 Databricks 失敗**

    安裝更多套件時，Azure Databricks 上的 Azure Machine Learning SDK 安裝會失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免安裝錯誤，請藉由凍結程式庫版本來安裝套件。 此問題與 Databricks 有關，而不是 Azure Machine Learning SDK。 您也可能會遇到其他程式庫的這個問題。 範例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果您持續遇到 Python 程式庫的安裝問題，則可以使用 init 腳本。 這種方法並不正式支援。 如需詳細資訊，請參閱叢集[範圍的初始化腳本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **Databricks 匯入錯誤：無法 `Timedelta` `pandas._libs.tslibs` 從匯入名稱**：如果您在使用自動化機器學習時看到此錯誤，請在您的筆記本中執行下列兩行：
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks 匯入錯誤：沒有名為 ' pandas ' 的模組**：如果您在使用自動化機器學習時看到此錯誤：

    1. 執行此命令以在您的 Azure Databricks 叢集中安裝兩個套件：
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 卸離叢集，然後將叢集重新附加至您的筆記本。
    
    如果這些步驟無法解決問題，請嘗試重新開機叢集。

* **Databricks FailToSendFeather**：如果您在 `FailToSendFeather` Azure Databricks 叢集上讀取資料時看到錯誤，請參閱下列解決方案：
    
    * 將 `azureml-sdk[automl]` 套件升級至最新版本。
    * 新增 `azureml-dataprep` 1.1.8 或更新版本。
    * 新增 `pyarrow` 0.11 版或更新版本。
    
## <a name="create-and-manage-workspaces"></a>建立及管理工作區

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

* **Azure 入口網站**：如果您直接從 SDK 或入口網站的共用連結查看您的工作區，您將無法在此延伸模組中使用訂用帳戶資訊來查看一般的 **[總覽**] 頁面。 您也無法切換至另一個工作區。 如果您需要查看另一個工作區，請直接移至[Azure Machine Learning studio](https://ml.azure.com)並搜尋工作區名稱。

* **Azure Machine Learning studio 入口網站中支援的瀏覽器**：建議您使用與作業系統相容的最新瀏覽器。 支援下列瀏覽器：
  * Microsoft Edge (新的 Microsoft Edge 最新版本。 不是 Microsoft Edge 舊版) 
  * Safari (最新版本，僅限 Mac)
  * Chrome (最新版本)
  * Firefox (最新版本)

## <a name="set-up-your-environment"></a>設定環境

* **建立 AmlCompute 時遇到問題**：在 GA 版本之前，從 Azure 入口網站建立其 Azure Machine Learning 工作區的使用者很罕見，可能無法在該工作區中建立 AmlCompute。 您可以對服務提出支援要求，或透過入口網站或 SDK 建立新的工作區來立即解除封鎖。

## <a name="work-with-data"></a>使用資料

### <a name="overloaded-azurefile-storage"></a>多載的 AzureFile 儲存體

如果您收到錯誤，請套用下列因應措施 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 。

如果您使用檔案共用來進行其他工作負載（例如資料傳輸），建議使用 blob，讓檔案共用可免費用於提交執行。 您也可以在兩個不同的工作區之間分割工作負載。

### <a name="passing-data-as-input"></a>以輸入的形式傳遞資料

*  **TypeError： FileNotFound：沒有這類檔案或目錄**：如果您提供的檔案路徑不是檔案所在位置，就會發生此錯誤。 您必須確定您參考檔案的方式與您在計算目標上裝載資料集的位置一致。 若要確保具有決定性的狀態，建議您在將資料集掛接至計算目標時使用抽象路徑。 例如，在下列程式碼中，我們會在計算目標的檔案系統根目錄下掛接資料集 `/tmp` 。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    如果您未包含前置正斜線 '/'，您必須在工作目錄前面加上前置詞，例如 `/mnt/batch/.../tmp/dataset` 計算目標上的，以指出您要裝載資料集的位置。

### <a name="data-labeling-projects"></a>標記專案的資料

|問題  |解決方案  |
|---------|---------|
|只能使用在 blob 資料存放區上建立的資料集。     |  這是目前版本的已知限制。       |
|建立之後，專案會顯示「正在初始化」一段很長的時間。     | 手動重新整理頁面。 初始化應該會在大約每秒20資料點時繼續。 缺乏 autorefresh 是已知的問題。         |
|在審核影像時，不會顯示新加上標籤的影像。     |   若要載入所有加上標籤的影像，請選擇**第一個**按鈕。 **第一個**按鈕會將您帶回清單的前端，但會載入所有加上標籤的資料。      |
|當物件偵測標記時按下 Esc 鍵，會在左上角建立零大小的標籤。 在此狀態下提交卷標失敗。     |   按一下旁邊的交叉標記來刪除標籤。  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a>資料漂移監視器

資料漂移監視的限制和已知問題：

* 分析歷程記錄資料的時間範圍限制為監視頻率設定的31個間隔。 
* 200功能的限制，除非未指定功能清單 () 使用的所有功能。
* 計算大小必須夠大，才能處理資料。
* 請確定您的資料集在指定的監視執行的開始和結束日期內有資料。
* 資料集監視器僅適用于包含50個或更多資料列的資料集。
* 資料集中的資料行或功能會根據下表中的條件分類為類別或數值。 如果功能不符合這些條件（例如，具有 >100 唯一值之 string 類型的資料行），則會從我們的資料漂移演算法中卸載此功能，但仍會進行分析。 

    | 功能類型 | 資料類型 | 條件 | 限制 | 
    | ------------ | --------- | --------- | ----------- |
    | 類別 | string、bool、int、float | 此功能中的唯一值數目小於100，且小於5% 的資料列數目。 | Null 會被視為它自己的類別。 | 
    | 數值 | int、float | 功能中的值是數值資料類型，而且不符合類別功能的條件。 | 如果 >的15% 值為 null，則會卸載功能。 | 

* 當您已[建立資料漂移監視器](how-to-monitor-datasets.md)，但在 Azure Machine Learning studio 中的 [**資料集監視器**] 頁面上看不到資料時，請嘗試下列操作。

    1. 檢查您是否已在頁面頂端選取正確的日期範圍。  
    1. 在 [**資料集監視器**] 索引標籤上，選取 [實驗] 連結以檢查執行狀態。  此連結位於資料表的最右邊。
    1. 如果順利完成執行，請檢查驅動程式記錄檔，以查看已產生多少計量，或是否有任何警告訊息。  按一下實驗後，在 [**輸出 + 記錄**] 索引標籤中尋找驅動程式記錄檔。

* 如果 SDK 函 `backfill()` 式不會產生預期的輸出，可能是因為驗證問題所造成。  當您建立要傳入此函數的計算時，請勿使用 `Run.get_context().experiment.workspace.compute_targets` 。  相反地，請使用如下所示的[ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py)來建立您傳遞至該函式的計算 `backfill()` ： 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

* **長時間計算準備時間：**

當您第一次連接或建立計算目標時，可能需要幾分鐘或更久的時間。 

從模型資料收集器中，最多可能需要 (，但通常小於) 10 分鐘，資料才會到達您的 blob 儲存體帳戶。 請等候10分鐘，以確保底下的儲存格會執行。

```python
import time
time.sleep(600)
```

## <a name="train-models"></a>將模型定型

* **ModuleErrors (沒有名為) 的模組**：如果您在 Azure ML 中提交實驗時遇到 ModuleErrors，這表示訓練腳本預期會安裝套件，但不會新增。 當您提供套件名稱之後，Azure ML 會在用於定型執行的環境中安裝套件。 

    如果您使用[估算器](concept-azure-machine-learning-architecture.md#estimators)來提交實驗，您可以 `pip_packages` `conda_packages` 根據要安裝封裝的來源，透過或估計工具中的參數來指定封裝名稱。 您也可以使用來指定具有所有相依性的 yml 檔案， `conda_dependencies_file` 或使用參數列出 txt 檔案中所有的 pip 需求 `pip_requirements_file` 。 如果您有自己的 Azure ML 環境物件，而您想要覆寫估計工具所使用的預設映射，您可以透過估計工具函數的參數來指定該環境 `environment` 。

    Azure ML 也提供適用于 TensorFlow、PyTorch、Chainer 和 SKLearn 的架構專屬估算器。 使用這些估算器可確保在用於定型的環境中，代表您安裝核心架構相依性。 如先前所述，您可以選擇指定額外的相依性。 
 
    Azure ML 維護的 docker 映射及其內容可以在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到。
    架構特定的相依性會列在個別的架構檔中- [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、 [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)。

    > [!Note]
    > 如果您認為特定套件很常見，可以在 Azure ML 維護的映射和環境中新增，請在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 問題。 
 
* **未定義 NameError (名稱) ，AttributeError (物件沒有任何屬性) **：此例外狀況應該來自您的定型腳本。 您可以從 Azure 入口網站查看記錄檔，以取得有關未定義的特定名稱或屬性錯誤的詳細資訊。 從 SDK 中，您可以使用 `run.get_details()` 來查看錯誤訊息。 這也會列出針對您的執行所產生的所有記錄檔。 請務必先查看您的訓練腳本並修正錯誤，再重新提交執行。 

* **Horovod 已關閉**：在大多數情況下，如果您遇到「AbortedError： Horovod 已關閉」，此例外狀況表示其中一個處理常式發生基礎例外狀況，導致 Horovod 關閉。 MPI 作業中的每個排名都會在 Azure ML 中取得專屬的專用記錄檔。 這些記錄檔的名稱為 `70_driver_logs` 。 如果是分散式訓練，記錄檔名稱的後面會加上， `_rank` 讓您更輕鬆地區分記錄檔。 若要找出造成 Horovod 關閉的確切錯誤，請流覽所有的記錄檔，然後在 `Traceback` driver_log 檔案的結尾尋找。 其中一個檔案會提供您實際的基礎例外狀況。 

* **執行或實驗刪除**：可以使用[實驗. archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--)方法，或從 Azure Machine Learning studio 用戶端的 [實驗] 索引標籤中，透過 [封存實驗] 按鈕來封存實驗。 此動作會隱藏來自清單查詢和 views 的實驗，但不會將其刪除。

    目前不支援永久刪除個別實驗或執行。 如需刪除工作區資產的詳細資訊，請參閱[匯出或刪除您的 Machine Learning 服務工作區資料](how-to-export-delete-data.md)。

* 計量**檔太大**： Azure Machine Learning 對於可從定型執行一次記錄的計量物件大小有內部限制。 如果您在記錄清單值度量時遇到「計量檔太大」錯誤，請嘗試將清單分割成較小的區塊，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    就內部而言，Azure ML 會將具有相同計量名稱的區塊串連成連續清單。

## <a name="automated-machine-learning"></a>自動化機器學習

* **TensorFlow**：從版本1.5.0 版的 SDK，自動化機器學習服務預設不會安裝 TensorFlow 模型。 若要安裝 TensorFlow 並搭配您的自動化 ML 實驗使用，請透過 CondaDependecies 安裝 TensorFlow = = 1.12.0。 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **實驗圖表**：二元分類圖表 (精確度-召回、ROC、增益曲線等等。自動化 ML 實驗反復專案中顯示的 ) ，在使用者介面中無法正確轉譯，自4/12 起。 圖表繪圖目前顯示的是反向結果，其中較佳的執行模型會以較低的結果顯示。 解決方案正在進行調查。

* **Databricks 取消自動化機器學習服務執行**：當您在 Azure Databricks 上使用自動化機器學習功能時，若要取消執行並開始新的實驗執行，請重新開機您的 Azure Databricks 叢集。

* **Databricks 自動機器學習服務 >10 次反覆運算**：在自動化機器學習設定中，如果您有10個以上的反復專案，請在提交回合時將設 `show_output` 為 `False` 。

* **AZURE MACHINE LEARNING SDK 和自動化機器學習的 Databricks 小工具**： Databricks 筆記本中不支援 Azure Machine Learning SDK widget，因為筆記本無法剖析 HTML 小工具。 您可以在入口網站中使用此 Python 程式碼，在您的 Azure Databricks 筆記本資料格中查看 widget：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup 失敗**： 
    * 在 Windows 上，從 Anaconda 提示字元執行 automl_setup。 若要安裝 Miniconda，請按一下[這裡](https://docs.conda.io/en/latest/miniconda.html)。
    * 執行命令，確定已安裝 conda 64 位，而不是32位 `conda info` 。 `platform` `win-64` 適用于 Windows 或 `osx-64` Mac。
    * 請確定已安裝 conda 4.4.10 或更新版本。 您可以使用命令來檢查版本 `conda -V` 。 如果您已安裝舊版，可以使用下列命令來更新它： `conda update conda` 。
    * 廠商`gcc: error trying to exec 'cc1plus'`
      *  如果 `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` 發生錯誤，請使用 sqlserverrp 命令來安裝 build essentials `sudo apt-get install build-essential` 。
      * 將新名稱當做第一個參數傳遞至 automl_setup，以建立新的 conda 環境。 使用來查看現有的 conda 環境 `conda env list` ，並將其移除 `conda env remove -n <environmentname>` 。
      
* **automl_setup_linux sh 失敗**：如果 Ubuntu linux 上的 automl_setup_linus sh 失敗，發生錯誤：`unable to execute 'gcc': No such file or directory`-
  1. 請確定已啟用輸出埠53和80。 在 Azure VM 上，您可以從 Azure 入口網站中選取 VM，然後按一下 [網路] 來執行此動作。
  2. 執行命令：`sudo apt-get update`
  3. 執行命令：`sudo apt-get install build-essential --fix-missing`
  4. `automl_setup_linux.sh`再次執行

* **ipynb 失敗**：
  * 若為本機 conda，請先確定 automl_setup 已 susccessfully 執行。
  * 請確定 subscription_id 是正確的。 藉由選取 [所有服務] 和 [訂用帳戶]，在 Azure 入口網站中尋找 subscription_id。 「<」和「>」字元不應該包含在 subscription_id 值中。 例如， `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 具有有效的格式。
  * 確保參與者或擁有者可以存取訂用帳戶。
  * 檢查區域是否為其中一個支援的區域： `eastus2` 、 `eastus` 、 `westcentralus` 、 `southeastasia` 、 `westeurope` 、 `australiaeast` 、 `westus2` 、 `southcentralus` 。
  * 請務必使用 Azure 入口網站存取區域。
  
* 匯**入 AutoMLConfig 失敗**：自動化機器學習服務版本1.0.76 中有套件變更，必須先卸載舊版，才能更新至新版本。 如果在 `ImportError: cannot import name AutoMLConfig` 1.0.76 至 v 1.0.76 或更新版本之前從 SDK 版本升級之後遇到，請執行下列程式來解決錯誤： `pip uninstall azureml-train automl` 和 `pip install azureml-train-auotml` 。 Automl_setup .cmd 腳本會自動執行此工作。 

* **工作區。 from_config 失敗**：如果呼叫 Ws = workspace。 from_config ( # A1 ' 失敗-
  1. 請確定 ipynb 筆記本已順利執行。
  2. 如果要從不在執行之資料夾下的資料夾執行筆記本 `configuration.ipynb` ，請將資料夾 aml_config，並將其包含的檔案 config.js複製到新的資料夾。 工作區。 from_config 會讀取筆記本資料夾或其上層資料夾的 config.js。
  3. 如果正在使用新的訂用帳戶、資源群組、工作區或區域，請確定您已 `configuration.ipynb` 再次執行筆記本。 只有在工作區已存在於指定的訂用帳戶下的指定資源群組中時，直接變更 config.js才會生效。
  4. 如果您想要變更區域，請變更工作區、資源群組或訂用帳戶。 `Workspace.create`將不會建立或更新工作區（如果已存在），即使指定的區域不同也一樣。
  
* **範例筆記本失敗**：如果範例筆記本失敗並出現錯誤，表示 preperty、方法或程式庫不存在：
  * 確定已在 jupyter 筆記本中選取 correctcorrect 核心。 核心會顯示在 [筆記本] 頁面的右上方。 預設值為 azure_automl。 請注意，核心會儲存為筆記本的一部分。 因此，如果您切換到新的 conda 環境，就必須選取筆記本中的新核心。
      * 針對 Azure Notebooks，它應該是 Python 3.6。 
      * 對於本機 conda 環境，它應該是您在 automl_setup 中指定的 conda envioronment 名稱。
  * 請確定筆記本適用于您所使用的 SDK 版本。 您可以在 jupyter 筆記本資料格中執行，以檢查 SDK 版本 `azureml.core.VERSION` 。 您可以從 GitHub 下載先前版本的範例筆記本，方法是按一下 `Branch` 按鈕，選取索引標籤， `Tags` 然後選取版本。

* **Windows 中的 Numpy 匯入失敗**：有些 Windows 環境會在載入 Numpy 時看到最新的 Python 版本3.6.8。 如果您看到此問題，請嘗試 Python 版本3.6.7。

* **Numpy 匯入失敗**：檢查自動化 ml conda 環境中的 tensorflow 版本。 支援的版本為 1.13 <。 從環境卸載 tensorflow 如果版本 >= 1.13，您可以檢查 tensorflow 版本並卸載，如下所示-
  1. 啟動命令 shell，啟用自動化 ml 套件安裝所在的 conda 環境。
  2. 輸入 `pip freeze` 並尋找 `tensorflow` （如果找到的話），列出的版本應該是 < 1.13
  3. 如果列出的版本不是支援的版本，請 `pip uninstall tensorflow` 在命令 shell 中輸入 y 進行確認。

## <a name="deploy--serve-models"></a>部署和提供模型

針對下列錯誤，請採取下列動作：

|錯誤  | 解決方法  |
|---------|---------|
|部署 web 服務時映射建立失敗     |  將 "pynacl = = 1.2.1" 新增為 Conda 檔案的 pip 相依性以進行映射設定       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   將部署中所使用 Vm 的 SKU 變更為具有更多記憶體的 SKU。 |
|FPGA 失敗     |  您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單：https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>在 AKS 叢集中更新 Azure Machine Learning 元件

Azure Kubernetes Service 叢集中安裝的 Azure Machine Learning 元件更新必須手動套用。 

您可以從 [Azure Machine Learning] 工作區卸離叢集，然後將叢集重新附加至工作區，以套用這些更新。 如果叢集中已啟用 TLS，當重新附加叢集時，您將需要提供 TLS/SSL 憑證和私密金鑰。 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

如果您不再具有 TLS/SSL 憑證和私密金鑰，或使用 Azure Machine Learning 所產生的憑證，您可以使用來連接到叢集並抓取密碼，以在卸離叢集之前先抓取檔案 `kubectl` `azuremlfessl` 。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes 會以64編碼的格式儲存秘密。 在將 `cert.pem` `key.pem` 密碼提供給之前，您必須先將它的和元件解碼為 64 `attach_config.enable_ssl` 。 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service 失敗中的 Webservices

Azure Kubernetes Service 中的許多 webservice 失敗，都可以使用連接到叢集來進行調試 `kubectl` 。 您可以藉由執行來取得 Azure Kubernetes Service 叢集的 `kubeconfig.json`

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>驗證錯誤

如果您從遠端工作執行計算目標的管理作業，您會收到下列錯誤的其中一個： 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

例如，如果您嘗試從 ML 管線建立或連結為遠端執行所提交的計算目標，您會收到錯誤。

## <a name="missing-user-interface-items-in-studio"></a>Studio 中缺少使用者介面專案

Azure 角色型存取控制可用來限制您可以使用 Azure Machine Learning 執行的動作。 這些限制可以防止使用者介面專案顯示在 Azure Machine Learning studio 中。 例如，如果您被指派無法建立計算實例的角色，建立計算實例的選項就不會出現在 studio 中。

如需詳細資訊，請參閱[管理使用者和角色](how-to-assign-roles.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure Machine Learning，請參閱更多疑難排解文章：

* [使用 Azure Machine Learning 進行 Docker 部署疑難排解](how-to-troubleshoot-deployment.md)
* [調試機器學習管線](how-to-debug-pipelines.md)
* [從 Azure Machine Learning SDK 進行 ParallelRunStep 類別的調試](how-to-debug-parallel-run-step.md)
* [使用 VS Code 進行機器學習計算實例的互動式調試](how-to-debug-visual-studio-code.md)
* [使用 Application Insights 來對機器學習管線進行 debug](how-to-debug-pipelines-application-insights.md)
