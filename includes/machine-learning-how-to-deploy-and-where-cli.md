---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542760"
---
## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](../articles/machine-learning/how-to-manage-workspace.md)。
- 模型。 如果您沒有定型的模型，您可以使用[本教學](https://aka.ms/azml-deploy-cloud)課程中提供的模型和相依性檔案。
- [適用于 Machine Learning 服務的 Azure 命令列介面（CLI）延伸](../articles/machine-learning/reference-azure-machine-learning-cli.md)模組


## <a name="connect-to-your-workspace"></a>連線到您的工作區

依照 Azure CLI 檔中的指示來[設定您的訂](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)用帳戶內容。

然後執行：

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

查看您可以存取的工作區。

## <a name="register-your-model"></a><a id="registermodel"></a>註冊您的模型

已註冊的模型是組成模型的一或多個檔案所在的邏輯容器。 例如，如果您有儲存在多個檔案中的模型，您可以在工作區中將其註冊為單一模型。 註冊檔案之後，您就可以下載或部署已註冊的模型，並接收您註冊的所有檔案。

> [!TIP]
> 當您註冊模型時，您會提供雲端位置（從定型回合）或本機目錄的路徑。 此路徑只是為了在註冊過程中，尋找要上傳的檔案。 它不需要符合輸入腳本中使用的路徑。 如需詳細資訊，請參閱[在您的輸入腳本中尋找模型](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)檔案。

機器學習模型會在您的 Azure Machine Learning 工作區中註冊。 模型可以來自 Azure Machine Learning 或其他地方。 註冊模型時，您可以選擇性地提供有關模型的中繼資料。 `tags`接著， `properties` 您可以將套用至模型註冊的和字典用於篩選模型。

下列範例示範如何註冊模型。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>從 Azure ML 訓練執行註冊模型

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path`參數會參考模型的雲端位置。 在此範例中，會使用單一檔案的路徑。 若要在模型註冊中包含多個檔案，請將設定 `--asset-path` 為包含檔案的資料夾路徑。

### <a name="register-a-model-from-a-local-file"></a>從本機檔案註冊模型

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

若要在模型註冊中包含多個檔案，請將設定 `-p` 為包含檔案的資料夾路徑。

如需的詳細資訊 `az ml model register` ，請參閱[參考檔](/cli/azure/ext/azure-cli-ml/ml/model)。

## <a name="define-an-entry-script"></a>定義專案腳本

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>定義推斷設定

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

下列命令示範如何使用 CLI 來部署模型：

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此範例中，設定會指定下列設定：

* 模型需要 Python
* [輸入腳本](#define-an-entry-script)，用來處理傳送至已部署服務的 web 要求
* 描述推斷所需之 Python 套件的 Conda 檔案

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊，請參閱[如何使用自訂 docker 映射部署模型](../articles/machine-learning/how-to-deploy-custom-docker-image.md)。

## <a name="choose-a-compute-target"></a>選擇計算目標

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>定義部署設定

部署設定可用的選項會根據您選擇的計算目標而有所不同。

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

如需詳細資訊，請參閱[az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy)檔。

## <a name="deploy-your-model"></a>部署模型

您現在已準備好部署您的模型。

### <a name="using-a-registered-model"></a>使用已註冊的模型

如果您在 Azure Machine Learning 工作區中註冊您的模型，請將 "mymodel>： 1" 取代為您的模型名稱和其版本號碼。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>使用本機模型

如果您不想要註冊模型，可以在的 inferenceconfig.js中傳遞 "sourceDirectory" 參數，以指定用來為模型提供服務的本機目錄。

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>刪除資源

若要刪除已部署的 webservice，請使用 `az ml service <name of webservice>` 。

若要從您的工作區刪除已註冊的模型，請使用`az ml model delete <model id>`

深入瞭解[刪除 webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete)和[刪除模型](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)