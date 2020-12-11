---
title: 將 ML 模型部署至 Kubernetes 服務
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Kubernetes Service 將 Azure Machine Learning 模型部署為 web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: f35943370c3f89fdf40844d551c3ed56b7cd26ae
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032876"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>將模型部署到 Azure Kubernetes Service 叢集

瞭解如何使用 Azure Machine Learning 在 Azure Kubernetes Service (AKS) 上將模型部署為 web 服務。 Azure Kubernetes Service 適用于高規模的生產部署。 如果您需要下列其中一項或多項功能，請使用 Azure Kubernetes service：

- __快速回應時間__
- 已部署 __服務的自動__ 調整
- __Logging__
- __模型資料收集__
- __驗證__
- __TLS 終止__
- __硬體加速__ 選項，例如 GPU 和可現場程式化閘道陣列 (FPGA) 

部署至 Azure Kubernetes Service 時，您會部署至已 __連線到您工作區__ 的 AKS 叢集。 如需將 AKS 叢集連線至工作區的相關資訊，請參閱 [建立和附加 Azure Kubernetes Service](how-to-create-attach-kubernetes.md)叢集。

> [!IMPORTANT]
> 建議您先在本機進行偵錯工具，再部署至 web 服務。 如需詳細資訊，請參閱[本機的調試](./how-to-troubleshoot-deployment-local.md)程式
>
> 您也可以參考 Azure Machine Learning - [部署至本機筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 在您的工作區中註冊的機器學習模型。 如果您沒有已註冊的模型，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

- Machine Learning 服務、 [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組的[Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組。

- 本文中的 __Python__ 程式碼片段假設已設定下列變數：

    * `ws` -設定為您的工作區。
    * `model` -設定為已註冊的模型。
    * `inference_config` -設定為模型的推斷設定。

    如需有關設定這些變數的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的 __CLI__ 程式碼片段假設您已建立 `inferenceconfig.json` 檔。 如需有關建立這份檔的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

- 連線到工作區的 Azure Kubernetes Service 叢集。 如需詳細資訊，請參閱 [建立和附加 Azure Kubernetes Service](how-to-create-attach-kubernetes.md)叢集。

    - 如果您想要將模型部署至 GPU 節點或 FPGA 節點 (或任何特定 SKU) ，您必須建立具有特定 SKU 的叢集。 不支援在現有的叢集中建立次要節點集區，並在次要節點集區中部署模型。

## <a name="understand-the-deployment-processes"></a>瞭解部署流程

Kubernetes 和 Azure Machine Learning 中都會使用 "deployment" 這個字。 在這兩種環境中，「部署」具有不同的意義。 在 Kubernetes 中， `Deployment` 是以宣告式 YAML 檔案指定的具象實體。 Kubernetes `Deployment` 具有已定義的生命週期和其他 Kubernetes 實體的具體關聯性，例如 `Pods` 和 `ReplicaSets` 。 您可以從檔和影片的 Kubernetes Kubernetes 瞭解如何 [進行](https://aka.ms/k8slearning)。

在 Azure Machine Learning 中，會使用「部署」來提供更通用的功能，並清除您的專案資源。 Azure Machine Learning 考慮部分部署的步驟如下：

1. 壓縮專案資料夾中的檔案，並忽略 amlignore 或. .gitignore 中所指定的檔案。
1. 擴充您的計算叢集 (與 Kubernetes) 
1. 建立或下載 dockerfile 至計算節點 (與 Kubernetes) 
    1. 系統會計算下列雜湊： 
        - 基底映射 
        - 自訂 docker 步驟 (參閱 [使用自訂 docker 基底映射部署模型](./how-to-deploy-custom-docker-image.md)) 
        - Conda 定義 YAML (參閱 [在 Azure Machine Learning 中建立 & 使用軟體環境](./how-to-use-environments.md)) 
    1. 系統會使用此雜湊作為 (ACR Azure Container Registry 工作區查閱中的索引鍵) 
    1. 如果找不到，則會在全域 ACR 中尋找相符的
    1. 如果找不到，系統會建立新的映射， (將會快取並推送至工作空間 ACR) 
1. 將壓縮的專案檔案下載至計算節點上的暫存儲存體
1. 解壓縮專案檔
1. 執行中的計算節點 `python <entry script> <arguments>`
1. 將記錄、模型檔案和其他寫入的檔案儲存到 `./outputs` 與工作區相關聯的儲存體帳戶
1. 縮小計算範圍，包括移除暫存儲存體 (與 Kubernetes 相關) 

### <a name="azure-ml-router"></a>Azure ML 路由器

前端元件 (azureml-fe) 將傳入推斷要求路由至部署的服務會視需要自動調整。 Azureml-fe 的調整是根據 AKS 叢集目的和大小 (節點數目) 。 當您 [建立或附加 AKS](how-to-create-attach-kubernetes.md)叢集時，會設定叢集目的和節點。 每個叢集都有一個 azureml-fe 服務，可能在多個 pod 上執行。

> [!IMPORTANT]
> 使用設定為 __開發/測試__ 的叢集時，會 **停用** 自我 scaler。

Azureml-fe 會向上延展 (垂直) 以使用更多核心，並 (水準) 以使用更多的 pod。 進行擴大決策時，會使用路由傳入推斷要求所花的時間。 如果這段時間超過臨界值，就會進行相應放大。 如果路由連入要求的時間持續超過閾值，則會發生相應放大。

相應減少和縮小時，會使用 CPU 使用量。 如果符合 CPU 使用量閾值，前端將會先向下調整。 如果 CPU 使用量下降到相應縮小臨界值，就會發生相應縮小作業。 只有當有足夠的可用叢集資源時，才會進行相應增加和相應放大。

## <a name="deploy-to-aks"></a>部署到 AKS

若要將模型部署至 Azure Kubernetes Service，請建立 __部署__ 設定，以描述所需的計算資源。 例如，核心和記憶體數目。 您也需要 __推斷__ 設定，其中描述裝載模型和 web 服務所需的環境。 如需有關建立推斷設定的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

> [!NOTE]
> 要部署的模型數目限制為每個容器) 每個部署 (1000 個模型。

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

如需此範例中所使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute?preserve-view=true&view=azure-ml-py)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?preserve-view=true&view=azure-ml-py)
* [模型. 部署](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 CLI 進行部署，請使用下列命令。 取代 `myaks` 為 AKS 計算目標的名稱。 取代 `mymodel:1` 為已註冊模型的名稱和版本。 `myservice`以用來提供此服務的名稱取代：

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

如需詳細資訊，請參閱 [az ml 模型部署](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 參考。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

如需使用 VS Code 的詳細資訊，請參閱透過 [VS Code 擴充功能部署至 AKS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 透過 VS Code 部署需要預先建立 AKS 叢集或將其附加至您的工作區。

---

### <a name="autoscaling"></a>自動調整

處理 Azure ML 模型部署自動調整的元件是 azureml-fe，也就是智慧型要求路由器。 因為所有推斷要求都會通過它，所以它具有必要的資料來自動調整已部署的模型 () 。

> [!IMPORTANT]
> * **請勿啟用模型部署的 Kubernetes 水準 Pod 自動調整程式 (HPA)**。 這麼做會導致兩個自動調整元件互相競爭。 Azureml-fe 是設計用來自動調整 Azure ML 所部署的模型，其中 HPA 必須從一般計量（例如 CPU 使用量或自訂計量設定）猜測或估計模型使用率。
> 
> * **Azureml-fe 無法調整 AKS** 叢集中的節點數目，因為這可能會導致非預期的成本增加。 相反地，它會在實體叢集界限內 **調整模型的複本數目** 。 如果您需要調整叢集中的節點數目，您可以手動調整叢集或 [設定 AKS 叢集自動調整程式](../aks/cluster-autoscaler.md)。

您可以藉由設定 `autoscale_target_utilization` 、 `autoscale_min_replicas` 和來控制 `autoscale_max_replicas` AKS web 服務的自動調整。 下列範例示範如何啟用自動調整：

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

相應增加/減少的決策是以目前容器複本的使用量為基礎。 處理要求時忙碌 (的複本數目) 除以目前複本的總數目是目前的使用率。 如果此數位超過 `autoscale_target_utilization` ，則會建立更多複本。 如果較低，則會降低複本。 根據預設，目標使用率為70%。

新增複本的決策是立即且快速 (大約1秒的) 。 移除複本的決策是保守 (大約1分鐘的) 。

您可以使用下列程式碼來計算所需的複本：

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

如需有關設定、和的詳細資訊 `autoscale_target_utilization` `autoscale_max_replicas` ，請 `autoscale_min_replicas` 參閱 [>akswebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) 模組參考。

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>使用受控制的推出 (預覽版，將模型部署到 AKS) 

使用端點以受控制的方式分析和升級模型版本。 您最多可以在單一端點後方部署六個版本。 端點提供下列功能：

* 設定 __傳送至每個端點的評分流量百分比__。 例如，將20% 的流量路由傳送至端點「測試」，而80% 到「生產」。

    > [!NOTE]
    > 如果您沒有考慮100% 的流量，則會將剩餘的百分比路由傳送至 __預設__ 的端點版本。 例如，如果您將端點版本 ' test ' 設定為取得10% 的流量，而「生產」為30%，則剩餘的60% 會傳送至預設的端點版本。
    >
    > 第一個建立的端點版本會自動設定為預設值。 您可以在 `is_default=True` 建立或更新端點版本時設定此變更。
     
* 將端點版本戳記為 __控制__ 或 __處理__。 例如，目前的生產端點版本可能是控制，而可能的新模型會部署為處理版本。 在評估處理版本的效能之後，如果其中一個控制項超越目前的控制項，它可能會升級為新的生產/控制項。

    > [!NOTE]
    > 您只能有 __一個__ 控制項。 您可以有多個治療。

您可以啟用 app insights 來查看端點的操作計量和已部署的版本。

### <a name="create-an-endpoint"></a>建立端點
當您準備好要部署模型之後，請建立計分端點並部署您的第一個版本。 下列範例顯示如何使用 SDK 部署和建立端點。 第一個部署會定義為預設版本，這表示所有版本的未指定流量百分位數將會移至預設版本。  

> [!TIP]
> 在下列範例中，設定會設定初始端點版本以處理20% 的流量。 因為這是第一個端點，所以它也是預設版本。 而且，由於我們沒有其他80% 流量的任何其他版本，因此也會將它路由傳送至預設值。 在部署了具有百分比流量的其他版本之前，這一個會有效地接收100% 的流量。

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>更新版本並將其新增至端點

將另一個版本新增至您的端點，並將評分流量百分位數設定為版本。 有兩種類型的版本：控制項和處理版本。 您可以有多個處理版本，以協助與單一控制項版本進行比較。

> [!TIP]
> 第二個版本（由下列程式碼片段所建立）接受10% 的流量。 第一個版本的設定為20%，因此只針對特定版本設定30% 的流量。 剩餘的70% 會傳送至第一個端點版本，因為它也是預設版本。

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

更新現有的版本，或在端點中刪除它們。 您可以變更版本的預設類型、控制項類型和流量百分位數。 在下列範例中，第二個版本會將其流量增加到40%，而且現在是預設值。

> [!TIP]
> 在下列程式碼片段之後，第二個版本現在是預設值。 它現在已設定為40%，而原始版本仍設定為20%。 這表示版本設定不會考慮40% 的流量。 剩餘的流量會路由傳送至第二個版本，因為它現在是預設值。 它會有效地接收80% 的流量。

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Web 服務驗證

部署至 Azure Kubernetes Service 時，依預設會啟用以 __金鑰為基礎的__ 驗證。 您也可以啟用以 __權杖為基礎的__ 驗證。 權杖型驗證要求用戶端使用 Azure Active Directory 帳戶要求驗證權杖，此權杖可用來對已部署的服務提出要求。

若要 __停__ 用驗證，請 `auth_enabled=False` 在建立部署設定時設定參數。 下列範例會使用 SDK 停用驗證：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

如需從用戶端應用程式進行驗證的詳細資訊，請參閱 [使用部署為 web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)。

### <a name="authentication-with-keys"></a>使用金鑰進行驗證

如果已啟用金鑰驗證，您可以使用 `get_keys` 方法來取得主要和次要驗證金鑰：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果您需要重新產生金鑰，請使用 [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py)

### <a name="authentication-with-tokens"></a>使用權杖進行驗證

若要啟用權杖驗證，請 `token_auth_enabled=True` 在建立或更新部署時設定參數。 下列範例會使用 SDK 啟用權杖驗證：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

如果已啟用權杖驗證，您可以使用 `get_token` 方法來取出 JWT 權杖和該權杖的到期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您將需要在權杖的時間之後要求新的權杖 `refresh_by` 。
>
> Microsoft 強烈建議您在與 Azure Kubernetes Service 叢集相同的區域中建立 Azure Machine Learning 工作區。 若要使用權杖進行驗證，Web 服務會呼叫 Azure Machine Learning 工作區的建立區域。 如果您的工作區區域無法使用，即使您的叢集與您的工作區位於不同的區域，您也將無法提取 web 服務的權杖。 這實際上會導致權杖型驗證無法使用，直到您的工作區區域再次可供使用為止。 此外，您的叢集區域和工作區區域之間的距離愈大，提取權杖所需的時間就愈長。
>
> 若要取得權杖，您必須使用 Azure Machine Learning SDK 或 [az ml 服務取得存取權杖](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) 命令。


### <a name="vulnerability-scanning"></a>弱點掃描

Azure 資訊安全中心為混合式雲端工作負載提供統一的安全性管理和進階威脅防護。 您應允許 Azure 資訊安全中心掃描您的資源，並遵循其建議。 如需詳細資訊，請參閱 [Azure Kubernetes Services 與資訊安全中心整合](../security-center/defender-for-kubernetes-introduction.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure RBAC 進行 Kubernetes 授權](../aks/manage-azure-rbac.md)
* [使用 Azure 虛擬網路的安全推斷環境](how-to-secure-inferencing-vnet.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
