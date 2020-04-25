---
title: 已知問題和疑難排解
titleSuffix: Azure Machine Learning
description: 取得 Azure Machine Learning 的已知問題、因應措施和疑難排解清單。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 58fd9225298b4322567f4feb02629e3ad4e0f00d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127576"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>已知問題和疑難排解 Azure Machine Learning

本文可協助您找出並修正使用 Azure Machine Learning 時可能會遇到的錯誤或失敗。

## <a name="diagnostic-logs"></a>診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 若要查看一些記錄： 
1. 流覽[Azure Machine Learning studio](https://ml.azure.com)。 
1. 在左側選取 [**實驗**] 
1. 選取實驗。
1. 選取 [執行]。
1. 在頂端，選取 [**輸出 + 記錄**]。

> [!NOTE]
> Azure Machine Learning 會在定型期間記錄各種來源的資訊，例如 AutoML 或執行定型作業的 Docker 容器。 其中有許多記錄檔並未記載。 如果您遇到問題並聯系 Microsoft 支援服務，他們可能會在進行疑難排解時使用這些記錄。


## <a name="resource-quotas"></a>資源配額

深入了解使用 Azure Machine Learning 時可能會遇到的[資源配額](how-to-manage-quotas.md)。

## <a name="installation-and-import"></a>安裝和匯入

* **Pip 安裝：不保證相依性會與單行安裝一致**： 

   這是已知的 pip 限制，因為當您將安裝為單一行時，它沒有正常運作的相依性解析程式。 第一個唯一的相依性是它所查看的唯一相依性。 

   在下列程式碼`azure-ml-datadrift`中`azureml-train-automl` ，和都是使用單行 pip 安裝進行安裝。 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   在此範例中，假設`azure-ml-datadrift`需要版本 > 1.0，而且`azureml-train-automl`需要版本 < 1.2。 如果的最新版本`azure-ml-datadrift`是1.3，則不論舊版的`azureml-train-automl`套件需求為何，這兩個封裝都會升級至1.3。 

   若要確定已為您的套件安裝適當的版本，請使用多行進行安裝，如下列程式碼所示。 此處的順序不是問題，因為 pip 會在下一行呼叫中明確降級。 因此，會套用適當的版本相依性。
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **錯誤訊息：無法解除安裝 'PyYAML'**

    適用于 Python 的 Azure Machine Learning SDK： PyYAML `distutils`是已安裝的專案。 因此，在有部分解決安裝的情況下，我們無法精確判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **安裝封裝時 Databricks 失敗**

    安裝更多套件時，Azure Databricks 上的 Azure Machine Learning SDK 安裝會失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免安裝錯誤，請藉由凍結程式庫版本來安裝套件。 此問題與 Databricks 有關，而不是 Azure Machine Learning SDK。 您也可能會遇到其他程式庫的這個問題。 範例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果您持續遇到 Python 程式庫的安裝問題，則可以使用 init 腳本。 這種方法並不正式支援。 如需詳細資訊，請參閱叢集[範圍的初始化腳本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **Databricks 匯入錯誤：無法從 ' _libs pandas ' 匯入名稱 ' Timedelta '**：如果您在使用自動化機器學習時看到此錯誤，請在您的筆記本中執行下列兩行：
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

* **Databricks FailToSendFeather**：如果您在 Azure Databricks `FailToSendFeather`叢集上讀取資料時看到錯誤，請參閱下列解決方案：
    
    * 將`azureml-sdk[automl]`套件升級至最新版本。
    * 新增`azureml-dataprep` 1.1.8 或更新版本。
    * 新增`pyarrow` 0.11 版或更新版本。
    
## <a name="create-and-manage-workspaces"></a>建立及管理工作區

> [!WARNING]
> 不支援將您的 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新的租使用者。 這麼做可能會導致錯誤。

* **Azure 入口網站**：如果您直接從 SDK 或入口網站的共用連結查看您的工作區，您將無法在此延伸模組中使用訂用帳戶資訊來查看一般的 **[總覽**] 頁面。 您也無法切換至另一個工作區。 如果您需要查看另一個工作區，請直接移至[Azure Machine Learning studio](https://ml.azure.com)並搜尋工作區名稱。

## <a name="set-up-your-environment"></a>設定您的環境

* **建立 AmlCompute 時遇到問題**：在 GA 版本之前，從 Azure 入口網站建立其 Azure Machine Learning 工作區的使用者很罕見，可能無法在該工作區中建立 AmlCompute。 您可以對服務提出支援要求，或透過入口網站或 SDK 建立新的工作區來立即解除封鎖。

## <a name="work-with-data"></a>使用資料

### <a name="overloaded-azurefile-storage"></a>多載的 AzureFile 儲存體

如果您收到錯誤`Unable to upload project files to working directory in AzureFile because the storage is overloaded`，請套用下列因應措施。

如果您使用檔案共用來進行其他工作負載（例如資料傳輸），建議使用 blob，讓檔案共用可免費用於提交執行。 您也可以在兩個不同的工作區之間分割工作負載。

### <a name="passing-data-as-input"></a>以輸入的形式傳遞資料

*  **TypeError： FileNotFound：沒有這類檔案或目錄**：如果您提供的檔案路徑不是檔案所在位置，就會發生此錯誤。 您必須確定您參考檔案的方式與您在計算目標上裝載資料集的位置一致。 若要確保具有決定性的狀態，建議您在將資料集掛接至計算目標時使用抽象路徑。 例如，在下列程式碼中，我們會在計算目標的檔案系統根目錄下掛接資料集`/tmp`。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    如果您未包含前置正斜線 '/'，您必須`/mnt/batch/.../tmp/dataset`在工作目錄前面加上前置詞，例如計算目標上的，以指出您要裝載資料集的位置。

### <a name="data-labeling-projects"></a>標記專案的資料

|問題  |解決方法  |
|---------|---------|
|只能使用在 blob 資料存放區上建立的資料集     |  這是目前版本的已知限制。       |
|建立之後，專案會顯示「正在初始化」一段很長的時間     | 手動重新整理頁面。 初始化應該會在大約每秒20資料點時繼續。 缺乏 autorefresh 是已知的問題。         |
|在審核影像時，不會顯示新加上標籤的影像     |   若要載入所有加上標籤的影像，請選擇**第一個**按鈕。 **第一個**按鈕會將您帶回清單的前端，但會載入所有加上標籤的資料。      |
|當物件偵測標記時按下 Esc 鍵，會在左上角建立零大小的標籤。 在此狀態下提交卷標失敗。     |   按一下旁邊的交叉標記來刪除標籤。  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

已知問題：

* **長時間計算準備時間**：當您第一次連接或建立計算目標時，可能需要幾分鐘或更久。 

## <a name="train-models"></a>將模型定型

* **ModuleErrors （沒有名為的模組）**：如果您在 Azure ML 中提交實驗時遇到 ModuleErrors，這表示訓練腳本預期會安裝套件，但不會新增。 一旦您提供套件名稱，Azure ML 就會將套件安裝在用於定型執行的環境中。 

    如果您使用[估算器](concept-azure-machine-learning-architecture.md#estimators)來提交實驗，您可以根據要安裝封裝的來源`pip_packages` ， `conda_packages`透過或估計工具中的參數來指定封裝名稱。 您也可以使用`conda_dependencies_file`來指定具有所有相依性的 yml 檔案，或使用`pip_requirements_file`參數列出 txt 檔案中所有的 pip 需求。 如果您有自己的 Azure ML 環境物件，而您想要覆寫估計工具所使用的預設映射，您可以透過估計工具函數的`environment`參數來指定該環境。

    Azure ML 也提供適用于 Tensorflow、PyTorch、Chainer 和 SKLearn 的架構專屬估算器。 使用這些估算器可確保在用於定型的環境中，代表您安裝核心架構相依性。 如先前所述，您可以選擇指定額外的相依性。 
 
    Azure ML 維護的 docker 映射及其內容可以在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到。
    架構特定的相依性會列在個別的架構檔中- [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、 [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)。

    > [!Note]
    > 如果您認為特定套件很常見，可以在 Azure ML 維護的映射和環境中新增，請在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 問題。 
 
* **NameError （名稱未定義）、AttributeError （物件沒有屬性）**：這個例外狀況應該來自您的定型腳本。 您可以從 Azure 入口網站查看記錄檔，以取得有關未定義的特定名稱或屬性錯誤的詳細資訊。 從 SDK 中，您可以使用`run.get_details()`來查看錯誤訊息。 這也會列出針對您的執行所產生的所有記錄檔。 請務必先查看您的訓練腳本並修正錯誤，再重新提交執行。 

* **Horovod 已關閉**：在大多數情況下，如果您遇到「AbortedError： Horovod 已關閉」，此例外狀況表示其中一個處理常式發生基礎例外狀況，導致 Horovod 關閉。 MPI 作業中的每個排名都會在 Azure ML 中取得專屬的專用記錄檔。 這些記錄檔的`70_driver_logs`名稱為。 如果是分散式訓練，記錄檔名稱的後面會加`_rank`上，讓您更輕鬆地區分記錄檔。 若要找出造成 Horovod 關閉的確切錯誤，請流覽所有的記錄檔，然後在 driver_log `Traceback`檔案的結尾尋找。 其中一個檔案會提供您實際的基礎例外狀況。 

* **執行或實驗刪除**：可以使用[實驗. archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--)方法，或從 Azure Machine Learning studio 用戶端的 [實驗] 索引標籤中，透過 [封存實驗] 按鈕來封存實驗。 此動作會隱藏來自清單查詢和 views 的實驗，但不會將其刪除。

    目前不支援永久刪除個別實驗或執行。 如需刪除工作區資產的詳細資訊，請參閱[匯出或刪除您的 Machine Learning 服務工作區資料](how-to-export-delete-data.md)。

* 計量**檔太大**： Azure Machine Learning 對於可從定型執行一次記錄的計量物件大小有內部限制。 如果您在記錄清單值度量時遇到「計量檔太大」錯誤，請嘗試將清單分割成較小的區塊，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    就內部而言，Azure ML 會將具有相同計量名稱的區塊串連成連續清單。

## <a name="automated-machine-learning"></a>自動化機器學習

* **張量流程**：自動化機器學習服務目前不支援張量流程版本1.13。 安裝此版本將導致封裝相依性停止運作。 我們正致力於在未來的版本中修正此問題。

* **實驗圖表**：自4/12 起，自動化 ML 實驗反復專案中顯示的二元分類圖表（精確度-召回、ROC、增益曲線等）無法正確轉譯在使用者介面中。 圖表繪圖目前顯示的是反向結果，其中較佳的執行模型會以較低的結果顯示。 解決方案正在進行調查。

* **Databricks 取消自動化機器學習服務執行**：當您在 Azure Databricks 上使用自動化機器學習功能時，若要取消執行並開始新的實驗執行，請重新開機您的 Azure Databricks 叢集。

* **Databricks 自動機器學習服務 >10 次反覆運算**：在自動化機器學習設定中，如果您有10個以上的`show_output`反復`False`專案，請在提交回合時將設為。

* **AZURE MACHINE LEARNING SDK 和自動化機器學習的 Databricks 小工具**： Databricks 筆記本中不支援 Azure Machine Learning SDK widget，因為筆記本無法剖析 HTML 小工具。 您可以在入口網站中使用此 Python 程式碼，在您的 Azure Databricks 筆記本資料格中查看 widget：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

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

如果您不再具有 TLS/SSL 憑證和私密金鑰，或使用 Azure Machine Learning 所產生的憑證，您可以使用`kubectl`來連接到叢集並抓取密碼`azuremlfessl`，以在卸離叢集之前先抓取檔案。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes 會以64編碼的格式儲存秘密。 在將密碼提供給`cert.pem` `key.pem` `attach_config.enable_ssl`之前，您必須先將它的和元件解碼為64。 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service 失敗中的 Webservices

Azure Kubernetes Service 中的許多 webservice 失敗，都可以使用`kubectl`連接到叢集來進行調試。 您可以藉由`kubeconfig.json`執行來取得 Azure Kubernetes Service 叢集的

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
