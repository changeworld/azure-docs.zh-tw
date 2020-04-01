---
title: 虛擬網路中的安全實驗和推理
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 虛擬網路中的 Azure 機器學習中保護實驗/培訓作業和推理/評分作業。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/13/2020
ms.openlocfilehash: 6e300bbec097201b33f0c576db91c2ca720fb921
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437394"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>在 Azure 虛擬網路中保護 Azure ML 實驗和推理作業
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將學習如何在 Azure 虛擬網路 (vnet) 中的 Azure 機器學習中保護實驗/培訓作業和推理/評分作業。

**虛擬網路**充當安全邊界,將 Azure 資源與公共 Internet 隔離開來。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 通過加入網路,您可以安全地訓練模型並訪問已部署的模型進行推理。

Azure 機器學習依賴於其他 Azure 服務來計算資源。 計算資源或[計算目標](concept-compute-target.md)用於訓練和部署模型。 可以在虛擬網路中創建目標。 例如,可以使用 Microsoft 數據科學虛擬機訓練模型,然後將模型部署到 Azure 庫伯奈斯服務 (AKS)。 有關虛擬網路的詳細資訊,請參閱[Azure 虛擬網路概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

本文還提供有關*高級安全設置*的詳細資訊,這些資訊對於基本或實驗性用例來說不需要。 本文的某些部分為各種方案提供配置資訊。 您無需按順序或完整完成說明。

> [!TIP]
> 除非明確調用,否則在虛擬網路中使用存儲帳戶或計算目標等資源將同時使用機器學習管道和非管道工作流(如腳本運行)。

> [!WARNING]
> Microsoft 不支援使用 Azure 機器學習設計器或自動化機器學習(從工作室)與虛擬網路內的資源。

## <a name="prerequisites"></a>Prerequisites

+ Azure 機器學習[工作區](how-to-manage-workspace.md)。

+ [Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)服務和[IP網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)的一般工作知識。

+ 預先存在的虛擬網路和子網,用於計算資源。

## <a name="use-a-storage-account-for-your-workspace"></a>為工作區使用儲存帳戶

要在虛擬網路中對工作區使用 Azure 儲存帳戶,請使用以下步驟:

1. 在虛擬網路後面創建計算資源(例如機器學習計算實例或群集),或將計算資源附加到工作區(例如,HDInsight 群集、虛擬機或 Azure Kubernetes 服務群集)。 計算資源可用於實驗或模型部署。

   有關詳細資訊,請參閱本文中的[「使用機器學習計算](#amlcompute)、[使用虛擬機或 HDInsight 叢集](#vmorhdi)」以及使用[Azure Kubernetes 服務](#aksvnet)部分。

1. 在 Azure 門戶中,轉到附加到工作區的存儲。

   [![附加到 Azure 機器學習工作區的儲存](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在**Azure 儲存**頁上,選擇__防火牆和虛擬網路__。

   ![Azure 門戶中 Azure 儲存頁上的「防火牆和虛擬網路」區域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在__防火牆和虛擬網路__頁面上,執行以下操作:
    - 選取 [選取的網路]____。
    - 在__虛擬網路__下,選擇 __「添加現有虛擬網路__連結」。。 此操作將添加計算所在的虛擬網路(請參閱步驟 1)。

        > [!IMPORTANT]
        > 存儲帳戶必須與用於訓練或推理的計算實例或群集位於同一虛擬網路和子網中。

    - 勾選此__儲存的 Microsoft 服務存取此儲存帳戶__「複選框」。

    > [!IMPORTANT]
    > 使用 Azure 機器學習 SDK 時,開發環境必須能夠連接到 Azure 儲存帳戶。 當存儲帳戶位於虛擬網路內時,防火牆必須允許從開發環境的 IP 位址進行訪問。
    >
    > 要開啟儲存帳戶的存取,*請存取開發用戶端的網頁瀏覽器*的儲存帳戶的__防火牆和虛擬網路__。 然後使用 __'新增用戶端 IP 位址__' 來加入__位址的__IP 位址範圍 。 您還可以使用 __「地址範圍」__ 欄位手動輸入開發環境的 IP 位址。 添加用戶端的 IP 位址後,可以使用 SDK 存取儲存帳戶。

   [![Azure 門戶中的「防火牆和虛擬網路」窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> 您可以將 Azure 機器學習的_預設儲存帳戶_或_非預設儲存帳戶_放在虛擬網路中。
>
> 創建工作區時,將自動預配預設存儲帳戶。
>
> 對於非預設儲存帳戶,`storage_account`[`Workspace.create()`函數](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)中的參數允許您按 Azure 資源 ID 指定自訂儲存帳戶。

## <a name="use-azure-data-lake-storage-gen-2"></a>使用 Azure 資料儲存第 2 代

Azure 資料儲存第 2 代是一組基於 Azure Blob 儲存建構的大數據分析功能。 它可用於存儲用於使用 Azure 機器學習訓練模型的數據。 

要在 Azure 機器學習工作區的虛擬網路中使用資料湖儲存第 2 代,請使用以下步驟:

1. 創建 Azure 資料儲存第 2 代帳戶。 有關詳細資訊,請參閱建立[Azure 資料湖儲存 Gen2 儲存帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

1. 使用上一節中的步驟[2-4,使用工作區的存儲帳戶](#use-a-storage-account-for-your-workspace),將帳戶放入虛擬網路中。

在虛擬網路中將 Azure 機器學習與資料儲存第 2 代一起使用時,請使用以下指南:

* 如果使用 SDK__建立資料集__,並且執行程式碼的系統__不在虛擬網路中__,請使用 參數`validate=False`。 此參數跳過驗證,如果系統與存儲帳戶的虛擬網路不同,驗證將失敗。 有關詳細資訊,請參閱[from_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)方法。

* 當使用 Azure 機器學習計算實例或計算群集使用數據集訓練模型時,它必須與存儲帳戶位於同一虛擬網路中。

## <a name="use-a-key-vault-instance-with-your-workspace"></a>將金鑰保管庫實例與工作區一起使用

Azure 機器學習使用與工作區關聯的密鑰保管庫實例來存儲以下認證:
* 關聯的儲存帳戶連接字串
* Azure 容器儲存庫實例的密碼
* 與資料儲存的連線字串

要將 Azure 機器學習實驗功能與虛擬網路後面的 Azure 密鑰保管庫一起使用,請使用以下步驟:

1. 轉到與工作區關聯的密鑰保管庫。

   [![與 Azure 機器學習工作區關聯的金鑰保管庫](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. 在 **"金鑰保管庫**"頁上,在左窗格中,選擇__防火牆和虛擬網路__。

   ![金鑰保管庫窗格中的「防火牆和虛擬網路」部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在__防火牆和虛擬網路__頁面上,執行以下操作:
    - 在 [允許存取來源]____ 之下，選取 [選取的網路]____。
    - 在__虛擬網路__下,選擇 __「添加現有虛擬網路__」以添加實驗計算所在的虛擬網路。
    - 在 __「允許受信任的 Microsoft 服務繞過此防火牆__」下,選擇 __「是__」。。

   [![金鑰保管庫窗格中的「防火牆和虛擬網路」部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>使用機器學習運算

要在虛擬網路中使用 Azure 機器學習計算實例或計算群集,必須滿足以下網路要求:

> [!div class="checklist"]
> * 虛擬網路必須與 Azure 機器學習工作區位於同一訂閱和區域中。
> * 為計算實例或群集指定的子網必須有足夠的未分配的 IP 位址來容納目標 VM 的數量。 如果子網沒有足夠的未分配的 IP 位址,則計算群集將部分分配。
> * 檢查您的安全原則或虛擬網路訂閱或資源組的鎖是否限制管理虛擬網路的許可權。 如果計劃通過限制流量來保護虛擬網路,請為計算服務保留某些埠。 如需詳細資訊，請參閱[必要連接埠](#mlcports)一節。
> * 如果要將多個計算實例或群集放在一個虛擬網路中,則可能需要請求增加一個或多個資源的配額。
> * 如果工作區的 Azure 儲存帳戶也在虛擬網路中受到保護,則它們必須與 Azure 機器學習計算實例或群集位於同一虛擬網路中。 

> [!TIP]
> 機器學習計算實例或群集會自動在包含虛擬網路的資源組中分配其他網路資源。 對於每個計算實例或群集,服務分配以下資源:
> 
> * 一個網路安全性群組
> * 一個公用 IP 位址
> * 一個負載平衡器
> 
> 這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)所限制。


### <a name="required-ports"></a><a id="mlcports"></a> 所需連接埠

Machine Learning Compute 目前使用 Azure Batch 服務將 VM 佈建在指定的虛擬網路中。 子網路必須允許來自 Batch 服務的輸入通訊。 使用此通訊可以安排在機器學習計算節點上運行,並與 Azure 存儲和其他資源進行通信。 Batch 服務在連接到 VM 的網路介面 (NIC) 級別添加網路安全組 (NSG)。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 從__BatchNode管理____的服務標籤__在埠 29876 和 29877 上的入站 TCP 流量。

    ![使用 BatchNode 管理服務標記的入站規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- ( 選擇性的 )埠 22 上的入站 TCP 流量允許遠程訪問。 僅當您希望在公共 IP 上使用 SSH 連接時,才使用此埠。

- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

- 對於計算實例從__Azure 機械學習____的服務標記__在埠 44224 上的入站 TCP 流量。

如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 阻止與計算節點的通信,則計算服務將計算節點的狀態設置為不可用。

不需要在子網級別指定 NSG,因為 Azure Batch 服務配置其自己的 NSG。 但是,如果指定的子網具有關聯的 NSG 或防火牆,請按照前面所述配置入站和出站安全規則。

Azure 門戶中的 NSG 規則設定顯示在以下影像中:

[![機器學習計算的入站 NSG 規則](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![機器學習計算的出站 NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>限制來自虛擬網路的出站連線

如果不想使用預設出站規則,並且確實希望限制虛擬網路的出站訪問,請使用以下步驟:

- 使用 NSG 規則拒絕出站網路連接。

- 對於__計算實例__或__計算群集__,將出站流量限制為以下項:
   - 使用儲存__的服務標記____.區域名稱__的 Azure 儲存。 Azure`{RegionName}`區域的名稱在哪裡。
   - Azure 容器註冊表,透過使用 Azure__容器註冊.區域名稱____的服務標記__。 Azure`{RegionName}`區域的名稱在哪裡。
   - 使用__Azure 機器學習____的服務標記__,Azure 機器學習
   - 使用__Azure 資源管理員____的服務標記__,Azure 資源管理員
   - 使用__Azure ActiveDirectory__ __的服務標記__,Azure 活動目錄

Azure 門戶中的 NSG 規則設定顯示在下圖中:

[![機器學習計算的出站 NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> 如果您計劃使用 Microsoft 提供的預設 Docker 映像,並啟用使用者託管依賴項,則必須使用__Microsoft 容器註冊.Region_Name(__ 例如,Microsoft 容器註冊.EastUS)__的服務標記__。
>
> 當您在訓練文稿中具有類似於以下代碼段的代碼時,需要此設定:
>
> __執行設定培訓__
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
> __估算器培訓__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>強制通道的使用者定義路由

如果使用機器學習計算強制隧道,則將[使用者定義的路由 (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)添加到包含計算資源的子網。

* 為 Azure Batch 服務在資源所在的區域中使用的每個 IP 位址建立 UDR。 這些 UDR 使批次處理服務能夠與計算節點進行任務調度。 要取得 Batch 服務的 IP 位址清單,請使用以下方法之一:

    * 下載[Azure IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=56519),`BatchNodeManagement.<region>`然後搜尋該檔`<region>`,Azure 區域所在的 位置。

    * 使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)下載資訊。 以下範例下載 IP 位址資訊並篩選出東美國 2 區域的資訊:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* 本地網路設備不得阻止到 Azure 儲存的出站流量。 具體而言,URL 以窗`<account>.table.core.windows.net`體`<account>.queue.core.windows.net`、`<account>.blob.core.windows.net`和 。

新增 UDR 時,請為每個相關的批次處理 IP 位址前置字串定義路由,並將__下一個躍點類型__設定為__Internet__。 下圖顯示了 Azure 門戶中此 UDR 的範例:

![位址前置文字的 UDR 範例](./media/how-to-enable-virtual-network/user-defined-route.png)

有關詳細資訊,請參閱[在虛擬網路中創建 Azure 批次處理池](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>建立虛擬網路建立計算叢集

要建立機器學習計算群集,請使用以下步驟:

1. 登錄到[Azure 機器學習工作室](https://ml.azure.com/),然後選擇訂閱和工作區。

1. 選取左側的 [計算]____。

1. 從中心選擇__訓練叢集__,__+__ 然後選擇 。

1. 在 __"新建訓練群集"__ 對話框中,展開 __「高級設置」__ 部分。

1. 要將此計算資源設定為使用虛擬網路,請在 __「設定虛擬網路__」部分執行以下操作:

    1. 在 __「資源群組__」下拉清單中,選擇包含虛擬網路的資源群組。
    1. 在__虛擬網路__下拉清單中,選擇包含子網的虛擬網路。
    1. 在__子網__下拉清單中,選擇要使用的子網。

   ![機器學習計算的虛擬網路設定](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

創建過程完成後,在實驗中使用群集來訓練模型。 如需詳細資訊，請參閱[選取與使用定型的計算目標](how-to-set-up-training-targets.md)。

## <a name="use-azure-databricks"></a>使用 Azure 資料塊

要在虛擬網路中使用 Azure 資料塊與工作區一起,必須滿足以下要求:

> [!div class="checklist"]
> * 虛擬網路必須與 Azure 機器學習工作區位於同一訂閱和區域中。
> * 如果工作區的 Azure 儲存帳戶也在虛擬網路中受到保護,則它們必須與 Azure 數據塊群集位於同一虛擬網路中。
> * 除了 Azure Databricks 使用__的數據塊專用__子網和__數據磚塊公共__子網外,還需要為虛擬網路創建的__預設__子網。

有關將 Azure 資料塊與虛擬網路一起使用的特定資訊,請參閱[在 Azure 虛擬網路中部署 Azure 資料塊](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虛擬機器或 HDInsight 叢集

> [!IMPORTANT]
> Azure 機器學習僅支援運行 Ubuntu 的虛擬機器。

要在具有工作區的虛擬網路中使用虛擬機或 Azure HDInsight 群集,請使用以下步驟:

1. 使用 Azure 門戶或 Azure CLI 創建 VM 或 HDInsight 群集,並將群集放入 Azure 虛擬網路。 如需詳細資訊，請參閱下列文章：
    * [為 Linux VM 建立與管理 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 要允許 Azure 機器學習與 VM 或群集上的 SSH 埠進行通信,請為網路安全組配置源條目。 SSH 連接埠通常是連接埠 22。 要允許來自此源的流量,請執行以下操作:

    * 在 __「源__」下拉清單中,選擇__服務標記__。

    * 在 __「源服務」 標記__下拉清單中,選擇__Azure 機器學習__。

    * 在 __「源埠範圍__下拉清單」__*__ 中, 選擇 。

    * 在 __「目標__」下拉清單中,選擇 __「任意__」。。

    * 在__目標埠範圍__下拉清單中,選擇__22__。

    * 在 __"協定__"下,選擇 __"任意__"。

    * 在 __"操作"__ 下,選擇 __"允許__"。

   ![用於在虛擬網路中的 VM 或 HDInsight 叢集上進行實驗的入站規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留網路安全組的預設出站規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

    如果不想使用預設出站規則,並且確實希望限制虛擬網路的出站訪問,請參閱[虛擬網路部分的"限制出站連接](#limiting-outbound-from-vnet)"。

1. 將 VM 或 HDInsight 群集附加到 Azure 機器學習工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service (AKS)

要將虛擬網路中的 AKS 新增到工作區,請使用以下步驟:

> [!IMPORTANT]
> 在開始以下過程之前,請按照[Azure Kubernetes 服務 (AKS) 中的配置進階網路](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites)的先決條件操作方法並規劃群集的 IP 定址。
>
> AKS 實例和 Azure 虛擬網路必須位於同一區域中。 如果保護虛擬網路中工作區使用的 Azure 儲存帳戶,它們必須與 AKS 實例位於同一虛擬網路中。

> [!WARNING]
> Azure 機器學習不支援使用啟用專用連結的 Azure 庫伯奈斯服務。

1. 登錄到[Azure 機器學習工作室](https://ml.azure.com/),然後選擇訂閱和工作區。

1. 選取左側的 [計算]____。

1. 從中心選擇 __「推理」群集__,__+__ 然後選擇 。

1. 在 __"新建推理群集"__ 對話框中,在 __「網路設定__」下選擇 __「進階__」 。

1. 要使用此計算資源使用虛擬網路,請執行以下操作:

    1. 在 __「資源群組__」下拉清單中,選擇包含虛擬網路的資源群組。
    1. 在__虛擬網路__下拉清單中,選擇包含子網的虛擬網路。
    1. 在__子網__下拉清單中,選擇子網。
    1. 在__庫伯內斯服務位址範圍__框中,輸入庫伯內斯服務位址範圍。 此位址範圍使用無類域間路由 (CIDR) 表示 IP 範圍來定義可用於群集的 IP 位址。 它不得與任何子網 IP 範圍重疊(例如,10.0.0.0/16)。
    1. 在__庫伯內斯 DNS 服務 IP 位址__框中,輸入庫伯內斯 DNS 服務 IP 位址。 此 IP 位址會指派給 Kubernetes DNS 服務。 它必須在 Kubernetes 服務地址範圍內(例如,10.0.0.10)。
    1. __Docker 閘道位址__框中,輸入 Docker 閘接位址。 此 IP 位址會指派給 Docker 橋接器， 它不得在任何子網 IP 範圍內或 Kubernetes 服務位址範圍(例如,172.17.0.1/16)。

   ![Azure 機器學習:機器學習計算虛擬網路設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 確保控制虛擬網路的 NSG 組為評分終結點啟用了入站安全規則,以便可以從虛擬網路外部調用該規則。
   > [!IMPORTANT]
   > 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

   [![輸入安全規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

您還可以使用 Azure 機器學習 SDK 在虛擬網路中添加 Azure 庫伯奈斯服務。 如果虛擬網路中已有 AKS 群集,請將其附加到工作區,[如如何部署到 AKS](how-to-deploy-and-where.md)中所述。 以下代碼在名為`default``mynetwork`的虛擬網路的子網路中建立新的 AKS 實體:

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

創建過程完成後,您可以在虛擬網路後面的 AKS 群集上運行推理或模型評分。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-and-where.md)。

### <a name="use-private-ips-with-azure-kubernetes-service"></a>將專用 IP 與 Azure 庫伯奈斯服務一起使用

默認情況下,公共 IP 位址分配給 AKS 部署。 在虛擬網路中使用 AKS 時,可以使用專用 IP 位址。 專用 IP 位址只能透過虛擬網路或聯接網路內部存取。

通過設定 AKS 來使用_內部負載均衡器_,啟用專用 IP 位址。 

> [!IMPORTANT]
> 創建 Azure 庫伯奈斯服務群集時,無法啟用專用 IP。 必須將其作為對現有群集的更新啟用。

以下代碼段演示如何**建立新的 AKS 群集**,然後更新它以使用專用 IP/內部負載均衡器:

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
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
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

指令引用`body.json`的檔案內容類似於以下 JSON 文件:

```json
{ 
    "location": "<region>", 
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
> 當前,在現有群集上執行__附加__操作時,無法配置負載均衡器。 必須首先附加群集,然後執行更新操作以更改負載均衡器。

有關將內部負載均衡器與 AKS 一起使用的詳細資訊,請參閱[使用 Azure Kubernetes 服務使用內部負載均衡器](/azure/aks/internal-lb)。

## <a name="use-azure-firewall"></a>使用 Azure 防火牆

使用 Azure 防火牆時,必須設定網路規則以允許流量往來和從以下位址流:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

新增規則時,將__協定__設定為任何,並將連接埠設定為`*`。

有關設定網路規則的詳細資訊,請參閱[部署和設定 Azure 防火牆](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)。

## <a name="use-azure-container-registry"></a>使用 Azure Container Registry

> [!IMPORTANT]
> Azure 容器註冊表 (ACR) 可以放在虛擬網路中,但必須滿足以下先決條件:
>
> * Azure 機器學習工作區必須是企業版。 有關升級的資訊,請參閱[升級到企業版](how-to-manage-workspace.md#upgrade)。
> * Azure 容器註冊表必須是高級版本。 有關升級的詳細資訊,請參閱更改[SKU](/azure/container-registry/container-registry-skus#changing-skus)。
> * Azure 容器註冊表必須與用於定型或推理的存儲帳戶和計算目標位於同一虛擬網路和子網中。
> * Azure 機器學習工作區必須包含[Azure 機器學習計算群集](how-to-set-up-training-targets.md#amlcompute)。
>
>     當 ACR 位於虛擬網路後面時,Azure 機器學習不能使用它直接生成 Docker 映射。 相反,計算群集用於生成映射。

1. 要查找工作區的 Azure 容器註冊表的名稱,請使用以下方法之一:

    __Azure 入口網站__

    從工作區的概述部分,「__註冊表__」值連結到 Azure 容器註冊表。

    ![工作區的 Azure 容器註冊表](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __Azure CLI__

    如果已[為 Azure CLI 安裝了機器學習擴展](reference-azure-machine-learning-cli.md)`az ml workspace show`,則可以使用 命令 顯示工作區資訊。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令會傳回如下值：`"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`。 字串的最後一部分是工作區的 Azure 容器註冊表的名稱。

1. 要限制對虛擬網路的訪問,請使用[註冊表的「設定網路存取」](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)中的步驟。 添加虛擬網路時,為 Azure 機器學習資源選擇虛擬網路和子網。

1. 使用 Azure 機器學習 Python SDK 配置計算群集以生成 Docker 映射。 以下代碼段展示如何執行此操作:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 存儲帳戶、計算群集和 Azure 容器註冊表必須都位於虛擬網路的同一子網中。
    
    有關詳細資訊,請參閱[update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none-)方法引用。

1. 如果對 Azure 機器學習工作區使用專用連結,並將工作區的 Azure 容器註冊表放在虛擬網路中,則還必須應用以下 Azure 資源管理器範本。 此範本使工作區能夠通過專用鏈路與 ACR 通信。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>後續步驟

* [設定訓練環境](how-to-set-up-training-targets.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 TLS 透過 Azure 機器學習保護 Web 服務](how-to-secure-web-service.md)
