---
title: 使用虛擬網路的安全推斷環境
titleSuffix: Azure Machine Learning
description: 使用隔離的 Azure 虛擬網路來保護您的 Azure Machine Learning 推斷環境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 4dfe2130d8274e2b3463c0fb42587b50fef77f18
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953728"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>使用虛擬網路保護 Azure Machine Learning 推斷環境

在本文中，您將瞭解如何在 Azure Machine Learning 中使用虛擬網路來保護推斷環境。

本文是五部分系列的第四部分，將逐步引導您保護 Azure Machine Learning 的工作流程。 強烈建議您仔細閱讀第 [一部： VNet 總覽](how-to-network-security-overview.md) ，以瞭解整體架構。 

請參閱本系列的其他文章：

[1. VNet 總覽](how-to-network-security-overview.md)  >  [保護工作區](how-to-secure-workspace-vnet.md)  >  [3。保護定型環境](how-to-secure-training-vnet.md)  >  **4。保護推斷環境**  >  [5。啟用 studio 功能](how-to-enable-studio-virtual-network.md)

在本文中，您將瞭解如何保護虛擬網路中的下列推斷資源：
> [!div class="checklist"]
> - 預設 Azure Kubernetes Service (AKS) 叢集
> - 私用 AKS 叢集
> - 具有 private link 的 AKS 叢集
> - Azure 容器執行個體 (ACI)


## <a name="prerequisites"></a>必要條件

+ 閱讀 [網路安全性總覽](how-to-network-security-overview.md) 文章，以瞭解常見的虛擬網路案例和整體虛擬網路架構。

+ 要搭配您的計算資源使用的現有虛擬網路和子網。

