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
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113514"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>設定 Azure 儲存體防火牆和虛擬網路

Azure 儲存體提供分層的安全性模型。 此模型使您能夠根據所使用的網路的類型和子集,保護和控制應用程式和企業環境需要的存儲帳戶的訪問級別。 設定網路規則後,只有通過指定網路集請求數據的應用程式才能存取儲存帳戶。 您可以將儲存帳戶的存取限制為來自指定 IP 位址、IP 範圍或 Azure 虛擬網路 (VNet) 中的子網清單的請求。

存儲帳戶具有可通過 Internet 訪問的公共終結點。 您還可以[為存儲帳戶創建專用終結點](storage-private-endpoints.md),該終結點將專用 IP 位址從 VNet 分配給儲存帳戶,並通過專用連結保護 VNet 和存儲帳戶之間的所有流量。 Azure 儲存防火牆為存儲帳戶的公共終結點提供訪問控制訪問許可權。 使用專用終結點時,還可以使用防火牆阻止通過公共終結點進行的所有訪問。 存儲防火牆配置還允許選擇受信任的 Azure 平台服務來安全地存取存儲帳戶。

在網路規則生效時存取存儲帳戶的應用程式仍然需要對請求進行適當的授權。 用於 Blob 和佇列的 Azure 活動目錄 (Azure AD) 認證、具有有效帳戶存取金鑰或 SAS 權杖的 Azure 活動目錄 (Azure AD) 認證授權。

