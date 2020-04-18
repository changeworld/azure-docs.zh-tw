---
title: 從 CLI 培訓與部署模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure CLI 的機器學習擴展從命令列訓練、註冊和部署模型。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617725"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>教學:從 CLI 訓練和部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教學中,使用 Azure CLI 的機器學習擴展來訓練、註冊和部署模型。

本教學中的 Python 訓練文稿使用[學習學的 scikit](https://scikit-learn.org/)學習來訓練基本模型。 本教程的重點不是腳本或模型,而是使用 CLI 與 Azure 機器學習一起使用的過程。

了解如何執行下列動作：

> [!div class="checklist"]
> * 安裝機器學習延伸
> * 建立 Azure Machine Learning 工作區
> * 建立用於訓練模型的計算資源
> * 定義和註冊用於訓練模型的資料集
> * 開始訓練執行
> * 註冊並下載模型
> * 將模型部署為 Web 服務
> * 使用 Web 服務對資料進行評分

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 要從**本地環境**使用此文件中的 CLI 指令,需要 Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用 Azure[雲外殼](https://azure.microsoft.com//features/cloud-shell/),則 CLI 將透過瀏覽器訪問並生活在雲中。

## <a name="download-the-example-project"></a>下載範例項目

對於本教學,下載專案[https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps)。 `examples/cli-train-deploy`本教程中的步驟使用目錄中的檔。

要取得檔案的本地複本。[請下載 .zip 存檔](https://github.com/microsoft/MLOps/archive/master.zip),或使用以下 Git 命令克隆記憶體庫:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>訓練檔案

專案中`examples/cli-train-deploy`的目錄包含以下檔案,這些檔案在訓練模型時使用:

* `.azureml\mnist.runconfig`:__執行設定檔__。 此檔定義訓練模型所需的運行時環境。 在此示例中,它還將用於訓練模型的數據裝載到訓練環境中。
* `scripts\train.py`:訓練腳本。 此文件訓練模型。
* `scripts\utils.py`:訓練文本使用的幫助程式檔。
* `.azureml\conda_dependencies.yml`:定義運行訓練腳本所需的軟體依賴項。
* `dataset.json`:數據集定義。 用於在 Azure 機器學習工作區中註冊 MNIST 數據集。

### <a name="deployment-files"></a>部署檔案

儲存函式庫包含以下檔案,這些檔案用於將訓練的模型部署為 Web 服務:

* `aciDeploymentConfig.yml`:__部署設定檔__。 此檔定義模型所需的託管環境。
* `inferenceConfig.json`:__推理設定檔__。 此檔定義服務用於使用模型對數據進行評分的軟體環境。
* `score.py`:接受傳入數據的 python 文本,使用模型對它進行評分,然後返回回應。
* `scoring-env.yml`:運行模型和`score.py`腳本所需的 conda 依賴項。
* `testdata.json`:可用於測試已部署的 Web 服務的資料檔。

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶

有幾種方法可以從 CLI 對 Azure 訂閱進行身份驗證。 最基本的是使用瀏覽器進行互動式身份驗證。 要進行互動身份驗證,請使用以下命令開啟命令列或終端機:

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則,您需要打開瀏覽器並按照命令列上的說明進行操作。 這些說明涉及瀏[https://aka.ms/devicelogin](https://aka.ms/devicelogin)覽和輸入授權代碼。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>安裝機器學習延伸

要安裝機器學習擴展,請使用以下命令:

```azurecli-interactive
az extension add -n azure-cli-ml
```

如果收到一條消息,指出擴展已安裝,請使用以下命令更新到最新版本:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>建立資源群組

資源組是 Azure 平臺上資源的基本容器。 使用 Azure 機器學習時,資源組將包含 Azure 機器學習工作區。 它還將包含工作區使用的其他 Azure 服務。 例如,如果使用基於雲的計算資源訓練模型,則該資源是在資源組中創建的。

要__建立新的資源組__,請使用以下命令。 取代為`<resource-group-name>`用於此資源組的名稱。 取代為`<location>`要用於此資源群組的 Azure 區域:

> [!TIP]
> 應選擇 Azure 機器學習可用的區域。 有關詳細資訊,請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的回應類似於以下 JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

有關使用資源群組的詳細資訊,請參閱 az[群組](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>建立工作區

要創建新工作區,請使用以下命令。 替換為`<workspace-name>`要用於此工作區的名稱。 取代為`<resource-group-name>`資源群組的名稱:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

此指令的輸出類似於以下 JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>將本地端項目連線到工作區

從終端或指令提示符中,使用以下指令將目錄更改為`cli-train-deploy`目錄,然後連接到工作區:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

此指令的輸出類似於以下 JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

此命令創建一`.azureml/config.json`個檔,其中包含連接到工作區所需的資訊。 本教學中的其餘`az ml`命令將使用此檔,因此您不必將工作區和資源組添加到所有命令。

## <a name="create-the-compute-target-for-training"></a>為訓練建立計算目標

此示例使用 Azure 機器學習計算群集來訓練模型。 要建立新的計算叢集,請使用以下指令:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

此指令的輸出類似於以下 JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

此命令創建一個名為的新`cpu-cluster`計算目標,最多有四個節點。 所選 VM 大小提供具有 GPU 資源的 VM。 有關 VM 大小的資訊,請參閱 [VM 類型和大小]。

> [!IMPORTANT]
> 計算目標的名稱(`cpu-cluster`在本例中)很重要;它由下一節中`.azureml/mnist.runconfig`使用的檔引用。

## <a name="define-the-dataset"></a>定義資料集

要訓練模型,可以使用數據集提供訓練數據。 要從 CLI 創建資料集,必須提供數據集定義檔。 回購`dataset.json`中提供的檔案使用 MNIST 資料創建新數據集。 它建立的資料集`mnist-dataset`名稱 。

要使用檔案註冊資料集,`dataset.json`請使用以下指令:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

此指令的輸出類似於以下 JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> 複製`id`條目的值,因為它在下一節中使用。

要檢視資料集的更全面的樣本,請使用以下命令:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>參考資料集

要使資料集在訓練環境中可用,必須從 runconfig 檔中引用它。 此檔`.azureml/mnist.runconfig`包含以下 YAML 項目:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

更改`id`條目的值以匹配註冊數據集時返回的值。 此值用於在訓練期間將數據載入到計算目標中。

此 YAML 在訓練期間會導致以下操作:

* 在訓練環境中裝載數據集(基於資料集的 ID),並將路徑存儲在環境變數中`mnist`裝載點的路徑。
* 使用`--data-folder`參數將數據(裝載點)的位置傳遞給腳本。

runconfig 檔還包含用於設定訓練執行中使用的環境的資訊。 如果檢查此檔案,您將看到它引用您之前創建`cpu-compute`的計算目標。 它還列出了訓練時要使用的節點數 (),`"nodeCount": "4"`並包含一`"condaDependencies"`個部分 ,其中列出了運行訓練腳本所需的 Python 包。

> [!TIP]
> 雖然可以手動創建 runconfig 檔,但此範例中的該檔是使用儲存庫中`generate-runconfig.py`包含的檔案創建的。 此檔獲取對已註冊數據集的引用,以程式方式創建運行配置,然後將其保留為檔。

有關執行設定檔的詳細資訊,請參閱[為模型培訓設定和使用計算目標](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)。 有關完整的 JSON 引用,請參閱[runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。

## <a name="submit-the-training-run"></a>提交訓練執行

在`cpu-cluster`計算目標上啟動訓練執行,請使用以下指令:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

這個指令指定實驗的名稱`myexperiment`。 實驗在工作區中存儲有關此運行的資訊。

參數`-c mnist`指定`.azureml/mnist.runconfig`檔案 。

該`-t`參數在 JSON 檔中存儲對此運行的引用,並將在接下來的步驟中使用以註冊和下載模型。

當訓練運行過程中,它會從遠端計算資源上的培訓課程中流式傳輸資訊。 部份資訊類似於以下文字:

```output
Predict the test set
Accuracy is 0.9185
```

此文本從訓練腳本中記錄,並顯示模型的準確性。 其他模型將具有不同的性能指標。

如果檢查訓練腳本,您會注意到,當訓練模型存儲到`outputs/sklearn_mnist_model.pkl`時,它也會使用 alpha 值。

模型已保存到訓練模型的計算`./outputs`目標上的目錄中。 在這種情況下,Azure 雲中的 Azure 機器學習計算實例。 培訓過程會自動將`./outputs`目錄的內容從進行訓練的計算目標上載到 Azure 機器學習工作區。 它儲存作為實驗的一部分(`myexperiment`在此範例中)。

## <a name="register-the-model"></a>註冊模型

要直接從實驗中存儲的版本註冊模型,請使用以下命令:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

此命令將訓練運行`outputs/sklearn_mnist_model.pkl`創建的檔註冊為`mymodel`名為 的新模型註冊。 引用`--assets-path`實驗中的路徑。 在這種情況下,實驗和運行資訊從訓練命令創建`runoutput.json`的檔載入。 創建`-t registeredmodel.json`JSON 檔,該檔引用此命令創建的新註冊模型,並且由使用已註冊模型的其他 CLI 命令使用。

此指令的輸出類似於以下 JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>模型版本設定

請注意為模型返回的版本號。 每次註冊使用此名稱的新模型時,版本都會遞增。 例如,可以使用以下命令下載模型並從本地檔案註冊它:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

第一個命令將已註冊的模型下載到當前目錄。 檔名為`sklearn_mnist_model.pkl`,這是註冊模型時引用的檔。 第二個指令註冊與上一個`-p "sklearn_mnist_model.pkl"`註冊 ( ) 同`mymodel`名的本地模型 ( ) 。 這一次,返回的 JSON 數據將版本列為 2。

## <a name="deploy-the-model"></a>部署模型

要部署模型,請使用以下命令:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> 您可能會收到有關"無法檢查本地 Web 服務存在" 或「 無法建立 Docker 用戶端」 的警告。 您可以安全地忽略這一點,因為您沒有部署本地 Web 服務。

此命令使用以前註冊的模型的版本`myservice`1 部署名為 的新服務。

該檔`inferenceConfig.yml`提供有關如何使用模型進行推理的資訊。 例如,它引用條目腳本`score.py`() 和軟體依賴項。

關於此檔案結構的詳細資訊,請參考[推理設定架構](reference-azure-machine-learning-cli.md#inference-configuration-schema)。 有關項目文稿的詳細資訊,請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md#prepare-to-deploy)。

描述了`aciDeploymentConfig.yml`用於託管服務的部署環境。 部署配置特定於用於部署的計算類型。 在這種情況下,使用 Azure 容器實例。 有關詳細資訊,請參閱[部署設定架構](reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

部署過程需要幾分鐘才能完成。

> [!TIP]
> 在此範例中,使用 Azure 容器實例。 部署到 ACI 會自動建立所需的 ACI 資源。 如果要改為部署到 Azure 庫伯奈斯服務,則必須提前創建 AKS 群集,並將其`az ml model deploy`指定為命令的 一部分。 有關部署到 AKS 的範例,請參閱[將模型部署到 Azure 庫伯奈斯服務群集](how-to-deploy-azure-kubernetes-service.md)。

幾分鐘后,返回類似於以下 JSON 的資訊:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>評分 URI

從`scoringUri`部署返回的是作為 Web 服務部署的模型的 REST 終結點。 您還可以使用以下指令取得此 URI:

```azurecli-interactive
az ml service show -n myservice
```

這個指令傳回相同的 JSON`scoringUri`文件,包括 。

REST 終結點可用於將數據發送到服務。 有關建立將資料傳送到服務的用戶端應用程式的資訊,請參閱[使用為 Web 服務部署的 Azure 機器學習模型](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>傳送到服務

雖然您可以創建用戶端應用程式來調用終結點,但機器學習 CLI 提供了一個可以充當測試客戶端的實用程式。 使用以下指令將`testdata.json`檔案中的資料傳送到服務:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> 如果使用 PowerShell,請使用以下指令:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

從指令的回應類似於`[ 3 ]`。

## <a name="clean-up-resources"></a>清除資源

> [!IMPORTANT]
> 您所建立的資源可用來作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

### <a name="delete-deployed-service"></a>移除已部署的服務

如果計劃繼續使用 Azure 機器學習工作區,但希望刪除已部署的服務以降低成本,請使用以下命令:

```azurecli-interactive
az ml service delete -n myservice
```

此命令返回包含已刪除服務名稱的 JSON 文檔。 可能需要幾分鐘時間才能刪除服務。

### <a name="delete-the-training-compute"></a>刪除訓練計算

如果計劃繼續使用 Azure 機器學習工作區,但希望刪除為培訓`cpu-cluster`創建的 計算目標,請使用以下命令:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

此命令返回包含已刪除計算目標的 ID 的 JSON 文檔。 可能需要幾分鐘時間才能刪除計算目標。

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用所建立的資源,請刪除這些資源,以免產生額外費用。

要刪除資源群組以及本文件中創建的所有 Azure 資源,請使用以下命令。 取代為`<resource-group-name>`前面建立的資源群組的名稱:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>後續步驟

在此 Azure 機器學習教學中,您將機器學習 CLI 用於以下任務:

> [!div class="checklist"]
> * 安裝機器學習延伸
> * 建立 Azure Machine Learning 工作區
> * 建立用於訓練模型的計算資源
> * 定義和註冊用於訓練模型的資料集
> * 開始訓練執行
> * 註冊並下載模型
> * 將模型部署為 Web 服務
> * 使用 Web 服務對資料進行評分

有關使用 CLI 的詳細資訊,請參閱[使用 Azure 機器學習的 CLI 延伸](reference-azure-machine-learning-cli.md)。
