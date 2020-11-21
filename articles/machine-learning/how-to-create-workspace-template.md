---
title: 使用 Azure Resource Manager 範本建立工作區
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Resource Manager 範本建立新的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: f07efcc18f3eff7e40232941befb563cd236266b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013033"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區


<br>

在本文中，您將了解使用 Azure Resource Manager 範本建立 Azure Machine Learning 工作區的數種方式。 Resource Manager 範本可讓您輕鬆地以單一、協調的作業建立資源。 範本是 JSON 文件，其定義部署所需的資源。 它也可以指定部署參數。 參數用來在使用範本時提供輸入值。

如需詳細資訊，請參閱 [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可[試用免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從 CLI 使用範本，您需要 [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) 或 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。

* 某些案例需要您開啟支援票證。 這些案例為：

    * __已啟用 Private Link 的工作區與客戶自控金鑰__
    * __虛擬網路背後工作區的 Azure Container Registry__

    如需詳細資訊，請參閱 [管理和增加配額](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

## <a name="limitations"></a>限制

* 建立新的工作區時，您可以允許工作區自動建立所需的 Azure 服務，或提供現有的服務。 提供現有的服務時，這些服務必須與工作區位於相同的 Azure 訂用帳戶中。

## <a name="workspace-resource-manager-template"></a>工作區 Resource Manager 範本

本檔中使用的 Azure Resource Manager 範本，可以在 Azure 快速入門範本 GitHub 存放庫的 [201-機器學習-advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) 目錄中找到。

此範本會建立下列 Azure 服務：

* Azure 儲存體帳戶
* Azure 金鑰保存庫
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning 工作區

資源群組是保存服務的容器。 各種服務是 Azure Machine Learning 工作區的必要項。

範例範本有兩個 **必要** 參數：

* 將建立資源的 **位置** 。

    範本將會針對大部分的資源使用您選取的位置。 例外狀況是 Application Insights 服務，該服務在其他服務可用的所有位置都無法使用。 如果您選取的位置無法使用，服務會建立在美國中南部位置。

* **WorkspaceName**，這是 Azure Machine Learning 工作區的易記名稱。

    > [!NOTE]
    > 工作區名稱不區分大小寫。

    其他服務的名稱會隨機產生。

> [!TIP]
> 雖然與本文件建立關聯的範本會建立新 Azure Container Registry，但您也可建立新的工作區，而不需要建立容器登錄。 當執行需要容器登錄的作業時，將會建立一個。 例如，定型或部署模型。
>
> 您也可以在 Azure Resource Manager 範本中參考現有的容器登錄或儲存體帳戶，而不是建立一個新的。 不過，您使用的容器登錄必須啟用系統 __管理員帳戶__ 。 如需啟用系統管理員帳戶的相關資訊，請參閱系統 [管理員帳戶](../container-registry/container-registry-authentication.md#admin-account)。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

如需範本的詳細資訊，請參閱下列文章：

* [編寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resource 類型](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>部署範本

若要部署您的範本，您必須建立資源群組。

如果您想要使用圖形化使用者介面，請參閱 [Azure 入口網站](#use-the-azure-portal) 一節。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

一旦成功建立您的資源群組，請使用下列命令來部署範本：

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

依預設，建立為範本一部分的所有資源都是新的。 不過，您也可以選擇使用現有的資源。 藉由提供範本的其他參數，您可以使用現有的資源。 例如，如果您想要使用現有的儲存體帳戶，請將 **storageAccountOption** 值設定為 **現有** 的，並在 **storageAccountName** 參數中提供儲存體帳戶的名稱。

> [!IMPORTANT]
> 如果您想要使用現有的 Azure 儲存體帳戶，它不能是 premium 帳戶 (Premium_LRS 和 Premium_GRS) 。 它也不能有階層命名空間 (用於 Azure Data Lake Storage Gen2) 。 使用工作區的預設儲存體帳戶並不支援 premium 儲存體或階層命名空間。 工作區的 _預設_ 儲存體帳戶不支援 premium 儲存體或階層命名空間。 您可以搭配使用 premium 儲存體或階層命名空間與 _非預設_ 儲存體帳戶。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>部署加密工作區

下列範例範本示範如何建立具有三項設定的工作區：

* 啟用工作區的高機密性設定。 這會建立新的 Cosmos DB 實例。
* 啟用工作區的加密。
* 使用現有的 Azure Key Vault 取出客戶管理的金鑰。 客戶管理的金鑰可用來為工作區建立新的 Cosmos DB 實例。

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> 建立工作區之後，即無法變更機密資料、加密、金鑰保存庫識別碼或金鑰識別碼的設定。 若要變更這些值，則必須使用新的值來建立新工作區。

如需詳細資訊，請參閱[靜態加密](concept-data-encryption.md#encryption-at-rest)。

> [!IMPORTANT]
> 使用此範本之前，訂用帳戶必須符合一些特定需求：
> * 您必須具有包含加密金鑰的現有 Azure Key Vault。
> * Azure Key Vault 必須位於打算建立 Azure Machine Learning 工作區的相同區域中。
> * 您必須指定 Azure Key Vault 的識別碼和加密金鑰的 URI。

若要取得此範本所需 `cmk_keyvault` (Key Vault 的識別碼) 和 `resource_cmk_uri` (金鑰 URI) 參數的值，請使用下列步驟：    

1. 若要取得 Key Vault 識別碼，請使用下列命令：  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    此命令會傳回如下值：`/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>`。  

1. 若要取得客戶管理金鑰的 URI 值，請使用下列命令：    

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    此命令會傳回如下值：`https://mykeyvault.vault.azure.net/keys/mykey/{guid}`。 

> [!IMPORTANT]  
> 建立工作區之後，即無法變更機密資料、加密、金鑰保存庫識別碼或金鑰識別碼的設定。 若要變更這些值，則必須使用新的值來建立新工作區。

若要允許使用客戶管理的金鑰，請在部署範本時設定下列參數：

* **encryption_status** 為 [ **已啟用**]。
* **cmk_keyvault** 為 `cmk_keyvault` 先前步驟中取得的值。
* **resource_cmk_uri** 為 `resource_cmk_uri` 先前步驟中取得的值。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

使用客戶管理的金鑰時，Azure Machine Learning 建立包含 Cosmos DB 實例的次要資源群組。 如需詳細資訊，請參閱 [靜態加密-Cosmos DB](concept-data-encryption.md#encryption-at-rest)。

您可以為資料提供的其他設定是將 **confidential_data** 參數設定為 **true**。 這樣做會執行下列動作：

* 開始加密 Azure Machine Learning 計算叢集的本機暫存磁片，提供您未在訂用帳戶中建立任何先前的叢集。 如果您先前已在訂用帳戶中建立叢集，請開啟支援票證，以針對您的計算叢集啟用暫存磁片的加密。
* 清除執行之間的本機暫存磁片。
* 使用 key vault，安全地將儲存體帳戶、容器登錄和 SSH 帳戶的認證從執行層傳遞至您的計算叢集。
* 啟用 IP 篩選以確保基礎 batch 集區無法由 AzureMachineLearningService 以外的任何外部服務呼叫。

    > [!IMPORTANT]
    > 建立工作區之後，即無法變更機密資料、加密、金鑰保存庫識別碼或金鑰識別碼的設定。 若要變更這些值，則必須使用新的值來建立新工作區。

  如需詳細資訊，請參閱待用 [加密](concept-data-encryption.md#encryption-at-rest)。

## <a name="deploy-workspace-behind-a-virtual-network"></a>在虛擬網路後方部署工作區

將 `vnetOption` 參數值設定為或時 `new` `existing` ，您就能夠建立虛擬網路背後的工作區所使用的資源。

> [!IMPORTANT]
> 針對 container registry，只支援 ' Premium ' sku。

> [!IMPORTANT]
> Application Insights 不支援在虛擬網路後方部署。

### <a name="only-deploy-workspace-behind-private-endpoint"></a>只在私人端點後方部署工作區

如果相關聯的資源不在虛擬網路後方，您可以將 **privateEndpointType** 參數設定為 `AutoAproval` 或， `ManualApproval` 以將工作區部署到私人端點後方。 這可以針對新的和現有的工作區進行。 更新現有的工作區時，請使用現有工作區中的資訊填入範本參數。

> [!IMPORTANT]
> Azure Government 區域或 Azure 中國的世紀區域無法使用具有 private link 的 Azure Machine Learning 工作區。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>使用新的虛擬網路

若要在新的虛擬網路後方部署資源，請將 **vnetOption** 設定為 [ **新增** ]，以及個別資源的虛擬網路設定。 下列部署示範如何在新的虛擬網路後方部署具有儲存體帳戶資源的工作區。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

或者，您可以在虛擬網路後方部署多個或所有的相依資源。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>使用現有的虛擬網路 & 資源

若要使用現有相關聯的資源來部署工作區，您必須將 **vnetOption** 參數設定為 **現有** 的，以及子網參數。 不過，在部署 **之前** ，您必須在虛擬網路中為每個資源建立服務端點。 如同新的虛擬網路部署，您可以在虛擬網路後方有一或多個資源。

> [!IMPORTANT]
> 子網應具有 `Microsoft.Storage` 服務端點

> [!IMPORTANT]
> 子網不允許建立私人端點。 停用私人端點以啟用子網。

1. 啟用資源的服務端點。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. 部署工作區

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 遵循[從自訂範本部署資源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)的步驟。 當您到達 [ __選取範本__ ] 畫面時，請從下拉式清單中選擇 [ **201-機器學習-advanced** ] 範本。
1. 選取 [ __選取範本__ ] 以使用範本。 根據您的部署案例提供下列必要資訊和任何其他參數。

   * 訂用帳戶：選取要用於這些資源的 Azure 訂用帳戶。
   * 資源群組：選取或建立資源群組以包含服務。
   * 區域：選取將在其中建立資源的 Azure 區域。
   * 工作區名稱：要用於將建立之Azure Machine Learning 工作區的名稱。 工作區名稱必須介於 3 到 33 個字元之間。 只能包含英數字元和 '-'。
   * 位置：選取將建立資源的位置。
1. 選取 [檢閱 + 建立]。
1. 在 [ __審核 + 建立__ ] 畫面中，同意列出的條款及條件，然後選取 [ __建立__]。

如需詳細資訊，請參閱[從自訂範本部署資源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 存取原則和 Azure Resource Manager 範本

當多次使用 Azure Resource Manager 範本來建立工作區和相關聯資源 (包括 Azure Key Vault) 時。 例如，多次使用範本並搭配相同的參數，以作為持續整合和部署管線的一部分。

大部分透過範本的資源建立作業均為等冪，但 Key Vault 會在每次使用範本時清除存取原則。 清除存取原則會針對正在使用它的任何現有工作區，中斷對 Key Vault 的存取權。 例如，Azure Notebooks VM 的停止/建立功能可能會失敗。  

若要避免這個問題，建議採用下列其中一種方法：

* 不要針對相同的參數多次部署範本。 或刪除現有的資源，然後使用範本予以重建。

* 檢查 Key Vault 的存取原則，然後使用這些原則來設定範本的 `accessPolicies` 屬性。 若要檢視存取原則，請使用下列 Azure CLI 命令：

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    如需使用範本 `accessPolicies` 區段的詳細資訊，請參閱 [AccessPolicyEntry 物件參考](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)。

* 檢查 Key Vault 資源是否已存在。 如果有，請不要透過範本予以重建。 例如，若要使用現有的 Key Vault，而不是建立新的，請對範本進行下列變更：

    * **新增** 可接受現有 Key Vault 資源識別碼的參數：

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **移除** 建立 Key Vault 資源的區段：

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * 從工作區的 `dependsOn` 區段 **移除** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 行。 此外，**變更** 工作區中 `properties` 區段的 `keyVault` 項目，以參考 `keyVaultId` 參數：

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    這些變更之後，即可在執行範本時指定現有 Key Vault 資源的識別碼。 然後範本會將工作區的 `keyVault` 屬性設定為其識別碼，以重複使用 Key Vault。

    若要取得 Key Vault 的識別碼，可參考原始範本執行的輸出，或使用 Azure CLI。 下列命令是使用 Azure CLI 取得 Key Vault 資源識別碼的範例：

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    此命令會傳回類似下列文字的值：

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>未連結至私人 DNS 區域的虛擬網路

使用私人端點建立工作區時，此範本會建立名為 __privatelink.api.azureml.ms__ 的私人 DNS 區域。 __虛擬網路連結__ 會自動新增到此私人 DNS 區域。 只會針對您在資源群組中建立的第一個工作區和私人端點新增連結;如果您在相同的資源群組中建立另一個具有私人端點的虛擬網路和工作區，則第二個虛擬網路可能不會新增至私人 DNS 區域。

若要查看私人 DNS 區域已存在的虛擬網路連結，請使用下列 Azure CLI 命令：

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

若要新增包含另一個工作區和私人端點的虛擬網路，請使用下列步驟：

1. 若要尋找您想要新增之網路的虛擬網路識別碼，請使用下列命令：

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    此命令會傳回類似于 ' "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" ' 的值。 儲存此值，並在下一個步驟中使用。

2. 若要將虛擬網路連結新增至 privatelink.api.azureml.ms 私人 DNS 區域，請使用下列命令。 針對 `--virtual-network` 參數，請使用前一個命令的輸出：

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/templates/deploy-rest.md)。
* [透過 Visual Studio 建立與部署 Azure 資源群組](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
* [如需 Azure Machine Learning 的其他相關範本，請參閱 Azure 快速入門範本存放庫](https://github.com/Azure/azure-quickstart-templates)