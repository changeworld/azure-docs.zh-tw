---
title: 網路隔離和隱私權
titleSuffix: Azure Machine Learning
description: 使用隔離的 Azure 虛擬網路搭配 Azure Machine Learning 來保護測試/定型以及推斷/評分作業。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: df819f5ff641af014750d6501c8b168e54917318
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420527"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>使用私人虛擬網路進行定型 & 推斷期間的網路隔離
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何藉由隔離 Azure 虛擬網路（vnet）中的 Azure Machine Learning 訓練和推斷作業，來保護您的機器學習服務生命週期。 Azure Machine Learning 依賴其他 Azure 服務來取得計算資源（也稱為[計算目標](concept-compute-target.md)），以定型和部署模型。 目標可以建立在虛擬網路內。 例如，您可以使用 Azure Machine Learning 計算來將模型定型，然後將模型部署至 Azure Kubernetes Service (AKS)。 

__虛擬網路__可充當安全邊界，將 Azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 藉由加入網路，您可以安全地將模型定型，並可存取所部署的模型以進行推斷。

## <a name="prerequisites"></a>Prerequisites

+ Azure Machine Learning [工作區](how-to-manage-workspace.md)。

+ 同時具備 [Azure 虛擬網路服務](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)和 [IP 網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)的一般知識。

+ 既有的虛擬網路和子網路，以便與您的計算資源搭配使用。

## <a name="private-endpoints"></a>私人端點

您也可以[啟用 Azure Private Link](how-to-configure-private-link.md) 以使用私人端點來連線到工作區。 私人端點是虛擬網路內的一組私人 IP 位址。 [請了解如何設定此私人端點。](how-to-configure-private-link.md)



> [!TIP]
> 您可以將虛擬網路和 Private Link 結合在一起，以保護工作區與其他 Azure 資源之間的通訊。 不過，有些組合需要 Enterprise Edition 工作區。 請使用下表來了解需要 Enterprise Edition 的狀況：
>
> | 狀況 | Enterprise</br>edition | 基本</br>edition |
> | ----- |:-----:|:-----:| 
> | 沒有虛擬網路或 Private Link | ✔ | ✔ |
> | 沒有 Private Link 的工作區。 虛擬網路中的其他資源 (Azure Container Registry 除外) | ✔ | ✔ |
> | 沒有 Private Link 的工作區。 具有 Private Link 的其他資源 | ✔ | |
> | 具有 Private Link 的工作區。 虛擬網路中的其他資源 (Azure Container Registry 除外) | ✔ | ✔ |
> | 具有 Private Link 的工作區和任何其他資源 | ✔ | |
> | 具有 Private Link 的工作區。 沒有 Private Link 或虛擬網路的其他資源 | ✔ | ✔ |
> | 虛擬網路中的 Azure Container Registry | ✔ | |
> | 工作區的客戶管理金鑰 | ✔ | |
> 

> [!WARNING]
> 
> 已啟用 Private Link 的工作區不支援 Azure Machine Learning 計算執行個體預覽。

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning studio

如果您的資料儲存在虛擬網路中，您必須使用工作區[受控身分識別](../active-directory/managed-identities-azure-resources/overview.md)，將您資料的存取權授與 studio。

