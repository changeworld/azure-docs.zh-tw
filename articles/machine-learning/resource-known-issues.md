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
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455718"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>已知問題和 Azure 機器學習故障排除

本文可説明您查找和更正使用 Azure 機器學習時遇到的錯誤或故障。

## <a name="sdk-installation-issues"></a>SDK 安裝問題

**錯誤訊息：無法解除安裝 'PyYAML'**

適用于 Python 的 Azure 機器學習 SDK：PyYAML 是一個已安裝的專案。 因此，在有部分解決安裝的情況下，我們無法精確判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**錯誤訊息：`ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda 的 Python 3.7.4 發行版本存在中斷 azureml-sdk 安裝的錯誤。 此問題在此[GitHub 問題](https://github.com/ContinuumIO/anaconda-issues/issues/11195)中討論 此問題，可以通過使用此命令創建新的 Conda 環境來解決此問題：
```bash
conda create -n <env-name> python=3.7.3
```
使用 Python 3.7.3 創建 Conda 環境，該環境不存在 3.7.4 中的安裝問題。

## <a name="training-and-experimentation-issues"></a>培訓和實驗問題

### <a name="metric-document-is-too-large"></a>公制文檔太大
Azure 機器學習對可以從訓練運行中一次記錄的指標物件的大小有內部限制。 如果在記錄清單值指標時遇到"指標文檔太大"錯誤，請嘗試將清單拆分為較小的塊，例如：

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

在內部，Azure ML 將具有相同指標名稱的塊串聯到連續清單中。

### <a name="moduleerrors-no-module-named"></a>模組錯誤（未命名模組）
如果在 Azure ML 中提交實驗時遇到模組錯誤，則意味著訓練腳本希望安裝包，但不會添加。 提供包名稱後，Azure ML 將在用於定型運行的環境中安裝包。 

如果使用["估計器"](concept-azure-machine-learning-architecture.md#estimators)提交實驗，則可以根據要從哪個源安裝包，通過`pip_packages``conda_packages`或估算器中的參數指定包名稱。 您還可以使用使用`conda_dependencies_file`或使用`pip_requirements_file`參數在 txt 檔中列出所有 pip 要求，指定包含所有依賴項的 yml 檔。 如果自己的 Azure ML 環境物件要覆蓋估計器使用的預設映射，則可以通過估計器建構函式的`environment`參數指定該環境。

Azure ML 還為 Tensorflow、PyTorch、鏈條和 SKLearn 提供了特定于框架的估算器。 使用這些估計器將確保核心框架依賴項代表您安裝在用於培訓的環境中。 您可以選擇如上所述指定額外的依賴項。 
 
Azure ML 維護的 Docker 映射及其內容可以在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到。
特定于框架的依賴項列在各自的框架文檔中 -[鏈子](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、PyTorch、TensorFlow、SKLearn。 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)

> [!Note]
> 如果您認為某個包是通用的，可以添加到 Azure ML 維護的圖像和環境中，請在[AzureML 容器](https://github.com/Azure/AzureML-Containers)中引發 GitHub 問題。 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>名稱錯誤（未定義名稱）、屬性錯誤（物件沒有屬性）
此異常應來自您的培訓腳本。 可以查看 Azure 門戶中的日誌檔，以獲取有關未定義的特定名稱或屬性錯誤的詳細資訊。 在 SDK 中，您可以使用`run.get_details()`查看錯誤訊息。 這還將列出為運行而生成的所有日誌檔。 請務必查看您的訓練腳本，並在重新提交運行之前修復錯誤。 

### <a name="horovod-has-been-shut-down"></a>霍羅沃德已經關閉
在大多數情況下，如果您遇到"中止錯誤：霍羅沃德已關閉"此異常意味著在導致 Horovod 關閉的一個進程中存在潛在的異常。 MPI 作業中的每個排名都獲取其在 Azure ML 中的專用日誌檔。 這些日誌被命名為`70_driver_logs`。 在分散式培訓的情況下，日誌名稱尾碼為，`_rank`以便更輕鬆地區分日誌。 要查找導致霍羅福關閉的確切錯誤，請流覽所有日誌檔，並在driver_log檔末尾查找`Traceback`。 這些檔之一將為您提供實際的基礎異常。 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>AmlCompute 中的 NCv3 機器上的 SR-IOV 可用性，用於分散式培訓
Azure 計算已推出 NCv3 電腦的[SR-IOV 升級](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/)，客戶可以利用 Azure ML 的託管計算產品 （AmlCompute）。 這些更新將支援整個 MPI 堆疊，並使用 Infiniband RDMA 網路來改進多節點分散式培訓性能，尤其是深度學習。

查看[更新計畫](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/)，查看何時將為您的區域推出支援。

### <a name="run-or-experiment-deletion"></a>運行或實驗刪除
可以使用[實驗.存檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--)方法或通過"存檔實驗"按鈕從 Azure 機器學習工作室用戶端中的"實驗"選項卡視圖存檔實驗。 此操作將隱藏清單查詢和視圖中的實驗，但不會將其刪除。

當前不支援永久刪除單個實驗或運行。 有關刪除工作區資源的詳細資訊，請參閱[匯出或刪除機器學習服務工作區資料](how-to-export-delete-data.md)。

## <a name="azure-machine-learning-compute-issues"></a>Azure 機器學習計算問題
使用 Azure 機器學習計算 （Aml 計算） 的已知問題。

### <a name="trouble-creating-amlcompute"></a>創建 Aml 計算時遇到問題

在 GA 版本之前從 Azure 門戶創建 Azure 機器學習工作區的某些使用者可能無法在該工作區中創建 AmlCompute，這一的可能性很小。 您可以針對服務提出支援請求，或者通過門戶或 SDK 創建新工作區，以便立即解除阻止自己。

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>中斷：SR-IOV 升級到 AmlCompute 中的 NCv3 電腦

Azure 計算將從 2019 年 11 月初開始更新 NCv3 SKU，以支援所有 MPI 實現和版本，以及適用于 InfiniBand 的虛擬機器的 RDMA 謂詞。 這將需要短暫的停機時間 -[閱讀更多有關 SR-IOV 升級](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku)的資訊。

作為 Azure 機器學習託管計算產品 （AmlCompute） 的客戶，此時無需進行任何更改。 根據[更新計畫](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku)，您需要在培訓中計畫一次短暫的休息。 該服務將負責更新叢集節點上的 VM 映射，並自動擴展群集。 升級完成後，除了獲得更高的 InfiniBand 頻寬、更低的延遲和更好的分散式應用程式性能外，您還可以使用所有其他 MPI 分發（如帶有 Pytorch 的 OpenMPI）。

## <a name="azure-machine-learning-designer-issues"></a>Azure 機器學習設計器問題

設計器的已知問題。

### <a name="long-compute-preparation-time"></a>計算準備時間長

創建新計算或喚起離開計算需要時間，可能需要幾分鐘甚至更長的時間。 團隊正在努力優化。


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>無法運行僅包含資料集的實驗 

您可能希望僅運行一個實驗，其中包含用於視覺化資料集的資料集。 但是，今天不允許運行僅包含資料集的實驗。 我們正在積極解決這個問題。
 
在修復之前，您可以將資料集連接到任何資料轉換模組（在資料集中選擇列、編輯中繼資料、拆分資料等）並運行實驗。 然後，您可以視覺化資料集。 

下圖顯示了如何：![視覺化資料](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>映像建置失敗

部署 Web 服務時，映像建置失敗。 因應措施是將 "pynacl==1.2.1" 當成 pip 相依性新增到 Conda 檔案，以用於映像設定。

## <a name="deployment-failure"></a>部署失敗

如果您發現 `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`，請將部署中所使用 VM 的 SKU 變更為具有更多記憶體的 SKU。

## <a name="fpgas"></a>FPGA

您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單：https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>自動化機器學習

Tensor 流自動機器學習當前不支援張量流版本 1.13。 安裝此版本將導致包依賴項停止工作。 我們正在努力在未來的版本中解決此問題。

### <a name="experiment-charts"></a>實驗圖表

自動 ML 實驗反覆運算中顯示的二進位分類圖表（精度召回、ROC、增益曲線等）自 4 月 12 日起在使用者介面中未正確呈現。 圖表圖當前顯示反向結果，其中性能更好的模型顯示，結果較低。 一項決議正在調查中。

## <a name="datasets-and-data-preparation"></a>資料集和資料準備

這些是 Azure 機器學習資料集的已知問題。

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>類型錯誤：檔未找到：沒有此類檔或目錄

如果提供的檔路徑不在檔所在的位置，則會發生此錯誤。 您需要確保引用檔的方式與在計算目標上裝載資料集的位置一致。 為了確保確定性狀態，我們建議在將資料集安裝到計算目標時使用抽象路徑。 例如，在以下代碼中，我們將資料集裝載到計算目的檔案系統的根目錄下`/tmp`。 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

如果不包括前導前斜杠"/"，則需要在計算目標`/mnt/batch/.../tmp/dataset`上對工作目錄進行首碼，以指示要將資料集裝載到何處。

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>無法從 HTTP 或 ADLS 第 2 代讀取 Parquet 檔

AzureML DataPrep SDK 版本 1.1.25 中存在一個已知問題，該問題通過從 HTTP 或 ADLS Gen 2 讀取 Parquet 檔來創建資料集時導致失敗。 它將失敗與`Cannot seek once reading started.`。 要解決此問題，請升級到`azureml-dataprep`高於 1.1.26 的版本，或降級到低於 1.1.24 的版本。

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>類型錯誤：裝載（）得到了一個意外的關鍵字參數"invocation_id"

如果 和 之間存在`azureml-core`不相容的版本，`azureml-dataprep`則會發生此錯誤。 如果看到此錯誤，則將`azureml-dataprep`包升級到較新版本（大於或等於 1.1.29）。

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks 與 Azure Machine Learning 問題。

### <a name="failure-when-installing-packages"></a>安裝包時失敗

安裝更多包時，Azure 機器學習 SDK 安裝在 Azure 資料塊上失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 為了避免安裝錯誤，請通過凍結庫版本來安裝包。 此問題與資料磚塊無關，而不是與 Azure 機器學習 SDK 相關。 您可能也會在其他庫中遇到此問題。 範例：

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

或者，如果您繼續面臨 Python 庫的安裝問題，則可以使用 init 腳本。 此方法不受官方支援。 有關詳細資訊，請參閱[群集範圍的腳本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

### <a name="cancel-an-automated-machine-learning-run"></a>取消自動機器學習運行

在 Azure 資料塊上使用自動機器學習功能時，要取消運行並開始新的實驗運行，請重新開機 Azure 資料塊群集。

### <a name="10-iterations-for-automated-machine-learning"></a>>10 次自動化機器學習反覆運算

在自動機器學習設置中，如果具有 10 次以上的反覆運算，則在`show_output`提交`False`運行時設置為。

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>用於 Azure 機器學習 SDK 和自動機器學習的小工具

資料磚塊筆記本不支援 Azure 機器學習 SDK 小部件，因為筆記本無法解析 HTML 小部件。 您可以在 Azure 資料磚塊筆記本儲存格中使用此 Python 代碼在門戶中查看小部件：

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>導入錯誤：無法從"熊貓._libs.tslibs"導入名稱"Timedelta"

如果您在使用自動機器學習時看到此錯誤，請運行筆記本中的以下兩行：
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>導入錯誤：沒有名為"pandas.core.indexs"的模組

如果您在使用自動機器學習時看到此錯誤：

1. 運行此命令以在 Azure 資料磚塊群集中安裝兩個包：

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. 分離，然後將群集重新連接到筆記本。

如果這些步驟不能解決問題，請嘗試重新開機群集。

### <a name="failtosendfeather"></a>失敗發送費

如果在讀取 Azure`FailToSendFeather`資料磚塊群集上的資料時出現錯誤，請參閱以下解決方案：

* 將`azureml-sdk[automl]`包升級到最新版本。
* 添加`azureml-dataprep`版本 1.1.8 或以上。
* 添加`pyarrow`版本 0.11 或以上。

## <a name="azure-portal"></a>Azure 入口網站

如果您從 SDK 或入口網站的共用連結直接檢視工作區，將無法在延伸模組中檢視包含訂用帳戶資訊的一般 [概觀] 頁面。 您也無法切換至另一個工作區。 如果需要查看另一個工作區，解決方法是直接轉到[Azure 機器學習工作室](https://ml.azure.com)並搜索工作區名稱。

## <a name="diagnostic-logs"></a>診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 要查看某些日誌，請訪問[Azure 機器學習工作室](https://ml.azure.com)並轉到工作區，然後選擇**工作區>實驗>運行>日誌**。  

> [!NOTE]
> Azure 機器學習在培訓期間從各種來源（如自動ML 或運行訓練作業的 Docker 容器）記錄資訊。 許多這些日誌沒有記錄。 如果您遇到問題並聯系 Microsoft 支援人員，他們也許能夠在故障排除期間使用這些日誌。

## <a name="activity-logs"></a>活動記錄

Azure 機器學習工作區中的某些操作不會將資訊記錄到__活動日誌__。 例如，啟動訓練運行或註冊模型。

其中一些操作將顯示在工作區的 __"活動"__ 區域中，但它們並不指示發起活動的人員。

## <a name="resource-quotas"></a>資源配額

深入了解使用 Azure Machine Learning 時可能會遇到的[資源配額](how-to-manage-quotas.md)。

## <a name="authentication-errors"></a>驗證錯誤

如果您從遠端工作執行計算目標的管理作業，您會收到下列錯誤的其中一個：

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

例如，如果您嘗試從 ML 管線建立或連結為遠端執行所提交的計算目標，您會收到錯誤。

## <a name="overloaded-azurefile-storage"></a>重載 Azure 檔存儲

如果收到錯誤`Unable to upload project files to working directory in AzureFile because the storage is overloaded`，請應用以下解決方法。

如果您將檔共用用於其他工作負荷（如資料傳輸），則建議使用 blob，以便檔共用可以自由地用於提交運行。 您還可以在兩個不同的工作區之間拆分工作負荷。

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure 庫伯內斯服務中的 Web 服務失敗

Azure Kubernetes 服務中的許多 Web 服務故障可以通過使用`kubectl`連接到群集來調試。 您可以通過運行 Azure`kubeconfig.json`庫伯奈斯服務群集獲取

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>更新 AKS 群集中的 Azure 機器學習元件

必須手動應用 Azure 庫伯內斯服務群集中安裝的 Azure 機器學習元件的更新。 

可以通過從 Azure 機器學習工作區分離群集，然後將群集重新附加到工作區來應用這些更新。 如果在群集中啟用了 SSL，則重新連接群集時需要提供 SSL 憑證和私密金鑰。 

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

如果不再有 SSL 憑證和私密金鑰，或者正在使用 Azure 機器學習生成的證書，則可以通過使用`kubectl`連接到群集並檢索機密`azuremlfessl`來在分離群集之前檢索檔。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>庫伯內特以基-64編碼格式存儲秘密。 在將機密和元件提供到`cert.pem``key.pem``attach_config.enable_ssl`之前，您需要對機密和元件進行基礎解碼。 

## <a name="labeling-projects-issues"></a>標記專案問題

標記專案的已知問題。

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>只能使用在 Blob 資料存儲上創建的資料集

這是當前版本的已知限制。

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>創建後，專案長時間顯示"初始化"

手動刷新頁面。 初始化應以大約每秒 20 個資料點的速度進行。 缺少自動刷新是一個已知問題。 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>查看圖像時，不會顯示新標記的圖像

要載入所有標記的圖像，請選擇 **"第一"** 按鈕。 "**第一個**"按鈕將帶您返回清單的前面，但載入所有標記的資料。

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>在標記物件檢測時按下 Esc 鍵會在左上角創建零尺寸標籤。 在此狀態下提交標籤失敗。

按一下標籤旁邊的交叉標記，刪除標籤。

## <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure 機器學習工作區移動到其他訂閱，或將所屬訂閱移動到新租戶。 這樣做可能會導致錯誤。
