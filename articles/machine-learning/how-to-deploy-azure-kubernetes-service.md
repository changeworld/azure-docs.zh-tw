---
title: 如何將模型部署至 Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Kubernetes Service，將您的 Azure Machine Learning 模型部署為 web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: db2e80ebb6cbe5f31f2d99a1403a15daf38fd877
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722379"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>將模型部署到 Azure Kubernetes Service 叢集
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用 Azure Machine Learning，在 Azure Kubernetes Service （AKS）上將模型部署為 web 服務。 Azure Kubernetes Service 適用于大規模的生產環境部署。 如果您需要下列一或多項功能，請使用 Azure Kubernetes 服務：

- __快速回應時間__。
- 自動調整已__部署的服務__。
- __硬體加速__選項，例如 GPU 和可現場程式化閘道陣列（FPGA）。

> [!IMPORTANT]
> 不會透過 Azure Machine Learning SDK 來提供叢集調整。 如需有關在 AKS 叢集中調整節點的詳細資訊，請參閱[調整 AKS 叢集中的節點計數](../aks/scale-cluster.md)。

部署到 Azure Kubernetes Service 時，您會部署到__連線到工作區__的 AKS 叢集。 有兩種方式可以將 AKS 叢集連線到您的工作區：

* 使用 Azure Machine Learning SDK、Machine Learning CLI 或[Azure Machine Learning studio](https://ml.azure.com)來建立 AKS 叢集。 此程式會自動將叢集連接到工作區。
* 將現有的 AKS 叢集附加至您的 Azure Machine Learning 工作區。 您可以使用 [Azure Machine Learning SDK]、[Machine Learning CLI] 或 Azure Machine Learning studio 來連接叢集。

> [!IMPORTANT]
> 建立或附加程式是一次性的工作。 一旦 AKS 叢集連線到工作區，您就可以將它用於部署。 如果您不再需要 AKS 叢集，您可以卸離或刪除該叢集。 卸離或刪除之後，您將無法再部署到叢集。

## <a name="prerequisites"></a>必要條件

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 在您的工作區中註冊的機器學習模型。 如果您沒有已註冊的模型，請參閱[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [Machine Learning 服務的 Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組， [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組。

- 本文中的__Python__程式碼片段假設已設定下列變數：

    * `ws`-設定至您的工作區。
    * `model`-設定為您的已註冊模型。
    * `inference_config`-設為模型的推斷設定。

    如需有關設定這些變數的詳細資訊，請參閱[如何和在何處部署模型](how-to-deploy-and-where.md)。

- 本文中的__CLI__程式碼片段假設您已建立 `inferenceconfig.json` 檔。 如需有關建立此檔的詳細資訊，請參閱[如何和部署模型的位置](how-to-deploy-and-where.md)。

## <a name="create-a-new-aks-cluster"></a>建立新的 AKS 叢集

**估計時間**：約20分鐘。

建立或附加 AKS 叢集是工作區的一次性程式。 您可以重複使用此叢集進行多個部署。 如果您刪除叢集或包含該叢集的資源群組，則必須在下次需要部署時建立新的叢集。 您可以將多個 AKS 叢集附加至您的工作區。

> [!TIP]
> 如果您想要使用 Azure 虛擬網路保護您的 AKS 叢集，您必須先建立虛擬網路。 如需詳細資訊，請參閱[使用 Azure 虛擬網路保護實驗和推斷](how-to-enable-virtual-network.md#aksvnet)。

如果您想要建立用於__開發__、__驗證__和__測試__的 AKS 叢集，而不是生產環境，您可以指定叢集__目的__來進行開發__測試__。

> [!WARNING]
> 如果您設定 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，則建立的叢集不適合用于生產層級的流量，而且可能會增加推斷時間。 開發/測試叢集也不保證容錯。 針對開發/測試叢集，我們建議至少有2個虛擬 Cpu。

下列範例示範如何使用 SDK 和 CLI 來建立新的 AKS 叢集：

**使用 SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> 針對[`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果您挑選 `agent_count` 和 `vm_size`的自訂值，但不 `DEV_TEST``cluster_purpose`，則您必須確定 `agent_count` 乘以 `vm_size` 大於或等於12個虛擬 cpu。 例如，如果您使用具有4個虛擬 Cpu 的「Standard_D3_v2」 `vm_size`，則您應該挑選3或更高的 `agent_count`。
>
> Azure Machine Learning SDK 不提供調整 AKS 叢集的支援。 若要調整叢集中的節點，請在 Azure Machine Learning studio 中使用適用于 AKS 叢集的 UI。 您只能變更節點計數，而不是叢集的 VM 大小。

如需此範例中使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute。 provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget。建立](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget。 wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**使用 CLI**

```azurecli
az ml computetarget create aks -n myaks
```

如需詳細資訊，請參閱[az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference。

## <a name="attach-an-existing-aks-cluster"></a>附加現有的 AKS 叢集

**估計時間：** 大約5分鐘。

如果您的 Azure 訂用帳戶中已經有 AKS 叢集，而且它是1.17 或更低版本，您可以使用它來部署映射。

> [!TIP]
> 現有的 AKS 叢集可以位於 Azure Machine Learning 工作區以外的 Azure 區域中。
>
> 如果您想要使用 Azure 虛擬網路保護您的 AKS 叢集，您必須先建立虛擬網路。 如需詳細資訊，請參閱[使用 Azure 虛擬網路保護實驗和推斷](how-to-enable-virtual-network.md#aksvnet)。

將 AKS 叢集附加至工作區時，您可以藉由設定 `cluster_purpose` 參數來定義使用叢集的方式。

如果您未設定 `cluster_purpose` 參數，或設定 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`，則叢集必須至少有12個可用的虛擬 Cpu。

如果您設定 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，叢集就不需要有12個虛擬 Cpu。 針對開發/測試，我們建議至少要有2個虛擬 Cpu。 不過，針對「開發/測試」設定的叢集不適合用于生產層級的流量，而且可能會增加推斷時間。 開發/測試叢集也不保證容錯。

> [!WARNING]
> 請勿從您的工作區，同時建立多個同時附加至相同 AKS 叢集的附件。 例如，使用兩個不同的名稱，將一個 AKS 叢集附加至工作區。 每個新的附件都會中斷先前現有的附件。
>
> 如果您想要重新附加 AKS 叢集（例如變更 SSL 或其他叢集設定），您必須先使用 AksCompute 移除現有的附件[（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)。

如需使用 Azure CLI 或入口網站建立 AKS 叢集的詳細資訊，請參閱下列文章：

* [建立 AKS 叢集（CLI）](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [建立 AKS 叢集（入口網站）](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

下列範例示範如何將現有的 AKS 叢集附加至您的工作區：

**使用 SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

如需此範例中使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AksCompute. attach_configuration （）](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute。 attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**使用 CLI**

若要使用 CLI 附加現有的叢集，您需要取得現有叢集的資源識別碼。 若要取得此值，請使用下列命令。 以 AKS 叢集的名稱取代 `myexistingcluster`。 將 `myresourcegroup` 取代為包含叢集的資源群組：

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

此命令會傳回類似下列文字的值：

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

若要將現有的叢集附加至您的工作區，請使用下列命令。 以上一個命令所傳回的值取代 `aksresourceid`。 將 `myresourcegroup` 取代為包含您工作區的資源群組。 將 `myworkspace` 取代為您的工作區名稱。

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

如需詳細資訊，請參閱[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference。

## <a name="deploy-to-aks"></a>部署到 AKS

若要將模型部署到 Azure Kubernetes Service，請建立__部署__設定，以描述所需的計算資源。 例如，核心和記憶體數目。 您也需要__推斷__設定，其中描述裝載模型和 web 服務所需的環境。 如需建立推斷設定的詳細資訊，請參閱[如何和部署模型的位置](how-to-deploy-and-where.md)。

### <a name="using-the-sdk"></a>使用 SDK

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

如需此範例中使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice。 deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [模型。部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 進行部署，請使用下列命令。 將 `myaks` 取代為 AKS 計算目標的名稱。 將 `mymodel:1` 取代為已註冊模型的名稱和版本。 以名稱取代 `myservice` 以授與此服務：

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

如需詳細資訊，請參閱[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference。

### <a name="using-vs-code"></a>使用 VS Code

如需使用 VS Code 的詳細資訊，請參閱透過[VS Code 延伸模組部署至 AKS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 透過 VS Code 部署時，必須事先建立 AKS 叢集或將其附加至您的工作區。

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>使用受控制的推出（預覽）將模型部署至 AKS
使用端點以控制的方式來分析和升級模型版本。 最多可在單一端點後部署6個版本，並設定每個已部署版本的評分流量百分比。 您可以啟用 app insights 來查看端點和已部署版本的操作計量。

### <a name="create-an-endpoint"></a>建立端點
當您準備好要部署模型之後，請建立評分端點，並部署您的第一個版本。 下列步驟說明如何使用 SDK 來部署和建立端點。 第一個部署會定義為預設版本，這表示所有版本之間未指定的流量百分位數，將會移至預設版本。  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>更新並將版本新增至端點

將另一個版本新增至您的端點，並將評分流量的百分位數設定為版本。 版本有兩種類型：控制項和處理版本。 可以有多個處理版本，以協助與單一控制項版本進行比較。

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
```

更新現有的版本，或在端點中將它們刪除。 您可以變更版本的預設類型、控制項類型和流量百分位數。

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Web 服務驗證

部署到 Azure Kubernetes Service 時，預設會啟用__金鑰型__驗證。 您也可以啟用__權杖型__驗證。 以權杖為基礎的驗證需要用戶端使用 Azure Active Directory 帳戶來要求驗證權杖，以用來對已部署的服務提出要求。

若要__停__用驗證，請在建立部署設定時設定 `auth_enabled=False` 參數。 下列範例會使用 SDK 來停用驗證：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

如需從用戶端應用程式進行驗證的詳細資訊，請參閱[使用部署為 web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)。

### <a name="authentication-with-keys"></a>使用金鑰進行驗證

如果已啟用金鑰驗證，您可以使用 `get_keys` 方法來取得主要和次要驗證金鑰：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果您需要重新產生金鑰，請使用[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>使用權杖進行驗證

若要啟用權杖驗證，請在建立或更新部署時設定 `token_auth_enabled=True` 參數。 下列範例會使用 SDK 來啟用權杖驗證：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

如果已啟用權杖驗證，您可以使用 `get_token` 方法來取出 JWT 權杖和該權杖的到期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您必須在權杖的 `refresh_by` 時間之後，要求新的權杖。
>
> Microsoft 強烈建議您在與 Azure Kubernetes Service 叢集相同的區域中建立您的 Azure Machine Learning 工作區。 若要使用權杖進行驗證，web 服務會呼叫您的 Azure Machine Learning 工作區建立所在的區域。 如果您的工作區區域無法使用，則即使您的叢集與工作區位於不同的區域，您也無法提取 web 服務的權杖。 這可有效地導致以權杖為基礎的驗證無法使用，直到您的工作區區域再次可用為止。 此外，您的叢集區域和工作區區域之間的距離愈大，提取權杖所需的時間就越長。

## <a name="update-the-web-service"></a>更新 Web 服務

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>後續步驟

* [保護虛擬網路中的實驗和推斷](how-to-enable-virtual-network.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