> [!IMPORTANT]
> 預設情況下,打開儲存帳戶的防火牆規則會阻止傳入的數據請求,除非請求來自在 Azure 虛擬網路 (VNet) 中運行的服務或來自允許的公共 IP 位址。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。
>
> 通過允許來自託管服務實例的子網的流量,可以授予對在 VNet 中運行的 Azure 服務的許可權。 您還可以透過下面描述的[例外](#exceptions)機制啟用數量有限的方案。 要透過 Azure 入口從儲存帳戶存取資料,您需要位於您設定的受信任邊界(IP 或 VNet)內的電腦上。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>案例

為了保護存儲帳戶,應首先配置一條規則,以拒絕從公共終結點上的所有網路(包括 Internet 流量)訪問流量。 然後,應配置授予從特定 VNet 訪問流量的規則。 您還可以設定規則以授予從選定的公共 Internet IP 位址範圍存取流量的許可權,從而啟用來自特定 Internet 或本地用戶端的連接。 此設定可讓您為應用程式設定安全的網路界限。

可以組合允許從特定虛擬網路和同一存儲帳戶上的公共 IP 位址範圍進行訪問的防火牆規則。 存儲防火牆規則可以應用於現有存儲帳戶,也可以在創建新存儲帳戶時應用。

存儲防火牆規則適用於存儲帳戶的公共終結點。 您不需要任何防火牆訪問規則來允許存儲帳戶的專用終結點的流量。 批准創建專用終結點的過程允許隱式訪問來自承載專用終結點的子網的流量。

所有的 Azure 儲存體網路通訊協定都會執行網路規則，包括 REST 和 SMB。 要使用 Azure 門戶、儲存資源管理員和 AZCopy 等工具存取資料,必須配置顯式網路規則。

一旦套用網路規則，就會對所有要求執行。 授與特定 IP 位址存取權的 SAS 權杖，是用來限制權杖持有者的存取權，但不會授與所設定網路規則以外的新存取權。

虛擬機器磁碟流量 (包括掛接和取消掛接作業以及磁碟 IO) 不受網路規則影響。 分頁 Blob 的 REST存取受網路規則保護。

傳統的儲存體帳戶不支援防火牆與虛擬網路。

您可以藉由建立例外狀況，使用儲存體帳戶中的非受控磁碟與已套用的網路規則來備份和還原 VM。 此程序記載於本文的[例外狀況](#exceptions)一節。 防火牆例外不適用於受控磁碟，因為它們已受 Azure 管理。

## <a name="change-the-default-network-access-rule"></a>變更預設的網路存取規則

儲存體帳戶預設接受來自任何網路用戶端的連線。 若要限制對所選網路的存取，您必須先變更預設動作。

> [!WARNING]
> 變更網路規則會影響應用程式連接到 Azure 儲存體的能力。 將預設網路規則設置為**拒絕**阻止對數據的所有訪問,除非還應用了**授予**訪問許可權的特定網路規則。 請務必先將存取權授與任何使用網路規則的允許網路，再變更預設規則以拒絕存取。

### <a name="managing-default-network-access-rules"></a>管理預設的網路存取規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的預設網路存取規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 默認情況下,要拒絕訪問,請選擇允許從**選定網路**進行訪問。 要允許來自所有網路的流量,請選擇允許從**所有網路**進行訪問。

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

您可以將儲存帳戶配置為僅允許從特定子網進行訪問。 允許的子網可能屬於同一訂閱中的 VNet 或其他訂閱中的 VNet,包括屬於其他 Azure 活動目錄租戶的訂閱。

為 VNet 內的 Azure 儲存體啟用[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)。 服務終結點通過 Azure 儲存服務的最佳路徑路由流量從 VNet。 子網和虛擬網路的標識也會隨每個請求一起傳輸。 然後,管理員可以為存儲帳戶配置網路規則,允許從 VNet 中的特定子網接收請求。 透過這些網路規則授與存取的用戶端，必須仍要繼續符合儲存體帳戶的授權需求，才能存取資料。

每個儲存體帳戶最多可支援 100 個虛擬網路規則，它們也可以結合 [IP 網路規則](#grant-access-from-an-internet-ip-range)。

### <a name="available-virtual-network-regions"></a>可用的虛擬網路區域

一般情況下，服務端點是在虛擬網路與相同 Azure 區域的服務執行個體之間運作。 當搭配 Azure 儲存體使用服務端點時，此範圍會擴充以包含[配對的區域](/azure/best-practices-availability-paired-regions)。 服務端點可在地區性容錯移轉期間維持持續性，而且能讓您存取唯讀的異地備援儲存體 (RA-GRS) 執行個體。 將虛擬網路存取權授與儲存體帳戶的網路規則，也會將存取權授與任何 RA-GRS 執行個體。

規劃地區服務中斷期間的災害復原時，應該事先在配對區域中建立 VNet。 為 Azure 儲存體啟用服務端點，並設定網路規則，以允許從這些替代虛擬網路進行存取。 然後將這些規則套用至您的異地備援儲存體帳戶。

> [!NOTE]
> 服務端點不適用於虛擬網路區域外和指定配對區域外的流量。 允許從虛擬網路進行存取的網路規則，只能套用到儲存體帳戶主要區域中的儲存體帳戶或指定配對區域中的儲存體帳戶。

### <a name="required-permissions"></a>所需的權限

為將虛擬網路規則套用至儲存體帳戶，使用者必須在要新增的子網路上具有適當權限。 所需的權限為「將服務加入子網路」**，以及加入「儲存體帳戶參與者」** 內建角色。 這也可以新增至自訂角色定義。

存儲帳戶和授予訪問許可權的虛擬網路可能位於不同的訂閱中,包括屬於其他 Azure AD 租戶的訂閱。

> [!NOTE]
> 授予屬於其他 Azure 活動目錄租戶的虛擬網路中子網的訪問許可權的規則配置目前只能透過 Powershell、CLI 和 REST API 進行支援。 無法通過 Azure 門戶配置此類規則,儘管可以在門戶中查看這些規則。

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
    > 目前,在創建規則期間,僅顯示屬於同一 Azure 活動目錄租戶的虛擬網路才能選擇。 要授予對屬於其他租戶的虛擬網路中子網的訪問許可權,請使用 Powershell、CLI 或 REST API。

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
    > 要在屬於另一個 Azure AD 租戶的 VNet 中添加子網的網路規則,請使用"/訂閱/訂閱 ID/資源組/資源組/資源組名稱/提供者/Microsoft.Network/虛擬網路/vNet 名稱/子網/子網名稱"中的完全限定**的虛擬網路資源 Id**參數。

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
    > 要在屬於另一個 Azure AD 租戶的 VNet 中添加子網的規則,請\<使用"/\>訂閱/\<訂閱 ID/ 資源\>組/ 資源組/資源組名稱 /提供程式\</Microsoft.Network/虛擬網路/vNet 名稱\>/子網/\<子網名稱\>"中的完全限定子網 ID。
    >
    > 可以使用**訂閱**參數檢索屬於另一個 Azure AD 租戶的 VNet 的子網 ID。

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
  > 部署在與存儲帳戶相同的區域中的服務使用專用 Azure IP 位址進行通信。 因此,不能基於特定 Azure 服務的公共出站 IP 位址範圍限制對特定 Azure 服務的訪問。

儲存防火牆規則的配置僅支援 IPV4 位址。

每個儲存帳戶最多支援 100 個 IP 網路規則。

### <a name="configuring-access-from-on-premises-networks"></a>設定內部部署網路存取權

為將內部部署網路存取權授與使用 IP 網路規則的儲存體帳戶，您必須識別網路所使用的網際網路對應 IP 位址。 請連絡網路系統管理員尋求協助。

如果您使用來自內部部署的 [ExpressRoute](/azure/expressroute/expressroute-introduction) 進行公用對等互連或 Microsoft 對等互連，您將必須識別所使用的 NAT IP 位址。 在公用對等互連中，每個 Expressroute 線路預設都會使用兩個 NAT IP 位址，而這兩個位址會在流量進入 Microsoft Azure 網路骨幹時套用至 Azure 服務流量。 對於 Microsoft 對等互連,使用的 NAT IP 位址是客戶提供的,或者由服務提供者提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 深入了解 [ExpressRoute 公用與 Microsoft 對等互連的 NAT。](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>管理 IP 網路規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的 IP 網路規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 要授予對互聯網 IP 範圍的訪問許可權,請在**防火牆** > **位址範圍**下輸入 IP 位址或位址範圍(以 CIDR 格式)。

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

網路規則有助於為應用程式和數據之間的連接創建安全的環境,適用於大多數方案。 但是,某些應用程式依賴於無法透過虛擬網路或 IP 位址規則唯一隔離的 Azure 服務。 但是,必須將此類服務授予存儲,以實現完整的應用程式功能。 在這種情況下,您可以使用 ***「允許受信任的 Microsoft 服務...***

### <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服務

某些 Microsoft 服務使用網路規則中無法包括的網路進行操作。 您可以授予此類受信任 Microsoft 服務對儲存帳戶的子集,同時維護其他應用的網路規則。 然後,這些受信任的服務將使用強身份驗證安全地連接到存儲帳戶。 我們為Microsoft服務啟用了兩種受信任訪問模式。

- 某些服務的資源**在訂閱中註冊時**,可以造訪**同一訂閱中的**儲存帳戶,以便選擇操作,例如編寫日誌或備份。
- 某些服務的資源可以通過為其系統分配的託管標識**分配 RBAC 角色**來授予對存儲帳戶的顯式訪問許可權。


啟用 **"允許受信任的 Microsoft 服務..."** 設置時,在與存儲帳戶相同的訂閱中註冊的以下服務的資源將被授予對一組有限操作的訪問許可權,如下所述:

| 服務                  | 資源提供者名稱     | 允許的作業                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure 備份             | Microsoft.RecoveryServices | 在 IAAS 虛擬機器中執行未受控磁碟備份與還原。 (若為受控磁碟則非必要)。 [深入了解](/azure/backup/backup-introduction-to-azure-backup)。 |
| Azure 資料箱           | Microsoft.DataBox          | 使用數據框將資料導入 Azure。 [深入了解](/azure/databox/data-box-overview)。 |
| Azure DevTest Labs       | Microsoft.DevTestLab       | 自訂映像建立和成品安裝。 [深入了解](/azure/devtest-lab/devtest-lab-overview)。 |
| Azure Event Grid         | Microsoft.EventGrid        | 啟用 Blob 儲存體事件發佈，並允許事件方格發佈到儲存體佇列。 深入了解 [Blob 儲存體事件](/azure/event-grid/event-sources)及[發佈至佇列](/azure/event-grid/event-handlers)。 |
| Azure 事件中心         | Microsoft.EventHub         | 使用事件中樞擷取封存資料。 [瞭解更多](/azure/event-hubs/event-hubs-capture-overview)。 |
| Azure 檔案同步          | Microsoft.StorageSync      | 使您能夠將上置檔案伺服器轉換為 Azure 檔案共享的快取。 允許多站點同步、快速災難恢復和雲端備份。 [深入了解](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 為新的 HDInsight 群集預配預設檔案系統的初始內容。 [深入了解](/azure/hdinsight/hdinsight-hadoop-use-blob-storage)。 |
| Azure 匯入匯出      | Microsoft.ImportExport     | 啟用將資料匯入 Azure 並使用導入/匯出服務從 Azure 匯出數據。 [深入了解](/azure/storage/common/storage-import-export-service)。  |
| Azure 監視器            | Microsoft.Insights         | 允許將監視數據寫入安全存儲帳戶,包括資源診斷日誌、Azure 活動目錄登錄和審核日誌以及 Microsoft Intune 日誌。 [深入了解](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。 |
| Azure 網路         | Microsoft.Network          | 儲存及分析網路流量記錄。 [深入了解](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 使用啟用防火牆的緩存、源或目標存儲帳戶時,為 Azure IaaS 虛擬機器的災難恢復啟用複製。  [深入了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)。 |

如果顯式[將 RBAC 角色分配給](storage-auth-aad.md#assign-rbac-roles-for-access-rights)[資源實例的系統分配的託管標識](../../active-directory/managed-identities-azure-resources/overview.md),則「**允許受信任的 Microsoft 服務..."** 設定還允許以下服務的特定實例存取存儲帳戶。 在這種情況下,實例的訪問範圍對應於分配給託管標識的 RBAC 角色。

| 服務                        | 資源提供者名稱                 | 目的            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azue 認知搜尋         | Microsoft.Search/searchServices        | 使認知搜索服務能夠存取存儲帳戶以進行索引、處理和查詢。 |
| Azure Container Registry 工作 | Microsoft.ContainerRegistry/registries | ACR 任務可以在構建容器映射時存取儲存帳戶。 |
| Azure Data Factory             | Microsoft.DataFactory/factories        | 允許透過 ADF 執行時存取儲存帳戶。 |
| Azure Data Share               | 微軟.資料共享/帳戶           | 允許通過數據共享存取儲存帳戶。 |
| Azure Logic Apps               | Microsoft.Logic/workflows              | 使邏輯應用能夠存取儲存帳戶。 [深入了解](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity)。 |
| Azure Machine Learning 服務 | Microsoft.MachineLearningServices      | 授權的 Azure 機器學習工作區將實驗輸出、模型和日誌寫入 Blob 儲存並讀取數據。 [深入了解](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace)。 | 
| Azure SQL 資料倉儲       | Microsoft.Sql                          | 允許使用 PolyBase 從特定的 SQL 資料庫實例導入和匯出數據。 [深入了解](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)。 |
| Azure 串流分析         | Microsoft.StreamAnalytics             | 允許將流式處理作業中的數據寫入 Blob 存儲。 此功能目前為預覽狀態。 [深入了解](/azure/stream-analytics/blob-output-managed-identity)。 |
| Azure Synapse Analytics        | 微軟.Synapse/工作區          | 支援從 Synapse 分析存取 Azure 儲存中的數據。 |


### <a name="storage-analytics-data-access"></a>儲存體分析資料存取

在某些情況下，需要來自網路界限外的存取權才能讀取診斷記錄和計量。 配置對存儲帳戶的受信任服務訪問許可權時,可以允許日誌檔、指標表或兩者進行讀取訪問。 [深入了解儲存體分析的使用方式。](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>管理例外狀況

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理網路規則例外狀況。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路]**** 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 在 **"例外**"下,選擇要授予的異常。

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

詳細瞭解[服務終結點](/azure/virtual-network/virtual-network-service-endpoints-overview)中的 Azure 網路服務終結點。

在[Azure 存儲安全指南](../blobs/security-recommendations.md)中深入瞭解 Azure 儲存安全性。
