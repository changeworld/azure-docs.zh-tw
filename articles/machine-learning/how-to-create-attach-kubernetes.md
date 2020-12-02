---
title: 建立並附加 Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: 瞭解如何透過 Azure Machine Learning 建立新的 Azure Kubernetes Service 叢集，或如何將現有的 AKS 叢集連結至您的工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: e773c2db9c7849dd9680f8ae0c600405f422d7e1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463180"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>建立並附加 Azure Kubernetes Service 叢集

Azure Machine Learning 可以將定型的機器學習模型部署到 Azure Kubernetes Service。 不過，您必須先從 Azure ML 工作區 __建立__ AZURE KUBERNETES SERVICE (AKS) 叢集，或 __附加__ 現有的 AKS 叢集。 本文提供建立和附加叢集的相關資訊。

## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- Machine Learning 服務、 [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組的[Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組。

- 如果您打算使用 Azure 虛擬網路來保護您的 Azure ML 工作區與 AKS 叢集之間的通訊，請在 [訓練 & 推斷文章中閱讀網路隔離](./how-to-network-security-overview.md) 。

## <a name="limitations"></a>限制

- 如果您需要在您的叢集中部署 **Standard Load Balancer (SLB)** ，而不是基本 LOAD BALANCER (BLB) ，請在 AKS 入口網站/CLI/SDK 中建立叢集，然後 **將** 它連結至 AML 工作區。

- 如果您有限制建立公用 IP 位址的 Azure 原則，AKS 叢集建立將會失敗。 AKS 需要公用 IP 來提供輸出 [流量](../aks/limit-egress-traffic.md)。 輸出流量文章也會提供指引，以透過公用 IP 鎖定來自叢集的輸出流量，但有幾個完整功能變數名稱除外。 有兩種方式可以啟用公用 IP：
    - 叢集可以使用預設使用 BLB 或 SLB 建立的公用 IP，或
    - 您可以在不使用公用 IP 的情況下建立叢集，然後使用具有使用者定義路由的防火牆來設定公用 IP。 如需詳細資訊，請參閱 [使用使用者定義的路由自訂](../aks/egress-outboundtype.md)叢集輸出。
    
    AML 控制平面不會與此公用 IP 通訊。 它會與部署的 AKS 控制平面交談。 

