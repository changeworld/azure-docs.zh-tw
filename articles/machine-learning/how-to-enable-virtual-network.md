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
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121199"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>使用私用端點和虛擬網路進行定型和推斷期間的網路隔離
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何藉由隔離 Azure 虛擬網路 (vnet) 中的 Azure Machine Learning 訓練和推斷作業，來保護您的機器學習服務生命週期。 __虛擬網路__可充當安全邊界，將 Azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 藉由加入網路，您可以安全地將模型定型，並可存取所部署的模型以進行推斷。

您可以使用__私人端點__從虛擬網路存取 Azure Machine Learning 工作區。 私人端點是虛擬網路內的一組私人 IP 位址，而您的工作區存取權僅限於虛擬網路。 私用端點有助於降低資料外泄的風險。 若要深入了解私人端點，請參閱 [Azure Private Link](/azure/private-link/private-link-overview) 一文。

> [!NOTE]
> 使用 Mozilla Firefox 時，您可能會遇到透過私用端點存取工作區的問題。 此問題可能與瀏覽器中的 DNS over HTTPS 設定有關。 我們建議使用 Microsoft Edge 或 Google Chrome 來解決此問題。

Azure Machine Learning 依賴其他 Azure 服務來儲存資料和計算資源， (用來定型和部署模型) 。 您也可以在虛擬網路內建立這些資源。 例如，您可以使用 Azure Machine Learning 計算來將模型定型，然後將模型部署至 Azure Kubernetes Service (AKS)。 

## <a name="prerequisites"></a>Prerequisites

+ Azure Machine Learning [工作區](how-to-manage-workspace.md)。

+ 同時具備 [Azure 虛擬網路服務](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)和 [IP 網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)的一般知識。

+ 既有的虛擬網路和子網路，以便與您的計算資源搭配使用。

