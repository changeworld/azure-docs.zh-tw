---
title: 為 Azure Cosmos 帳戶配置基於虛擬網路的訪問
description: 本文件將說明設定 Azure Cosmos DB 虛擬網路服務端點所需的步驟。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366238"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>設定從虛擬網路 (VNet) 存取

您可以設定 Azure Cosmos DB 帳戶，只允許從 Azure 虛擬網路的特定子網路存取。 若要限制只能從虛擬網路中的子網路進行連線來存取 Azure Cosmos DB 帳戶：

1. 使子網路能夠將子網路和虛擬網路身分識別傳送至 Azure Cosmos DB。 為此，您可以在特定子網路上啟用 Azure Cosmos DB 的服務端點。

1. 在 Azure Cosmos DB 帳戶中新增規則，將子網路指定為可存取帳戶的來源。

> [!NOTE]
> 在子網路上啟用 Azure Cosmos DB 帳戶的服務端點時，送到 Azure Cosmos DB 的流量來源就會從公用 IP 切換為虛擬網路和子網路。 對於任何要從這個子網路存取的 Azure Cosmos DB 帳戶，都會套用流量切換。 如果您的 Azure Cosmos DB 帳戶有允許此子網路的 IP 型防火牆，已啟用服務的子網路所發出的要求將不再符合 IP 防火牆規則，而會被拒絕。
>
> 若要深入了解，請參閱本文的[從 IP 防火牆規則移轉至虛擬網路存取控制清單](#migrate-from-firewall-to-vnet)一節所說明的步驟。

以下幾節將說明如何設定 Azure Cosmos DB 帳戶的虛擬網路服務端點。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>使用 Azure 入口網站來設定服務端點

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>對現有的 Azure 虛擬網路和子網路設定服務端點

1. 從 [所有資源]**** 刀鋒視窗中，尋找要保護的 Azure Cosmos DB 帳戶。

1. 從設定功能表中選取 [防火牆與虛擬網路]****，並選擇允許從 [選取的網路]**** 進行存取。

1. 若要將存取權授與現有虛擬網路的子網路，請在 [虛擬網路]**** 下選取 [新增現有 Azure 虛擬網路]****。

1. 選取您要從中新增 Azure 虛擬網路的**訂用帳戶**。 選取要提供 Azure Cosmos DB 帳戶存取權的 Azure **虛擬網路**和**子網路**。 接下來，選取 [啟用]**** 以啟用具有 "Microsoft.AzureCosmosDB" 服務端點的選定網路。 完成後，選擇 **"添加**"。

   ![選取虛擬網路和子網路](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. 在 Azure Cosmos DB 帳戶啟用從虛擬網路的存取後，它將只會允許來自這個選定子網路的流量。 您所新增的虛擬網路和子網路應會如下列螢幕擷取畫面所示：

   ![已成功設定虛擬網路和子網路](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 若要啟用虛擬網路服務端點，您必須具備下列訂用帳戶權限：
>   * 虛擬網路的訂用帳戶：網路參與者
>   * Azure Cosmos DB 帳戶的訂用帳戶：DocumentDB 帳戶參與者
>   * 如果您的虛擬網路和 Azure Cosmos DB 帳戶位於不同的訂閱中，請確保具有虛擬網路的訂閱也已`Microsoft.DocumentDB`註冊資來源提供者。 要註冊資來源提供者，請參閱[Azure 資來源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)文章。

以下是向資來源提供者註冊訂閱的說明。

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>對新的 Azure 虛擬網路和子網路設定服務端點

1. 從 [所有資源]**** 刀鋒視窗中，尋找要保護的 Azure Cosmos DB 帳戶。  

1. 從設定功能表中選取 [防火牆與 Azure 虛擬網路]****，並選擇允許從 [選取的網路]**** 進行存取。  

1. 若要授與新 Azure 虛擬網路的存取權，請在 [虛擬網路]**** 下選取 [新增虛擬網路]****。  

1. 提供建立新虛擬網路所需的詳細資料，然後選取 [建立]****。 這會建立已啟用 "Microsoft.AzureCosmosDB" 服務端點的子網路。

   ![選取虛擬網路與新虛擬網路的子網路](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

如果 Azure Cosmos DB 帳戶由 Azure 認知搜索等其他 Azure 服務使用，或者從流分析或 Power BI 訪問，則可以通過選擇**從全域 Azure 資料中心中的"接受連接**"來允許訪問。

若要確保您可以從入口網站存取 Azure Cosmos DB 計量，您必須啟用 [允許從 Azure 入口網站存取]**** 選項。 若要深入了解這些選項，請參閱[設定 IP 防火牆](how-to-configure-firewall.md)一文。 啟用存取之後，請選取 [儲存]**** 以儲存設定。

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>移除現有虛擬網路或子網路

1. 從 [所有資源]**** 刀鋒視窗中，尋找您要對其指派端點的 Azure Cosmos DB 帳戶。  

1. 從設定功能表中選取 [防火牆與虛擬網路]****。  

1. 若要移除虛擬網路或子網路規則，請選取該虛擬網路或子網路旁的 […]****，然後選取 [移除]****。

   ![移除虛擬網路](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. 選取 [儲存]**** 以套用變更。

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>使用 Azure PowerShell 來設定服務端點

> [!NOTE]
> 當您使用 PowerShell 或 Azure CLI 時，請務必在參數中指定 IP 篩選器和虛擬網路 ACL 的完整清單，而不是只指定必須新增的項目。

若要使用 Azure PowerShell 對 Azure Cosmos DB 帳戶設定服務端點，請使用下列步驟：  

1. 安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 並[登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。  

1. 對現有的虛擬網路子網路啟用服務端點。  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. 取得虛擬網路資訊。

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 準備宇宙資料庫虛擬網路規則

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 使用新的虛擬網路終結點配置更新 Azure Cosmos DB 帳戶屬性： 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 若要確認 Azure Cosmos DB 帳戶已透過上一個步驟中設定的虛擬網路服務端點進行更新，請執行下列命令：

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>使用 Azure CLI 來設定服務端點

如果子網已為其配置，則可以為服務終結點配置 Azure Cosmos 帳戶。 還可以在 Cosmos 帳戶上啟用服務終結點，其中子網尚未為其配置，然後在以後配置子網時開始工作。 這種靈活性允許同時無法訪問 Cosmos 帳戶和虛擬網路資源的管理員使其配置彼此獨立。

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>創建新的 Cosmos 帳戶並將其連接到新虛擬網路的後端子網

在此示例中，虛擬網路和子網在創建時啟用了服務終結點。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>獨立地將 Cosmos 帳戶連接到後端子網

此示例旨在演示如何將 Azure Cosmos 帳戶連接到現有新虛擬網路，其中子網尚未配置為服務終結點。 這是通過使用 參數來完成的`--ignore-missing-vnet-service-endpoint`。 這允許 Cosmos 帳戶的配置在完成虛擬網路子網的配置之前完成，而不會出錯。 子網路設定完成後，便可透過已設定的子網路存取 Cosmos 帳戶。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>從 IP 防火牆規則移轉至虛擬網路 ACL

要將 Azure Cosmos DB 帳戶從使用 IP 防火牆規則遷移到使用虛擬網路服務終結點，請使用以下步驟。

為子網的服務終結點配置 Azure Cosmos DB 帳戶後，來自該子網的請求將發送到 Azure Cosmos DB，其中包含虛擬網路和子網源資訊，而不是源公共 IP 位址。 這些請求將不再與 Azure Cosmos DB 帳戶上配置的 IP 篩選器匹配，因此需要以下步驟來避免停機。

在繼續操作之前，請使用上面顯示的步驟在虛擬網路和子網上啟用 Azure Cosmos DB 服務終結點，這些步驟"為虛擬網路的現有子網啟用服務終結點"。

1. 獲取虛擬網路和子網資訊：

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 為 Azure Cosmos DB 帳戶準備新的虛擬網路規則物件：

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 更新 Azure Cosmos DB 帳戶以啟用從子網訪問服務終結點：

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 對從子網訪問的所有 Azure Cosmos DB 帳戶重複上述步驟。

1. 從 Azure Cosmos DB 帳戶的防火牆規則中刪除子網的 IP 防火牆規則。

## <a name="next-steps"></a>後續步驟

* 若要設定 Azure Cosmos DB 的防火牆，請參閱[防火牆支援](firewall-support.md)一文。
