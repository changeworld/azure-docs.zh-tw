---
title: 設定 Azure 儲存體防火牆和虛擬網路 | Microsoft Docs
description: 為儲存體帳戶設定多層式的網路安全性。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 6f0e7c514835227fafd439803107531fbc62285c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133841"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>設定 Azure 儲存體防火牆和虛擬網路

Azure 儲存體提供分層的安全性模型。 此模型可讓您根據所使用的網路類型和子集，保護及控制您的應用程式和企業環境所需的儲存體帳戶存取層級。 設定網路規則時，只有透過一組指定網路要求資料的應用程式可以存取儲存體帳戶。 您可以限制從指定的 IP 位址、IP 範圍或 Azure 虛擬網路（VNet）中的子網清單，存取您儲存體帳戶的要求。

儲存體帳戶具有可透過網際網路存取的公用端點。 您也可以[為儲存體帳戶建立私人端點](storage-private-endpoints.md)，這會將私人 IP 位址從您的 vnet 指派給儲存體帳戶，並透過私人連結保護 vnet 與儲存體帳戶之間的所有流量。 Azure 儲存體防火牆會為儲存體帳戶的公用端點提供存取控制存取。 使用私用端點時，您也可以使用防火牆來封鎖所有透過公用端點的存取。 您的儲存體防火牆設定也可以讓選取受信任的 Azure 平臺服務安全地存取儲存體帳戶。

當網路規則生效時，存取儲存體帳戶的應用程式仍然需要適當的要求授權。 使用適用于 blob 和佇列的 Azure Active Directory （Azure AD）認證、具有有效的帳戶存取金鑰，或使用 SAS 權杖，即可支援授權。

