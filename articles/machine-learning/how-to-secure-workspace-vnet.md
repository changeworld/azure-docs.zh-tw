---
title: 使用虛擬網路保護 Azure Machine Learning 工作區
titleSuffix: Azure Machine Learning
description: 使用隔離的 Azure 虛擬網路來保護您的 Azure Machine Learning 工作區和相關聯的資源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: e718ed13cfd67092b50b42584d861a2bcf5dacc5
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664073"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>使用虛擬網路保護 Azure Machine Learning 工作區
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在虛擬網路中保護 Azure Machine Learning 工作區與其相關聯的資源。


本文是五部分系列的第二部分，將逐步引導您保護 Azure Machine Learning 的工作流程。 強烈建議您仔細閱讀第 [一部： VNet 總覽](how-to-network-security-overview.md) ，以瞭解整體架構。 

請參閱本系列的其他文章：

[1. VNet 總覽](how-to-network-security-overview.md)  >  **2。保護工作區**  >  [3。保護定型環境](how-to-secure-training-vnet.md)  >  [4。保護推斷環境](how-to-secure-inferencing-vnet.md)  >  [5。啟用 studio 功能](how-to-enable-studio-virtual-network.md)

在本文中，您將瞭解如何在虛擬網路中啟用下列工作區資源：
> [!div class="checklist"]
> - Azure Machine Learning 工作區
> - Azure 儲存體帳戶
> - Azure Machine Learning 資料存放區和資料集
> - Azure 金鑰保存庫
> - Azure Container Registry

## <a name="prerequisites"></a>必要條件

+ 閱讀 [網路安全性總覽](how-to-network-security-overview.md) 文章，以瞭解常見的虛擬網路案例和整體虛擬網路架構。

+ 要搭配您的計算資源使用的現有虛擬網路和子網。

