---
title: 已知問題和疑難排解
titleSuffix: Azure Machine Learning
description: 獲取 Azure 機器學習的已知問題、解決方法和故障排除的清單。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619382"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>已知問題和 Azure 機器學習故障排除

本文可説明您查找和更正使用 Azure 機器學習時可能遇到的錯誤或故障。

## <a name="diagnostic-logs"></a>診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 要檢視某些紀錄: 
1. 存[取 Azure 機器學習工作室](https://ml.azure.com)。 
1. 在左側,選擇 **"實驗"** 
1. 選擇實驗。
1. 選擇運行。
1. 在頂端,選擇 **「輸出 」 紀錄**。

> [!NOTE]
> Azure 機器學習在培訓期間從各種來源(如自動ML 或運行訓練作業的 Docker 容器)記錄資訊。 許多這些日誌沒有記錄。 如果您遇到問題並聯繫 Microsoft 支援人員,他們也許能夠在故障排除期間使用這些日誌。


## <a name="resource-quotas"></a>資源配額

深入了解使用 Azure Machine Learning 時可能會遇到的[資源配額](how-to-manage-quotas.md)。

## <a name="installation-and-import"></a>安裝及匯入

* **錯誤訊息：無法解除安裝 'PyYAML'**

    用於 Python 的 Azure 機器學習 SDK:PyYAML 是一個`distutils`已安裝的專案。 因此，在有部分解決安裝的情況下，我們無法精確判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **安裝套件時繼資料**

    安裝更多包時,Azure 機器學習 SDK 安裝在 Azure 數據塊上失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 為了避免安裝錯誤,請通過凍結庫版本來安裝包。 此問題與數據磚塊無關,而不是與 Azure 機器學習 SDK 相關。 您可能也會在其他庫中遇到此問題。 範例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者,如果您繼續面臨 Python 庫的安裝問題,則可以使用 init 文稿。 此方法不受官方支援。 有關詳細資訊,請參閱[群集範圍的文稿](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* **資料磚塊匯入錯誤:無法從「pandas._libs.tslibs」匯入名稱「Timedelta」:** 如果您在使用自動機器學習時看到此錯誤,請運行筆記本中的以下兩行:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **資料磚塊匯入錯誤:沒有名為「pandas.core.indexes」的模組**:如果您在使用自動機器學習時看到此錯誤:

    1. 執行此指令以在 Azure 資料磚區列安裝兩個套件:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 分離,然後將群集重新連接到筆記本。
    
    如果這些步驟不能解決問題,請嘗試重新啟動群集。

* **資料塊 FailtoSendFeather:** 如果在`FailToSendFeather`讀取 Azure 資料塊群集上的資料時看到錯誤,請參閱以下解決方案:
    
    * 將`azureml-sdk[automl]`包升級到最新版本。
    * 添加`azureml-dataprep`版本1.1.8或以上。
    * 添加`pyarrow`版本0.11或以上。

## <a name="create-and-manage-workspaces"></a>建立及管理工作區

> [!WARNING]
> 不支援將 Azure 機器學習工作區移動到其他訂閱,或將所屬訂閱移動到新租戶。 這樣做可能會導致錯誤。

* **Azure 門戶**:如果直接從 SDK 或門戶的共用連結查看工作區,則將無法在擴展中查看包含訂閱資訊的正常**概述**頁面。 您也無法切換至另一個工作區。 如果需要查看其他工作區,直接轉到[Azure 機器學習工作室](https://ml.azure.com)並搜索工作區名稱。

## <a name="set-up-your-environment"></a>設定您的環境

* **創建 AmlCompute 時遇到問題**:在 GA 發表之前從 Azure 門戶創建 Azure 機器學習工作區的某些使用者可能無法在該工作區中創建 AmlCompute,這一的可能性很小。 您可以針對服務提出支援請求,或者通過門戶或 SDK 創建新工作區,以便立即解除阻止自己。

## <a name="work-with-data"></a>使用資料

### <a name="overloaded-azurefile-storage"></a>重載 Azure 檔案儲存

如果收到錯誤`Unable to upload project files to working directory in AzureFile because the storage is overloaded`,請應用以下解決方法。

如果您將檔案共享用於其他工作負荷(如資料傳輸),則建議使用 blob,以便檔案共用可以自由地用於提交運行。 您還可以在兩個不同的工作區之間拆分工作負荷。

### <a name="passing-data-as-input"></a>將資料作為輸入傳遞

*  **類型錯誤:檔未找到:沒有此類檔或目錄**:如果您提供的檔案路徑不在檔案所在的位置,則會發生此錯誤。 您需要確保引用檔的方式與在計算目標上裝載數據集的位置一致。 為了確保確定性狀態,我們建議在將數據集安裝到計算目標時使用抽象路徑。 例如,在以下代碼中,我們將資料集載入到計算目標檔案系統的根目錄下`/tmp`。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    如果不包括前導前斜杠"/",則需要在計算目標`/mnt/batch/.../tmp/dataset`上對工作目錄進行前綴,以指示要將數據集裝載到何處。

### <a name="data-labeling-projects"></a>資料標籤項目

|問題  |解決方案  |
|---------|---------|
|只能使用在 Blob 資料儲存上建立的資料集     |  這是當前版本的已知限制。       |
|創建後,專案長時間顯示"初始化"     | 手動刷新頁面。 初始化應以大約每秒 20 個數據點的速度進行。 缺少自動刷新是一個已知問題。         |
|檢視影像時,不會顯示新標記的影像     |   要載入所有標記的影像,請選擇 **「第一」** 按鈕。 「**第一個**」按鈕將帶您返回清單的前面,但載入所有標記的資料。      |
|在標記物件檢測時按下 Esc 鍵會在左上角創建零尺寸標籤。 在此狀態下提交標籤失敗。     |   按下標籤旁邊的交叉標記,刪除標籤。  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

已知問題：

* **較長的計算準備時間**:當您第一次連接到或創建計算目標時,可能需要幾分鐘甚至更長時間。 

## <a name="train-models"></a>將模型定型

* **模組錯誤(未命名模組):** 如果在 Azure ML 中提交實驗時遇到模組錯誤,則意味著訓練文本希望安裝包,但不會添加。 提供包名稱後,Azure ML 將在用於定型運行的環境中安裝包。 

    如果使用[「估計器」](concept-azure-machine-learning-architecture.md#estimators)提交實驗,則可以根據要從哪個源安裝包`pip_packages``conda_packages`,通過或估算器中的參數指定包名稱。 您還可以使用使用`conda_dependencies_file`或`pip_requirements_file`使用 參數在 txt 檔案中列出所有 pip 要求,指定包含所有依賴項的 yml 檔。 如果自己的 Azure ML 環境物件要覆蓋估計器使用的預設映射,則可以通過估計器建`environment`構函數的 參數指定該環境。

    Azure ML 還為 Tensorflow、PyTorch、鏈條和 SKLearn 提供了特定於框架的估算器。 使用這些估計器將確保核心框架依賴項代表您安裝在用於培訓的環境中。 您可以選擇如上所述指定額外的依賴項。 
 
    Azure ML 維護的 Docker 映像及其內容可以在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到。
    特定於框架的依賴項列在各自的框架文檔中 -[鏈子](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、PyTorch、TensorFlow、SKLearn。 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)

    > [!Note]
    > 如果您認為某個包是通用的,可以添加到 Azure ML 維護的圖像和環境中,請在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中引發 GitHub 問題。 
 
* **NameError(未定義名稱)、屬性錯誤(物件沒有屬性):** 此異常應來自您的訓練文稿。 可以查看 Azure 門戶中的日誌檔,以獲取有關未定義的特定名稱或屬性錯誤的詳細資訊。 在 SDK 中,您`run.get_details()`可以使用 查看錯誤訊息。 這還將列出為運行而生成的所有日誌檔。 請務必查看您的訓練腳本,並在重新提交運行之前修復錯誤。 

* **Horovod 已被關閉**:在大多數情況下,如果您遇到"AbortedError:Horovod 已關閉",此異常意味著導致霍羅沃德關閉的一個進程存在潛在的異常。 MPI 作業中的每個排名都獲取其在 Azure ML 中的專用日誌檔。 這些紀錄被命名為`70_driver_logs`。 在分散式培訓的情況下,日誌名稱後綴為,`_rank`以便更輕鬆地區分日誌。 要尋找導致霍羅福關閉的確切錯誤,請瀏覽所有紀錄檔,並在driver_log檔案末尾尋找`Traceback`。 這些檔之一將為您提供實際的基礎異常。 

* **運行或實驗刪除**:實驗可以通過「存檔實驗」按鈕使用[實驗.存檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--)方法,或者通過「存檔實驗」按鈕從 Azure 機器學習工作室用戶端中的「實驗」選項卡檢視存檔。 此操作將隱藏清單查詢和檢視中的實驗,但不會將其刪除。

    當前不支援永久刪除單個實驗或運行。 有關刪除工作區資源的詳細資訊,請參閱[匯出或刪除機器學習服務工作區資料](how-to-export-delete-data.md)。

* **指標文檔太大**:Azure 機器學習對指標物件的大小有內部限制,可以從訓練運行中一次記錄。 如果在紀錄清單值指標時遇到「指標文檔太大」錯誤,請嘗試將清單拆分為較小的塊,例如:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    在內部,Azure ML 將具有相同指標名稱的塊串聯到連續清單中。

## <a name="automated-machine-learning"></a>自動化機器學習

* **張力流**:自動機器學習當前不支援張量流版本 1.13。 安裝此版本將導致包依賴項停止工作。 我們正在努力在未來的版本中解決此問題。

* **實驗圖表**:自動 ML 實驗反覆運算中顯示的二進位分類圖表(精度召回、ROC、增益曲線等)自 4 月 12 日起在用戶介面中未正確呈現。 圖表圖當前顯示反向結果,其中性能更好的模型顯示,結果較低。 一項決議正在調查中。

* **數據塊取消自動機器學習運行**:當您在 Azure 資料塊上使用自動機器學習功能時,要取消運行並啟動新的實驗運行,請重新啟動 Azure 數據塊群集。

* **數據磚塊>10 次自動機器學習反覆運算**:在自動機器學習設置中,如果具有 10 次以上的`show_output``False`反覆運算,則在提交運行時設置為。

* **Azure 機器學習 SDK 和自動機器學習的數據磚塊小部件**:Databricks 筆記本中不支援 Azure 機器學習 SDK 小部件,因為筆記本無法解析 HTML 小部件。 您可以在 Azure 資料磚區塊筆記的儲存格中使用這個 Python 代碼在門戶中檢視小元件:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>部署和提供模型

針對以下錯誤執行以下操作:

|錯誤  | 解決方案  |
|---------|---------|
|部署 Web 服務時映像產生失敗     |  將 pynacl=1.2.1"作為點相依檔檔加入 Conda 檔以映像設定       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   將部署中使用的 VM 的 SKU 更改為具有更多記憶體的 SKU。 |
|FPGA 故障     |  您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單：https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>更新 AKS 群組集中的 Azure 機器學習元件

必須手動應用 Azure 庫伯內斯服務群集中安裝的 Azure 機器學習元件的更新。 

可以通過從 Azure 機器學習工作區分離群集,然後將群集重新附加到工作區來應用這些更新。 如果在群集中啟用了 TLS,則重新連接群集時需要提供 TLS/SSL 證書和私鑰。 

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

如果不再有 TLS/SSL 證書和私鑰,或者正在使用 Azure 機器學習生成的證書`kubectl`,則可以通過使用 連接到群`azuremlfessl`集並檢索機密 來在分離群集之前檢索檔。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>庫伯內特以基-64編碼格式存儲秘密。 在將機密和元件提供到`cert.pem``key.pem``attach_config.enable_ssl`之前,您需要對機密和元件進行基礎解碼。 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure 庫伯內斯服務中的 Web 服務失敗

Azure Kubernetes 服務中的許多 Web`kubectl`服務故障可以通過使用 連接到群集來調試。 您可以透過執行 Azure`kubeconfig.json`庫伯奈斯服務叢集

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
