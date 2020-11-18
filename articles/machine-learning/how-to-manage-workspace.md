---
title: 在入口網站中建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何使用適用于 Python 的 SDK 來建立、查看和刪除 Azure 入口網站中的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 1a7204fea1a77dbca57ffc7d512f81e46c4d3b5f
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873383"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>建立和管理 Azure Machine Learning 工作區 

在本文中，您將使用適用于 Python 的 Azure 入口網站或 [SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) ，來建立、查看和刪除 [Azure Machine Learning](overview-what-is-azure-ml.md)的 [**Azure Machine Learning 工作區**](concept-workspace.md)

當您的需求變更或自動化的需求增加時，您也可以 [使用 CLI](reference-azure-machine-learning-cli.md)或透過 [VS Code 擴充](tutorial-setup-vscode-extension.md)功能來建立和刪除工作區。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* 如果使用 Python SDK，請 [安裝 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。

## <a name="create-a-workspace"></a>建立工作區

# <a name="python"></a>[Python](#tab/python)

* **預設規格。** 依預設，會自動建立相依資源和資源群組。 此程式碼會建立名為的工作區 `myworkspace` ，以及中所命名的資源群組 `myresourcegroup` `eastus2` 。
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    `create_resource_group`如果您有想要用於工作區的現有 Azure 資源群組，請設定為 False。

* <a name="create-multi-tenant"></a>**多個租使用者。**  如果您有多個帳戶，請新增您想要使用之 Azure Active Directory 的租使用者識別碼。  在 [ **Azure Active Directory、外部** 身分識別] 下的 [Azure 入口網站](https://portal.azure.com)中尋找您的租使用者識別碼。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[主權雲端](reference-machine-learning-cloud-parity.md)**。 如果您是在主權雲端中工作，您將需要額外的程式碼來向 Azure 進行驗證。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **使用現有的 Azure 資源**。  您也可以使用 Azure 資源識別碼格式來建立使用現有 Azure 資源的工作區。 在 Azure 入口網站中或使用 SDK 尋找特定的 Azure 資源識別碼。 此範例假設資源群組、儲存體帳戶、金鑰保存庫、App Insights 和 container registry 都已存在。

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

如需詳細資訊，請參閱 [工作區 SDK 參考](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)。

如果您在存取訂用帳戶時遇到問題，請參閱 [設定 Azure Machine Learning 資源與工作流程的驗證](how-to-setup-authentication.md)，以及 [Azure Machine Learning 筆記本中的驗證](https://aka.ms/aml-notebook-auth) 。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 使用您 Azure 訂閱的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

1. 在 Azure 入口網站的左上角，選取 [+建立資源]。

      ![建立新的資源](./media/how-to-manage-workspace/create-workspace.gif)

1. 使用搜尋列尋找 **Machine Learning**。

1. 選取 [Machine Learning]  。

1. 在 [Machine Learning]  窗格中選取 [建立]  來開始操作。

1. 提供下列資訊來設定新的工作區：

   欄位|描述 
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。 工作區名稱不區分大小寫。
   訂用帳戶 |選取您要使用的 Azure 訂用帳戶。
   資源群組 | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 您需要 *參與者* 或 *擁有* 者角色，才能使用現有的資源群組。  如需存取的詳細資訊，請參閱 [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)。
   區域 | 選取最接近您的使用者與資料資源的 Azure 區域，以建立您的工作區。
   | 儲存體帳戶 | 工作區的預設儲存體帳戶。 預設會建立一個新的。 |
   | Key Vault | 工作區所使用的 Azure Key Vault。 預設會建立一個新的。 |
   | Application Insights | 工作區的 application insights 實例。 預設會建立一個新的。 |
   | Container Registry | 工作區的 Azure Container Registry。 依預設， _不_ 會針對工作區一開始建立新的。 相反地，當您在定型或部署期間建立 Docker 映射時，就會建立此檔案。 |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="設定您的工作區。":::

1. 當您完成工作區的設定時，請選取 [ **審核 + 建立**]。 （選擇性）使用 [ [網路](#networking) ] 和 [ [Advanced](#advanced) ] 區段來設定工作區的更多設定。

1. 檢查設定，並進行任何額外的變更或修正。 當您對設定感到滿意之後，請選取 [ **建立**]。

   > [!Warning] 
   > 在雲端中建立工作區可能需要數分鐘的時間。

   程序完成後，會出現部署成功訊息。 
 
 1. 若要檢視新的工作區，選取 [前往資源]  。
 
---

### <a name="networking"></a>網路功能  

> [!IMPORTANT]  
> 如需搭配使用私人端點和虛擬網路與工作區的詳細資訊，請參閱 [網路隔離和隱私權](how-to-network-security-overview.md)。


# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK 提供可搭配工作區使用的 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?preserve-view=true&view=azure-ml-py) 類別 [。建立 ( # B1 ](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-&preserve-view=true) 以建立具有私人端點的工作區。 此類別需要現有的虛擬網路。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 預設的網路設定是使用可在公用網際網路上存取的 __公用端點__。 若要將您的工作區存取限制為您所建立的 Azure 虛擬網路，您可以改為選取 __私人端點__ (預覽) 作為連線 __方法__，然後使用 [ __+ 新增__ ] 設定端點。   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="私人端點選取":::  

1. 在 [ __建立私人端點__ ] 表單上，設定要使用的位置、名稱和虛擬網路。 如果您想要使用端點搭配私人 DNS 區域，請選取 [ __與私人 dns 區域整合__ ]，然後使用 [ __私人 dns 區域__ ] 欄位選取區域。 選取 __[確定]__ 以建立端點。   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="私人端點建立":::   

1. 當您完成網路的設定時，您可以選取 [ __審核] + [建立__]，或前進至選擇性的 [ __Advanced__ configuration]。

---

> [!IMPORTANT]  
> 使用私人端點搭配 Azure Machine Learning 工作區目前處於公開預覽狀態。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。     
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="multiple-workspaces-with-private-endpoint"></a>具有私人端點的多個工作區

當您建立私人端點時，會建立名為 __privatelink.api.azureml.ms__ 的新私人 DNS 區域。 這包含虛擬網路的連結。 如果您在相同的資源群組中建立具有私人端點的多個工作區，則只有第一個私人端點的虛擬網路可以新增至 DNS 區域。 若要新增其他工作區/私人端點所使用之虛擬網路的專案，請使用下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取包含工作區的資源群組。 然後選取名為 __privatelink.api.azureml.ms__ 的私人 DNS 區域資源
2. 在 [ __設定__] 中，選取 [ __虛擬網路連結__]。
3. 選取 [新增]  。 從 [ __新增虛擬網路連結__ ] 頁面中，提供唯一的 __連結名稱__，然後選取要新增的 __虛擬網路__ 。 選取 __[確定]__ 以新增網路連結。

如需詳細資訊，請參閱 [Azure 私人端點 DNS](../private-link/private-endpoint-dns.md)設定。

### <a name="vulnerability-scanning"></a>弱點掃描

Azure 資訊安全中心為混合式雲端工作負載提供統一的安全性管理和進階威脅防護。 您應允許 Azure 資訊安全中心掃描您的資源，並遵循其建議。 如需詳細資訊，請參閱資訊安全中心和[Azure Kubernetes Services 與資訊安全中心整合](../security-center/defender-for-kubernetes-introduction.md)的[Azure Container Registry 映射掃描](../security-center/defender-for-container-registries-introduction.md)。

### <a name="advanced"></a>進階

根據預設，工作區的中繼資料會儲存在 Microsoft 所維護的 Azure Cosmos DB 實例中。 這項資料會使用 Microsoft 管理的金鑰進行加密。

若要限制 Microsoft 在您的工作區上收集的資料，請在入口網站中選取 [ __高業務影響] 工作區__ ，或 `hbi_workspace=true ` 在 Python 中設定。 如需此設定的詳細資訊，請參閱待用 [加密](concept-data-encryption.md#encryption-at-rest)。

> [!IMPORTANT]  
> 只有在建立工作區時，才能選取 [高業務衝擊]。 建立工作區之後，就無法變更此設定。   

#### <a name="use-your-own-key"></a>使用您自己的金鑰

您可以提供自己的金鑰進行資料加密。 這麼做會建立 Azure Cosmos DB 實例，以將中繼資料儲存在您的 Azure 訂用帳戶中。

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

使用下列步驟來提供您自己的金鑰：

> [!IMPORTANT]  
> 在遵循這些步驟之前，您必須先執行下列動作：   
>
> 1. 使用訂用帳戶的參與者許可權，在身分識別和存取管理) 中授權 __Machine Learning 應用程式__ (。  
> 1. 遵循 [設定客戶管理的金鑰](../cosmos-db/how-to-setup-cmk.md) 的步驟：
>     * 註冊 Azure Cosmos DB 提供者
>     * 建立並設定 Azure Key Vault
>     * 產生金鑰
>   
>     您不需要手動建立 Azure Cosmos DB 實例，系統會在工作區建立期間為您建立一個實例。 此 Azure Cosmos DB 實例會根據此模式使用名稱，在個別的資源群組中建立： `<your-workspace-resource-name>_<GUID>` 。   
>   
> 建立工作區之後，就無法變更此設定。 如果您刪除工作區所使用的 Azure Cosmos DB，也必須刪除正在使用該工作區的工作區。

# <a name="python"></a>[Python](#tab/python)

使用 `cmk_keyvault` 和 `resource_cmk_uri` 來指定客戶管理的金鑰。

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 選取 [ __客戶管理的金鑰__]，然後選取 [ __按一下以選取金鑰__]。

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="客戶管理的金鑰":::

1. 在 [ __從 Azure Key Vault 選取金鑰__ ] 表單中，選取現有的 Azure Key Vault、其包含的金鑰，以及金鑰的版本。 此金鑰用來加密 Azure Cosmos DB 中儲存的資料。 最後，使用 [ __選取__ ] 按鈕來使用此索引鍵。

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="選取金鑰":::

---

### <a name="download-a-configuration-file"></a>下載設定檔

如果您將建立 [計算實例](tutorial-1st-experiment-sdk-setup.md#azure)，請略過此步驟。  計算實例已為您建立此檔案的複本。

# <a name="python"></a>[Python](#tab/python)

如果您打算在本機環境上使用 () 參考此工作區的程式碼 `ws` ，請撰寫設定檔：

```python
ws.write_config()
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

如果您打算在參考此工作區的本機環境上使用程式碼， 請從工作區的 [概觀] 區段中，選取 [下載 config.xml]。  

   ![下載 config.json](./media/how-to-manage-workspace/configure.png)

---

使用 Python 指令碼或 Jupyter Notebook 將文件置於目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。 當您建立計算實例時，會為您將此檔案新增至 VM 上的正確目錄。

## <a name="connect-to-a-workspace"></a>連線到工作區

在您的 Python 程式碼中，您會建立工作區物件以連接到您的工作區。  此程式碼會讀取設定檔案的內容，以尋找您的工作區。  如果您尚未經過驗證，您將會收到登入的提示。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**多個租使用者。**  如果您有多個帳戶，請新增您想要使用之 Azure Active Directory 的租使用者識別碼。  在 [ **Azure Active Directory、外部** 身分識別] 下的 [Azure 入口網站](https://portal.azure.com)中尋找您的租使用者識別碼。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[主權雲端](reference-machine-learning-cloud-parity.md)**。 如果您是在主權雲端中工作，您將需要額外的程式碼來向 Azure 進行驗證。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
如果您在存取訂用帳戶時遇到問題，請參閱 [設定 Azure Machine Learning 資源與工作流程的驗證](how-to-setup-authentication.md)，以及 [Azure Machine Learning 筆記本中的驗證](https://aka.ms/aml-notebook-auth) 。

## <a name="find-a-workspace"></a><a name="view"></a>尋找工作區

查看您可以使用的所有工作區清單。

# <a name="python"></a>[Python](#tab/python)

在 Azure 入口網站的 [訂用帳戶] [頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中尋找您的訂閱。  複製識別碼，並在下列程式碼中使用它來查看該訂用帳戶可使用的所有工作區。

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在 [頂端搜尋] 欄位中，輸入 **Machine Learning**。  

1. 選取 [Machine Learning]  。

   ![搜尋 Azure Machine Learning 工作區](./media/how-to-manage-workspace/find-workspaces.png)

1. 查看找到的工作區清單。 您可以根據訂用帳戶、資源群組和位置來篩選。  

1. 選取工作區以顯示其屬性。

---


## <a name="delete-a-workspace"></a>刪除工作區

當您不再需要工作區時，請將其刪除。  

# <a name="python"></a>[Python](#tab/python)

刪除工作區 `ws` ：

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

預設動作不會刪除與工作區相關聯的資源，例如 container registry、儲存體帳戶、金鑰保存庫和 application insights。  `delete_dependent_resources`如果設定為 True，則也會刪除這些資源。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您要刪除的工作區頂端的 [ **刪除**  ]。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="刪除工作區":::

---

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>刪除 Azure Container Registry

Azure Machine Learning 工作區會使用 Azure Container Registry (ACR) 進行某些作業。 它會在第一次需要 ACR 執行個體時自動建立一個。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>範例

建立工作區的範例：
* 使用 Azure 入口網站來 [建立工作區和計算實例](tutorial-1st-experiment-sdk-setup.md)
* 使用 Python SDK [在您自己的環境中建立工作區](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>後續步驟

擁有工作區之後，請瞭解如何 [定型和部署模型](tutorial-train-models-with-aml.md)。