+ 若要將資源部署到虛擬網路或子網，您的使用者帳戶必須具有 Azure 角色型存取控制 (Azure RBAC) 中下列動作的許可權：

    - 虛擬網路資源上的「Microsoft. Network/virtualNetworks/join/action」。
    - 子網資源上的「Microsoft. Network/virtualNetworks/subnet/join/action」。

    如需有關具有網路功能的 Azure RBAC 的詳細資訊，請參閱 [網路內建角色](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

若要在虛擬網路中使用 AKS 叢集，必須符合下列網路需求：

> [!div class="checklist"]
> * 遵循在 [Azure Kubernetes Service 中設定 advanced 網路的必要條件 (AKS) ](../aks/configure-azure-cni.md#prerequisites)。
> * AKS 實例和虛擬網路必須位於相同的區域中。 如果您在虛擬網路中保護工作區所使用的 Azure 儲存體帳戶 () ，它們也必須與 AKS 實例位於相同的虛擬網路中。


若要將虛擬網路中的 AKS 新增至您的工作區，請使用下列步驟：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)，然後選取您的訂用帳戶和工作區。

1. 選取左側的 [計算]。

1. 從中央選取 [推斷叢集]，然後選取 [+]。

1. 在 [新增推斷叢集] 對話方塊中，選取 [網路設定] 底下的 [進階]。

1. 若要將此計算資源設定為使用虛擬網路，請執行下列動作：

    1. 在 [資源群組] 下拉式清單中，選取包含虛擬網路的資源群組。
    1. 在 [虛擬網路] 下拉式清單中，選取包含子網路的虛擬網路。
    1. 在 [子網路] 下拉式清單中選取子網路。
    1. 在 [Kubernetes Service 位址範圍] 方塊中，輸入 Kubernetes 服務的位址範圍。 此位址範圍會使用無類別網域間路由選擇 (CIDR) 標記法的 IP 範圍來定義可供叢集使用的 IP 位址。 此範圍不得與任何子網路 IP 範圍重疊 (例如，10.0.0.0/16)。
    1. 在 [Kubernetes DNS 服務 IP 位址] 方塊中，輸入 Kubernetes DNS 服務的 IP 位址。 此 IP 位址會指派給 Kubernetes DNS 服務。 其必須位於 Kubernetes 服務位址範圍內 (例如，10.0.0.10)。
    1. 在 [Docker 橋接器位址] 方塊中，輸入 Docker 橋接器的位址。 此 IP 位址會指派給 Docker 橋接器， 其不得位於任何子網路 IP 範圍或 Kubernetes Service 位址範圍中 (例如，172.17.0.1/16)。

   ![Azure Machine Learning：Machine Learning Compute 虛擬網路設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 當您將模型部署為 web 服務以進行 AKS 時，會建立計分端點來處理推斷要求。 如果您想要從虛擬網路外部呼叫該虛擬網路的 IP 位址，請確定控制該虛擬網路的 NSG 群組具有啟用的輸入安全性規則。

    若要尋找評分端點的 IP 位址，請查看已部署服務的評分 URI。 如需有關如何查看評分 URI 的詳細資訊，請參閱 [使用部署為 web 服務的模型](how-to-consume-web-service.md#connection-information)。

   > [!IMPORTANT]
   > 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](../virtual-network/network-security-groups-overview.md#default-security-rules)中的預設安全性規則一節。

   [![輸入安全性規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > 針對評分端點的映射中所顯示的 IP 位址，會與您的部署不同。 雖然相同的 IP 會由所有部署共用到一個 AKS 叢集，但每個 AKS 叢集都會有不同的 IP 位址。

您在虛擬網路中也可以使用 Azure Machine Learning SDK 新增 Azure Kubernetes 服務。 如果您在虛擬網路中已經有 AKS 叢集，請將其連結至工作區，如[如何部署至 AKS](how-to-deploy-and-where.md) 所述。 下列程式碼會在名為 `mynetwork` 的虛擬網路其 `default` 子網路中建立新的 AKS 執行個體：

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

建立程序完成之後，您可以在虛擬網路背後的 AKS 叢集上執行推斷或模型評分。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-and-where.md)。

如需有關搭配 Kubernetes 使用 Role-Based 存取控制的詳細資訊，請參閱 [使用 AZURE RBAC 進行 Kubernetes 授權](../aks/manage-azure-rbac.md)。

## <a name="network-contributor-role"></a>網路參與者角色

> [!IMPORTANT]
> 如果您藉由提供您先前建立的虛擬網路來建立或附加 AKS 叢集，您必須將 AKS 叢集的「 _網路參與者_ 」角色授與服務主體 (SP) 或受控識別，以包含虛擬網路的資源群組。
>
> 若要將身分識別新增為網路參與者，請使用下列步驟：

1. 若要尋找 AKS 的服務主體或受控識別識別碼，請使用下列 Azure CLI 命令。 將 `<aks-cluster-name>` 取代為叢集的名稱。 `<resource-group-name>`以 _包含 AKS_ 叢集的資源組名取代：

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    如果此命令傳回的值 `msi` ，請使用下列命令來識別受控識別的主體識別碼：

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 若要尋找包含您虛擬網路的資源群組識別碼，請使用下列命令。 `<resource-group-name>`以 _包含虛擬網路_ 的資源組名取代：

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. 若要將服務主體或受控識別新增為網路參與者，請使用下列命令。 取代為 `<SP-or-managed-identity>` 服務主體或受控識別所傳回的識別碼。 取代為 `<resource-group-id>` 包含虛擬網路的資源群組所傳回的識別碼：

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
如需使用內部負載平衡器搭配 AKS 的詳細資訊，請參閱 [使用內部負載平衡器搭配 Azure Kubernetes Service](../aks/internal-lb.md)。

## <a name="secure-vnet-traffic"></a>保護 VNet 流量

有兩種方法可將 AKS 叢集的流量與虛擬網路隔離：

* __私用 AKS__ 叢集：此方法會使用 Azure Private Link 來保護與叢集的通訊，以進行部署/管理作業。
* __內部 AKS 負載平衡器__：此方法會將您部署的端點設定為 AKS，以使用虛擬網路內的私人 IP。

> [!WARNING]
> 內部負載平衡器無法與使用 kubenet 的 AKS 叢集搭配使用。 如果您想要同時使用內部負載平衡器和私人 AKS 叢集，請使用 Azure 容器網路介面設定私人 AKS 叢集 (CNI) 。 如需詳細資訊，請參閱 [Azure Kubernetes Service 中的設定 AZURE CNI 網路](../aks/configure-azure-cni.md)。

### <a name="private-aks-cluster"></a>私用 AKS 叢集

根據預設，AKS 叢集具有具有公用 IP 位址的控制平面或 API 伺服器。 您可以藉由建立私人 AKS 叢集，將 AKS 設定為使用私用控制平面。 如需詳細資訊，請參閱 [建立私人 Azure Kubernetes Service](../aks/private-clusters.md)叢集。

建立私人 AKS 叢集之後，請 [將叢集連接至虛擬網路](how-to-create-attach-kubernetes.md) ，以搭配 Azure Machine Learning 使用。

> [!IMPORTANT]
> 在搭配 Azure Machine Learning 使用啟用私人連結的 AKS 叢集之前，您必須開啟支援事件以啟用此功能。 如需詳細資訊，請參閱 [管理和增加配額](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

### <a name="internal-aks-load-balancer"></a>內部 AKS 負載平衡器

根據預設，AKS 部署會使用 [公用負載平衡器](../aks/load-balancer-standard.md)。 在本節中，您將瞭解如何設定 AKS 以使用內部負載平衡器。 內部 (或私用) 負載平衡器會在只允許私人 Ip 作為前端時使用。 內部負載平衡器可用來對虛擬網路內的流量進行負載平衡

藉由設定 AKS 來使用 _內部負載平衡_ 器，即可啟用私人負載平衡器。 

#### <a name="enable-private-load-balancer"></a>啟用私人負載平衡器

> [!IMPORTANT]
> 在 Azure Machine Learning studio 中建立 Azure Kubernetes Service 叢集時，您無法啟用私人 IP。 使用 Python SDK 或適用于機器學習的 Azure CLI 擴充功能時，您可以使用內部負載平衡器建立一個。

下列範例示範如何使用 SDK 和 CLI 來 __建立具有私人 IP/內部負載平衡器的新 AKS__ 叢集：

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> 使用 CLI，您只能使用內部負載平衡器來建立 AKS 叢集。 沒有 az ml 命令可升級現有的叢集，以使用內部負載平衡器。

如需詳細資訊，請參閱 [az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference （建立參考）。

---

將 __現有叢集附加__ 至您的工作區時，您必須等到附加作業之後，才能設定負載平衡器。 如需有關附加叢集的詳細資訊，請參閱 [附加現有的 AKS](how-to-create-attach-kubernetes.md)叢集。

附加現有的叢集之後，您可以接著更新叢集以使用內部負載平衡器/私人 IP：

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>啟用 (ACI) 的 Azure 容器實例

部署模型時會以動態方式建立 Azure 容器執行個體。 若要讓 Azure Machine Learning 能夠在虛擬網路內部建立 ACI，您必須為部署所使用的子網路啟用 __子網路委派__。

> [!WARNING]
> 使用虛擬網路中的 Azure 容器實例時，虛擬網路必須：
> * 在與您的 Azure Machine Learning 工作區相同的資源群組中。
> * 如果您的工作區有 __私人端點__，則用於 Azure 容器實例的虛擬網路必須與工作區私人端點所使用的虛擬網路相同。
>
> 使用虛擬網路內的 Azure 容器實例時，您工作區的 Azure Container Registry (ACR) 也不能在虛擬網路中。

若要在工作區的虛擬網路中使用 ACI，請使用下列步驟：

1. 若要在虛擬網路上啟用子網路委派，請使用[新增或移除子網路委派](../virtual-network/manage-subnet-delegation.md)一文中的資訊。 您可以在建立虛擬網路時啟用委派，也可以將其新增至現有網路。

    > [!IMPORTANT]
    > 在啟用委派時，請使用 `Microsoft.ContainerInstance/containerGroups` 作為 [將子網路委派給服務] 值。

2. 使用 [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true) 來部署模型，並使用 `vnet_name` 和 `subnet_name` 參數。 將這些參數設定為啟用委派的虛擬網路名稱和子網路。

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>限制來自虛擬網路的輸出連線能力

如果您不想要使用預設輸出規則，而且想要限制虛擬網路的輸出存取權，您必須允許存取 Azure Container Registry。 例如，請確定您的網路安全性群組 (NSG) 包含允許存取 __AzureContainerRegistry. RegionName__ 服務標記的規則，其中 ' {RegionName} 是 Azure 區域的名稱。

## <a name="next-steps"></a>下一步

本文是四部分虛擬網路系列的第三部分。 請參閱文章的其餘部分，以瞭解如何保護虛擬網路：

* [第1部分：虛擬網路總覽](how-to-network-security-overview.md)
* [第2部分：保護工作區資源](how-to-secure-workspace-vnet.md)
* [第3部分：保護定型環境的安全](how-to-secure-training-vnet.md)
* [第5部分：啟用 studio 功能](how-to-enable-studio-virtual-network.md)