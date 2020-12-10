---
title: 使用虛擬網路保護定型環境
titleSuffix: Azure Machine Learning
description: 使用隔離的 Azure 虛擬網路來保護您的 Azure Machine Learning 訓練環境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 131feaf6ff01659b7d126604a5d081275e64508f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029561"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>使用虛擬網路保護 Azure Machine Learning 定型環境

在本文中，您將瞭解如何使用 Azure Machine Learning 中的虛擬網路來保護定型環境。

本文是五部分系列的第三部分，將逐步引導您保護 Azure Machine Learning 的工作流程。 強烈建議您仔細閱讀第 [一部： VNet 總覽](how-to-network-security-overview.md) ，以瞭解整體架構。 

請參閱本系列的其他文章：

[1. VNet 總覽](how-to-network-security-overview.md)  >  [保護工作區](how-to-secure-workspace-vnet.md)  >  **3。保護定型環境**  >  [4。保護推斷環境](how-to-secure-inferencing-vnet.md)   >  [5。啟用 studio 功能](how-to-enable-studio-virtual-network.md)

在本文中，您將瞭解如何保護虛擬網路中的下列定型計算資源：
> [!div class="checklist"]
> - Azure Machine Learning 計算叢集
> - Azure Machine Learning 計算執行個體
> - Azure Databricks
> - 虛擬機器
> - HDInsight 叢集

## <a name="prerequisites"></a>必要條件

+ 閱讀 [網路安全性總覽](how-to-network-security-overview.md) 文章，以瞭解常見的虛擬網路案例和整體虛擬網路架構。

+ 要搭配您的計算資源使用的現有虛擬網路和子網。