+ 若要將資源部署到虛擬網路或子網，您的使用者帳戶必須具有 Azure 角色型存取控制 (RBAC) 中下列動作的許可權：

    - 虛擬網路資源上的「Microsoft. Network/virtualNetworks/join/action」。
    - 子網資源上的「Microsoft. Network/virtualNetworks/subnet/join/action」。

    如需具有網路功能之 RBAC 的詳細資訊，請參閱[網路功能內建角色](/azure/role-based-access-control/built-in-roles#networking)。


## <a name="secure-the-workspace-with-private-endpoint"></a>使用私人端點保護工作區

Azure Private Link 可讓您使用私人端點連接到工作區。 私人端點是虛擬網路內的一組私人 IP 位址。 然後，您可以將工作區的存取許可權制為只在私人 IP 位址上進行。 Private Link 有助於降低資料遭到外泄的風險。

如需設定 Private Link 工作區的詳細資訊，請參閱 [如何設定 Private Link](how-to-configure-private-link.md)。


## <a name="secure-azure-storage-accounts"></a>保護 Azure 儲存體帳戶

在本節中，您會瞭解如何使用服務端點保護 Azure 儲存體帳戶。 不過，您也可以使用私人端點來保護 Azure 儲存體。 如需詳細資訊，請參閱 [使用私用端點進行 Azure 儲存體](../storage/common/storage-private-endpoints.md)。

> [!IMPORTANT]
> 適用於 Azure Machine Learning 的_預設儲存體帳戶_ 或_非預設儲存體帳戶_都可以放在虛擬網路中。
>
> 建立工作區時便會自動佈建預設儲存體帳戶。
>
> 若為非預設儲存體帳戶，[`Workspace.create()` 函式](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 中的 `storage_account` 參數可讓您依 Azure 資源識別碼來指定自訂儲存體帳戶。

若要在虛擬網路中針對工作區使用 Azure 儲存體帳戶作，請使用下列步驟：

1. 在 Azure 入口網站中，移至您想要在工作區中使用的儲存體服務。

   [![連結至 Azure Machine Learning 工作區的儲存體](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在 [儲存體服務帳戶] 頁面上，選取 [ __防火牆和虛擬網路__]。

   ![Azure 入口網站中 [Azure 儲存體] 頁面上的 [防火牆和虛擬網路] 區域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在 [防火牆和虛擬網路] 頁面上，執行下列動作：
    1. 選取 [選取的網路]。
    1. 在 [虛擬網路] 底下，選取 [新增現有虛擬網路] 連結。 此動作會將您的計算所在的虛擬網路新增 (請參閱步驟 1) 。

        > [!IMPORTANT]
        > 儲存體帳戶必須與用於定型或推斷的計算執行個體或叢集位於相同的虛擬網路和子網路中。

    1. 選取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊。

    > [!IMPORTANT]
    > 在使用 Azure Machine Learning SDK 時，您的開發環境必須能夠連線到 Azure 儲存體帳戶。 當儲存體帳戶位於虛擬網路內時，防火牆必須允許從開發環境的 IP 位址進行存取。
    >
    > 若要啟用對於儲存體帳戶的存取權，請*從開發用戶端上的網頁瀏覽器*造訪儲存體帳戶 的 [防火牆和虛擬網路]。 然後使用 [新增用戶端 IP 位址] 核取方塊，將用戶端的 IP 位址新增至 [位址範圍]。 您也可以使用 [位址範圍] 欄位，手動輸入開發環境的 IP 位址。 用戶端新增好 IP 位址之後，即可使用 SDK 來存取儲存體帳戶。

   [![Azure 入口網站中的 [防火牆和虛擬網路] 窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-datastores-and-datasets"></a>安全資料存放區和資料集

在本節中，您會瞭解如何使用虛擬網路中的資料存放區和資料集使用方式來取得 SDK 體驗。 如需有關 studio 體驗的詳細資訊，請參閱 [使用虛擬網路中的 Azure Machine Learning studio](how-to-enable-studio-virtual-network.md)。

若要使用 SDK 存取資料，您必須使用資料儲存所在之個別服務所需的驗證方法。 例如，如果您註冊資料存放區以存取 Azure Data Lake 存放區 Gen2，您仍然必須使用 [[連線至 Azure 儲存體服務]](how-to-access-data.md#azure-data-lake-storage-generation-2)中所述的服務主體。

### <a name="disable-data-validation"></a>停用資料驗證

依預設，當您嘗試使用 SDK 存取資料時，Azure Machine Learning 會執行資料有效性和認證檢查。 如果資料位於虛擬網路後方，Azure Machine Learning 無法完成這些檢查。 若要避免這種情況，您必須建立資料存放區和資料集來略過驗證。

### <a name="use-datastores"></a>使用資料存放區

 Azure Data Lake 儲存 Gen1 和 Azure Data Lake 存放區預設會略過驗證，因此不需要採取進一步的動作。 不過，針對下列服務，您可以使用類似的語法來略過資料存放區驗證：

- Azure Blob 儲存體
- Azure 檔案共用
- PostgreSQL
- Azure SQL Database

下列程式碼範例會建立新的 Azure Blob 資料存放區並設定 `skip_validation=True` 。

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>使用資料集

略過資料集驗證的語法類似于下列資料集類型：
- 分隔的檔案
- JSON 
- Parquet
- SQL
- 檔案

下列程式碼會建立新的 JSON 資料集和設定 `validate=False` 。

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>安全 Azure Key Vault

Azure Machine Learning 會使用相關聯的 Key Vault 實例來儲存下列認證：
* 相關聯的儲存體帳戶連接字串
* Azure Container Repository 執行個體的密碼
* 資料存放區的連接字串

若要在虛擬網路背後搭配 Azure Key Vault 使用 Azure Machine Learning 測試，請使用下列步驟：

1. 移至與工作區相關聯的 Key Vault。

1. 在 [ __Key Vault__ ] 頁面的左窗格中，選取 [ __網路__]。

1. 在 [ __防火牆和虛擬網路__ ] 索引標籤上，執行下列動作：
    1. 在 [ __允許存取來源__] 底下，選取 [ __私人端點] 和 [選取的網路__]。
    1. 在 [虛擬網路] 底下選取 [新增現有虛擬網路]，以新增測試計算所在的虛擬網路。
    1. 在 [ __允許信任的 Microsoft 服務略過此防火牆？__] 下，選取 __[是]__。

   [![[金鑰保存庫] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>啟用 Azure Container Registry (ACR) 

若要在虛擬網路內使用 Azure Container Registry，您必須符合下列需求：

* Azure Machine Learning 工作區必須是 Enterprise Edition。 如需升級的相關資訊，請參閱[升級至 Enterprise Edition](how-to-manage-workspace.md#upgrade)。

* 您的 Azure Container Registry 必須是 Premium 版本。 如需升級的詳細資訊，請參閱[變更 SKU](/azure/container-registry/container-registry-skus#changing-skus)。

* Azure Container Registry 所在的虛擬網路和子網路必須與用於定型或推斷的儲存體帳戶和計算目標相同。

* Azure Machine Learning 工作區必須包含 [Azure Machine Learning 計算叢集](how-to-create-attach-compute-sdk.md#amlcompute)。

    當 ACR 位於虛擬網路後方時，Azure Machine Learning 就無法使用 ACR 來直接建置 Docker 映像。 但可改為使用計算叢集來建置映像。

完成這些需求之後，請使用下列步驟來啟用 Azure Container Registry。

1. 使用下列其中一種方法，尋找您工作區的 Azure Container Registry 名稱：

    __Azure 入口網站__

    在工作區的 [概觀] 區段中，[登錄] 值會連結至 Azure Container Registry。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="工作區的 Azure Container Registry" border="true":::

    __Azure CLI__

    如果您已[安裝適用於 Azure CLI 的 Machine Learning 擴充功能](reference-azure-machine-learning-cli.md)，則可以使用 `az ml workspace show` 命令來顯示工作區資訊。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令會傳回如下值：`"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`。 字串的最後一個部分是工作區的 Azure Container Registry 名稱。

1. 使用「 [設定適用于登錄的網路存取](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)」中的步驟，限制對您虛擬網路的存取。 在新增虛擬網路時，請選取 Azure Machine Learning 資源的虛擬網路和子網路。

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

1. 套用下列 Azure Resource Manager 範本。 此範本可讓您的工作區與 ACR 通訊。

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

## <a name="next-steps"></a>接下來的步驟

本文是四部分虛擬網路系列的第一篇。 請參閱文章的其餘部分，以瞭解如何保護虛擬網路：

* [第1部分：虛擬網路總覽](how-to-network-security-overview.md)
* [第3部分：保護定型環境的安全](how-to-secure-training-vnet.md)
* [第4部分：保護推斷環境](how-to-secure-inferencing-vnet.md)
* [第5部分：啟用 studio 功能](how-to-enable-studio-virtual-network.md)