- 如果您 **附加** 已 [啟用授權 IP 範圍的 AKS 叢集來存取 API 伺服器](../aks/api-server-authorized-ip-ranges.md)，請啟用 AKS 叢集的 AML 控制平面 IP 範圍。 AML 控制平面會部署在配對的區域，並在 AKS 叢集上部署推斷 pod。 如果沒有 API 伺服器的存取權，則無法部署推斷 pod。 在 AKS 叢集中啟用 IP 範圍時，請使用兩個[配對區域](../best-practices-availability-paired-regions.md)的[ip 範圍](https://www.microsoft.com/download/confirmation.aspx?id=56519)。

    授權的 IP 範圍僅適用于 Standard Load Balancer。

- **附加** AKS 叢集時，它必須與您的 Azure Machine Learning 工作區位於相同的 Azure 訂用帳戶中。

- 如果您想要使用私人 AKS 叢集 (使用 Azure Private Link) ，您必須先建立叢集，然後 **將它附加** 至工作區。 如需詳細資訊，請參閱 [建立私人 Azure Kubernetes Service](../aks/private-clusters.md)叢集。

- AKS 叢集的計算名稱在您的 Azure ML 工作區中必須是唯一的。
    - 名稱是必要的，且長度必須介於3到24個字元之間。
    - 有效字元是大寫和小寫字母、數位和-字元。
    - 名稱必須以字母開頭。
    - 名稱在 Azure 區域內的所有現有計算中都必須是唯一的。 如果您選擇的名稱不是唯一的，您會看到警示。
   
 - 如果您想要將模型部署至 **GPU** 節點或 **FPGA** 節點 (或任何特定 sku) ，您必須建立具有特定 sku 的叢集。 不支援在現有的叢集中建立次要節點集區，並在次要節點集區中部署模型。
 
- 建立或附加叢集時，您可以選擇是否要建立用於 __開發/測試__ 或 __生產環境__ 的叢集。 如果您想要建立用於 __開發__、 __驗證__ 和 __測試__ 的 AKS 叢集，而不是生產環境，請將叢集 __用途__ 設定為 __開發/測試__。 如果您未指定叢集用途，則會建立 __生產__ 叢集。 

    > [!IMPORTANT]
    > __開發/測試__ 叢集不適合用于生產層級的流量，可能會增加推斷時間。 開發/測試叢集也不保證容錯。

- 建立或附加叢集時，如果叢集將用於 __生產環境__，則必須包含至少12個 __虛擬 cpu__。 您可以將叢集中的 __節點數目__ 乘以選取的 VM 大小所提供的 __核心數目__ ，來計算虛擬 cpu 的數目。 例如，如果您使用「Standard_D3_v2」的 VM 大小（有4個虛擬核心），則您應該選取 [3] 或 [大於] 節點數目。

    針對 __開發/測試__ 叢集，我們會建議至少2個虛擬 cpu。

- Azure Machine Learning SDK 不提供調整 AKS 叢集的支援。 若要調整叢集中的節點，請在 Azure Machine Learning studio 中使用您的 AKS 叢集的 UI。 您只能變更節點計數，而不是叢集的 VM 大小。 如需有關調整 AKS 叢集中節點的詳細資訊，請參閱下列文章：

    - [手動調整 AKS 叢集中的節點計數](../aks/scale-cluster.md)
    - [在 AKS 中設定叢集自動調整程式](../aks/cluster-autoscaler.md)

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes Service 版本

Azure Kubernetes Service 可讓您使用各種 Kubernetes 版本來建立叢集。 如需可用版本的詳細資訊，請參閱 [Azure Kubernetes Service 中支援的 Kubernetes 版本](../aks/supported-kubernetes-versions.md)。

使用下列其中一種方法 **建立** Azure Kubernetes Service 叢集時，您無法在建立的叢集 *版本中選擇* ：

* Azure Machine Learning studio 或 Azure 入口網站的 Azure Machine Learning 區段。
* Azure CLI 的 Machine Learning 延伸模組。
* Azure Machine Learning SDK。

建立 AKS 叢集的這些方法會使用叢集的 __預設__ 版本。 當新的 Kubernetes 版本可供使用 *時，預設版本會隨時間變更*。

**附加** 現有的 AKS 叢集時，我們支援所有目前支援的 AKS 版本。

> [!NOTE]
> 在某些情況下，您可能會有已不再支援的較舊叢集的邊緣案例。 在此情況下，附加作業會傳回錯誤並列出目前支援的版本。
>
> 您可以附加 **預覽** 版本。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 使用預覽版本的支援可能有所限制。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="available-and-default-versions"></a>可用和預設版本

若要尋找可用和預設的 AKS 版本，請使用[az AKS get 版本](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions) [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)命令。 例如，下列命令會傳回美國西部區域中可用的版本：

```azurecli-interactive
az aks get-versions -l westus -o table
```

此命令的輸出類似下列文字：

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

若要尋找透過 Azure Machine Learning **建立** 叢集時所使用的預設版本，您可以使用 `--query` 參數來選取預設版本：

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

此命令的輸出類似下列文字：

```text
Result
--------
1.16.13
```

如果您想要以程式設計 **方式檢查可用的版本**，請使用 [容器服務用戶端清單協調器](/rest/api/container-service/container%20service%20client/listorchestrators) REST API。 若要尋找可用的版本，請查看其中的 `orchestratorType` 專案 `Kubernetes` 。 相關聯的 `orchestrationVersion` 專案包含可 **附加** 至工作區的可用版本。

若要尋找透過 Azure Machine Learning **建立** 叢集時所使用的預設版本，請尋找的專案， `orchestratorType` 其中 `Kubernetes` 是 `default` 和 `true` 。 相關聯的 `orchestratorVersion` 值是預設版本。 下列 JSON 程式碼片段顯示一個範例專案：

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>建立新的 AKS 叢集

**預估時間**：約10分鐘。

建立或附加 AKS 叢集是工作區的一次性流程。 您可以重複使用此叢集進行多個部署。 如果您刪除叢集或包含該叢集的資源群組，您必須在下次需要部署時建立新的叢集。 您可以將多個 AKS 叢集附加至您的工作區。

下列範例示範如何使用 SDK 和 CLI 來建立新的 AKS 叢集：

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

如需此範例中所使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget 建立](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

如需詳細資訊，請參閱 [az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference （建立參考）。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

如需在入口網站中建立 AKS 叢集的詳細資訊，請參閱在 [Azure Machine Learning studio 中建立計算目標](how-to-create-attach-compute-studio.md#inference-clusters)。

---

## <a name="attach-an-existing-aks-cluster"></a>附加現有的 AKS 叢集

**預估時間：** 大約5分鐘。

如果您的 Azure 訂用帳戶中已經有 AKS 叢集，而且它是1.17 版或更低版本，您可以使用它來部署映射。

> [!TIP]
> 現有的 AKS 叢集可以位於 Azure Machine Learning 工作區以外的 Azure 區域中。


> [!WARNING]
> 請勿從您的工作區建立多個同時附加至相同 AKS 叢集的附件。 例如，使用兩個不同的名稱將一個 AKS 叢集附加至工作區。 每個新的附件都會中斷先前現有的附件 (s) 。
>
> 如果您想要重新附加 AKS 叢集（例如變更 TLS 或其他叢集設定），必須先使用 AksCompute 移除現有的附件 [。 ( # B1 卸離 ](/python/api/azureml-core/azureml.core.compute.akscompute?preserve-view=true&view=azure-ml-py#detach--)。

如需有關使用 Azure CLI 或入口網站建立 AKS 叢集的詳細資訊，請參閱下列文章：

* [建立 AKS 叢集 (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&preserve-view=true&toc=%2fazure%2faks%2fTOC.json&view=azure-cli-latest#az-aks-create)
* [ (入口網站) 建立 AKS 叢集 ](../aks/kubernetes-walkthrough-portal.md?preserve-view=true&view=azure-cli-latest)
* [在 Azure 快速入門範本上建立 AKS 叢集 (ARM 範本) ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

下列範例示範如何將現有的 AKS 叢集連結至您的工作區：

# <a name="python"></a>[Python](#tab/python)

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

如需此範例中所使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AksCompute.attach_configuration ( # B1 ](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute 附加](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 CLI 附加現有的叢集，您需要取得現有叢集的資源識別碼。 若要取得此值，請使用下列命令。 `myexistingcluster`以您的 AKS 叢集名稱取代。 取代 `myresourcegroup` 為包含叢集的資源群組：

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

此命令會傳回類似下列文字的值：

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

若要將現有的叢集附加至您的工作區，請使用下列命令。 取代 `aksresourceid` 為前一個命令所傳回的值。 取代 `myresourcegroup` 為包含您工作區的資源群組。 取代 `myworkspace` 為您的工作區名稱。

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

如需詳細資訊，請參閱 [az ml computetarget 附加 aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) 參考。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

如需在入口網站中附加 AKS 叢集的詳細資訊，請參閱在 [Azure Machine Learning studio 中建立計算目標](how-to-create-attach-compute-studio.md#inference-clusters)。

---

## <a name="detach-an-aks-cluster"></a>卸離 AKS 叢集

若要從您的工作區卸離叢集，請使用下列其中一種方法：

> [!WARNING]
> 使用 Azure Machine Learning studio、SDK 或機器學習服務的 Azure CLI 擴充功能來卸離 AKS 叢集， **並不會刪除 AKS** 叢集。 若要刪除叢集，請參閱搭配 [使用 Azure CLI 與 AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster)。

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要將現有的叢集卸離您的工作區，請使用下列命令。 將取代為 `myaks` AKS 叢集附加至工作區的名稱。 取代 `myresourcegroup` 為包含您工作區的資源群組。 取代 `myworkspace` 為您的工作區名稱。

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure Machine Learning studio 中，選取 [ __計算__]、[ __推斷__ 叢集] 以及您想要移除的叢集。 使用卸 __離__ 連結來卸離叢集。

## <a name="next-steps"></a>後續步驟

* [使用 Azure RBAC 進行 Kubernetes 授權](../aks/manage-azure-rbac.md)
* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [將模型部署到 Azure Kubernetes Service 叢集](how-to-deploy-azure-kubernetes-service.md)