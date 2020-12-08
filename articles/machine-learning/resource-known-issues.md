---
title: 已知問題和疑難排解
titleSuffix: Azure Machine Learning
description: 取得在 Azure Machine Learning 中尋找和修正錯誤或失敗的協助。 瞭解已知問題、疑難排解和因應措施。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, contperfq4
ms.date: 11/09/2020
ms.openlocfilehash: 55ac11b7888a8e351b52554f76fb44af35633c16
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780972"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Azure Machine Learning 中的已知問題和疑難排解

本文可協助您針對使用 Azure Machine Learning 時可能遇到的已知問題進行疑難排解。 

如需疑難排解的詳細資訊，請參閱本文結尾的 [後續步驟](#next-steps) 。

> [!TIP]
> 錯誤或其他問題可能是您在使用 Azure Machine Learning 時遇到的 [資源配額](how-to-manage-quotas.md) 結果。 

## <a name="access-diagnostic-logs"></a>存取診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 若要查看一些記錄： 
1. 造訪 [Azure Machine Learning studio](https://ml.azure.com)。 
1. 選取左側的 [**實驗**] 
1. 選取實驗。
1. 選取 [執行]。
1. 在頂端，選取 [ **輸出 + 記錄**]。

> [!NOTE]
> Azure Machine Learning 在定型期間記錄來自各種來源的資訊，例如 AutoML 或執行定型作業的 Docker 容器。 其中有許多記錄並未記載。 如果您遇到問題並聯繫 Microsoft 支援服務，他們在進行疑難排解時可能會使用這些記錄。


## <a name="installation-and-import"></a>安裝和匯入
                           
* **Pip 安裝：相依性不保證會與單行安裝一致：** 

   這是 pip 的已知限制，因為當您將安裝成單一行時，它不會有運作中的相依性解析程式。 第一個唯一的相依性是唯一的唯一相依性。 

   在下列程式碼中 `azureml-datadrift` ， `azureml-train-automl` 會使用單行 pip 安裝進行安裝。 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   在此範例中，假設 `azureml-datadrift` 需要版本 > 1.0，且 `azureml-train-automl` 需要 < 1.2 版。 如果的最新版本 `azureml-datadrift` 是1.3，則不論舊版的套件需求為何，這兩個套件都會升級為 1.3 `azureml-train-automl` 。 

   若要確定已為您的套件安裝適當的版本，請使用下列程式碼中的多行來進行安裝。 順序不是問題，因為在下一行呼叫中，pip 明確降級。 因此，會套用適當的版本相依性。
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **安裝 azureml-automl 用戶端時，不保證會安裝說明套件：** 
   
   執行已啟用模型說明的遠端 AutoML 執行時，您會看到錯誤訊息「請安裝 azureml-說明模型套件以進行模型說明」。 這是已知的問題。 解決方法是遵循下列其中一個步驟：
  
  1. 在本機安裝 azureml-說明模型。
   ```
      pip install azureml-explain-model
   ```
  2. 在 AutoML 設定中傳遞 model_explainability = False，以完全停用可解釋性功能。
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
   
   使用 pip 手動設定您的環境時，您可能會注意到屬性錯誤 (特別是因為安裝了不支援的套件版本而從 pandas) 。 為了避免這類錯誤， [請使用 automl_setup 安裝 AUTOML SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md)：
   
    1. 開啟 Anaconda 提示，並複製一組範例筆記本的 GitHub 存放庫。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd 到使用方法-azureml/自動化機器學習資料夾，其中範例筆記本已解壓縮然後執行：
    
    ```bash
    automl_setup
    ```
    
* **KeyError：在本機計算或 Azure Databricks 叢集上執行 AutoML 時的「品牌」**

    如果在2020年6月10日之後建立新的環境，使用 SDK 1.7.0 或更早版本，定型可能會因為 .py cpuinfo 套件中的更新而失敗，並出現此錯誤。 在2020年6月10日或之前建立的 (環境不受影響，因為會使用快取的定型影像，在遠端計算上執行實驗。 ) 若要解決此問題，請執行下列兩個步驟之一：
    
    * 將 SDK 版本更新為1.8.0 或更新版本 (這也會將 .py cpuinfo 降級為 5.0.0) ：
    
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

* **Azure Machine Learning SDK 安裝失敗，發生例外狀況： ModuleNotFoundError：沒有名稱為 ' ruamel ' 或 ' ImportError：沒有名稱為 ruamel. yaml ' 的模組**
   
   在適用于 python 的所有版本 Azure Machine Learning SDK 的 conda 基礎環境中，安裝適用于 Python 的 Azure Machine Learning SDK ( # B0 20.1.1) 時，就會遇到此問題。 請參閱下列因應措施：

    * 避免在 conda 基礎環境上安裝 Python SDK，而是建立您的 conda 環境，並在該新建立的使用者環境上安裝 SDK。 最新的 pip 應可在該新的 conda 環境中運作。

    * 若要在 docker 中建立映射，但無法從 conda 基底環境切換，請在 docker 檔案中釘選 pip<= 20.1.1。

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **安裝套件時 Databricks 失敗**

    安裝更多套件時，Azure Databricks Azure Machine Learning SDK 安裝會失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免安裝錯誤，請凍結程式庫版本來安裝套件。 此問題與 Databricks 相關，而不是 Azure Machine Learning SDK。 您也可能會遇到其他程式庫的這個問題。 範例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果您持續遇到 Python 程式庫的安裝問題，您可以使用 init 腳本。 未正式支援此方法。 如需詳細資訊，請參閱叢集 [範圍的初始化腳本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **Databricks 匯入錯誤：無法匯入名稱 `Timedelta` 來源 `pandas._libs.tslibs`**：如果您在使用自動化機器學習時看到此錯誤，請在您的筆記本中執行下列兩行：
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks 匯入錯誤：沒有名稱為 ' pandas ' 的模組**：若您在使用自動化機器學習時看到此錯誤：

    1. 執行此命令，在您的 Azure Databricks 叢集中安裝兩個套件：
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 中斷連結，然後將叢集重新附加至您的筆記本。
    
    如果這些步驟無法解決問題，請嘗試重新開機叢集。

* **Databricks FailToSendFeather**：如果在 Azure Databricks 叢集 `FailToSendFeather` 上讀取資料時看到錯誤，請參閱下列解決方案：
    
    * 將 `azureml-sdk[automl]` 套件升級為最新版本。
    * 新增 `azureml-dataprep` 1.1.8 版或更新版本。
    * 新增 `pyarrow` 0.11 版或更新版本。
    
## <a name="create-and-manage-workspaces"></a>建立及管理工作區

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

* **Azure 入口網站**： 
  * 如果您直接從 SDK 或 Azure 入口網站的共用連結移至您的工作區，就無法在延伸模組中查看具有訂用帳戶資訊的標準 **總覽** 頁面。 在此案例中，您也無法切換至另一個工作區。 若要查看另一個工作區，請直接移至 [Azure Machine Learning studio](https://ml.azure.com) 並搜尋工作區名稱。
  * 所有資產 (資料集、實驗、計算等等) 僅適用于 [Azure Machine Learning studio](https://ml.azure.com)。 Azure 入口網站 *不* 提供這些功能。

* **Azure Machine Learning studio 入口網站中支援的瀏覽器**：建議您使用與您的作業系統相容的最新瀏覽器。 支援下列瀏覽器：
  * Microsoft Edge (新的 Microsoft Edge 最新版本。 不 Microsoft Edge 舊版) 
  * Safari (最新版本，僅限 Mac)
  * Chrome (最新版本)
  * Firefox (最新版本)

## <a name="set-up-your-environment"></a>設定您的環境

* **建立 AmlCompute 時發生問題**：在 GA 版本之前，某些從 Azure 入口網站建立其 Azure Machine Learning 工作區的使用者很少可能無法在該工作區中建立 AmlCompute。 您可以對服務提出支援要求，或透過入口網站或 SDK 建立新的工作區，以立即解除封鎖。

* **Azure Container Registry 目前不支援資源組名中的 unicode 字元**：由於 ACR 要求的資源組名包含 unicode 字元，因此可能會失敗。 若要減輕此問題，建議您在不同名稱的資源群組中建立 ACR。

## <a name="work-with-data"></a>使用資料

### <a name="overloaded-azurefile-storage"></a>多載的 AzureFile 儲存體

如果您收到錯誤，請套用下列因應措施 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 。

如果您針對其他工作負載（例如資料傳輸）使用檔案共用，建議使用 blob，讓檔案共用可用於提交執行。 您也可以在兩個不同的工作區之間分割工作負載。

### <a name="passing-data-as-input"></a>以輸入的形式傳遞資料

*  **TypeError： FileNotFound：沒有這類檔案或目錄**：如果您提供的檔案路徑不是檔案所在的位置，就會發生此錯誤。 您必須確定您參考該檔案的方式，與您在計算目標上裝載資料集的位置一致。 若要確保具決定性狀態，建議您在將資料集裝載至計算目標時使用抽象路徑。 例如，在下列程式碼中，我們會將資料集裝載在計算目標的檔案系統根目錄下 `/tmp` 。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    如果您未包含前置正斜線 '/'，則必須在工作目錄前面加上前置詞，例如 `/mnt/batch/.../tmp/dataset` 在計算目標上，以指出您要裝載資料集的位置。

### <a name="mount-dataset"></a>裝載資料集
* **資料集初始化失敗：等候掛接點就緒已超時**： 
  * 如果您沒有任何輸出 [網路安全性群組](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview) 規則且正在使用 `azureml-sdk>=1.12.0` 中，請更新其相依性，使 `azureml-dataset-runtime` 其成為特定次要版本的最新版本，或者，如果您在執行中使用它，請重新建立您的環境，讓它可以有修正程式的最新修補程式。 
  * 如果您使用的是 `azureml-sdk<1.12.0` ，請升級至最新版本。
  * 如果您有輸出 NSG 規則，請確認有允許服務標籤之所有流量的輸出規則 `AzureResourceMonitor` 。

### <a name="data-labeling-projects"></a>資料標記專案

|問題  |解決方案  |
|---------|---------|
|只能使用在 blob 資料存放區上建立的資料集。     |  這是目前版本的已知限制。       |
|建立之後，專案會顯示「正在初始化」一段很長的時間。     | 手動重新整理頁面。 初始化應以大約每秒20資料點的速度進行。 缺少自動重新整理是已知的問題。         |
|審核影像時，不會顯示新標記的影像。     |   若要載入所有加上標籤的影像，請選擇 **第一個** 按鈕。 **第一個** 按鈕會將您帶回清單的前方，但會載入所有標記的資料。      |
|在標記進行物件偵測時按下 Esc 鍵會在左上角建立零大小的標籤。 在此狀態下提交卷標會失敗。     |   按一下旁邊的交叉標示刪除標籤。  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> 資料漂移監視器

資料漂移監視的限制和已知問題：

* 分析歷程記錄資料的時間範圍限制為監視頻率設定的31個間隔。 
* 200功能的限制，除非未指定功能清單 () 所使用的所有功能。
* 計算大小必須夠大，才能處理資料。
* 確定您的資料集在指定的監視執行的開始和結束日期內有資料。
* 資料集監視器只適用于包含50個數據列或以上的資料集。
* 資料集中的資料行（或功能）會根據下表中的條件分類為類別或數值。 如果此功能不符合這些條件（例如，具有 >100 唯一值之字串類型的資料行），此功能會從我們的資料漂移演算法中卸載，但仍會進行分析。 

    | 功能類型 | 資料類型 | 條件 | 限制 | 
    | ------------ | --------- | --------- | ----------- |
    | 類別 | string、bool、int、float | 功能中唯一值的數目小於100，且小於資料列數目的5%。 | Null 會被視為本身的類別。 | 
    | 數值 | int、float | 功能中的值是數值資料類型，而且不符合分類功能的條件。 | 如果 >15% 的值為 null，則會卸載功能。 | 

* 當您在 Azure Machine Learning studio 中 [建立資料漂移監視器](how-to-monitor-datasets.md) 但無法在 [ **資料集監視器** ] 頁面上看到資料時，請嘗試下列動作。

    1. 檢查您是否已在頁面頂端選取正確的日期範圍。  
    1. 在 [ **資料集監視器** ] 索引標籤上，選取 [實驗] 連結以檢查執行狀態。  此連結位於資料表最右邊。
    1. 如果執行成功完成，請檢查驅動程式記錄檔，以查看已產生多少計量，或是否有任何警告訊息。  按一下實驗之後，在 [ **輸出 + 記錄** ] 索引標籤中尋找驅動程式記錄檔。

* 如果 SDK `backfill()` 函數未產生預期的輸出，可能是因為驗證問題所致。  當您建立要傳入此函式的計算時，請勿使用 `Run.get_context().experiment.workspace.compute_targets` 。  相反地，請使用 [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) （如下所示）來建立您傳遞至該函式的計算 `backfill()` ： 

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

### <a name="dataset-visualization-in-the-designer"></a>設計工具中的資料集視覺效果

在 [ **資料集** 資產] 頁面或使用 SDK 註冊資料集之後，您可以在左側清單中的 [ **資料集** ] 類別下找到它。

但是，當您將資料集拖曳至畫布並進行視覺化時，可能會因為下列原因而無法視覺化：

- 目前，您只能在設計工具中將表格式資料集視覺化。 如果您在設計工具外註冊檔案資料集，則無法在設計工具畫布中將它視覺化。
- 您的資料集會儲存在虛擬網路中 (VNet) 。 如果您想要視覺化，您需要啟用資料存放區的工作區受控識別。
    1. 移至相關的資料存放區，然後按一下 [**更新認證** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="更新認證":::]
    1. 選取 **[是]** 可啟用工作區受控識別。
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="啟用工作區受控識別":::

### <a name="long-compute-preparation-time"></a>長時間計算準備時間

當您第一次連接或建立計算目標時，可能需要幾分鐘或更長的時間。 

從模型資料收集器中，最多可能需要 (，但通常不到) 10 分鐘，資料就會抵達您的 blob 儲存體帳戶。 等候10分鐘，以確保下方的儲存格將會執行。

```python
import time
time.sleep(600)
```

### <a name="log-for-real-time-endpoints"></a>即時端點的記錄

即時端點的記錄是客戶資料。 如需即時端點的疑難排解，您可以使用下列程式碼來啟用記錄。 

請參閱本文中有關監視 web 服務端點 [的詳細](./how-to-enable-app-insights.md#query-logs-for-deployed-models)資料。

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
如果您有多個租使用者，您可能需要在之前新增下列驗證程式代碼： `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>將模型定型

* **ModuleErrors (沒有名稱為) 的模組**：如果您在 Azure ML 中提交實驗時遇到 ModuleErrors，這表示定型腳本預期會安裝套件，但不會新增套件。 提供套件名稱之後，Azure ML 會在用於定型回合的環境中安裝套件。 

    如果您使用估算器來提交實驗，您可以 `pip_packages` `conda_packages` 根據您要安裝套件的來源，在估算器中透過或參數指定封裝名稱。 您也可以使用來指定具有所有相依性的 yml 檔案， `conda_dependencies_file` 或使用參數列出 txt 檔案中的所有 pip 需求 `pip_requirements_file` 。 如果您有自己的 Azure ML 環境物件，而您想要覆寫估算器所使用的預設映射，您可以透過估算器函式的參數來指定該環境 `environment` 。

    Azure ML 也提供適用于 TensorFlow、PyTorch、Chainer 和 SKLearn 的架構專屬估算器。 使用這些估算器可確保您在用於定型的環境中，代表您安裝了 core framework 相依性。 您可以選擇指定額外的相依性，如上所述。 
 
    您可以在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到 Azure ML 維護的 docker 映射及其內容。
    架構特有的相依性列在個別的架構檔中- [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)、 [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)、 [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)、 [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)。

    > [!Note]
    > 如果您認為特定套件在 Azure ML 維護的映射和環境中有很大的不足，請在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 問題。 
 
* **未定義 NameError (名稱) ，AttributeError (物件沒有屬性)**：此例外狀況應來自您的定型腳本。 您可以從 Azure 入口網站查看記錄檔，以取得有關未定義特定名稱或屬性錯誤的詳細資訊。 您可以從 SDK 使用 `run.get_details()` 來查看錯誤訊息。 這也會列出針對您的執行所產生的所有記錄檔。 重新提交您的執行之前，請務必先查看您的訓練腳本並修正錯誤。 

* **Horovod 已關閉**：在大部分情況下，如果您遇到「AbortedError： Horovod 已關閉」這個例外狀況，就表示其中一個處理常式的基礎例外狀況導致 Horovod 關機。 MPI 作業中的每個排名在 Azure ML 中都會有其本身的專用記錄檔。 這些記錄檔的名稱為 `70_driver_logs`。 在分散式定型的情況下，記錄檔名稱的結尾會加上 `_rank`，以便區分記錄檔。 若要找出造成 Horovod 關機的確切錯誤，請流覽所有記錄檔，然後 `Traceback` 在 driver_log 檔案的結尾尋找。 其中一個檔案將提供實際的根本例外狀況。 

* **執行或實驗刪除**：您可以使用 [實驗.](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) 封存方法來封存實驗，或從 Azure Machine Learning Studio 用戶端的實驗索引標籤中，透過 [封存實驗] 按鈕來封存實驗。 此動作會從清單查詢和 views 中隱藏實驗，但不會將其刪除。

    目前不支援永久刪除個別實驗或執行。 如需有關刪除工作區資產的詳細資訊，請參閱 [匯出或刪除您的 Machine Learning 服務工作區資料](how-to-export-delete-data.md)。

* 計量 **檔太大**： Azure Machine Learning 具有可從定型執行一次記錄的計量物件大小的內部限制。 如果在記錄清單值計量時發生「計量文件太大」錯誤，請嘗試將清單分割為較小的區塊，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    在內部，Azure ML 會將具有相同計量名稱的區塊串連為連續清單。

## <a name="automated-machine-learning"></a>自動化機器學習

* **將 AutoML 相依性升級為較新版本的最新版本將會中斷相容性**：從 SDK 的版本1.13.0 開始，由於我們在先前的套件中釘選的舊版本與較新版本的版本不相容，因此不會在舊版 sdk 中載入模型。 您將會看到如下的錯誤：
  * 找不到模組：例如 `No module named 'sklearn.decomposition._truncated_svd` ，
  * 匯入錯誤：例如 `ImportError: cannot import name 'RollingOriginValidator'` ，
  * 屬性錯誤：例如 `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  若要解決這個問題，請根據您的 AutoML SDK 訓練版本，執行下列兩個步驟之一：
  1. 如果您的 AutoML SDK 定型版本大於1.13.0，您需要 `pandas == 0.25.1` 和 `sckit-learn==0.22.1` 。 如果版本不符，請將 scikit-learn-學習及/或 pandas 升級為正確的版本，如下所示：
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. 如果您的 AutoML SDK 定型版本小於或等於1.12.0，您需要 `pandas == 0.23.4` 和 `sckit-learn==0.20.3` 。 如果版本不符，請將 scikit-learn-學習和/或 pandas 降級為正確的版本，如下所示：
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```
 
* **預測 R2 分數一律為零**：如果所提供的定型資料具有的時間序列包含與最後一個 `n_cv_splits`  +  資料點相同的值，就會發生此問題 `forecasting_horizon` 。 如果您的時間序列中必須要有這種模式，您可以將主要計量切換為正規化的根本平方誤差。
 
* **TensorFlow**：從 SDK 版本1.5.0 版來，自動化機器學習預設不會安裝 TensorFlow 模型。 若要安裝 TensorFlow 並搭配自動化 ML 實驗使用，請透過 CondaDependecies 安裝 TensorFlow = = 1.12.0。 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **實驗圖表**：二元分類圖表 (精確度-召回、ROC、增益曲線等。在4/12 之後，自動 ML 實驗反復專案中顯示的 ) 不會在使用者介面中正確呈現。 圖表繪圖目前顯示反向結果，其中以較低的結果顯示較佳的執行模型。 解決方案正在進行調查。

* **Databricks 取消自動化機器學習執行**：當您在 Azure Databricks 上使用自動化機器學習功能時，若要取消執行並開始新的實驗回合，請重新開機您的 Azure Databricks 叢集。

* **自動化機器學習的 Databricks >10 次反覆運算**：在自動化機器學習設定中，如果您有10個以上的反復專案，請在 `show_output` `False` 提交執行時將設定為。

* **適用于 AZURE MACHINE LEARNING SDK 和自動化機器學習服務的 Databricks 小工具**： Databricks 筆記本中不支援 Azure Machine Learning SDK widget，因為筆記本無法剖析 HTML 小工具。 您可以使用 Azure Databricks 筆記本儲存格中的此 Python 程式碼，在入口網站中查看 widget：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup 失敗**： 
    * 在 Windows 上，從 Anaconda 提示字元執行 automl_setup。 使用此連結來 [安裝 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。
    * 執行命令，確定已安裝 conda 64 位，而不是32位 `conda info` 。 `platform` `win-64` 適用于 Windows 或 `osx-64` Mac。
    * 確定已安裝 conda 4.4.10 或更新版本。 您可以使用命令來檢查版本 `conda -V` 。 如果您已安裝先前的版本，您可以使用下列命令來更新它： `conda update conda` 。
    * 轉銷商 `gcc: error trying to exec 'cc1plus'`
      *  如果 `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` 發生錯誤，請使用命令來安裝 build essentials `sudo apt-get install build-essential` 。
      * 傳遞新名稱做為 automl_setup 的第一個參數，以建立新的 conda 環境。 使用來查看現有的 conda 環境 `conda env list` ，並將其移除 `conda env remove -n <environmentname>` 。
      
* **automl_setup_linux. sh 失敗**：如果 Ubuntu linux 上的 automl_setup_linus sh 失敗，並出現下列錯誤： `unable to execute 'gcc': No such file or directory`-
  1. 請確定已啟用輸出埠53和80。 在 Azure VM 上，您可以從 Azure 入口網站選取 VM，然後按一下 [網路]，來進行這項作業。
  2. 執行命令：`sudo apt-get update`
  3. 執行命令：`sudo apt-get install build-essential --fix-missing`
  4. `automl_setup_linux.sh`重新執行

* **.ipynb 失敗**：
  * 若為本機 conda，請先確定 automl_setup 已成功執行。
  * 請確定 subscription_id 正確。 選取 [所有服務]，然後選取 [訂用帳戶]，以找出 Azure 入口網站中的 subscription_id。 字元 "<" 和 ">" 不應包含在 subscription_id 值中。 例如， `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 具有有效的格式。
  * 確定訂用帳戶的參與者或擁有者存取權。
  * 檢查區域是否為其中一個支援的區域： `eastus2` 、 `eastus` 、 `westcentralus` 、 `southeastasia` 、 `westeurope` 、、、 `australiaeast` `westus2` `southcentralus` 。
  * 使用 Azure 入口網站確定存取區域。
  
* 匯 **入 AutoMLConfig 失敗**：自動化機器學習版1.0.76 中有套件變更，需要先卸載舊版本，然後再更新為新版本。 如果在 `ImportError: cannot import name AutoMLConfig` v 1.0.76 至 v 1.0.76 或更新版本之前從 SDK 版本升級之後遇到，請執行下列程式來解決錯誤： `pip uninstall azureml-train automl` `pip install azureml-train-auotml` Automl_setup .cmd 腳本會自動執行此工作。 

* **workspace.from_config 失敗**：如果呼叫 ws = Workspace.from_config ( # A1 ' 失敗-
  1. 確定已成功執行 .ipynb 筆記本。
  2. 如果正在從執行所在資料夾下的資料夾中執行筆記本 `configuration.ipynb` ，請將資料夾 aml_config，並將其包含的檔案 config.js複製到新資料夾。 Workspace.from_config 讀取筆記本資料夾或其父資料夾的 config.js。
  3. 如果使用新的訂用帳戶、資源群組、工作區或區域，請確定您已再次執行 `configuration.ipynb` 筆記本。 如果工作區已存在於指定的訂用帳戶下的指定資源群組中，則直接變更 config.js將會運作。
  4. 如果您想要變更區域，請變更工作區、資源群組或訂用帳戶。 `Workspace.create` 將不會建立或更新工作區（如果已存在），即使指定的區域不同也是一樣。
  
* **範例筆記本失敗**：如果範例筆記本失敗，並出現屬性、方法或程式庫不存在的錯誤：
  * 確定已在 jupyter 筆記本中選取正確的核心。 核心會顯示在 [筆記本] 頁面的右上方。 預設值為 azure_automl。 請注意，核心會儲存為筆記本的一部分。 因此，如果您切換至新的 conda 環境，就必須在筆記本中選取新的核心。
      * 針對 Azure Notebooks，它應該是 Python 3.6。 
      * 在本機 conda 環境中，它應該是您在 automl_setup 中指定的 conda 環境名稱。
  * 確定筆記本適用于您所使用的 SDK 版本。 您可以在 jupyter 筆記本資料格中執行，以檢查 SDK 版本 `azureml.core.VERSION` 。 您可以從 GitHub 下載舊版範例筆記本，方法是按一下按鈕、選取索引標籤， `Branch` `Tags` 然後選取版本。

* **Windows 中的 Numpy 匯入失敗**：有些 Windows 環境看到使用最新 Python 版本 3.6.8 Numpy 載入的錯誤。 如果您看到此問題，請嘗試使用 Python 版本3.6.7。

* **Numpy 匯入失敗**：檢查自動化 ml conda 環境中的 TensorFlow 版本。 支援的版本為 1.13 <。 如果版本為 >= 1.13，請從環境卸載 TensorFlow。您可以檢查 TensorFlow 的版本，並將其卸載，如下所示：
  1. 啟動命令 shell，並啟用自動 ml 封裝的安裝 conda 環境。
  2. 輸入 `pip freeze` 並尋找， `tensorflow` 如果找到的話，所列出的版本應該是 < 1.13
  3. 如果列出的版本不是支援的版本，請 `pip uninstall tensorflow` 在命令 shell 中輸入 y 確認。

## <a name="deploy--serve-models"></a>部署和提供模型

針對下列錯誤採取下列動作：

|錯誤  | 解決方法  |
|---------|---------|
|部署 web 服務時映射建立失敗     |  新增 "pynacl = = 1.2.1" 作為映射設定 Conda 檔案的 pip 相依性       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   將部署中所使用 Vm 的 SKU 變更為具有更多記憶體的 Vm。 |
|FPGA 失敗     |  您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單：https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>更新 AKS 叢集中的 Azure Machine Learning 元件

您必須手動套用 Azure Kubernetes Service 叢集中所安裝 Azure Machine Learning 元件的更新。 

您可以從 Azure Machine Learning 工作區卸離叢集，然後將叢集重新附加到工作區，以套用這些更新。 如果叢集中已啟用 TLS，則在重新附加叢集時，您將需要提供 TLS/SSL 憑證和私密金鑰。 

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

如果您不再有 TLS/SSL 憑證和私密金鑰，或您使用 Azure Machine Learning 所產生的憑證，您可以使用和取得密碼來連接到叢集，以便在卸離叢集之前取出檔案 `kubectl` `azuremlfessl` 。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes 會以 base-64 編碼格式儲存秘密。 您將需要以64為基礎來解碼 `cert.pem` 和 `key.pem` 元件，再提供這些秘密 `attach_config.enable_ssl` 。 

### <a name="detaching-azure-kubernetes-service"></a>卸離 Azure Kubernetes Service

使用 Azure Machine Learning studio、SDK 或機器學習服務的 Azure CLI 擴充功能來卸離 AKS 叢集，並不會刪除 AKS 叢集。 若要刪除叢集，請參閱搭配 [使用 Azure CLI 與 AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster)。

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service 失敗中的 Webservices

Azure Kubernetes Service 中的許多 Webservice 失敗都可以藉由使用 `kubectl` 連線到叢集來偵錯。 您可以藉由執行來取得 Azure Kubernetes Service 叢集的 `kubeconfig.json`

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

您可以使用 Azure 角色型存取控制來限制可使用 Azure Machine Learning 執行的動作。 這些限制可以防止使用者介面專案顯示在 Azure Machine Learning studio 中。 例如，如果您被指派無法建立計算實例的角色，則建立計算實例的選項不會出現在 studio 中。

如需詳細資訊，請參閱[管理使用者和角色](how-to-assign-roles.md)。

## <a name="compute-cluster-wont-resize"></a>計算叢集不會調整大小

如果您的 Azure Machine Learning 計算叢集出現在調整大小 (0-> 0) 節點狀態時，可能是因為 Azure 資源鎖定所造成。

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>後續步驟

請參閱更多關於 Azure Machine Learning 的疑難排解文章：

* [使用 Azure Machine Learning 進行 Docker 部署疑難排解](how-to-troubleshoot-deployment.md)
* [調試機器學習管線](how-to-debug-pipelines.md)
* [從 Azure Machine Learning SDK 進行 ParallelRunStep 類別的調試](how-to-debug-parallel-run-step.md)
* [使用 VS Code 進行機器學習計算實例的互動式偵錯工具](how-to-debug-visual-studio-code.md)
* [使用 Application Insights 來進行機器學習管線的調試](./how-to-log-pipelines-application-insights.md)
