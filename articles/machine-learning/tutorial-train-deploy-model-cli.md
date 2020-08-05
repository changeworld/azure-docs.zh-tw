---
title: 透過 CLI 訓練和部署模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure CLI 的機器學習延伸模組，透過命令列訓練、註冊和部署模型。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: b4167f8958f7a1613c4d48625f7a79a02c7588d0
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543396"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>教學課程：透過 CLI 訓練和部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教學課程中，您會使用 Azure CLI 的機器學習延伸模組來訓練、註冊和部署模型。

本教學課程中的 Python 訓練指令碼使用 [scikit-learn](https://scikit-learn.org/) 來訓練基本模型。 本教學課程的重點並非指令碼或模型，而是透過 CLI 使用 Azure Machine Learning 的流程。

了解如何執行下列動作：

> [!div class="checklist"]
> * 安裝機器學習延伸模組
> * 建立 Azure Machine Learning 工作區
> * 建立用來訓練模型的計算資源
> * 定義和註冊用來訓練模型的資料集
> * 開始執行訓練
> * 註冊及下載模型
> * 將模型部署為 Web 服務
> * 使用 Web 服務為資料評分

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從您的**本機環境**使用本文件中的 CLI 命令，您需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果您使用 [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，CLI 可透過瀏覽器存取，並在雲端運作。

## <a name="download-the-example-project"></a>下載範例專案

針對本教學課程，請下載 [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) 專案。 本教學課程中的步驟會使用 `examples/cli-train-deploy` 目錄中的檔案。

若要取得檔案的本機複本，請[下載 .zip 封存](https://github.com/microsoft/MLOps/archive/master.zip)，或是使用下列 Git 命令來複製存放庫：

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>訓練檔案

專案的 `examples/cli-train-deploy` 目錄包含下列檔案，這些檔案會在訓練模型時使用：

* `.azureml\mnist.runconfig`:「執行設定」檔。 這個檔案會定義訓練模型所需的執行階段環境。 在此範例中，其也會將用來訓練模型的資料掛接到訓練環境。
* `scripts\train.py`:訓練指令碼。 此檔案會訓練模型。
* `scripts\utils.py`:訓練指令碼使用的協助程式檔案。
* `.azureml\conda_dependencies.yml`:定義執行訓練指令碼所需的軟體相依性。
* `dataset.json`:資料集定義。 用來在 Azure Machine Learning 工作區中註冊 MNIST 資料集。

### <a name="deployment-files"></a>部署檔案

存放庫包含下列檔案，用來將訓練後的模型部署為 Web 服務：

* `aciDeploymentConfig.yml`:「部署設定」檔。 這個檔案會定義模型所需的裝載環境。
* `inferenceConfig.json`:「推斷設定」檔。 這個檔案會定義服務用來以模型為資料評分的軟體環境。
* `score.py`:接受傳入資料、使用模型進行評分，然後傳回回應的 Python 指令碼。
* `scoring-env.yml`:執行模型和 `score.py` 指令碼所需的 conda 相依性。
* `testdata.json`:可用來測試所部署 Web 服務的資料檔案。

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶

您可藉由數種方式透過 CLI 向您的 Azure 訂用帳戶進行驗證。 最基本的方式是使用瀏覽器，以互動方式進行驗證。 若要以互動方式進行驗證，請開啟命令列或終端機，並使用下列命令：

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示需要瀏覽至 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，並輸入授權碼。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>安裝機器學習延伸模組

若要安裝機器學習延伸模組，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

若您收到延伸模組已安裝的訊息，請使用下列命令更新為最新版本：

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是 Azure 平台上資源的基本容器。 使用 Azure Machine Learning 時，資源群組會包含您的 Azure Machine Learning 工作區， 以及該工作區使用的其他 Azure 服務。 舉例來說，若您使用雲端式計算資源來訓練模型，該資源就會建立在資源群組中。

若要「建立新的資源群組」，請使用下列命令。 將 `<resource-group-name>` 取代成要用於此資源群組的名稱。 將 `<location>` 取代成要用於此資源群組的 Azure 區域：

> [!TIP]
> 建議您選取可使用 Azure Machine Learning 的區域。 如需資訊，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的回應類似下列 JSON：

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

如需使用資源群組的詳細資訊，請參閱 [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>建立工作區

若要建立新的工作區，請使用下列命令。 將 `<workspace-name>` 取代成要用於此工作區的名稱。 將 `<resource-group-name>` 取代成資源群組的名稱：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

此命令的輸出類似下列 JSON：

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

## <a name="connect-local-project-to-workspace"></a>將本機專案連線到工作區

透過終端機或命令提示字元，使用下列命令將目錄變更為 `cli-train-deploy` 目錄，然後連線到您的工作區：

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

此命令的輸出類似下列 JSON：

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

此命令會建立 `.azureml/config.json` 檔案，內含連線到工作區所需的資訊。 本教學課程中使用的其餘 `az ml` 命令將會使用此檔案，因此您不需要將工作區和資源群組新增到所有命令。

## <a name="create-the-compute-target-for-training"></a>建立訓練用的計算目標

此範例使用 Azure Machine Learning Compute 叢集來訓練模型。 若要建立新的計算叢集，請使用下列命令：

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

此命令的輸出類似下列 JSON：

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

此命令會建立名為 `cpu-cluster` 的新計算目標，最多包含四個節點。 所選取的 VM 大小會提供包含 GPU 資源的 VM。 如需 VM 大小的資訊，請參閱＜VM 類型和大小＞。

> [!IMPORTANT]
> 計算目標的名稱 (此案例中為 `cpu-cluster`) 相當重要；下一節所使用的 `.azureml/mnist.runconfig` 檔案會參考此名稱。

## <a name="define-the-dataset"></a>定義資料集

若要訓練模型，您可以使用資料集來提供訓練資料。 若要透過 CLI 建立資料集，您必須提供資料集定義檔案。 存放庫中提供的 `dataset.json` 檔案會使用 MNIST 資料建立新的資料集。 其建立的資料集名為 `mnist-dataset`。

若要使用 `dataset.json` 檔案註冊資料集，請使用下列命令：

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

此命令的輸出類似下列 JSON：

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
> 複製 `id` 項目的值，原因是您會在下一節中使用該值。

若要查看更完整的資料集範本，請使用下列命令：

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>參考資料集

若要讓資料集可在訓練環境中使用，您必須從 runconfig 檔案中參考該資料集。 `.azureml/mnist.runconfig` 檔案包含下列 YAML 項目：

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

變更 `id` 項目的值，使其與您註冊資料集時所傳回的值相符。 這個值會用來在訓練期間將資料載入計算目標。

此 YAML 會在訓練期間產生下列動作：

* 在訓練環境中掛接資料集 (以資料集的識別碼為基礎)，並在 `mnist` 環境變數中儲存掛接點的路徑。
* 使用 `--data-folder` 引數，將資料在訓練環境內的位置 (掛接點) 傳遞給指令碼。

runconfig 檔案也包含用來設定執行訓練時所用環境的資訊。 若您檢查此檔案，您將會發現其參考您稍早建立的 `cpu-compute` 計算目標。 其也會在訓練時列出要使用的節點數 (`"nodeCount": "4"`)，並包含列出執行訓練指令碼所需 Python 套件的 `"condaDependencies"` 區段。

> [!TIP]
> 雖然您可以手動建立 runconfig 檔案，但此範例中所使用的 runconfig 檔案使用存放庫內含的 `generate-runconfig.py` 檔案建立而成。 此檔案會取得註冊資料集的參考、透過程式設計方式建立執行設定，然後將其保存到檔案。

如需執行設定檔的詳細資訊，請參閱[設定及使用計算目標來將模型定型](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)。 如需完整的 JSON 參考，請參閱 [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。

## <a name="submit-the-training-run"></a>提交訓練執行

若要在 `cpu-cluster` 計算目標上開始執行訓練，請使用下列命令：

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

此命令會指定實驗的名稱 (`myexperiment`)。 實驗會在工作區中儲存此執行的相關資訊。

`-c mnist` 參數會指定 `.azureml/mnist.runconfig` 檔案。

`-t` 參數會將此執行的參考儲存在 JSON 檔案中，並且將會在接下來的步驟中使用該參考來註冊和下載模型。

在執行訓練的過程中，會從遠端計算資源上的訓練工作階段串流資訊。 該資訊的一部分類似下列文字：

```output
Predict the test set
Accuracy is 0.9185
```

此文字會從訓練指令碼記錄，並顯示模型的正確性。 其他模型會有不同的效能計量。

若您檢查訓練指令碼，您會發現其會在將訓練後的模型儲存到 `outputs/sklearn_mnist_model.pkl` 時，使用 Alpha 值。

模型已儲存到用來訓練該模型之計算目標上的 `./outputs` 目錄。 在此案例中，為 Azure 雲端中的 Azure Machine Learning Compute 執行個體。 訓練流程會自動將用來執行訓練之計算目標中 `./outputs` 目錄的內容上傳到您的 Azure Machine Learning 工作區。 其會儲存為實驗 (在此範例中為 `myexperiment`) 的一部分。

## <a name="register-the-model"></a>註冊模型

若要從您實驗的已儲存版本直接註冊模型，請使用下列命令：

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

此命令會以名為 `mymodel` 的新模型註冊，來註冊執行訓練時建立的 `outputs/sklearn_mnist_model.pkl` 檔案。 `--assets-path` 會在實驗中參考路徑。 在此案例中，實驗和執行資訊會從訓練命令建立的 `runoutput.json` 檔案載入。 `-t registeredmodel.json` 會建立參考此命令所建立新註冊模型的 JSON 檔案，並會由其他使用註冊模型的 CLI 命令使用。

此命令的輸出類似下列 JSON：

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

請注意針對模型傳回的版本號碼。 每次您使用此名稱註冊新模型時，版本號碼都會遞增。 例如，您可以使用下列命令從本機檔案下載模型並註冊該模型：

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

第一個命令會將註冊模型下載到目前的目錄。 檔案名稱為 `sklearn_mnist_model.pkl`，即您註冊模型時參考的檔案。 第二個命令會使用與先前註冊相同的名稱 (`mymodel`) 來註冊本機模型 (`-p "sklearn_mnist_model.pkl"`)。 這次，傳回的 JSON 資料會將版本列為第 2 版。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，請使用下列命令：

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> 您可能會收到「無法檢查 LocalWebservice 是否存在」或「無法建立 Docker 用戶端」的相關警告。 您可以放心地忽略這些警告，原因是您不會部署本機 Web 服務。

此命令會使用您先前註冊的第 1 版模型來部署名為 `myservice` 的新服務。

`inferenceConfig.yml` 檔案提供如何使用模型進行推斷的資訊。 舉例來說，其會參考輸入腳本 (`score.py`) 和軟體相依性。

如需此檔案結構的詳細資訊，請參閱[推斷設定結構描述](reference-azure-machine-learning-cli.md#inference-configuration-schema)。 如需輸入腳本的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

`aciDeploymentConfig.yml` 描述用來裝載服務的部署環境。 部署設定僅供您用於部署的計算類型使用。 在此案例中，會使用 Azure 容器執行個體。 如需詳細資訊，請參閱[部署設定結構描述](reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

部署流程需要幾分鐘的時間才能完成。

> [!TIP]
> 在此範例中，會使用 Azure 容器執行個體。 部署到 ACI 的過程會自動建立所需的 ACI 資源。 若您要改為部署到 Azure Kubernetes Service，您必須事先建立 AKS 叢集，並在 `az ml model deploy` 命令中指定該叢集。 如需部署到 AKS 的範例，請參閱[將模型部署到 Azure Kubernetes Sevice 叢集](how-to-deploy-azure-kubernetes-service.md) (機器翻譯)。

幾分鐘後，系統會傳回類似下列 JSON 的資訊：

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

從部署傳回的 `scoringUri` 是部署為 Web 服務之模型的 REST 端點。 您也可以使用下列命令取得此 URI：

```azurecli-interactive
az ml service show -n myservice
```

此命令會傳回相同的 JSON 文件，包括 `scoringUri`。

REST 端點可以用來將資料傳送到服務。 如需相關資訊，了解如何建立將資料傳送到服務的用戶端應用程式，請參閱[使用部署為 Web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md) (機器翻譯)

### <a name="send-data-to-the-service"></a>將資料傳送到服務

雖然您可以建立用戶端應用程式來呼叫端點，但機器學習服務 CLI 會提供可以用作測試用戶端的公用程式。 請使用下列命令，將 `testdata.json` 檔案中的資料傳送到服務：

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> 若您使用 PowerShell，請改為使用下列命令：
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

命令的回應類似 `[ 3 ]`。

## <a name="clean-up-resources"></a>清除資源

> [!IMPORTANT]
> 您所建立的資源可用來作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

### <a name="delete-deployed-service"></a>刪除已部署的服務

若您打算繼續使用 Azure Machine Learning 工作區，但想要清除已部署的服務來減少成本，請使用下列命令：

```azurecli-interactive
az ml service delete -n myservice
```

此命令會傳回包含已刪除服務名稱的 JSON 文件。 刪除服務可能需要幾分鐘的時間。

### <a name="delete-the-training-compute"></a>刪除訓練計算

若您打算繼續使用 Azure Machine Learning 工作區，但想要清除為訓練建立的 `cpu-cluster` 計算目標，請使用下列命令：

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

此命令會傳回包含已刪除計算目標識別碼的 JSON 文件。 刪除計算目標可能需要幾分鐘的時間。

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用所建立的資源，請刪除這些資源以免產生額外的費用。

若要刪除資源群組，以及所有在此文件中建立的 Azure 資源，請使用下列命令。 將 `<resource-group-name>` 取代成您稍早建立的資源群組名稱：

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>後續步驟

在這個 Azure Machine Learning 教學課程中，您使用機器學習 CLI 執行了下列工作：

> [!div class="checklist"]
> * 安裝機器學習延伸模組
> * 建立 Azure Machine Learning 工作區
> * 建立用來訓練模型的計算資源
> * 定義和註冊用來訓練模型的資料集
> * 開始執行訓練
> * 註冊及下載模型
> * 將模型部署為 Web 服務
> * 使用 Web 服務為資料評分

如需使用 CLI 的詳細資訊，請參閱[使用 Azure Machine Learning 的 CLI 延伸模組](reference-azure-machine-learning-cli.md)。