> [!IMPORTANT]
> 開啟儲存體帳戶的防火牆規則時，預設會封鎖資料的傳入要求，除非要求來自于 Azure 虛擬網路（VNet）內的服務，或來自允許的公用 IP 位址。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。
>
> 您可以允許來自裝載服務實例之子網的流量，授與從 VNet 內運作的 Azure 服務的存取權。 您也可以透過下面所述的[例外](#exceptions)機制來啟用有限的案例數。 若要透過 Azure 入口網站從儲存體帳戶存取資料，您必須位於所設定之受信任界限（IP 或 VNet）內的電腦上。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>案例

若要保護您的儲存體帳戶，您應該先設定規則，以根據預設，拒絕存取公用端點上所有網路（包括網際網路流量）的流量。 然後，您應該設定規則，以授與來自特定 Vnet 之流量的存取權。 您也可以設定規則，以授與從選取的公用網際網路 IP 位址範圍存取流量的許可權，啟用來自特定網際網路或內部部署用戶端的連線。 此設定可讓您為應用程式設定安全的網路界限。

您可以結合防火牆規則，允許從特定的虛擬網路存取，以及從相同儲存體帳戶上的公用 IP 位址範圍進行存取。 儲存體防火牆規則可以套用至現有的儲存體帳戶，或在建立新的儲存體帳戶時套用。

儲存體防火牆規則適用于儲存體帳戶的公用端點。 您不需要任何防火牆存取規則來允許儲存體帳戶之私人端點的流量。 核准建立私用端點的程式，會授與裝載私用端點的子網之流量的隱含存取權。

所有的 Azure 儲存體網路通訊協定都會執行網路規則，包括 REST 和 SMB。 若要使用 Azure 入口網站、儲存體總管和 AZCopy 之類的工具來存取資料，必須設定明確的網路規則。

一旦套用網路規則，就會對所有要求執行。 授與特定 IP 位址存取權的 SAS 權杖，是用來限制權杖持有者的存取權，但不會授與所設定網路規則以外的新存取權。

虛擬機器磁碟流量 (包括掛接和取消掛接作業以及磁碟 IO) 不受網路規則影響。 分頁 Blob 的 REST存取受網路規則保護。

傳統的儲存體帳戶不支援防火牆與虛擬網路。

您可以藉由建立例外狀況，使用儲存體帳戶中的非受控磁碟與已套用的網路規則來備份和還原 VM。 此程序記載於本文的[例外狀況](#exceptions)一節。 防火牆例外不適用於受控磁碟，因為它們已受 Azure 管理。

## <a name="change-the-default-network-access-rule"></a>變更預設的網路存取規則

儲存體帳戶預設接受來自任何網路用戶端的連線。 若要限制對所選網路的存取，您必須先變更預設動作。

> [!WARNING]
> 變更網路規則會影響應用程式連接到 Azure 儲存體的能力。 將預設的網路規則設定為 [**拒絕**] 會封鎖對資料的所有存取，除非也套用**授**與存取權的特定網路規則。 請務必先將存取權授與任何使用網路規則的允許網路，再變更預設規則以拒絕存取。

### <a name="managing-default-network-access-rules"></a>管理預設的網路存取規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的預設網路存取規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 若要預設拒絕存取，請選擇允許從**選取的網路**進行存取。 若要允許來自所有網路的流量，請選擇允許來自**所有網路**的存取。

1. 按一下 [儲存]**** 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 顯示儲存體帳戶的預設規則狀態。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. 根據預設，將預設規則設定為拒絕網路存取。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. 根據預設，將預設規則設定為允許網路存取。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 顯示儲存體帳戶的預設規則狀態。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. 根據預設，將預設規則設定為拒絕網路存取。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. 根據預設，將預設規則設定為允許網路存取。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>授與虛擬網路存取權

您可以將儲存體帳戶設定為僅允許來自特定子網的存取。 允許的子網可能屬於相同訂用帳戶中的 VNet，或位於不同訂用帳戶中的 VNet，包括屬於不同 Azure Active Directory 租使用者的訂閱。

為 VNet 內的 Azure 儲存體啟用[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)。 服務端點會透過最佳路徑，將來自 VNet 的流量路由傳送至 Azure 儲存體服務。 子網和虛擬網路的身分識別也會隨每個要求傳送。 然後，系統管理員可以設定儲存體帳戶的網路規則，以允許從 VNet 中的特定子網接收要求。 透過這些網路規則授與存取的用戶端，必須仍要繼續符合儲存體帳戶的授權需求，才能存取資料。

每個儲存體帳戶最多可支援 100 個虛擬網路規則，它們也可以結合 [IP 網路規則](#grant-access-from-an-internet-ip-range)。

### <a name="available-virtual-network-regions"></a>可用的虛擬網路區域

一般情況下，服務端點是在虛擬網路與相同 Azure 區域的服務執行個體之間運作。 當搭配 Azure 儲存體使用服務端點時，此範圍會擴充以包含[配對的區域](/azure/best-practices-availability-paired-regions)。 服務端點可在地區性容錯移轉期間維持持續性，而且能讓您存取唯讀的異地備援儲存體 (RA-GRS) 執行個體。 將虛擬網路存取權授與儲存體帳戶的網路規則，也會將存取權授與任何 RA-GRS 執行個體。

規劃地區服務中斷期間的災害復原時，應該事先在配對區域中建立 VNet。 為 Azure 儲存體啟用服務端點，並設定網路規則，以允許從這些替代虛擬網路進行存取。 然後將這些規則套用至您的異地備援儲存體帳戶。

> [!NOTE]
> 服務端點不適用於虛擬網路區域外和指定配對區域外的流量。 允許從虛擬網路進行存取的網路規則，只能套用到儲存體帳戶主要區域中的儲存體帳戶或指定配對區域中的儲存體帳戶。

### <a name="required-permissions"></a>所需的權限

為將虛擬網路規則套用至儲存體帳戶，使用者必須在要新增的子網路上具有適當權限。 所需的權限為「將服務加入子網路」**，以及加入「儲存體帳戶參與者」** 內建角色。 這也可以新增至自訂角色定義。

儲存體帳戶和虛擬網路授與的存取權可能在不同的訂用帳戶中，包括屬於不同 Azure AD 租使用者的訂用帳戶。

> [!NOTE]
> 目前只有透過 Powershell、CLI 和 REST Api 才支援設定規則，以授與屬於不同 Azure Active Directory 租使用者之虛擬網路中子網的存取權。 這類規則無法透過 Azure 入口網站進行設定，但可以在入口網站中看到。

### <a name="managing-virtual-network-rules"></a>管理虛擬網路規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的虛擬網路規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 若要使用新網路規則將存取權授與虛擬網路，請按一下 [虛擬網路]**** 下的 [新增現有的虛擬網路]****，選取 [虛擬網路]**** 和 [子網路]**** 選項，然後按一下 [新增]****。 若要建立新的虛擬網路並授與存取權，請按一下 [新增新的虛擬網路]****。 提供建立新虛擬網路所需的資訊，並按一下 [建立]****。

    > [!NOTE]
    > 如果 Azure 儲存體的服務端點未針對所選虛擬網路和子網路事先設定，可以將其設定為這項作業的一部分。
    >
    > 目前，只有屬於相同 Azure Active Directory 租使用者的虛擬網路，才會在建立規則時顯示選取範圍。 若要授與屬於另一個租使用者之虛擬網路中子網的存取權，請使用 Powershell、CLI 或 REST Api。

1. 若要移除虛擬網路或子網路規則，請按一下 […]**** 開啟虛擬網路或子網路的快顯功能表，然後按一下 [移除]****。

1. 按一下 [儲存]**** 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 列出虛擬網路規則。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. 啟用現有虛擬網路和子網路上的 Azure 儲存體服務端點。

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. 為虛擬網路和子網路新增網路規則。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 若要為屬於另一個 Azure AD 租使用者之 VNet 中的子網新增網路規則，請使用 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 格式的完整**VirtualNetworkResourceId**參數。

1. 移除虛擬網路和子網路的網路規則。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則網路規則不會生效。

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 列出虛擬網路規則。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. 啟用現有虛擬網路和子網路上的 Azure 儲存體服務端點。

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. 為虛擬網路和子網路新增網路規則。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 若要在屬於另一個 Azure AD 租使用者的 VNet 中新增子網的規則，請使用完整的子網識別碼，格式為\<"/subscriptions/訂\>用\<帳戶識別碼/resourceGroups/\>resourceGroup\<-name/providers/Microsoft.Network/virtualNetworks/\>VNet\<-name/subnets/\>subnet-name"。
    >
    > 您可以使用**訂**用帳戶參數來抓取屬於另一個 Azure AD 租使用者之 VNet 的子網識別碼。

1. 移除虛擬網路和子網路的網路規則。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則網路規則不會生效。

## <a name="grant-access-from-an-internet-ip-range"></a>授與網際網路 IP 範圍存取權

您可以將儲存體帳戶設定為允許從特定公用網際網路 IP 位址範圍進行存取。 此設定可將存取權授與特定的網際網路型服務和內部部署網路，並且封鎖一般網際網路流量。

使用 [CIDR 標記法](https://tools.ietf.org/html/rfc4632)以 16.17.18.0/24** 的格式，或 16.17.18.19** 一類的個別 IP 位址，提供允許的網際網路位址範圍。

   > [!NOTE]
   > 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 這些範圍應該使用個別的 IP 位址規則設定。

只有**公用網際網路** IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定義)。 私人網路包括以 _10.*_、_172.16.*_ - _172.31.*_ 和 _192.168.*_ 開頭的位址。

   > [!NOTE]
   > IP 網路規則不會影響源自儲存體帳戶所在 Azure 區域的要求。 使用[虛擬網路規則](#grant-access-from-a-virtual-network)來允許同一個區域的要求。

  > [!NOTE]
  > 在與儲存體帳戶相同的區域中部署的服務會使用私人 Azure IP 位址進行通訊。 因此，您無法根據其公用輸出 IP 位址範圍來限制對特定 Azure 服務的存取。

儲存體防火牆規則的設定僅支援 IPV4 位址。

每個儲存體帳戶最多支援100個 IP 網路規則。

### <a name="configuring-access-from-on-premises-networks"></a>設定內部部署網路存取權

為將內部部署網路存取權授與使用 IP 網路規則的儲存體帳戶，您必須識別網路所使用的網際網路對應 IP 位址。 請連絡網路系統管理員尋求協助。

如果您使用來自內部部署的 [ExpressRoute](/azure/expressroute/expressroute-introduction) 進行公用對等互連或 Microsoft 對等互連，您將必須識別所使用的 NAT IP 位址。 在公用對等互連中，每個 Expressroute 線路預設都會使用兩個 NAT IP 位址，而這兩個位址會在流量進入 Microsoft Azure 網路骨幹時套用至 Azure 服務流量。 針對 Microsoft 對等互連，所使用的 NAT IP 位址是由客戶提供，或由服務提供者提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 深入了解 [ExpressRoute 公用與 Microsoft 對等互連的 NAT。](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>管理 IP 網路規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的 IP 網路規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 若要授與網際網路 IP 範圍的存取權，請在 [**防火牆** > **位址範圍**] 底下輸入 IP 位址或位址範圍（採用 CIDR 格式）。

1. 若要移除 IP 網路規則，請按一下位址範圍旁的垃圾桶圖示。

1. 按一下 [儲存]**** 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 列出 IP 網路規則。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. 新增個別 IP 位址的網路規則。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. 新增 IP 位址範圍的網路規則。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. 移除個別 IP 位址的網路規則。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. 移除 IP 位址範圍的網路規則。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則網路規則不會生效。

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 列出 IP 網路規則。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. 新增個別 IP 位址的網路規則。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. 新增 IP 位址範圍的網路規則。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. 移除個別 IP 位址的網路規則。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. 移除 IP 位址範圍的網路規則。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則網路規則不會生效。

## <a name="exceptions"></a>例外狀況

網路規則有助於建立安全的環境，以便在您的應用程式與資料之間，針對大部分案例進行連接。 不過，某些應用程式相依于無法透過虛擬網路或 IP 位址規則來唯一隔離的 Azure 服務。 但是，您必須將這類服務授與儲存體，才能啟用完整的應用程式功能。 在這種情況下，您可以使用 [***允許信任的 Microsoft 服務 ...*** ] 設定來啟用這類服務，以存取您的資料、記錄或分析。

### <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服務

某些 Microsoft 服務是從無法包含在網路規則中的網路進行操作。 您可以將這類受信任的 Microsoft 服務存取權授與儲存體帳戶，同時維護其他應用程式的網路規則。 這些信任的服務接著會使用增強式驗證來安全地連線到您的儲存體帳戶。 我們已為 Microsoft 服務啟用兩種受信任的存取模式。

- 某些服務的資源在**您的訂用帳戶中註冊時**，可以存取**相同訂**用帳戶中的儲存體帳戶來進行選取作業，例如寫入記錄或備份。
- 某些服務的資源可以藉由將**RBAC 角色指派**給其系統指派的受控識別，來授與對您儲存體帳戶的明確存取權。


當您啟用 [**允許受信任的 Microsoft 服務 ...** ] 設定時，在與儲存體帳戶相同的訂用帳戶中註冊的下列服務資源，會被授與存取權給一組有限的作業，如下所述：

| Service                  | 資源提供者名稱     | 允許的作業                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure 備份             | Microsoft.RecoveryServices | 在 IAAS 虛擬機器中執行未受控磁碟備份與還原。 (若為受控磁碟則非必要)。 [深入了解](/azure/backup/backup-introduction-to-azure-backup)。 |
| Azure 資料箱           | Microsoft.DataBox          | 使用資料箱，啟用將資料匯入至 Azure 的功能。 [深入了解](/azure/databox/data-box-overview)。 |
| Azure DevTest Labs       | Microsoft.DevTestLab       | 自訂映像建立和成品安裝。 [深入了解](/azure/devtest-lab/devtest-lab-overview)。 |
| Azure Event Grid         | Microsoft.EventGrid        | 啟用 Blob 儲存體事件發佈，並允許事件方格發佈到儲存體佇列。 深入了解 [Blob 儲存體事件](/azure/event-grid/event-sources)及[發佈至佇列](/azure/event-grid/event-handlers)。 |
| Azure 事件中心         | Microsoft.EventHub         | 使用事件中樞擷取封存資料。 [深入瞭解](/azure/event-hubs/event-hubs-capture-overview)。 |
| Azure 檔案同步          | Microsoft.StorageSync      | 可讓您將內部內部部署檔案伺服器轉換為 Azure 檔案共用的快取。 允許多網站同步處理、快速的嚴重損壞修復，以及雲端端備份。 [深入了解](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 為新的 HDInsight 叢集布建預設檔案系統的初始內容。 [深入了解](/azure/hdinsight/hdinsight-hadoop-use-blob-storage)。 |
| Azure 匯入匯出      | Microsoft.ImportExport     | 可讓您使用匯入/匯出服務將資料匯入 Azure，以及從 Azure 匯出資料。 [深入了解](/azure/storage/common/storage-import-export-service)。  |
| Azure 監視器            | Microsoft.Insights         | 允許將監視資料寫入受保護的儲存體帳戶，包括資源記錄、Azure Active Directory 登入和審核記錄，以及 Microsoft Intune 記錄。 [深入了解](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。 |
| Azure 網路         | Microsoft.Network          | 儲存及分析網路流量記錄。 [深入了解](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 當使用已啟用防火牆的快取、來源或目標儲存體帳戶時，啟用複寫以進行 Azure IaaS 虛擬機器的嚴重損壞修復。  [深入了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)。 |

如果您明確地[將 RBAC 角色指派](storage-auth-aad.md#assign-rbac-roles-for-access-rights)給該資源實例的[系統指派受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，[**允許受信任的 Microsoft 服務**] 設定也會允許下列服務的特定實例存取儲存體帳戶。 在此情況下，實例的存取範圍會對應至指派給受控識別的 RBAC 角色。

| Service                        | 資源提供者名稱                 | 目的            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azue 認知搜尋         | Microsoft.Search/searchServices        | 可讓認知搜尋服務存取儲存體帳戶以進行索引、處理和查詢。 |
| Azure Container Registry 工作 | Microsoft.ContainerRegistry/registries | 建立容器映射時，ACR 工作可以存取儲存體帳戶。 |
| Azure Data Factory             | Microsoft.DataFactory/factories        | 允許透過 ADF 執行時間存取儲存體帳戶。 |
| Azure Data Share               | DataShare/accounts           | 允許透過資料共用存取儲存體帳戶。 |
| Azure Logic Apps               | Microsoft.Logic/workflows              | 讓邏輯應用程式能夠存取儲存體帳戶。 [深入了解](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity)。 |
| Azure Machine Learning 服務 | Microsoft.MachineLearningServices      | 已授權的 Azure Machine Learning 工作區會將實驗輸出、模型和記錄寫入 Blob 儲存體，並讀取資料。 [深入了解](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace)。 | 
| Azure SQL 資料倉儲       | Microsoft.Sql                          | 允許使用 PolyBase 從特定的 SQL Database 實例匯入和匯出資料。 [深入了解](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)。 |
| Azure 串流分析         | Microsoft.StreamAnalytics             | 允許將串流作業中的資料寫入 Blob 儲存體。 這項功能目前為預覽狀態。 [深入了解](/azure/stream-analytics/blob-output-managed-identity)。 |
| Azure Synapse Analytics        | Synapse/工作區          | 可讓您從 Synapse 分析存取 Azure 儲存體中的資料。 |


### <a name="storage-analytics-data-access"></a>儲存體分析資料存取

在某些情況下，必須從網路界限外部存取讀取資源記錄和計量。 設定可信任的服務存取儲存體帳戶時，您可以允許記錄檔、計量資料表或兩者的讀取權限。 [深入了解儲存體分析的使用方式。](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>管理例外狀況

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理網路規則例外狀況。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 在 [**例外**狀況] 底下，選取您想要授與的例外狀況。

1. 按一下 [儲存]**** 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 顯示儲存體帳戶網路規則的例外狀況。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. 設定儲存體帳戶網路規則的例外狀況。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. 移除儲存體帳戶網路規則的例外狀況。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則移除例外狀況不會生效。

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 顯示儲存體帳戶網路規則的例外狀況。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. 設定儲存體帳戶網路規則的例外狀況。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. 移除儲存體帳戶網路規則的例外狀況。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則移除例外狀況不會生效。

## <a name="next-steps"></a>後續步驟

深入瞭解[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)中的 Azure 網路服務端點。

深入瞭解[Azure 儲存體安全性指南](../blobs/security-recommendations.md)中的 Azure 儲存體安全性。