> [!IMPORTANT]
> 雖然大部分的 studio 都能使用儲存在虛擬網路中的資料，但整合式筆記本__並不會__。 整合式筆記本不支援使用虛擬網路中的存放裝置。 相反地，您可以使用計算實例中的 Jupyter 筆記本。 如需詳細資訊，請參閱[存取計算實例筆記本中的資料](#access-data-in-a-compute-instance-notebook)一節。

如果您無法授與 studio 存取權，就會收到此錯誤， `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 並停用下列作業：

* 預覽 studio 中的資料。
* 在設計工具中將資料視覺化。
* 提交 AutoML 實驗。
* 開機磁碟區標專案。

Studio 支援從虛擬網路中的下列資料存放區類型讀取資料：

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="add-resources-to-the-virtual-network"></a>將資源新增至虛擬網路 

將您的工作區和儲存體帳戶新增至相同的虛擬網路，讓它們可以彼此存取。

1. 若要將您的工作區連線到虛擬網路，請[啟用 Azure 私人連結](how-to-configure-private-link.md)。 這項功能目前為預覽狀態，適用于美國東部、美國西部2、美國中南部區域。

1. 若要將您的儲存體帳戶連線到虛擬網路，請設定[[防火牆] 和 [虛擬網路] 設定](#use-a-storage-account-for-your-workspace)。

### <a name="configure-a-datastore-to-use-managed-identity"></a>設定資料存放區以使用受控識別

將您的工作區和儲存體服務帳戶新增至虛擬網路之後，您必須將資料存放區設定為使用受控識別來存取您的資料。 這些步驟會使用 Azure 資源型存取控制（RBAC），將工作區受控識別新增為儲存體服務的__讀取器__。 __讀者__存取可讓工作區取得防火牆設定，並確保資料不會離開虛擬網路。

1. 在 studio 中，選取 [__資料存放區__]。

1. 若要建立新的資料存放區，請選取 [ __+ 新增資料__存放區]。 若要更新現有的資料存放區，請選取資料存放區，然後選取 [__更新認證__]。

1. 在 [資料存放區設定] 中，選取 __[是]__ 以__允許 Azure Machine Learning 服務使用工作空間受控識別存取儲存體__。

> [!NOTE]
> 這些變更可能需要10分鐘的時間才會生效。

### <a name="azure-blob-storage-blob-data-reader"></a>Azure Blob 儲存體 Blob 資料讀取器

針對__Azure Blob 儲存體__，工作區受控識別也會新增為[Blob 資料讀取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)，讓它可以從 blob 儲存體讀取資料。


### <a name="azure-machine-learning-designer-default-datastore"></a>Azure Machine Learning 設計工具預設資料存放區

根據預設，設計工具會使用附加至您工作區的儲存體帳戶來儲存輸出。 不過，您可以指定它將輸出儲存到您有權存取的任何資料存放區。 如果您的環境使用虛擬網路，您可以使用這些控制項來確保您的資料保持安全且可供存取。

若要為管線設定新的預設儲存體：

1. 在管線草稿中，選取靠近管線標題附近的 [**設定] 齒輪圖示**。
1. 選取 [**選取預設資料**存放區]。
1. 指定新的資料存放區。

您也可以根據每個模組來覆寫預設的資料存放區。 這可讓您控制每個個別模組的儲存位置。

1. 選取您要指定其輸出的模組。
1. 展開 [**輸出設定**] 區段。
1. 選取 [覆**寫預設輸出設定**]。
1. 選取 [**設定輸出設定**]。
1. 指定新的資料存放區。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 存取控制

您可以使用 RBAC 和 POSIX 樣式的存取控制清單（Acl）來控制虛擬網路內的資料存取。

若要使用 RBAC，請將工作區受控識別新增至[Blob 資料讀取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 如需詳細資訊，請參閱[角色型存取控制](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)。

若要使用 Acl，可以將存取權指派給工作區受控識別，就像任何其他安全性原則一樣。 如需詳細資訊，請參閱檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。


### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 存取控制

Azure Data Lake Storage Gen1 只支援 POSIX 樣式的存取控制清單。 您可以將工作區受控識別的存取權指派給資源，就像任何其他安全性原則一樣。 如需詳細資訊，請參閱[Azure Data Lake Storage Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。


### <a name="azure-sql-database-contained-user"></a>Azure SQL Database 包含的使用者

若要使用受控識別存取儲存在 Azure SQL Database 中的資料，您必須建立對應至受控識別的 SQL 內含使用者。 如需從外部提供者建立使用者的詳細資訊，請參閱[建立對應至 Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)身分識別的包含使用者。

建立 SQL 包含的使用者之後，請使用[Grant t-sql 命令](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)授與許可權。

### <a name="connect-to-the-studio"></a>連接至 studio

如果您是從虛擬網路內部的資源（例如，計算實例或虛擬機器）存取 studio，則必須允許從虛擬網路到 studio 的輸出流量。 

例如，如果您使用網路安全性群組（NSG）來限制輸出流量，請將規則新增至__AzureFrontDoor__的__服務標記__目的地。

## <a name="use-a-storage-account-for-your-workspace"></a>針對工作區使用儲存體帳戶

> [!IMPORTANT]
> 適用於 Azure Machine Learning 的_預設儲存體帳戶_ 或_非預設儲存體帳戶_都可以放在虛擬網路中。
>
> 建立工作區時便會自動佈建預設儲存體帳戶。
>
> 若為非預設儲存體帳戶，[`Workspace.create()` 函式](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 中的 `storage_account` 參數可讓您依 Azure 資源識別碼來指定自訂儲存體帳戶。

若要將 Azure 儲存體服務用於虛擬網路中的工作區，請使用下列步驟：

1. 在虛擬網路後方建立計算資源 (例如，Machine Learning 計算執行個體或叢集)，或將計算資源連結至工作區 (例如，HDInsight 叢集、虛擬機器或 Azure Kubernetes Service 叢集)。 計算資源可以是用於實驗或模型部署的。

   如需詳細資訊，請參閱本文中的[使用 Machine Learning 計算](#amlcompute)、[使用虛擬機器或 HDInsight 叢集](#vmorhdi)和[使用 Azure Kubernetes Service](#aksvnet) 等節。

1. 在 [Azure 入口網站中，移至您想要在工作區中使用的儲存體服務。

   [![連結至 Azure Machine Learning 工作區的儲存體](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在 [儲存體服務帳戶] 頁面上，選取 [__防火牆和虛擬網路__]。

   ![Azure 入口網站中 [Azure 儲存體] 頁面上的 [防火牆和虛擬網路] 區域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在 [防火牆和虛擬網路] 頁面上，執行下列動作：
    - 選取 [選取的網路]。
    - 在 [虛擬網路] 底下，選取 [新增現有虛擬網路] 連結。 此動作會新增您的計算所在的虛擬網路（請參閱步驟1）。

        > [!IMPORTANT]
        > 儲存體帳戶必須與用於定型或推斷的計算執行個體或叢集位於相同的虛擬網路和子網路中。

    - 選取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊。

    > [!IMPORTANT]
    > 在使用 Azure Machine Learning SDK 時，您的開發環境必須能夠連線到 Azure 儲存體帳戶。 當儲存體帳戶位於虛擬網路內時，防火牆必須允許從開發環境的 IP 位址進行存取。
    >
    > 若要啟用對於儲存體帳戶的存取權，請*從開發用戶端上的網頁瀏覽器*造訪儲存體帳戶 的 [防火牆和虛擬網路]。 然後使用 [新增用戶端 IP 位址] 核取方塊，將用戶端的 IP 位址新增至 [位址範圍]。 您也可以使用 [位址範圍] 欄位，手動輸入開發環境的 IP 位址。 用戶端新增好 IP 位址之後，即可使用 SDK 來存取儲存體帳戶。

   [![Azure 入口網站中的 [防火牆和虛擬網路] 窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>使用資料存放區和資料集

本節涵蓋 SDK 體驗的資料存放區和資料集使用方式。 如需有關 studio 體驗的詳細資訊，請參閱[Machine Learning studio](#machine-learning-studio)一節。

根據預設，當您嘗試使用 SDK 存取資料時，Azure Machine Learning 會執行資料有效性和認證檢查。 如果您的資料位於虛擬網路後方，Azure Machine Learning 無法存取資料並使其檢查失敗。 若要避免這種情況，您必須建立會略過驗證的資料存放區和資料集。

### <a name="use-a-datastore"></a>使用資料存放區

 Azure Data Lake 存放區 Gen1 和 Azure Data Lake 存放區 Gen2 預設會略過驗證，因此不需要採取任何進一步的動作。 不過，針對下列服務，您可以使用類似的語法來略過資料存放區驗證：

- Azure Blob 儲存體
- Azure 檔案共用
- PostgreSQL
- Azure SQL Database

下列程式碼範例會建立新的 Azure Blob 資料存放區和集合 `skip_validation=True` 。

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-a-dataset"></a>使用資料集

略過資料集驗證的語法與下列資料集類型類似：
- 分隔的檔案
- JSON 
- Parquet
- SQL
- 檔案

下列程式碼會建立新的 JSON 資料集，並設定 `validate=False` 。

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>計算叢集和執行個體 

若要在虛擬網路中使用[受控 Azure Machine Learning __計算目標__](concept-compute-target.md#azure-machine-learning-compute-managed)或 [Azure Machine Learning 計算__執行個體__](concept-compute-instance.md)，您必須符合下列網路需求：

> [!div class="checklist"]
> * 虛擬網路必須在與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
> * 為計算執行個體或叢集指定的子網路必須有足夠的未指派 IP 位址，以容納目標 VM 的數目。 如果子網路沒有足夠的未指派 IP 位址，則計算叢集只會完成部分配置。
> * 查看您對虛擬網路其訂用帳戶或資源群組的安全性原則或鎖定是否限制了管理虛擬網路的權限。 如果您打算透過限制流量來保護虛擬網路，請針對計算服務開放一些連接埠。 如需詳細資訊，請參閱[必要連接埠](#mlcports)一節。
> * 如果您要將多個計算執行個體或叢集放在一個虛擬網路中，您可能必須要求一或多個資源的配額增加。
> * 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，則這些帳戶必須位於與 Azure Machine Learning 計算執行個體或叢集相同的虛擬網路中。 
> * 若要讓計算執行個體 Jupyter 功能能夠運作，請確定您並未停用 Web 通訊端通訊。

> [!TIP]
> Machine Learning 計算執行個體或叢集會自動將額外的網路資源配置__在包含虛擬網路的資源群組中__。 針對每個計算執行個體或叢集，服務會配置下列資源：
> 
> * 一個網路安全性群組
> * 一個公用 IP 位址
> * 一個負載平衡器
> 
> 如果是叢集，這些資源會在每次叢集縮小為 0 個節點時遭到刪除 (然後再重新建立)，不過如果是執行個體，資源則會一直保留到執行個體遭到徹底刪除為止 (停止執行個體並不會移除資源)。 
> 這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)所限制。


### <a name="required-ports"></a><a id="mlcports"></a> 所需連接埠

如果您打算藉由限制進出公用網際網路的網路流量來保護虛擬網路，您必須允許來自 Azure Batch 服務的輸入通訊。

Batch 服務會在連結至 VM 的網路介面 (NIC) 層級新增網路安全性群組 (NSG)。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 連接埠 29876 和 29877 上的輸入 TCP 流量，來自 __BatchNodeManagement__ 的__服務標籤__。

    ![使用 BatchNodeManagement 服務標籤的輸入規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (選擇性) 連接埠 22 上的輸入 TCP 流量，用來允許遠端存取。 只有在想要於公用 IP 上使用 SSH 進行連線時，才可使用此連接埠。

- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

- 針對連接埠 44224 上的計算執行個體輸入 TCP 流量，來自 __AzureMachineLearning__ 的__服務標籤__。

> [!IMPORTANT]
> 如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 封鎖對計算節點的通訊，則計算服務會將計算節點的狀態設定為 [無法使用]。
>
> 您不需要在子網路層級指定 NSG，因為 Azure Batch 服務會設定其本身的 NSG。 不過，如果包含 Azure Machine Learning 計算的子網有相關聯的 Nsg 或防火牆，您也必須允許先前所列的流量。

下圖顯示 Azure 入口網站中的 NSG 規則設定：

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Machine Learning Compute 的輸入 NSG 規則" border="true":::



![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 限制來自虛擬網路的輸出連線能力

如果您不想要使用預設輸出規則，而是想要限制虛擬網路的輸出存取，請使用下列步驟：

- 使用 NSG 規則來拒絕輸出網際網路連線。

- 針對__計算執行個體__或__計算叢集__，限制目的地為下列項目的輸出流量：
   - Azure 儲存體，方法是使用 __Storage.RegionName__ 的__服務標籤__。 其中 `{RegionName}` 是 Azure 區域的名稱。
   - Azure Container Registry，方法是使用 __AzureContainerRegistry.RegionName__ 的__服務標籤__。 其中 `{RegionName}` 是 Azure 區域的名稱。
   - Azure Machine Learning，方法是使用 __AzureMachineLearning__ 的__服務標籤__
   - Azure Resource Manager，方法是使用 __AzureResourceManager__ 的__服務標籤__
   - Azure Active Directory，方法是使用 __AzureActiveDirectory__ 的__服務標籤__

下圖顯示 Azure 入口網站中的 NSG 規則設定：

[![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> 如果您打算使用 Microsoft 所提供的預設 Docker 映射，以及啟用使用者管理的相依性，您也必須使用下列__服務__標籤：
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

如果您使用[強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)搭配 Azure Machine Learning 計算，則必須允許從包含計算資源的子網與公用網際網路進行通訊。 這項通訊用於工作排程和存取 Azure 儲存體。

有兩種方式可以完成此動作：

* 使用[虛擬網路 NAT](../virtual-network/nat-overview.md)。 NAT 閘道會為您虛擬網路中的一或多個子網提供輸出網際網路連線能力。 如需相關資訊，請參閱[使用 NAT 閘道資源設計虛擬網路](../virtual-network/nat-gateway-resource.md)。

* 將[使用者定義的路由（udr）](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)新增至包含計算資源的子網。 在資源所在的區域中，為 Azure Batch 服務所使用的每個 IP 位址建立一個 UDR。 這些 UDR 可讓 Batch 服務與計算節點通訊，以排程工作。 此外，也請為資源所在的 Azure Machine Learning 服務新增 IP 位址，必須有此 IP 位址才能存取計算執行個體。 若要取得 Batch 服務和 Azure Machine Learning 服務的 IP 位址清單，請使用下列其中一種方法：

    * 下載 [Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)並搜尋檔案中的 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`，其中 `<region>` 是您的 Azure 區域。

    * 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 下載該資訊。 下列範例會下載 IP 位址資訊，並篩選出美國東部 2 區域的資訊：

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```
    
    當您新增 UDR 時，請為每個相關的「批次 IP」位址首碼定義路由，然後將 [下一個躍點類型] 設定為 [網際網路]。 下圖顯示此 UDR 在 Azure 入口網站中的範例：

    ![位址首碼的 UDR 範例](./media/how-to-enable-virtual-network/user-defined-route.png)

    除了您定義的任何 Udr 外，您必須透過內部部署網路設備，允許 Azure 儲存體的輸出流量。 具體而言，此流量的 Url 會採用下列格式： `<account>.table.core.windows.net` 、 `<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 。 

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

如果您在 Azure 計算實例上使用筆記本，您必須確定您的筆記本是在與您的資料相同的虛擬網路和子網後方的計算資源上執行。 

您必須在建立時，將您的計算實例設定為位於相同虛擬網路中的 [**設定**  >  **虛擬網路**] 下的 [Advanced settings]。 您無法將現有的計算實例新增至虛擬網路。

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

若要將虛擬網路中的 Azure Kubernetes Service (AKS) 新增至您的工作區，請使用下列步驟：

> [!IMPORTANT]
> 在開始進行下列程序前，請先遵循[在 Azure Kubernetes Service (AKS) 中設定進階網路](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)操作說明中的必要條件，並規劃叢集的 IP 位址。
>
> AKS 執行個體和 Azure 虛擬網路必須位於相同的區域。 如果您在虛擬網路中保護工作區所使用的 Azure 儲存體帳戶，則這些帳戶必須位於與 AKS 執行個體相同的虛擬網路中。

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

1. 請確定控制虛擬網路的 NSG 群組已針對評分端點啟用輸入安全性規則，以便可從虛擬網路外部加以呼叫。
   > [!IMPORTANT]
   > 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

   [![輸入安全性規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

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

### <a name="use-private-ips-with-azure-kubernetes-service"></a>搭配 Azure Kubernetes Service 使用私人 IP

根據預設，系統會向 AKS 部署指派公用 IP 位址。 在虛擬網路內使用 AKS 時，您可以改為使用私人 IP 位址。 您只能從虛擬網路或已加入的網路內部存取私人 IP 位址。

藉由將 AKS 設定為使用_內部負載平衡器_即可啟用私人 IP 位址。 

#### <a name="network-contributor-role"></a>網路參與者角色

> [!IMPORTANT]
> 如果您藉由提供您先前建立的虛擬網路來建立或連結 AKS 叢集，您必須將_網路參與者_角色授與包含虛擬網路的資源群組，以取得 AKS 叢集的服務主體（SP）或受控識別。 這必須在您嘗試將內部負載平衡器變更為私人 IP 之前完成。
>
> 若要將身分識別新增為網路參與者，請使用下列步驟：

1. 若要尋找 AKS 的服務主體或受控識別識別碼，請使用下列 Azure CLI 命令。 將 `<aks-cluster-name>` 取代為叢集的名稱。 將取代 `<resource-group-name>` 為_包含 AKS_叢集的資源組名：

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    如果此命令傳回的值 `msi` ，請使用下列命令來識別受控識別的主體識別碼：

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 若要尋找包含您虛擬網路之資源群組的識別碼，請使用下列命令。 將取代 `<resource-group-name>` 為_包含虛擬網路_的資源組名：

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. 若要將服務主體或受控識別新增為網路參與者，請使用下列命令。 `<SP-or-managed-identity>`以服務主體或受控識別傳回的識別碼取代。 將取代為 `<resource-group-id>` 包含虛擬網路的資源群組所傳回的識別碼：

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
如需使用內部負載平衡器搭配 AKS 的詳細資訊，請參閱 [使用內部負載平衡器搭配 Azure Kubernetes Service](/azure/aks/internal-lb)。

#### <a name="enable-private-ip"></a>啟用私人 IP

> [!IMPORTANT]
> 建立 Azure Kubernetes Service 叢集時無法啟用私人 IP。 您必須透過對現有叢集更新的方式來加以啟用。

下列程式碼片段示範如何__建立新的 AKS 叢集__，然後將其更新為使用私人 IP/內部負載平衡器：

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

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

命令所參考 `body.json` 檔案的內容會與下列 JSON 文件類似：

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
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

將__現有的叢集附加__至您的工作區時，您必須等到附加作業之後，才能設定負載平衡器。

如需連接叢集的詳細資訊，請參閱[附加現有的 AKS](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)叢集。

附加現有的叢集之後，您就可以更新叢集以使用私人 IP。

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

## <a name="use-azure-container-instances-aci"></a>使用 Azure 容器執行個體 (ACI)

部署模型時會以動態方式建立 Azure 容器執行個體。 若要讓 Azure Machine Learning 能夠在虛擬網路內部建立 ACI，您必須為部署所使用的子網路啟用__子網路委派__。

> [!WARNING]
> 在虛擬網路中使用 Azure 容器實例時，虛擬網路必須位於與您 Azure Machine Learning 工作區相同的資源群組中。
>
> 在虛擬網路內使用 Azure 容器實例時，您工作區的 Azure Container Registry （ACR）也不能在虛擬網路中。

若要在工作區的虛擬網路中使用 ACI，請使用下列步驟：

1. 若要在虛擬網路上啟用子網路委派，請使用[新增或移除子網路委派](../virtual-network/manage-subnet-delegation.md)一文中的資訊。 您可以在建立虛擬網路時啟用委派，也可以將其新增至現有網路。

    > [!IMPORTANT]
    > 在啟用委派時，請使用 `Microsoft.ContainerInstance/containerGroups` 作為 [將子網路委派給服務] 值。

2. 使用 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) 來部署模型，並使用 `vnet_name` 和 `subnet_name` 參數。 將這些參數設定為啟用委派的虛擬網路名稱和子網路。

## <a name="azure-firewall"></a>Azure 防火牆

如需搭配使用 Azure Machine Learning 與 Azure 防火牆的詳細資訊，請參閱[使用 Azure 防火牆後方的 Azure Machine Learning 工作區](how-to-access-azureml-behind-firewall.md)。

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> Azure Container Registry (ACR) 可以放在虛擬網路中，但必須符合下列必要條件：
>
> * Azure Machine Learning 工作區必須是 Enterprise Edition。 如需升級的相關資訊，請參閱[升級至 Enterprise Edition](how-to-manage-workspace.md#upgrade)。
> * 您的 Azure Machine Learning 工作區區域應為 [[已啟用 privated 連結] 區域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)。 
> * Azure Container Registry 必須是進階版本。 如需升級的詳細資訊，請參閱[變更 SKU](/azure/container-registry/container-registry-skus#changing-skus)。
> * Azure Container Registry 所在的虛擬網路和子網路必須與用於定型或推斷的儲存體帳戶和計算目標相同。
> * Azure Machine Learning 工作區必須包含 [Azure Machine Learning 計算叢集](how-to-set-up-training-targets.md#amlcompute)。
>
>     當 ACR 位於虛擬網路後方時，Azure Machine Learning 就無法使用 ACR 來直接建置 Docker 映像。 但可改為使用計算叢集來建置映像。

1. 若要尋找工作區的 Azure Container Registry 名稱，請使用下列其中一種方法：

    __Azure 入口網站__

    在工作區的 [概觀] 區段中，[登錄] 值會連結至 Azure Container Registry。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="工作區的 Azure Container Registry" border="true":::

    __Azure CLI__

    如果您已[安裝適用於 Azure CLI 的 Machine Learning 擴充功能](reference-azure-machine-learning-cli.md)，則可以使用 `az ml workspace show` 命令來顯示工作區資訊。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令會傳回如下值：`"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`。 字串的最後一個部分是工作區的 Azure Container Registry 名稱。

1. 若要限制對於您虛擬網路的存取，請使用[設定登錄的網路存取](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)中的步驟。 在新增虛擬網路時，請選取 Azure Machine Learning 資源的虛擬網路和子網路。

1. 使用 Azure Machine Learning Python SDK 來設定計算叢集，以建置 Docker 映像。 下列程式碼片段示範如何執行此操作：

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 您的儲存體帳戶、計算叢集和 Azure Container Registry 都必須位於虛擬網路的相同子網路中。
    
    如需詳細資訊，請參閱 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) 方法參考。

1. 您必須套用下列 Azure Resource Manager 範本。 此範本可讓您的工作區與 ACR 通訊。

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

## <a name="key-vault-instance"></a>金鑰保存庫執行個體 

Azure Machine Learning 會使用與工作區相關聯的金鑰保存庫執行個體來儲存下列認證：
* 相關聯的儲存體帳戶連接字串
* Azure Container Repository 執行個體的密碼
* 資料存放區的連接字串

若要在虛擬網路背後搭配 Azure Key Vault 使用 Azure Machine Learning 測試，請使用下列步驟：

1. 移至與工作區相關聯的金鑰保存庫。

   [![與 Azure Machine Learning 工作區相關聯的金鑰保存庫](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. 在 [金鑰保存庫] 頁面上，選取左窗格中的 [防火牆和虛擬網路]。

   ![[金鑰保存庫] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在 [防火牆和虛擬網路] 頁面上，執行下列動作：
    - 在 [允許存取來源] 之下，選取 [選取的網路]。
    - 在 [虛擬網路] 底下選取 [新增現有虛擬網路]，以新增測試計算所在的虛擬網路。
    - 在 [允許受信任的 Microsoft 服務略過此防火牆] 底下選取 [是]。

   [![[金鑰保存庫] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

若要搭配您的工作區使用虛擬網路中的 Azure Databricks，您必須符合下列需求：

> [!div class="checklist"]
> * 虛擬網路必須在與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
> * 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，則這些帳戶必須位於與 Azure Databricks 叢集相同的虛擬網路中。
> * 除了 Azure Databricks 所使用的 __databricks-private__ 和 __databricks-public__ 子網路外，還需要有針對虛擬網路所建立的__預設__子網路。

如需搭配虛擬網路使用 Azure Databricks 的特定資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虛擬機器或 HDInsight 叢集

> [!IMPORTANT]
> Azure Machine Learning 僅支援執行 Ubuntu 的虛擬機器。

若要使用虛擬網路中的虛擬機器或 Azure HDInsight 叢集來搭配您的工作區，請使用下列步驟：

1. 透過使用 Azure 入口網站或 Azure CLI 來建立 VM 或 HDInsight 叢集，並將叢集放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文章：
    * [建立和管理適用於 Linux VM 的 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要讓 Azure Machine Learning 能夠與 VM 或叢集上的 SSH 連接埠通訊，請設定網路安全性群組的來源項目。 SSH 連接埠通常是連接埠 22。 若要允許來自此來源的流量，請執行下列動作：

    * 在 [來源] 下拉式清單中選取 [服務標籤]。

    * 在 [來源服務標籤] 下拉式清單中選取 [AzureMachineLearning]。

    * 在 [來源連接埠範圍] 下拉式清單中選取 [ __*__ ]。

    * 在 [目的地] 下拉式清單中選取 [任何]。

    * 在 [目的地連接埠範圍] 下拉式清單中選取 [22]。

    * 在 [通訊協定] 底下選取 [任何]。

    * 在 [動作] 底下選取 [允許]。

   ![用於對虛擬網路內的 VM 或 HDInsight 叢集進行測試的輸入規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    請保留網路安全性群組的預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

    如果您不想使用預設輸出規則，而是想要限制虛擬網路的輸出存取，請參閱[限制來自虛擬網路的輸出連線能力](#limiting-outbound-from-vnet)一節。

1. 將 VM 或 HDInsight 叢集連結至 Azure Machine Learning 工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。


## <a name="next-steps"></a>後續步驟

* [設定定型環境](how-to-set-up-training-targets.md)
* [設定私人端點](how-to-configure-private-link.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