+ 若要將資源部署到虛擬網路或子網，您的使用者帳戶必須具有下列 Azure 角色型存取控制中動作的許可權 (RBAC) ：

    - 虛擬網路資源上的「Microsoft 網路/virtualNetworks/聯結/動作」。
    - 子網資源上的「Microsoft 網路/virtualNetworks/子網/聯結/動作」。

    如需具有網路功能之 RBAC 的詳細資訊，請參閱[網路功能內建角色](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>保護您的工作區

您的 Azure Machine Learning 工作區可以有__公用端點__或__私人端點__。 公用端點是一組可在公用網際網路上存取的 IP 位址，而私人端點則是虛擬網路內的一組私人 IP 位址。 

若要限制只能透過私人 IP 位址進行工作區的存取，請使用私人端點。

由於只允許從虛擬網路對工作區進行通訊，因此使用工作區的任何開發環境都必須是虛擬網路的成員。 例如，虛擬網路中的虛擬機器。

> [!IMPORTANT]
> 私用端點不會影響 Azure 控制平面 (管理作業) 例如刪除工作區或管理計算資源。 例如，建立、更新或刪除計算目標。 這些作業會在公用網際網路上正常執行。
>
> 私人端點會防止從虛擬網路外部存取工作區。

某些具有私用端點的資源組合需要 Enterprise edition 工作區。 請使用下表來了解需要 Enterprise Edition 的狀況：

| 狀況 | Enterprise</br>edition | 基本</br>edition |
| ----- |:-----:|:-----:| 
| 沒有虛擬網路或私人端點 | ✔ | ✔ |
| 沒有私用端點的工作區。 虛擬網路中的其他資源 (Azure Container Registry 除外) | ✔ | ✔ |
| 沒有私用端點的工作區。 具有私用端點的其他資源 | ✔ | |
| 具有私用端點的工作區。 虛擬網路中的其他資源 (Azure Container Registry 除外) | ✔ | ✔ |
| 工作區和任何其他具有私用端點的資源 | ✔ | |
| 具有私用端點的工作區。 沒有私人端點或虛擬網路的其他資源 | ✔ | ✔ |
| 虛擬網路中的 Azure Container Registry | ✔ | |
| 客戶管理的工作區金鑰 | ✔ | |

> [!WARNING]
> 
> 啟用私用端點的工作區中不支援 Azure Machine Learning 計算實例。
>
> Azure Machine Learning 不支援使用已啟用私用端點的 Azure Kubernetes Service。 您可以改為在虛擬網路中使用 Azure Kubernetes Service。 如需詳細資訊，請參閱[保護 Azure 虛擬網路內的 Azure ML 實驗和推斷作業](how-to-enable-virtual-network.md)。

若要深入瞭解 Azure 中的私用端點，請參閱[Azure 私人連結](/azure/private-link/private-link-overview)一文。

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>建立使用私用端點的工作區

您可以使用 Azure Machine Learning SDK、CLI、Azure Resource Manager 範本或 Azure 入口網站，建立具有私人端點的新工作區。

__需求__

* 您搭配私人端點使用的虛擬網路必須停用網路原則。 如需詳細資訊，請參閱[停用私人端點的網路原則](/azure/private-link/disable-private-endpoint-network-policy)。

__限制__

* 您不能透過公用網際網路連線到工作區，只可從虛擬網路內部連接。

* 如果使用私人端點建立了多個工作區，而且它們使用相同的私人 DNS 區域，則只有第一個工作區會新增至私人 DNS 區域的__虛擬網路連結__。

    若要解決這項限制，請手動新增額外工作區的虛擬網路連結。 如需詳細資訊，請參閱[什麼是虛擬網路連結](/azure/dns/private-dns-virtual-network-links)。

__組態__

如需有關如何使用虛擬網路和私人端點來建立工作區，以及其他設定選項的詳細資訊，請參閱下列文章：

* [使用 Azure Resource Manager 範本來建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。
* [在入口網站中建立工作區](how-to-manage-workspace.md)。
* [使用 Azure CLI 建立工作區](how-to-manage-workspace-cli.md)。
* 若要使用 Python SDK，請參閱[PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py)和[工作區。建立 ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-)參考檔。

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning studio

__需求__

* 若要存取儲存體帳戶中的資料，儲存體帳戶必須與工作區位於相同的虛擬網路中。

* 如果您的資料儲存在虛擬網路中，您必須使用工作區[受控身分識別](../active-directory/managed-identities-azure-resources/overview.md)，將您資料的存取權授與 studio。

    > [!IMPORTANT]
    > 雖然大部分的 studio 都能使用儲存在虛擬網路中的資料，但整合式筆記本__並不會__。 整合式筆記本不支援使用虛擬網路中的存放裝置。 相反地，您可以使用計算實例中的 Jupyter 筆記本。 如需詳細資訊，請參閱[計算叢集 & 實例](#compute-instance)一節。

    如果您未授與 studio 存取權，將會收到此錯誤， `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 無法使用下列作業：

    * 預覽 studio 中的資料。
    * 在設計工具中將資料視覺化。
    * 提交 AutoML 實驗。
    * 開機磁碟區標專案。

__限制__

* Azure Machine Learning studio 支援從虛擬網路中的下列資料存放區類型讀取資料：

    * Azure Blob
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure SQL Database

__組態__

* __設定資料存放區以使用受控身分識別__來存取您的資料。 這些步驟會使用 Azure 資源型存取控制 (RBAC) ，將工作區受控識別新增為儲存體服務的__讀取器__。 __讀者__存取可讓工作區取得防火牆設定，並確保資料不會離開虛擬網路。

    1. 在 studio 中，選取 [__資料存放區__]。

    1. 若要建立新的資料存放區，請選取 [ __+ 新增資料__存放區]。 若要更新現有的資料存放區，請選取資料存放區，然後選取 [__更新認證__]。

    1. 在 [資料存放區設定] 中，選取 __[是]__ 以__允許 Azure Machine Learning 服務使用工作空間受控識別存取儲存體__。

    > [!NOTE]
    > 這些變更可能需要10分鐘的時間才會生效。

* 針對__Azure Blob 儲存體__，您也必須將工作區受控識別新增為[Blob 資料讀取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)，讓它可以從 blob 儲存體讀取資料。

* 根據預設，設計工具會使用附加至您工作區的儲存體帳戶來儲存輸出。 不過，您可以指定它將輸出儲存到您有權存取的任何資料存放區。 如果您的環境使用虛擬網路，您可以使用這些控制項來確保您的資料保持安全且可供存取。 若要為管線設定新的預設儲存體：

    1. 在管線草稿中，選取靠近管線標題附近的 [**設定] 齒輪圖示**。
    1. 選取 [**選取預設資料**存放區] 專案。
    1. 指定新的資料存放區。

    您也可以根據每個模組來覆寫預設的資料存放區。 這可讓您控制每個個別模組的儲存位置。

    1. 選取您要指定其輸出的模組。
    1. 展開 [**輸出設定**] 區段。
    1. 選取 [覆**寫預設輸出設定**]。
    1. 選取 [**設定輸出設定**]。
    1. 指定新的資料存放區。

* 如果使用__Azure Data Lake Storage Gen2__，您可以使用 RBAC 和 POSIX 樣式的存取控制清單 (acl) 來控制虛擬網路內的資料存取。

    若要使用 RBAC，請將工作區受控識別新增至[Blob 資料讀取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 如需詳細資訊，請參閱[角色型存取控制](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)。

    若要使用 Acl，可以將存取權指派給工作區受控識別，就像任何其他安全性原則一樣。 如需詳細資訊，請參閱檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

* __Azure Data Lake Storage Gen1__只支援 POSIX 樣式的存取控制清單。 您可以將工作區受控識別的存取權指派給資源，就像任何其他安全性原則一樣。 如需詳細資訊，請參閱[Azure Data Lake Storage Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。

* 若要使用受控識別存取儲存在__Azure SQL Database__中的資料，您必須建立對應至受控識別的 SQL 內含使用者。 如需從外部提供者建立使用者的詳細資訊，請參閱[建立對應至 Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)身分識別的包含使用者。

    建立 SQL 包含的使用者之後，請使用[Grant t-sql 命令](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)授與許可權。

* 如果您是__從虛擬網路內部的資源存取 studio__ (例如，計算實例或虛擬機器) ，您必須允許從虛擬網路到 studio 的輸出流量。 

    例如，如果您使用網路安全性群組 (NSG) 來限制輸出流量，請將規則新增至__AzureFrontDoor__的__服務標記__目的地。

## <a name="use-datastores-and-datasets"></a>使用資料存放區和資料集

> [!NOTE]
> 本節涵蓋 SDK 體驗的資料存放區和資料集使用方式。 如需有關 studio 體驗的詳細資訊，請參閱[Machine Learning studio](#machine-learning-studio)一節。

__限制__

根據預設，當您嘗試使用 SDK 存取資料時，Azure Machine Learning 會執行資料有效性和認證檢查。 如果您的資料位於虛擬網路後方，Azure Machine Learning 無法存取資料並使其檢查失敗。 若要解決此問題，請在建立資料存放區和資料集時略過驗證。

* 使用__資料__存放區時：

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

* 使用__資料集__時：

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

## <a name="azure-storage-account"></a>Azure 儲存體帳戶

> [!IMPORTANT]
> 您可以將 Azure Machine Learning 的_預設儲存體帳戶_，以及虛擬網路中的_非預設儲存體帳戶_。

__需求__

* 儲存體帳戶必須與用於定型或推斷的計算執行個體或叢集位於相同的虛擬網路和子網路中。

__組態__

若要保護您的工作區所使用的 Azure 儲存體帳戶，請在您的虛擬網路上啟用儲存體帳戶的__私人端點__或__服務端點__。

* 若要將儲存體帳戶設定為使用__私用端點__，請參閱[使用私用端點](/azure/storage/common/storage-private-endpoints.md)一文。

* 若要設定儲存體帳戶以使用__服務端點__，請使用下列步驟：

    1. 若要將儲存體帳戶新增至您的工作區所使用的虛擬網路，請使用[設定 Azure 儲存體防火牆和虛擬網路](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)一文中的__授與虛擬網路存取權__一節中的資訊。
    1. 若要允許從虛擬網路上的 Microsoft 服務存取 (例如 Azure Machine Learning) ，請使用[設定 Azure 儲存體防火牆和虛擬網路](/azure/storage/common/storage-network-security#exceptions)一文的__例外__狀況一節中的資訊。
    1. 在使用 Azure Machine Learning SDK 時，您的開發環境必須能夠連線到 Azure 儲存體帳戶。 當儲存體帳戶位於虛擬網路內時，防火牆必須允許從開發環境的 IP 位址進行存取。 若要新增開發環境的 IP 位址，請使用[設定 Azure 儲存體防火牆和虛擬網路](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)一文中的__授與網際網路 ip 範圍的存取權__一節中的資訊。

## <a name="azure-container-registry"></a>Azure Container Registry

__需求__

* Azure Machine Learning 工作區必須是 Enterprise Edition。 如需升級的相關資訊，請參閱[升級至 Enterprise Edition](how-to-manage-workspace.md#upgrade)。
* 您的 Azure Machine Learning 工作區區域應該是[私人連結啟用區域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)。 
* 您的 Azure Container Registry 必須是 Premium 版本。 如需升級的詳細資訊，請參閱[變更 SKU](/azure/container-registry/container-registry-skus#changing-skus)。
* Azure Container Registry 所在的虛擬網路和子網路必須與用於定型或推斷的儲存體帳戶和計算目標相同。
* Azure Machine Learning 工作區必須包含 [Azure Machine Learning 計算叢集](how-to-set-up-training-targets.md#amlcompute)。

__限制__

* 當 ACR 位於虛擬網路後方時，Azure Machine Learning 就無法使用 ACR 來直接建置 Docker 映像。 但可改為使用計算叢集來建置映像。

__組態__

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

1. 若要讓您的工作區與 ACR 實例通訊，請套用下列 Azure Resource Manager 範本：

    > [!WARNING]
    > 此範本會為您的工作區啟用私用端點，並將其變更為企業工作區。 您無法復原這些變更。

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

__需求__

__限制__

__組態__ 

若要搭配虛擬網路背後的 Azure Key Vault 使用 Azure Machine Learning 試驗功能，請使用[設定 Azure Key Vault 防火牆和虛擬網路](/azure/key-vault/general/network-security)一文。

> [!IMPORTANT]
> 遵循文章中的步驟時，請使用您的測試計算資源所使用的相同虛擬網路。 您也必須__允許受信任的 Microsoft 服務略過此防火牆__。

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>計算叢集和執行個體 

__需求__

* 虛擬網路必須在與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
* 為計算執行個體或叢集指定的子網路必須有足夠的未指派 IP 位址，以容納目標 VM 的數目。 如果子網路沒有足夠的未指派 IP 位址，則計算叢集只會完成部分配置。
* 如果您打算透過限制流量來保護虛擬網路，某些埠必須針對計算服務保持開啟。
* 如果您要將多個計算執行個體或叢集放在一個虛擬網路中，您可能必須要求一或多個資源的配額增加。
* 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，則這些帳戶必須位於與 Azure Machine Learning 計算執行個體或叢集相同的虛擬網路中。 
* 若要讓計算執行個體 Jupyter 功能能夠運作，請確定您並未停用 Web 通訊端通訊。

__限制__

* Machine Learning 計算執行個體或叢集會自動將額外的網路資源配置__在包含虛擬網路的資源群組中__。 針對每個計算執行個體或叢集，服務會配置下列資源：

    * 一個網路安全性群組
    * 一個公用 IP 位址
    * 一個負載平衡器
    
    對於__計算__叢集，這些資源會 (刪除，並在每次叢集相應減少為0個節點時重新建立) 。
    
    對於__計算實例__，資源會保留在實例刪除之前， (停止並不會移除) 的資源。

    這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)所限制。

__組態__

* 若要建立 Machine Learning Compute 叢集，請使用下列步驟：

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

* 如果您在 Azure 計算實例上使用筆記本，您必須確定您的筆記本是在與您的資料相同的虛擬網路和子網後方的計算資源上執行。 

    在 [**設定**  >  **虛擬網路**] 的 [Advanced settings] 底下，將您的計算實例設定為相同的虛擬網路。 您無法將現有的計算實例新增至虛擬網路。

* 如果您打算藉由限制進出公用網際網路的網路流量來保護虛擬網路，您必須允許來自 Azure Batch 服務的輸入通訊。

    Batch 服務會在連結至 VM 的網路介面 (NIC) 層級新增網路安全性群組 (NSG)。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

    - 連接埠 29876 和 29877 上的輸入 TCP 流量，來自 __BatchNodeManagement__ 的__服務標籤__。

    - (選擇性) 連接埠 22 上的輸入 TCP 流量，用來允許遠端存取。 只有在想要於公用 IP 上使用 SSH 進行連線時，才可使用此連接埠。

    - 任何連接埠上傳至虛擬網路的輸出流量。

    - 任何連接埠上傳至網際網路的輸出流量。

    - 針對連接埠 44224 上的計算執行個體輸入 TCP 流量，來自 __AzureMachineLearning__ 的__服務標籤__。

    > [!IMPORTANT]
    > 如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 封鎖對計算節點的通訊，則計算服務會將計算節點的狀態設定為 [無法使用]。
    >
    > 您不需要在子網路層級指定 NSG，因為 Azure Batch 服務會設定其本身的 NSG。 不過，如果包含 Azure Machine Learning 計算的子網有相關聯的 Nsg 或防火牆，您也必須允許先前所列的流量。

* 如果您不想要使用預設輸出規則，而是想要限制虛擬網路的輸出存取，請使用下列步驟：

    1. 使用 NSG 規則來拒絕輸出網際網路連線。
    1. 針對__計算執行個體__或__計算叢集__，限制目的地為下列項目的輸出流量：
        - Azure 儲存體，方法是使用 __Storage.RegionName__ 的__服務標籤__。 其中 `{RegionName}` 是 Azure 區域的名稱。
        - Azure Container Registry，方法是使用 __AzureContainerRegistry.RegionName__ 的__服務標籤__。 其中 `{RegionName}` 是 Azure 區域的名稱。
        - Azure Machine Learning，方法是使用 __AzureMachineLearning__ 的__服務標籤__
        - Azure Resource Manager，方法是使用 __AzureResourceManager__ 的__服務標籤__
        - Azure Active Directory，方法是使用 __AzureActiveDirectory__ 的__服務標籤__

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

* 如果您使用[強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)搭配 Azure Machine Learning 計算，則必須允許從包含計算資源的子網與公用網際網路進行通訊。 這項通訊用於工作排程和存取 Azure 儲存體。

    有兩種方式可以完成此動作：

    * 使用[虛擬網路 NAT](../virtual-network/nat-overview.md)。 NAT 閘道會為您虛擬網路中的一或多個子網提供輸出網際網路連線能力。 如需相關資訊，請參閱[使用 NAT 閘道資源設計虛擬網路](../virtual-network/nat-gateway-resource.md)。

    * 將[使用者定義的路由 (udr) ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)新增至包含計算資源的子網。 在資源所在的區域中，為 Azure Batch 服務所使用的每個 IP 位址建立一個 UDR。 這些 UDR 可讓 Batch 服務與計算節點通訊，以排程工作。 此外，也請為資源所在的 Azure Machine Learning 服務新增 IP 位址，必須有此 IP 位址才能存取計算執行個體。 若要取得 Batch 服務和 Azure Machine Learning 服務的 IP 位址清單，請使用下列其中一種方法：

        * 下載 [Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)並搜尋檔案中的 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`，其中 `<region>` 是您的 Azure 區域。

        * 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 下載該資訊。 下列範例會下載 IP 位址資訊，並篩選出美國東部 2 區域的資訊：

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        當您新增 UDR 時，請為每個相關的「批次 IP」位址首碼定義路由，然後將 [下一個躍點類型] 設定為 [網際網路]。 

        除了您定義的任何 Udr 外，您必須透過內部部署網路設備，允許 Azure 儲存體的輸出流量。 具體而言，此流量的 Url 會採用下列格式： `<account>.table.core.windows.net` 、 `<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 。 

        如需詳細資訊，請參閱[在虛擬網路中建立 Azure Batch 集區](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__需求__

* Azure Kubernetes Service (AKS) 實例和 Azure 虛擬網路必須位於相同的區域。 如果您保護虛擬網路中的工作區所使用的 Azure 儲存體帳戶 (s) ，它們必須與 AKS 實例位於相同的虛擬網路中。

* 若要__規劃叢集的 IP 位址__，請遵循在[Azure Kubernetes Service (AKS) 一文中設定 advanced 網路中](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)的必要條件。

* 若要限制 AKS 叢集的輸入和輸出通訊，請遵循 Azure Kubernetes Service 一文中[控制叢集節點](/azure/aks/limit-egress-traffic)的輸出流量中的指引，以確定已正確設定來自 AKS 的_輸出_通訊。 _輸入_通訊需求（如果有的話）會在下面的設定區段中呼叫。

__限制__

* 如果您想要使用已啟用私人連結的 Azure Kubernetes Service，您必須將它附加至您的工作區。 您無法從 Azure Machine Learning (SDK、入口網站、CLI 等 ) ，建立具有私用連結的 Azure Kubernetes Service 叢集。

__組態__

> [!IMPORTANT]
> 本節包含多項設定。 選取最符合您需求的其中一個。

* 若要__使用虛擬網路背後的 AKS，請使用公用負載平衡器__：

    1. 建立或附加 AKS 叢集。 如果您要__建立__新的叢集，您必須指定要用於叢集的虛擬網路。
    
        下列範例示範如何使用 Python SDK 建立新的 AKS 叢集：

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

        如果您有現有的 AKS 叢集，且該叢集已在虛擬網路後方，請使用[部署至 Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)一文中的資訊。

    1. 請確定控制虛擬網路的網路安全性群組具有針對評分端點啟用的__輸入__安全性規則，以便可從虛擬網路外部呼叫。

* 若要__使用虛擬網路背後的 AKS，請使用私人負載平衡器__：

    1. 建立或附加 AKS 叢集。 如果您要__建立__新的叢集，您必須指定要用於叢集的虛擬網路。
    
        下列範例示範如何使用 Python SDK 建立新的 AKS 叢集：

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

        如果您有現有的 AKS 叢集，且該叢集已在虛擬網路後方，請使用[部署至 Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)一文中的資訊。

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

    1. 若要將 AKS 叢集更新為使用__私用負載平衡器__，請使用 Python SDK。 下列程式碼片段示範如何更新已新增或附加至工作區的現有 AKS 叢集：
    
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

* 若要__使用私用端點附加 AKS__：

    1. 使用下列 Azure CLI 命令來取得 AKS 叢集將使用之子網的__子網識別碼__。 例如，虛擬網路的預設子網：
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        此命令會傳回虛擬網路中子網的識別碼陣列。 下列 JSON 是來自僅具有一個子網之虛擬網路的範例：

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        如果傳回多個識別符，請選取您要使用的識別碼。

    1. 若要建立私人端點的 AKS 叢集，請使用[建立私用 Azure Kubernetes Service](/azure/aks/private-clusters#advanced-networking)叢集文章的__Advanced 網路__區段中的資訊。 建立叢集時，請使用前一個命令中的子網識別碼與 `--vnet-subnet-id` 參數。

    1. 若要附加叢集，請使用[部署至 Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)一文中的資訊。

    > [!TIP]
    > 使用此設定並限制來自 AKS 叢集的輸出通訊時，您不需要允許埠__1194__或__9000__上的通訊。 如需其他應該允許的埠，請參閱 Azure Kubernetes Service 一文中的控制叢集節點的輸出[流量中](/azure/aks/limit-egress-traffic)的指引。

## <a name="use-azure-container-instances-aci"></a>使用 Azure 容器執行個體 (ACI)

__需求__

* 部署模型時會以動態方式建立 Azure 容器執行個體。 若要讓 Azure Machine Learning 能夠在虛擬網路內部建立 ACI，您必須為部署所使用的子網路啟用__子網路委派__。

__限制__

* 虛擬網路必須位於與您的 Azure Machine Learning 工作區相同的資源群組中。

* 您工作區的 Azure Container Registry (ACR) 也不能在虛擬網路中。

__組態__

若要在工作區的虛擬網路中使用 ACI，請使用下列步驟：

1. 若要在虛擬網路上啟用子網路委派，請使用[新增或移除子網路委派](../virtual-network/manage-subnet-delegation.md)一文中的資訊。 您可以在建立虛擬網路時啟用委派，也可以將其新增至現有網路。

    > [!IMPORTANT]
    > 在啟用委派時，請使用 `Microsoft.ContainerInstance/containerGroups` 作為 [將子網路委派給服務] 值。

2. 使用 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) 來部署模型，並使用 `vnet_name` 和 `subnet_name` 參數。 將這些參數設定為啟用委派的虛擬網路名稱和子網路。

## <a name="azure-databricks"></a>Azure Databricks

__需求__

* 虛擬網路必須在與 Azure Machine Learning 工作區相同的訂用帳戶和區域中。
* 如果工作區的 Azure 儲存體帳戶也在虛擬網路中受到保護，則這些帳戶必須位於與 Azure Databricks 叢集相同的虛擬網路中。
* 除了 Azure Databricks 所使用的 __databricks-private__ 和 __databricks-public__ 子網路外，還需要有針對虛擬網路所建立的__預設__子網路。

__限制__

__組態__

如需搭配虛擬網路使用 Azure Databricks 的特定資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虛擬機器或 HDInsight 叢集

__需求__

* Azure Machine Learning 僅支援執行 Ubuntu 的虛擬機器。
* 必須在虛擬機器或 HDInsight 叢集上啟用 SSH 埠。

__限制__

__組態__

1. 透過使用 Azure 入口網站或 Azure CLI 來建立 VM 或 HDInsight 叢集，並將叢集放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文章：

    * [建立和管理適用於 Linux VM 的 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 系統會針對以 Linux 為基礎的 Azure 虛擬機器自動建立 NSG。 此 NSG 允許從任何來源存取埠22。 如果您想要限制對 SSH 埠的存取，您必須允許來自 Azure Machine Learning 的存取。 若要保留 Azure ML 的存取權，您必須允許來源__服務標記__為__AzureMachineLearning__的__來源服務__進行存取。 例如，下列 Azure CLI 命令會將 SSH 規則修改為只允許來自 Azure Machine Learning 的存取。

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    如需詳細資訊，請參閱適用于 Linux 虛擬機器的 Azure 虛擬網路一文中的[建立網路安全性群組](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups)一節。
    
    請保留網路安全性群組的預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

1. 將 VM 或 HDInsight 叢集連結至 Azure Machine Learning 工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 防火牆後方的 Azure Machine Learning 工作區](how-to-access-azureml-behind-firewall.md)。
* [設定定型環境](how-to-set-up-training-targets.md)
* [設定私人端點](how-to-configure-private-link.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
