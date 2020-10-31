---
title: 針對 Azure Cosmos 帳戶設定以虛擬網路為基礎的存取
description: 本文件將說明設定 Azure Cosmos DB 虛擬網路服務端點所需的步驟。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bbdbc7a0ed71634522bd68dbf0807264a409525
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101692"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>設定從虛擬網路 (VNet) 存取 Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

您可以設定 Azure Cosmos 帳戶，以只允許從虛擬網路 (VNet) 的特定子網路存取。 藉由啟用[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，即可存取虛擬網路內子網路中的 Azure Cosmos DB，來自該子網路的流量會傳送至 Azure Cosmos DB，並具備子網路和虛擬網路的身分識別。 Azure Cosmos DB 服務端點啟用之後，可以將它新增至您的 Azure Cosmos 帳戶以限制對子網路的存取。

根據預設，如果要求伴隨有效的授權權杖，就可以從任何來源存取 Azure Cosmos 帳戶。 當您在 VNet 內新增一或多個子網路時，只有來自那些子網路的要求會取得有效回應。 來自任何其他來源的要求將會收到 403 (禁止) 回應。 

您可以設定 Azure Cosmos DB 帳戶，只允許從 Azure 虛擬網路的特定子網路存取。 若要限制只能從虛擬網路中的子網路進行連線來存取 Azure Cosmos DB 帳戶：

1. 使子網路能夠將子網路和虛擬網路身分識別傳送至 Azure Cosmos DB。 為此，您可以在特定子網路上啟用 Azure Cosmos DB 的服務端點。

1. 在 Azure Cosmos DB 帳戶中新增規則，將子網路指定為可存取帳戶的來源。

> [!NOTE]
> 在子網路上啟用 Azure Cosmos DB 帳戶的服務端點時，送到 Azure Cosmos DB 的流量來源就會從公用 IP 切換為虛擬網路和子網路。 對於任何要從這個子網路存取的 Azure Cosmos DB 帳戶，都會套用流量切換。 如果您的 Azure Cosmos DB 帳戶有允許此子網路的 IP 型防火牆，已啟用服務的子網路所發出的要求將不再符合 IP 防火牆規則，而會被拒絕。
>
> 若要深入了解，請參閱本文的[從 IP 防火牆規則移轉至虛擬網路存取控制清單](#migrate-from-firewall-to-vnet)一節所說明的步驟。

以下幾節將說明如何設定 Azure Cosmos DB 帳戶的虛擬網路服務端點。

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>使用 Azure 入口網站來設定服務端點

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>對現有的 Azure 虛擬網路和子網路設定服務端點

1. 從 [所有資源]  刀鋒視窗中，尋找要保護的 Azure Cosmos DB 帳戶。

1. 從設定功能表中選取 [防火牆與虛擬網路]  ，並選擇允許從 [選取的網路]  進行存取。

1. 若要將存取權授與現有虛擬網路的子網路，請在 [虛擬網路]  下選取 [新增現有 Azure 虛擬網路]  。

1. 選取您要從中新增 Azure 虛擬網路的 **訂用帳戶** 。 選取要提供 Azure Cosmos DB 帳戶存取權的 Azure **虛擬網路** 和 **子網路** 。 接下來，選取 [啟用]  以啟用具有 "Microsoft.AzureCosmosDB" 服務端點的選定網路。 完成時，請選取 [ **新增** ]。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="選取虛擬網路和子網路":::

1. 在 Azure Cosmos DB 帳戶啟用從虛擬網路的存取後，它將只會允許來自這個選定子網路的流量。 您所新增的虛擬網路和子網路應會如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="選取虛擬網路和子網路":::

> [!NOTE]
> 若要啟用虛擬網路服務端點，您必須具備下列訂用帳戶權限：
>   * 虛擬網路的訂用帳戶：網路參與者
>   * Azure Cosmos DB 帳戶的訂用帳戶：DocumentDB 帳戶參與者
>   * 如果您的虛擬網路和 Azure Cosmos DB 帳戶位於不同的訂用帳戶，請確定具有虛擬網路的訂用帳戶也已 `Microsoft.DocumentDB` 註冊資源提供者。 若要註冊資源提供者，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md) 文章。

以下是向資源提供者註冊訂用帳戶的指示。

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>對新的 Azure 虛擬網路和子網路設定服務端點

1. 從 [所有資源]  刀鋒視窗中，尋找要保護的 Azure Cosmos DB 帳戶。  

1. 從設定功能表中選取 [防火牆與 Azure 虛擬網路]  ，並選擇允許從 [選取的網路]  進行存取。  

1. 若要授與新 Azure 虛擬網路的存取權，請在 [虛擬網路]  下選取 [新增虛擬網路]  。  

1. 提供建立新虛擬網路所需的詳細資料，然後選取 [建立]  。 這會建立已啟用 "Microsoft.AzureCosmosDB" 服務端點的子網路。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="選取虛擬網路和子網路":::

如果您的 Azure Cosmos DB 帳戶會由其他 Azure 服務（例如 Azure 認知搜尋）使用，或從串流分析或 Power BI 存取，您可以選取 [ **接受來自全球 Azure 資料中心內的** 連線]，以允許存取。

若要確保您可以從入口網站存取 Azure Cosmos DB 計量，您必須啟用 [允許從 Azure 入口網站存取]  選項。 若要深入了解這些選項，請參閱[設定 IP 防火牆](how-to-configure-firewall.md)一文。 啟用存取之後，請選取 [儲存]  以儲存設定。

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>移除現有虛擬網路或子網路

1. 從 [所有資源]  刀鋒視窗中，尋找您要對其指派端點的 Azure Cosmos DB 帳戶。  

1. 從設定功能表中選取 [防火牆與虛擬網路]  。  

1. 若要移除虛擬網路或子網路規則，請選取該虛擬網路或子網路旁的 […]  ，然後選取 [移除]  。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="選取虛擬網路和子網路":::

1. 選取 [儲存] 來套用您的變更。

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>使用 Azure PowerShell 來設定服務端點

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 對 Azure Cosmos DB 帳戶設定服務端點，請使用下列步驟：  

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。  

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

   > [!NOTE]
   > 當您使用 PowerShell 或 Azure CLI 時，請務必在參數中指定 IP 篩選器和虛擬網路 ACL 的完整清單，而不是只指定必須新增的項目。

1. 取得虛擬網路資訊。

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 準備 Cosmos DB 的虛擬網路規則

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 使用新的虛擬網路端點設定來更新 Azure Cosmos DB 帳戶屬性： 

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

如果服務端點已設定好子網，則可在稍後建立或更新時，為服務端點設定 Azure Cosmos 帳戶。 您也可以在尚未設定子網的 Cosmos 帳戶上啟用服務端點，然後在稍後設定子網時開始運作。 這種彈性可讓沒有 Cosmos 帳戶和虛擬網路資源存取權的系統管理員，讓他們的設定彼此獨立。

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>建立新的 Cosmos 帳戶，並將它連線到新虛擬網路的後端子網

在此範例中，會建立虛擬網路和子網，並在建立時為兩者啟用服務端點。

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>將 Cosmos 帳戶獨立連線並設定為後端子網

此範例旨在示範如何將 Azure Cosmos 帳戶連線到現有的新虛擬網路，而該子網尚未針對服務端點進行設定。 這是使用參數完成的 `--ignore-missing-vnet-service-endpoint` 。 如此一來，在虛擬網路的子網設定完成之前，Cosmos 帳戶的設定才能完成，而不會發生錯誤。 子網路設定完成後，便可透過已設定的子網路存取 Cosmos 帳戶。

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

## <a name="port-range-when-using-direct-mode"></a>使用直接模式時的埠範圍

當您透過直接模式連線使用服務端點搭配 Azure Cosmos 帳戶時，您必須確定從10000到20000的 TCP 埠範圍是開啟的。

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>從 IP 防火牆規則移轉至虛擬網路 ACL

若要將 Azure Cosmos DB 帳戶從使用 IP 防火牆規則遷移至使用虛擬網路服務端點，請使用下列步驟。

針對子網的服務端點設定 Azure Cosmos DB 帳戶之後，該子網的要求會傳送至具有虛擬網路和子網來源資訊的 Azure Cosmos DB，而不是來源公用 IP 位址。 這些要求將不再符合 Azure Cosmos DB 帳戶上設定的 IP 篩選器，這也是為什麼必須執行下列步驟以避免停機的原因。

繼續之前，請使用上述「為虛擬網路的現有子網啟用服務端點」中顯示的步驟，在虛擬網路和子網上啟用 Azure Cosmos DB 服務端點。

1. 取得虛擬網路和子網資訊：

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

1. 更新 Azure Cosmos DB 帳戶，以從子網啟用服務端點存取：

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 針對從子網存取的所有 Azure Cosmos DB 帳戶重複上述步驟。

1. 從 Azure Cosmos DB 帳戶的防火牆規則中移除子網的 IP 防火牆規則。

## <a name="frequently-asked-questions"></a>常見問題集

以下是與從虛擬網路設定存取權的一些常見問答集：

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>筆記本和 Mongo/Cassandra Shell 目前是否與已啟用虛擬網路的帳戶相容？

目前，VNET 存取不支援在 Cosmos DB 資料總管中的 [Mongo shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) 和 [Cassandra shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) 整合，以及 [Jupyter 筆記本服務](./cosmosdb-jupyter-notebooks.md)。 目前正在積極開發此功能。

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>是否可以在 Azure Cosmos 帳戶中同時指定虛擬網路服務端點和 IP 存取控制原則？ 

您可以在您的 Azure Cosmos 帳戶中同時啟用虛擬網路服務端點和 IP 存取控制原則 (也稱為防火牆)。 這兩個功能彼此互補，且可共同確保 Azure Cosmos 帳戶的隔離與安全性。 使用 IP 防火牆可確保靜態 IP 可以存取您的帳戶。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>如何限制對虛擬網路內子網路的存取？ 

若要從子網路存取 Azure Cosmos 帳戶，必須執行兩個步驟。 首先，您要允許子網路流量將其子網路和虛擬網路身分識別攜帶至 Azure Cosmos DB。 您可以在子網路上啟用 Azure Cosmos DB 的服務端點來達到這個目的。 接下來要在 Azure Cosmos 帳戶中新增規則，將這個子網路指定為可存取帳戶的來源。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>虛擬網路 ACL 與 IP 防火牆會拒絕要求或連線嗎？ 

在新增了 IP 防火牆或虛擬網路存取規則時，只有來自已允許來源的要求能取得有效回應。 系統會拒絕其他要求，並顯示 403 (禁止)。 請務必區別 Azure Cosmos 帳戶的防火牆和連線層級防火牆。 來源仍可連線至服務，且連線本身不會被拒絕。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>當我在子網路上啟用 Azure Cosmos DB 的服務端點時，我的要求開始遭到封鎖。 發生什麼情形？

在子網路上啟用 Azure Cosmos DB 的服務端點後，送到帳戶的流量來源就會從公用 IP 切換至虛擬網路和子網路。 如果您的 Azure Cosmos 帳戶只有 IP 型防火牆，來自已啟用服務之子網路的流量將不再符合 IP 防火牆規則，因此會遭到拒絕。 請執行步驟以順暢地從 IP 型防火牆移轉至虛擬網路型存取控制。

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>具有 VNET 服務端點的 Azure Cosmos 帳戶需要額外的 RBAC 權限嗎？

當您將 VNet 服務端點新增至 Azure Cosmos 帳戶之後，若要對帳戶設定進行任何變更，您需要針對 Azure Cosmos 帳戶上設定的所有 VNET 存取 `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` 動作。 這是必要權限，因為授權程序驗證對資源 (例如，資料庫和虛擬網路資源) 的存取，然後再評估任何屬性。
 
即使使用者未使用 Azure CLI 來指定 VNET ACL，授權還是會驗證 VNet 資源動作的權限。 目前，Azure Cosmos 帳戶的控制平面支援設定 Azure Cosmos 帳戶的完整狀態。 控制平面呼叫的其中一個參數是 `virtualNetworkRules`。 如果未指定此參數，Azure CLI 會進行 GET 資料庫呼叫以擷取 `virtualNetworkRules`，並在更新呼叫中使用此值。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>對等互連的虛擬網路也可以存取 Azure Cosmos 帳戶嗎？ 
只有新增至 Azure Cosmos 帳戶的虛擬網路和其子網路可以存取。 只有將對等互連之虛擬網路內的子網路新增到帳戶之後，其對等互連的 VNet 才能存取帳戶。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>允取存取單一 Cosmos 帳戶的子網路數目上限為何？ 
目前，您最多可以允許 256 個子網路存取一個 Azure Cosmos 帳戶。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>可以啟用來自 VPN 和 Express Route 的存取嗎？ 
若要透過 Express Route 從內部部署存取 Azure Cosmos 帳戶，您需要啟用 Microsoft 對等互連。 一旦制訂 IP 防火牆或虛擬網路存取規則，就可以新增公用 IP 位址 (用於您 Azure Cosmos 帳戶 IP 防火牆上的 Microsoft 對等互連)，以允許內部部署服務存取 Azure Cosmos 帳戶。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>我需要更新網路安全性群組 (NSG) 規則嗎？ 
NSG 規則可用來限制子網路和虛擬網路的往返連線。 當您將 Azure Cosmos DB 的服務端點新增至子網路時，不需要在 NSG 中針對您的 Azure Cosmos 帳戶開啟輸出連線。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>服務端點是否可供所有 VNet 使用？
否，只有 Azure Resource Manager 虛擬網路可以啟用服務端點。 傳統虛擬網路不支援服務端點。

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>啟用 Azure Cosmos DB 的服務端點存取時，我可以「接受來自公用 Azure 資料中心內的連線」嗎？  
只有當您需要讓其他 Azure 第一方服務 (例如，Azure Data Factory、Azure 認知搜尋) 或任何部署於指定 Azure 區域的服務存取 Azure Cosmos DB 帳戶時，此項目才是必要的。

## <a name="next-steps"></a>後續步驟

* 若要設定 Azure Cosmos DB 的防火牆，請參閱[防火牆支援](how-to-configure-firewall.md)一文。