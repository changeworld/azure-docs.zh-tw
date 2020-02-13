---
title: 保護虛擬網路中的實驗和推斷
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 虛擬網路內的 Azure Machine Learning 中，保護測試/定型作業和推斷/評分作業。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: fd358801b5fe84aac754b5a975234688a707e544
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169955"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>在 Azure 虛擬網路中保護 Azure ML 實驗和推斷作業
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure 虛擬網路（vnet）中 Azure Machine Learning 保護測試/定型作業，以及推斷/評分作業。

**虛擬網路**會作為安全性界限，將您的 Azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 藉由加入網路，您可以安全地定型模型，並存取已部署的模型以進行推斷。

Azure Machine Learning 依賴其他 Azure 服務來計算資源。 計算資源或[計算目標](concept-compute-target.md)是用來定型和部署模型。 目標可以在虛擬網路內建立。 例如，您可以使用 Microsoft 資料科學虛擬機器來定型模型，然後將模型部署至 Azure Kubernetes Service （AKS）。 如需虛擬網路的詳細資訊，請參閱[Azure 虛擬網路總覽](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

本文也提供有關「*高級安全性設定*」的詳細資訊，也就是基本或實驗性使用案例不需要的資訊。 本文的某些章節會提供各種案例的設定資訊。 您不需要依序或完整完成指示。

> [!TIP]
> 除非特別呼叫，否則使用資源（例如儲存體帳戶或虛擬網路內的計算目標）將會使用機器學習管線，以及非管線工作流程（例如腳本執行）。

> [!WARNING]
> Microsoft 不支援使用 Azure Machine Learning 設計工具或自動化機器學習（從 studio）與虛擬網路內的資源。

## <a name="prerequisites"></a>Prerequisites

+ Azure Machine Learning[工作區](how-to-manage-workspace.md)。

+ [Azure 虛擬網路服務](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)和[IP 網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)的一般工作知識。

+ 既有的虛擬網路和子網，可與您的計算資源搭配使用。

## <a name="use-a-storage-account-for-your-workspace"></a>針對您的工作區使用儲存體帳戶

若要在虛擬網路中使用 Azure 儲存體帳戶作為工作區，請使用下列步驟：

1. 建立虛擬網路後方的計算資源（例如，Machine Learning 計算實例或叢集），或將計算資源附加至工作區（例如，HDInsight 叢集、虛擬機器或 Azure Kubernetes Service 叢集）。 計算資源可以用於實驗或模型部署。

   如需詳細資訊，請參閱這篇文章中的[使用 Machine Learning 計算](#amlcompute)、[使用虛擬機器或 HDInsight](#vmorhdi)叢集和[使用 Azure Kubernetes Service](#aksvnet)一節。

1. 在 Azure 入口網站中，移至附加至工作區的儲存體。

   [![附加至 Azure Machine Learning 工作區的儲存體](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在 [ **Azure 儲存體**] 頁面上，選取 [__防火牆和虛擬網路__]。

   ![[Azure 儲存體] 頁面上的 [防火牆和虛擬網路] 區域 Azure 入口網站](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在 [__防火牆和虛擬網路__] 頁面上，執行下列動作：
    - 選取 [選取的網路]。
    - 在 [__虛擬網路__] 底下，選取 [__新增現有的虛擬網路__] 連結。 此動作會新增您的計算所在的虛擬網路（請參閱步驟1）。

        > [!IMPORTANT]
        > 儲存體帳戶必須與用於定型或推斷的計算實例或叢集位於相同的虛擬網路中。

    - 選取 [__允許受信任的 Microsoft 服務存取此儲存體帳戶__] 核取方塊。

    > [!IMPORTANT]
    > 使用 Azure Machine Learning SDK 時，您的開發環境必須能夠連接到 Azure 儲存體帳戶。 當儲存體帳戶位於虛擬網路內時，防火牆必須允許從開發環境的 IP 位址進行存取。
    >
    > 若要啟用儲存體帳戶的存取權，請*從開發用戶端上的網頁瀏覽器*造訪儲存體帳戶的__防火牆和虛擬網路__。 然後使用 [__新增您的用戶端 ip 位址__] 核取方塊，將用戶端的 ip 位址新增至__位址範圍__。 您也可以使用 [__位址範圍__] 欄位，手動輸入開發環境的 IP 位址。 新增用戶端的 IP 位址之後，即可使用 SDK 來存取儲存體帳戶。

   [![Azure 入口網站中的 [防火牆和虛擬網路] 窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> 您可以將 Azure Machine Learning 的_預設儲存體帳戶_，或虛擬網路中的_非預設儲存體帳戶_。
>
> 當您建立工作區時，會自動布建預設儲存體帳戶。
>
> 針對非預設儲存體帳戶， [`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)函式中的 `storage_account` 參數可讓您依 AZURE 資源識別碼指定自訂儲存體帳戶。

## <a name="use-azure-data-lake-storage-gen-2"></a>使用 Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen 2 是一組適用于大規模資料分析的功能，建置於 Azure Blob 儲存體上。 它可以用來儲存用來將模型定型的資料，Azure Machine Learning。 

若要在 Azure Machine Learning 工作區的虛擬網路中使用 Data Lake Storage Gen 2，請使用下列步驟：

1. 建立 Azure Data Lake Storage gen 2 帳戶。 如需詳細資訊，請參閱[建立 Azure Data Lake Storage Gen2 儲存體帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

1. 使用上一節中的步驟2-4，將[儲存體帳戶用於您的工作區](#use-a-storage-account-for-your-workspace)，以將帳戶放在虛擬網路中。

在虛擬網路內使用具有 Data Lake Storage Gen 2 的 Azure Machine Learning 時，請使用下列指導方針：

* 如果您使用__SDK 來建立資料集__，而執行程式碼的系統__不在虛擬網路中__，請使用 `validate=False` 參數。 此參數會略過驗證，如果系統不在與儲存體帳戶相同的虛擬網路中，就會失敗。 如需詳細資訊，請參閱[from_files （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)方法。

* 使用 Azure Machine Learning 計算實例或計算叢集來使用資料集來定型模型時，它必須位於與儲存體帳戶相同的虛擬網路中。

## <a name="use-a-key-vault-instance-with-your-workspace"></a>搭配您的工作區使用 key vault 實例

Azure Machine Learning 會使用與工作區相關聯的金鑰保存庫實例來儲存下列認證：
* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫實例的密碼
* 資料存放區的連接字串

若要搭配虛擬網路背後的 Azure Key Vault 使用 Azure Machine Learning 試驗功能，請使用下列步驟：

1. 移至與工作區相關聯的金鑰保存庫。

   [![與 Azure Machine Learning 工作區相關聯的金鑰保存庫](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. 在 [ **Key Vault** ] 頁面上，選取左窗格中的 [__防火牆和虛擬網路__]。

   ![[Key Vault] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在 [__防火牆和虛擬網路__] 頁面上，執行下列動作：
    - 在 [允許存取來源] 之下，選取 [選取的網路]。
    - 在 [__虛擬網路__] 底下，選取 [__新增現有的虛擬網路__]，以新增您的實驗計算所在的虛擬網路。
    - 在 [__允許信任的 Microsoft 服務略過此防火牆__] 底下，選取 __[是]__ 。

   [![[Key Vault] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>使用 Machine Learning Compute

若要在虛擬網路中使用 Azure Machine Learning 計算實例或計算叢集，必須符合下列網路需求：

> [!div class="checklist"]
> * 虛擬網路必須位於與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
> * 為計算實例或叢集指定的子網必須有足夠的未指派 IP 位址，以容納目標 Vm 的數目。 如果子網沒有足夠的未指派 IP 位址，將會部分配置計算叢集。
> * 查看虛擬網路的訂用帳戶或資源群組的安全性原則或鎖定，是否限制管理虛擬網路的許可權。 如果您打算透過限制流量來保護虛擬網路，請讓計算服務的一些埠保持開啟。 如需詳細資訊，請參閱[必要連接埠](#mlcports)一節。
> * 如果您要將多個計算實例或叢集放在一個虛擬網路中，您可能需要要求增加一或多個資源的配額。
> * 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，它們必須位於與 Azure Machine Learning 計算實例或叢集相同的虛擬網路中。 

Machine Learning 計算實例或叢集會自動在包含虛擬網路的資源群組中配置額外的網路資源。 針對每個計算實例或叢集，服務會配置下列資源：

* 一個網路安全性群組
* 一個公用 IP 位址
* 一個負載平衡器

這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)所限制。


### <a id="mlcports"></a> 所需連接埠

Machine Learning Compute 目前使用 Azure Batch 服務將 VM 佈建在指定的虛擬網路中。 子網路必須允許來自 Batch 服務的輸入通訊。 您可以使用此通訊來排程 Machine Learning Compute 節點上的執行，以及與 Azure 儲存體和其他資源進行通訊。 Batch 服務會在連接至 Vm 的網路介面（Nic）層級新增網路安全性群組（Nsg）。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 埠29876和29877上的輸入 TCP 流量，來自__BatchNodeManagement__的__服務標記__。

    ![使用 BatchNodeManagement 服務標記的輸入規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- 選擇性埠22上的輸入 TCP 流量，以允許遠端存取。 只有當您想要在公用 IP 上使用 SSH 進行連接時，才使用此埠。

- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

- 針對來自__AzureMachineLearning__ __服務標記__的埠44224上的計算實例輸入 TCP 流量。

如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 封鎖與計算節點的通訊，則計算服務會將計算節點的狀態設定為 [無法使用]。

您不需要在子網層級指定 Nsg，因為 Azure Batch 服務會設定自己的 Nsg。 不過，如果指定的子網有相關聯的 Nsg 或防火牆，請設定前面所述的輸入和輸出安全性規則。

下列影像顯示 Azure 入口網站中的 NSG 規則設定：

[![Machine Learning Compute 的輸入 NSG 規則](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>限制來自虛擬網路的輸出連線能力

如果您不想要使用預設輸出規則，而您想要限制虛擬網路的輸出存取，請使用下列步驟：

- 使用 NSG 規則拒絕連出網際網路連線。

- 針對__計算實例__或__計算__叢集，將輸出流量限制為下列專案：
   - Azure 儲存體，方法是使用__RegionName__的__服務標記__。 其中 `{RegionName}` 是 Azure 區域的名稱。
   - Azure Container Registry，方法是使用__AzureContainerRegistry. RegionName__的__服務標記__。 其中 `{RegionName}` 是 Azure 區域的名稱。
   - Azure Machine Learning，方法是使用__AzureMachineLearning__的__服務標記__
   
- 針對__計算實例__，請同時新增下列專案：
   - Azure Resource Manager，方法是使用__AzureResourceManager__的__服務標記__
   - Azure Active Directory，方法是使用__AzureActiveDirectory__的__服務標記__

下圖顯示 Azure 入口網站中的 NSG 規則設定：

[![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> 如果您打算使用 Microsoft 所提供的預設 Docker 映射，以及啟用使用者管理的相依性，您也必須使用__Region_Name MicrosoftContainerRegistry__的__服務__標籤（例如，MicrosoftContainerRegistry. EastUS）。
>
> 當您的定型腳本中有類似下列程式碼片段的程式碼時，需要進行此設定：
>
> __RunConfig 訓練__
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
> __估計工具訓練__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>強制通道的使用者定義路由

如果您搭配 Machine Learning Compute 使用強制通道，請將[使用者定義的路由（udr）](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)新增至包含計算資源的子網。

* 在資源所在的區域中，為 Azure Batch 服務所使用的每個 IP 位址建立一個 UDR。 這些 Udr 可讓 Batch 服務與計算節點進行通訊，以進行工作排程。 若要取得 Batch 服務的 IP 位址清單，請使用下列其中一種方法：

    * 下載[AZURE IP 範圍和服務](https://www.microsoft.com/download/details.aspx?id=56519)標籤，並搜尋檔案中的 `BatchNodeManagement.<region>`，其中 `<region>` 是您的 Azure 區域。

    * 使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)下載資訊。 下列範例會下載 IP 位址資訊，並篩選出美國東部2區域的資訊：

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* 您的內部部署網路應用裝置不得封鎖對 Azure 儲存體的輸出流量。 具體而言，Url 的格式為 `<account>.table.core.windows.net`、`<account>.queue.core.windows.net`和 `<account>.blob.core.windows.net`。

當您新增 Udr 時，請定義每個相關批次 IP 位址首碼的路由，並將 __[下一個躍點類型]__ 設定為 [__網際網路__]。 下圖顯示此 UDR 在 Azure 入口網站中的範例：

![位址首碼的 UDR 範例](./media/how-to-enable-virtual-network/user-defined-route.png)

如需詳細資訊，請參閱[在虛擬網路中建立 Azure Batch 集](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)區。

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>在虛擬網路中建立計算叢集

若要建立 Machine Learning Compute 叢集，請使用下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)中，選取您的 Azure Machine Learning 工作區。

1. 在 [__應用程式__] 區段中，選取 [__計算__]，然後選取 [__新增計算__]。

1. 若要將此計算資源設定為使用虛擬網路，請執行下列動作：

    a. 針對 [__網路__設定]，選取 [ __Advanced__]。

    b. 在 [__資源群組__] 下拉式清單中，選取包含虛擬網路的資源群組。

    c. 在 [__虛擬網路__] 下拉式清單中，選取包含子網的虛擬網路。

    d. 在 [__子網__] 下拉式清單中，選取要使用的子網。

   ![Machine Learning Compute 的虛擬網路設定](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

您也可以透過使用 Azure Machine Learning SDK 建立 Machine Learning Compute 叢集。 下列程式碼會在名為 `default` 的虛擬網路其 `mynetwork` 子網路中建立新的 Machine Learning Compute 叢集：

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

當建立程式完成時，您可以在實驗中使用叢集來定型模型。 如需詳細資訊，請參閱[選取與使用定型的計算目標](how-to-set-up-training-targets.md)。

## <a name="use-azure-databricks"></a>使用 Azure Databricks

若要在虛擬網路中搭配您的工作區使用 Azure Databricks，必須符合下列需求：

> [!div class="checklist"]
> * 虛擬網路必須位於與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
> * 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，它們必須位於與 Azure Databricks 叢集相同的虛擬網路中。
> * 除了 Azure Databricks 使用的__databricks-私__用和__databricks-public__子網之外，也需要為虛擬網路建立的__預設__子網。

如需搭配虛擬網路使用 Azure Databricks 的特定資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虛擬機器或 HDInsight 叢集

> [!IMPORTANT]
> Azure Machine Learning 僅支援執行 Ubuntu 的虛擬機器。

若要在虛擬網路中使用虛擬機器或 Azure HDInsight 叢集與您的工作區，請使用下列步驟：

1. 使用 Azure 入口網站或 Azure CLI 建立 VM 或 HDInsight 叢集，並將叢集放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文章：
    * [建立和管理適用於 Linux VM 的 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允許 Azure Machine Learning 與 VM 或叢集上的 SSH 埠通訊，請設定網路安全性群組的來源專案。 SSH 連接埠通常是連接埠 22。 若要允許來自此來源的流量，請執行下列動作：

    * 在 [__來源__] 下拉式清單中，選取 [__服務標記__]。

    * 在 [__來源服務標記__] 下拉式清單中，選取 [ __AzureMachineLearning__]。

    * 在 [__來源埠範圍__] 下拉式清單中，選取 [ __*__ ]。

    * 在 [__目的地__] 下拉式清單中，選取 [__任何__]。

    * 在 [__目的地埠範圍__] 下拉式清單中，選取 [ __22__]。

    * 在 [__通訊協定__] 底下，選取 [__任何__]。

    * 在 [__動作__] 底下，選取 [__允許__]。

   ![在虛擬網路內的 VM 或 HDInsight 叢集上進行實驗的輸入規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留網路安全性群組的預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

    如果您不想要使用預設輸出規則，而想要限制虛擬網路的輸出存取，請參閱[限制來自虛擬網路的輸出](#limiting-outbound-from-vnet)連線一節。

1. 將 VM 或 HDInsight 叢集連結至您的 Azure Machine Learning 工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service (AKS)

若要將虛擬網路中的 AKS 新增至您的工作區，請使用下列步驟：

> [!IMPORTANT]
> 開始下列程式之前，請遵循在[Azure Kubernetes Service 中設定 advanced 網路中的必要條件（AKS）](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites)作法和規劃叢集的 IP 位址。
>
> AKS 實例和 Azure 虛擬網路必須位於相同的區域。 如果您保護虛擬網路中的工作區所使用的 Azure 儲存體帳戶，它們必須位於與 AKS 實例相同的虛擬網路中。

1. 在[Azure 入口網站](https://portal.azure.com)中，請確定控制虛擬網路的 NSG 具有可使用__AzureMachineLearning__做為**來源**而啟用 Azure Machine Learning 的輸入規則。

    [![Azure Machine Learning 新增計算窗格](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. 選取您的 Azure Machine Learning 工作區。

1. 在 [__應用程式__] 區段中，選取 [__計算__]，然後選取 [__新增計算__]。

1. 若要將此計算資源設定為使用虛擬網路，請執行下列動作：

    - 針對 [__網路__設定]，選取 [ __Advanced__]。

    - 在 [__資源群組__] 下拉式清單中，選取包含虛擬網路的資源群組。

    - 在 [__虛擬網路__] 下拉式清單中，選取包含子網的虛擬網路。

    - 在 [__子網__] 下拉式清單中，選取 [子網]。

    - 在 [ __Kubernetes 服務位址範圍__] 方塊中，輸入 Kubernetes 服務位址範圍。 此位址範圍會使用無類別網域間路由（CIDR）標記法 IP 範圍來定義可用於叢集的 IP 位址。 它不得與任何子網 IP 範圍重迭（例如 10.0.0.0/16）。

    - 在 [ __KUBERNETES dns 服務 ip 位址__] 方塊中，輸入 Kubernetes DNS 服務的 ip 位址。 此 IP 位址會指派給 Kubernetes DNS 服務。 它必須在 Kubernetes 服務位址範圍內（例如10.0.0.10）。

    - 在 [ __docker 橋接器位址__] 方塊中，輸入 docker 橋接器位址。 此 IP 位址會指派給 Docker 橋接器， 不得位於任何子網 IP 範圍或 Kubernetes 服務位址範圍（例如，172.17.0.1/16）。

   ![Azure Machine Learning： Machine Learning Compute 虛擬網路設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 請確定控制虛擬網路的 NSG 群組具有針對評分端點啟用的輸入安全性規則，以便可從虛擬網路外部呼叫。
   > [!IMPORTANT]
   > 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

   [![輸入安全性規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

您也可以使用 Azure Machine Learning SDK，在虛擬網路中新增 Azure Kubernetes Service。 如果您在虛擬網路中已經有 AKS 叢集，請將其附加至工作區，如[如何部署至 AKS](how-to-deploy-and-where.md)中所述。 下列程式碼會在名為 `mynetwork`之虛擬網路的 `default` 子網中建立新的 AKS 實例：

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

當建立程式完成時，您可以在虛擬網路背後的 AKS 叢集上執行推斷或模型評分。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-and-where.md)。

### <a name="use-private-ips-with-azure-kubernetes-service"></a>搭配 Azure Kubernetes Service 使用私人 Ip

根據預設，公用 IP 位址會指派給 AKS 部署。 在虛擬網路內使用 AKS 時，您可以改為使用私人 IP 位址。 只能從虛擬網路或聯結的網路內部存取私人 IP 位址。

藉由將 AKS 設定為使用_內部負載平衡器_，可啟用私人 IP 位址。 

> [!IMPORTANT]
> 建立 Azure Kubernetes Service 叢集時，您無法啟用私人 IP。 您必須將它啟用為現有叢集的更新。

下列程式碼片段示範如何**建立新的 AKS**叢集，然後將其更新為使用私人 IP/內部負載平衡器：

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

命令所參考之 `body.json` 檔案的內容，與下列 JSON 檔類似：

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> 目前，您無法在現有叢集上執行__附加__作業時設定負載平衡器。 您必須先連接叢集，然後執行更新作業來變更負載平衡器。

如需搭配 AKS 使用內部負載平衡器的詳細資訊，請參閱[使用內部負載平衡器搭配 Azure Kubernetes Service](/azure/aks/internal-lb)。

## <a name="use-azure-firewall"></a>使用 Azure 防火牆

使用 Azure 防火牆時，您必須設定網路規則，以允許進出下列位址的流量：

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

新增規則時，請將__通訊協定__設定為 [任何]，並將埠設為 [`*`]。

如需設定網路規則的詳細資訊，請參閱[部署和設定 Azure 防火牆](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)。

## <a name="next-steps"></a>後續步驟

* [設定定型環境](how-to-set-up-training-targets.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)