+ 若要將資源部署到虛擬網路或子網，您的使用者帳戶必須具有 Azure 角色型存取控制 (Azure RBAC) 中下列動作的許可權：

    - 虛擬網路資源上的「Microsoft. Network/virtualNetworks/join/action」。
    - 子網資源上的「Microsoft. Network/virtualNetworks/subnet/join/action」。

    如需有關具有網路功能的 Azure RBAC 的詳細資訊，請參閱 [網路內建角色](../role-based-access-control/built-in-roles.md#networking)


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>計算叢集和執行個體 

若要在虛擬網路中使用 [受控 Azure Machine Learning __計算目標__](concept-compute-target.md#azure-machine-learning-compute-managed)或 [Azure Machine Learning 計算 __執行個體__](concept-compute-instance.md)，您必須符合下列網路需求：

> [!div class="checklist"]
> * 虛擬網路必須在與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
> * 為計算執行個體或叢集指定的子網路必須有足夠的未指派 IP 位址，以容納目標 VM 的數目。 如果子網路沒有足夠的未指派 IP 位址，則計算叢集只會完成部分配置。
> * 查看您對虛擬網路其訂用帳戶或資源群組的安全性原則或鎖定是否限制了管理虛擬網路的權限。 如果您打算透過限制流量來保護虛擬網路，請針對計算服務開放一些連接埠。 如需詳細資訊，請參閱[必要連接埠](#mlcports)一節。
> * 如果您要將多個計算執行個體或叢集放在一個虛擬網路中，您可能必須要求一或多個資源的配額增加。
> * 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，則這些帳戶必須位於與 Azure Machine Learning 計算執行個體或叢集相同的虛擬網路中。 
> * 若要讓計算執行個體 Jupyter 功能能夠運作，請確定您並未停用 Web 通訊端通訊。 請確定您的網路允許 websocket 連接到 *. instances.azureml.net 和 *. instances.azureml.ms。 
> * 當計算實例部署在私人連結工作區時，只能從虛擬網路中存取。 如果您使用自訂 DNS 或 hosts 檔案，請新增 `<instance-name>.<region>.instances.azureml.ms` 具有工作區私人端點私人 IP 位址的專案。 如需詳細資訊，請參閱 [自訂 DNS](./how-to-custom-dns.md) 文章。
    
> [!TIP]
> Machine Learning 計算執行個體或叢集會自動將額外的網路資源配置 __在包含虛擬網路的資源群組中__。 針對每個計算執行個體或叢集，服務會配置下列資源：
> 
> * 一個網路安全性群組
> * 一個公用 IP 位址
> * 一個負載平衡器
> 
> 如果是叢集，這些資源會在每次叢集縮小為 0 個節點時遭到刪除 (然後再重新建立)，不過如果是執行個體，資源則會一直保留到執行個體遭到徹底刪除為止 (停止執行個體並不會移除資源)。 
> 這些資源會被訂用帳戶的[資源配額](../azure-resource-manager/management/azure-subscription-service-limits.md)所限制。


### <a name="required-ports"></a><a id="mlcports"></a> 所需連接埠

如果您打算藉由限制進出公用網際網路的網路流量來保護虛擬網路，您必須允許來自 Azure Batch 服務的輸入通訊。

Batch 服務會在連結至 VM 的網路介面 (NIC) 層級新增網路安全性群組 (NSG)。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 連接埠 29876 和 29877 上的輸入 TCP 流量，來自 __BatchNodeManagement__ 的 __服務標籤__。

    ![使用 BatchNodeManagement 服務標籤的輸入規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (選擇性) 連接埠 22 上的輸入 TCP 流量，用來允許遠端存取。 只有在想要於公用 IP 上使用 SSH 進行連線時，才可使用此連接埠。

- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

- 針對連接埠 44224 上的計算執行個體輸入 TCP 流量，來自 __AzureMachineLearning__ 的 __服務標籤__。

> [!IMPORTANT]
> 如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 封鎖對計算節點的通訊，則計算服務會將計算節點的狀態設定為 [無法使用]。
>
> 您不需要在子網路層級指定 NSG，因為 Azure Batch 服務會設定其本身的 NSG。 但是，如果包含 Azure Machine Learning 計算的子網有相關聯的 Nsg 或防火牆，您也必須允許先前列出的流量。

下圖顯示 Azure 入口網站中的 NSG 規則設定：

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Machine Learning Compute 的輸入 NSG 規則" border="true":::



![Machine Learning Compute 的輸入 NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 限制來自虛擬網路的輸出連線能力

如果您不想要使用預設輸出規則，而是想要限制虛擬網路的輸出存取，請使用下列步驟：

- 使用 NSG 規則來拒絕輸出網際網路連線。

- 針對 __計算執行個體__ 或 __計算叢集__，限制目的地為下列項目的輸出流量：
   - Azure 儲存體，方法是使用 __Storage.RegionName__ 的 __服務標籤__。 其中 `{RegionName}` 是 Azure 區域的名稱。
   - Azure Container Registry，方法是使用 __AzureContainerRegistry.RegionName__ 的 __服務標籤__。 其中 `{RegionName}` 是 Azure 區域的名稱。
   - Azure Machine Learning，方法是使用 __AzureMachineLearning__ 的 __服務標籤__
   - Azure Resource Manager，方法是使用 __AzureResourceManager__ 的 __服務標籤__
   - Azure Active Directory，方法是使用 __AzureActiveDirectory__ 的 __服務標籤__

下圖顯示 Azure 入口網站中的 NSG 規則設定：

[![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> 如果您打算使用 Microsoft 提供的預設 Docker 映射，以及啟用使用者管理的相依性，您也必須使用下列 __服務標記__：
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> 當您用來作為定型指令碼一部分的程式碼與下列程式碼片段類似時，則需要進行下列設定：
>
> __RunConfig 定型__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __估算器定型__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>強制通道

如果您搭配 Azure Machine Learning 的計算使用 [強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ，您必須允許從包含計算資源的子網與公用網際網路進行通訊。 這項通訊用於工作排程和存取 Azure 儲存體。

您可以透過兩種方式來完成此動作：

* 使用 [虛擬網路 NAT](../virtual-network/nat-overview.md)。 NAT 閘道可為您虛擬網路中的一或多個子網提供輸出網際網路連線能力。 如需詳細資訊，請參閱 [使用 NAT 閘道資源設計虛擬網路](../virtual-network/nat-gateway-resource.md)。

* 將 [使用者定義的路由 (udr) ](../virtual-network/virtual-networks-udr-overview.md) 新增至包含計算資源的子網。 在資源所在的區域中，為 Azure Batch 服務所使用的每個 IP 位址建立一個 UDR。 這些 UDR 可讓 Batch 服務與計算節點通訊，以排程工作。 此外，也請為資源所在的 Azure Machine Learning 服務新增 IP 位址，必須有此 IP 位址才能存取計算執行個體。 若要取得 Batch 服務和 Azure Machine Learning 服務的 IP 位址清單，請使用下列其中一種方法：

    * 下載 [Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)並搜尋檔案中的 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`，其中 `<region>` 是您的 Azure 區域。

    * 使用 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) 下載該資訊。 下列範例會下載 IP 位址資訊，並篩選出美國東部 2 區域的資訊：

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > 如果您使用美國弗吉尼亞州、US-Arizona 地區或中國東部2區域，則這些命令不會傳回任何 IP 位址。 相反地，請使用下列其中一個連結來下載 IP 位址清單：
        >
        > * [適用于 Azure Government 的 Azure IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [適用于 Azure 中國的 Azure IP 範圍和服務標記](https://www.microsoft.com//download/details.aspx?id=57062)
    
    當您新增 UDR 時，請為每個相關的「批次 IP」位址首碼定義路由，然後將 [下一個躍點類型] 設定為 [網際網路]。 下圖顯示此 UDR 在 Azure 入口網站中的範例：

    ![位址首碼的 UDR 範例](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > IP 位址可能會隨著時間而變更。

    除了您定義的任何 Udr 之外，您也必須透過內部部署網路設備來允許 Azure 儲存體的輸出流量。 具體而言，此流量的 Url 的格式如下： `<account>.table.core.windows.net` 、 `<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 。 

    如需詳細資訊，請參閱[在虛擬網路中建立 Azure Batch 集區](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>在虛擬網路中建立計算叢集

若要建立 Machine Learning Compute 叢集，請使用下列步驟：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)，然後選取您的訂用帳戶和工作區。

1. 選取左側的 [計算]。

1. 從中央選取 [定型叢集]，然後選取 [+]。

1. 在 [新增定型叢集] 對話方塊中，展開 [進階設定] 區段。

1. 若要將此計算資源設定為使用虛擬網路，請在 [設定虛擬網路] 區段中執行下列動作：

    1. 在 [資源群組] 下拉式清單中，選取包含虛擬網路的資源群組。
    1. 在 [虛擬網路] 下拉式清單中，選取包含子網路的虛擬網路。
    1. 在 [子網路] 下拉式清單中，選取要使用的子網路。

   ![Machine Learning Compute 的虛擬網路設定](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

您也可以透過使用 Azure Machine Learning SDK 建立 Machine Learning Compute 叢集。 下列程式碼會在名為 `mynetwork` 的虛擬網路其 `default` 子網路中建立新的 Machine Learning Compute 叢集：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

建立程序完成後，您便會在實驗中使用叢集來將模型定型。 如需詳細資訊，請參閱[選取與使用定型的計算目標](how-to-set-up-training-targets.md)。

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>存取計算實例筆記本中的資料

如果您在 Azure 計算實例上使用筆記本，您必須確定您的筆記本正在相同虛擬網路和子網後方的計算資源上執行，以作為您的資料。 

您必須在 [**設定**  >  **虛擬網路**] 下的 [Advanced settings] 下，將您的計算實例設定為在相同的虛擬網路中。 您無法將現有的計算實例新增至虛擬網路。

## <a name="azure-databricks"></a>Azure Databricks

若要搭配您的工作區使用虛擬網路中的 Azure Databricks，您必須符合下列需求：

> [!div class="checklist"]
> * 虛擬網路必須在與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
> * 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，則這些帳戶必須位於與 Azure Databricks 叢集相同的虛擬網路中。
> * 除了 Azure Databricks 所使用的 __databricks-private__ 和 __databricks-public__ 子網路外，還需要有針對虛擬網路所建立的 __預設__ 子網路。

如需搭配虛擬網路使用 Azure Databricks 的特定資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虛擬機器或 HDInsight 叢集

> [!IMPORTANT]
> Azure Machine Learning 僅支援執行 Ubuntu 的虛擬機器。

在本節中，您將瞭解如何使用虛擬網路中的虛擬機器或 Azure HDInsight 叢集搭配您的工作區。

### <a name="create-the-vm-or-hdinsight-cluster"></a>建立 VM 或 HDInsight 叢集

透過使用 Azure 入口網站或 Azure CLI 來建立 VM 或 HDInsight 叢集，並將叢集放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文章：
* [建立和管理適用於 Linux VM 的 Azure 虛擬網路](../virtual-machines/linux/tutorial-virtual-network.md)

* [使用 Azure 虛擬網路延伸 HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>設定網路埠 

允許 Azure Machine Learning 與 VM 或叢集上的 SSH 埠通訊，設定網路安全性群組的來源專案。 SSH 連接埠通常是連接埠 22。 若要允許來自此來源的流量，請執行下列動作：

1. 在 [來源] 下拉式清單中選取 [服務標籤]。

1. 在 [來源服務標籤] 下拉式清單中選取 [AzureMachineLearning]。

    ![用於對虛擬網路內的 VM 或 HDInsight 叢集進行測試的輸入規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. 在 [來源連接埠範圍] 下拉式清單中選取 [ __*__ ]。

1. 在 [目的地] 下拉式清單中選取 [任何]。

1. 在 [目的地連接埠範圍] 下拉式清單中選取 [22]。

1. 在 [通訊協定] 底下選取 [任何]。

1. 在 [動作] 底下選取 [允許]。

請保留網路安全性群組的預設輸出規則。 如需詳細資訊，請參閱[安全性群組](../virtual-network/network-security-groups-overview.md#default-security-rules)中的預設安全性規則一節。

如果您不想使用預設輸出規則，而是想要限制虛擬網路的輸出存取，請參閱[限制來自虛擬網路的輸出連線能力](#limiting-outbound-from-vnet)一節。

### <a name="attach-the-vm-or-hdinsight-cluster"></a>連接 VM 或 HDInsight 叢集

將 VM 或 HDInsight 叢集連結至 Azure Machine Learning 工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

## <a name="next-steps"></a>後續步驟

本文是四部分虛擬網路系列的第三部分。 請參閱文章的其餘部分，以瞭解如何保護虛擬網路：

* [第1部分：虛擬網路總覽](how-to-network-security-overview.md)
* [第2部分：保護工作區資源](how-to-secure-workspace-vnet.md)
* [第4部分：保護推斷環境](how-to-secure-inferencing-vnet.md)
* [第5部分：啟用 studio 功能](how-to-enable-studio-virtual-network.md)