---
title: 虛擬網路
titleSuffix: Azure Cognitive Services
description: 為您的認知服務資源設定多層式網路安全性。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: aahi
ms.openlocfilehash: f79cfce514b81c5829ee7791c18e24d3bc6563b5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369370"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>設定 Azure 認知服務虛擬網路

Azure 認知服務提供多層式安全性模型。 此模型可讓您將認知服務帳戶固定在特定的網路子集。 設定網路規則時，只有透過一組指定網路要求資料的應用程式才能存取帳戶。 您可以使用要求篩選來限制資源的存取權。 只允許來自指定 IP 位址、IP 範圍或 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)中子網清單的要求。

在網路規則生效時存取認知服務資源的應用程式需要授權。 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 認證或使用有效的 API 金鑰來支援授權。

> [!IMPORTANT]
> 開啟認知服務帳戶的防火牆規則時，預設會封鎖資料的傳入要求。 為了允許要求通過，必須符合下列其中一個條件：

> * 要求應源自于 Azure 虛擬網路內的服務， (VNet) 在目標認知服務帳戶的允許子網清單上。 要求中來自 VNet 的端點必須設定為您認知服務帳戶的 [自訂子域](cognitive-services-custom-subdomains.md) 。
> * 或者，要求應源自允許的 IP 位址清單。
>
> 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>案例

若要保護您的認知服務資源，您應該先設定規則以拒絕存取來自所有網路的流量， (包括) 預設的網際網路流量。 然後，您應該設定規則，以授與特定 Vnet 中流量的存取權。 此設定可讓您為應用程式設定安全的網路界限。 您也可以設定規則，授與存取所選取公用網際網路 IP 位址範圍中流量的權限，這會啟用來自特定網際網路或內部部署用戶端的連線。

系統會將所有網路通訊協定上的網路規則強制套用至 Azure 認知服務，包括 REST 和 WebSocket。 若要使用 Azure 測試主控台之類的工具來存取資料，必須設定明確的網路規則。 您可以將網路規則套用至現有的認知服務資源，或建立新的認知服務資源。 一旦套用網路規則，就會對所有要求執行。

## <a name="supported-regions-and-service-offerings"></a>支援的區域和服務供應專案

[可使用認知服務的區域](https://azure.microsoft.com/global-infrastructure/services/)支援虛擬網路 (vnet) 。 認知服務支援網路規則設定的服務標記。 以下列出的服務包含在 **CognitiveServicesManagement** 服務標記中。

> [!div class="checklist"]
> * 異常偵測器
> * 電腦視覺
> * 內容仲裁
> * 自訂視覺
> * 臉部
> * 表單辨識器
> * 語言理解 (LUIS)
> * 個人化工具
> * 文字分析
> * QnA Maker
> * 翻譯工具文字
> * 沈浸式閱讀程式

> [!NOTE]
> 如果您使用的是 LUIS， **CognitiveServicesManagement** 標記只會讓您使用 SDK 或 REST API 來使用服務。 若要從虛擬網路存取和使用 LUIS 入口網站，您將需要使用下列標記：  
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**
> * **AzureActiveDirectory**
> * **AzureFrontDoor.Frontend**


## <a name="change-the-default-network-access-rule"></a>變更預設的網路存取規則

根據預設，認知服務資源會接受來自任何網路上用戶端的連線。 若要限制對所選網路的存取權，您必須先變更預設動作。

> [!WARNING]
> 變更網路規則會影響您的應用程式連線到 Azure 認知服務的能力。 將預設的網路規則設定為 **拒絕** 以封鎖所有的資料存取，除非也套用了 **授與** 存取權的特定網路規則。 請務必先將存取權授與任何使用網路規則的允許網路，再變更預設規則以拒絕存取。 如果您允許列出內部部署網路的 IP 位址，請務必從您的內部部署網路新增所有可能的連出公用 IP 位址。

### <a name="managing-default-network-access-rules"></a>管理預設的網路存取規則

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來管理認知服務資源的預設網路存取規則。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 移至您想要保護的認知服務資源。

1. 選取名為 [ **虛擬網路** ] 的 **資源管理** 功能表。

   ![虛擬網路選項](media/vnet/virtual-network-blade.png)

1. 若要預設拒絕存取，請選擇允許 **所選網路** 存取權。 只要 **選取 [網路** ] 設定，unaccompanied 由設定的 **虛擬網路** 或 **位址範圍** ，就會有效地拒絕所有的存取。 當拒絕所有存取時，就不允許嘗試取用認知服務資源的要求。 您仍然可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來設定認知服務資源。
1. 若要允許來自所有網路的流量，請選擇允許 **所有網路** 存取權。

   ![虛擬網路拒絕](media/vnet/virtual-network-deny.png)

1. 選取 [儲存] 來套用您的變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安裝 [Azure PowerShell](/powershell/azure/install-az-ps) 並登 [入](/powershell/azure/authenticate-azureps)，或選取 [ **試試看** ]。

1. 顯示認知服務資源的預設規則狀態。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. 根據預設，將預設規則設定為拒絕網路存取。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. 根據預設，將預設規則設定為允許網路存取。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並登 [入](/cli/azure/authenticate-azure-cli)，或選取 [ **試試看** ]。

1. 顯示認知服務資源的預設規則狀態。

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. 根據預設，將預設規則設定為拒絕網路存取。

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. 根據預設，將預設規則設定為允許網路存取。

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

**_

## <a name="grant-access-from-a-virtual-network"></a>授與虛擬網路存取權

您可以將認知服務資源設定為僅允許來自特定子網的存取。 允許的子網可能屬於相同訂用帳戶或不同訂用帳戶中的 VNet，包括屬於不同 Azure Active Directory 租使用者的訂用帳戶。

在 VNet 內啟用 Azure 認知服務的 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md) 。 服務端點會透過 Azure 認知服務服務的最佳路徑，將來自 VNet 的流量路由傳送至 Azure 認知服務。 子網路和虛擬網路的身分識別也會隨著每個要求傳輸。 系統管理員接著可以設定認知服務資源的網路規則，以允許從 VNet 中的特定子網接收要求。 透過這些網路規則授與存取權的用戶端，必須繼續符合認知服務資源的授權需求，才能存取資料。

每個認知服務資源最多可支援100個虛擬網路規則，這些規則可能會與 [IP 網路規則](#grant-access-from-an-internet-ip-range)結合。

### <a name="required-permissions"></a>所需的權限

若要將虛擬網路規則套用至認知服務資源，使用者必須擁有所要新增之子網的適當許可權。 必要的許可權是預設的 _Contributor * 角色或 *認知服務參與者* 角色。 您也可以將必要的許可權新增至自訂角色定義。

認知服務資源和授與存取權的虛擬網路可能在不同的訂用帳戶中，包括屬於不同 Azure AD 租使用者的訂用帳戶。

> [!NOTE]
> 目前只透過 Powershell、CLI 和 REST API 支援設定規則，以授與權限存取虛擬網路中的子網路，而這些虛擬網路屬於不同的 Azure Active Directory 租用戶。 這類規則無法透過 Azure 入口網站進行設定，但可以在入口網站中看到。

### <a name="managing-virtual-network-rules"></a>管理虛擬網路規則

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來管理認知服務資源的虛擬網路規則。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 移至您想要保護的認知服務資源。

1. 選取名為 [ **虛擬網路** ] 的 **資源管理** 功能表。

1. 請確定您已選取允許從 **所選網路** 進行存取。

1. 若要授與具有現有網路規則之虛擬網路的存取權，請選取 [ **虛擬網路** ] 下的 [ **新增現有的虛擬網路** ]。

   ![新增現有的 vNet](media/vnet/virtual-network-add-existing.png)

1. 選取 [ **虛擬網路** ] 和 [ **子網** ] 選項，然後選取 [ **啟用** ]。

   ![新增現有的 vNet 詳細資料](media/vnet/virtual-network-add-existing-details.png)

1. 若要建立新的虛擬網路並授與存取權，請選取 [ **新增虛擬網路** ]。

   ![新增 vNet](media/vnet/virtual-network-add-new.png)

1. 提供建立新虛擬網路所需的資訊，然後選取 [ **建立** ]。

   ![建立 vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > 如果 Azure 認知服務的服務端點先前尚未針對選取的虛擬網路和子網進行設定，您可以將它設定為這項操作的一部分。
    >
    > 目前，只有屬於相同 Azure Active Directory 租用戶的虛擬網路，才會在建立規則時顯示以供選取。 當虛擬網路屬於另一個租用戶時，若要授與權限存取其中的子網路，請使用 Powershell、CLI 或 REST Api。

1. 若要移除虛擬網路或子網規則，請選取 [ **...** ] 開啟虛擬網路或子網的內容功能表，然後選取 [ **移除** ]。

   ![移除 vNet](media/vnet/virtual-network-remove.png)

1. 選取 [儲存] 來套用您的變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安裝 [Azure PowerShell](/powershell/azure/install-az-ps) 並登 [入](/powershell/azure/authenticate-azureps)，或選取 [ **試試看** ]。

1. 列出虛擬網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 在現有的虛擬網路和子網上啟用 Azure 認知服務的服務端點。

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. 為虛擬網路和子網路新增網路規則。

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > 當 VNet 屬於另一個另一個 Azure AD 租用戶時，若要為其中子網路新增網路規則，請使用完整的 **VirtualNetworkResourceId** 參數，格式為 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name"。

1. 移除虛擬網路和子網路的網路規則。

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並登 [入](/cli/azure/authenticate-azure-cli)，或選取 [ **試試看** ]。

1. 列出虛擬網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 在現有的虛擬網路和子網上啟用 Azure 認知服務的服務端點。

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. 為虛擬網路和子網路新增網路規則。

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > 當 VNet 屬於另一個 Azure AD 租用戶時，若要為其中子網路新增規則，請使用完整的子網路識別碼，格式為 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name"。
    > 
    > 您可以使用 **subscription** 參數，為屬於另一個 Azure AD 租用戶的 VNet 擷取子網路識別碼。

1. 移除虛擬網路和子網路的網路規則。

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

**_

> [!IMPORTANT]
> 請務必 [將預設規則設](#change-the-default-network-access-rule) 為 _ * deny * *，否則網路規則不會有任何作用。

## <a name="grant-access-from-an-internet-ip-range"></a>授與網際網路 IP 範圍存取權

您可以設定認知服務資源，以允許來自特定公用網際網路 IP 位址範圍的存取。 此設定會授與特定服務和內部部署網路的存取權，有效地封鎖一般網際網路流量。

使用格式的 [CIDR 標記法](https://tools.ietf.org/html/rfc4632) `16.17.18.0/24` 或個別的 IP 位址（例如），以提供允許的網際網路位址範圍 `16.17.18.19` 。

   > [!Tip]
   > 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 這些範圍應該使用個別的 IP 位址規則設定。

只有 **公用網際網路** IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定義)。 私人網路包括開頭為 `10.*` 、和的位址 `172.16.*`  -  `172.31.*` `192.168.*` 。

   > [!NOTE]
   > IP 網路規則不會影響源自于與認知服務資源相同 Azure 區域的要求。 使用[虛擬網路規則](#grant-access-from-a-virtual-network)來允許同一個區域的要求。

目前僅支援 IPV4 位址。 每個認知服務資源最多可支援100個 IP 網路規則，這些規則可能會與 [虛擬網路規則](#grant-access-from-a-virtual-network)結合。

### <a name="configuring-access-from-on-premises-networks"></a>設定內部部署網路存取權

若要將您的內部部署網路存取權授與具有 IP 網路規則的認知服務資源，您必須識別網路所使用的網際網路面向 IP 位址。 請連絡網路系統管理員尋求協助。

如果您在內部部署使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 進行公用對等互連或 Microsoft 對等互連，您將需要識別 NAT IP 位址。 針對公用對等互連，每個 ExpressRoute 線路預設都會使用兩個 NAT IP 位址。 當流量進入 Microsoft Azure 網路骨幹時，每個都會套用至 Azure 服務流量。 針對 Microsoft 對等互連，使用的 NAT IP 位址是由客戶提供，或由服務提供者提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 深入了解 [ExpressRoute 公用與 Microsoft 對等互連的 NAT。](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>管理 IP 網路規則

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來管理認知服務資源的 IP 網路規則。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 移至您想要保護的認知服務資源。

1. 選取名為 [ **虛擬網路** ] 的 **資源管理** 功能表。

1. 請確定您已選取允許從 **所選網路** 進行存取。

1. 若要授與網際網路 IP 範圍的存取權，請在 [ **防火牆** 位址範圍] 下，輸入 (為 [CIDR 格式](https://tools.ietf.org/html/rfc4632)) 的 IP 位址或位址範圍  >  **** 。 只接受有效的公用 IP (非保留) 位址。

   ![新增 IP 範圍](media/vnet/virtual-network-add-ip-range.png)

1. 若要移除 IP 網路規則，請選取 <span class="docon docon-delete x-hidden-focus"></span> 位址範圍旁的垃圾桶圖示。

   ![刪除 IP 範圍](media/vnet/virtual-network-delete-ip-range.png)

1. 選取 [儲存] 來套用您的變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安裝 [Azure PowerShell](/powershell/azure/install-az-ps) 並登 [入](/powershell/azure/authenticate-azureps)，或選取 [ **試試看** ]。

1. 列出 IP 網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. 新增個別 IP 位址的網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 新增 IP 位址範圍的網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 移除個別 IP 位址的網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 移除 IP 位址範圍的網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並登 [入](/cli/azure/authenticate-azure-cli)，或選取 [ **試試看** ]。

1. 列出 IP 網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. 新增個別 IP 位址的網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. 新增 IP 位址範圍的網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. 移除個別 IP 位址的網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. 移除 IP 位址範圍的網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

**_

> [!IMPORTANT]
> 請務必 [將預設規則設](#change-the-default-network-access-rule) 為 _ * deny * *，否則網路規則不會有任何作用。

## <a name="use-private-endpoints"></a>使用私人端點

您可以使用認知服務資源的 [私人端點](../private-link/private-endpoint-overview.md) ，讓虛擬網路上的用戶端 (VNet) 安全地透過 [Private Link](../private-link/private-link-overview.md)存取資料。 私人端點會使用您認知服務資源的 VNet 位址空間中的 IP 位址。 VNet 上的用戶端與資源之間的網路流量會在 Microsoft 骨幹網路上進行 VNet 和私人連結，以消除公用網際網路的洩漏。

認知服務資源的私人端點可讓您：

* 藉由設定防火牆來封鎖認知服務服務的公用端點上的所有連線，以保護您的認知服務資源。
* 藉由讓您封鎖 VNet 中的資料遭到外泄，來提高 VNet 的安全性。
* 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [expressroute](../expressroute/expressroute-locations.md) 搭配私人對等互連，安全地從連線到 VNet 的內部部署網路連線到認知服務資源。

### <a name="conceptual-overview"></a>概念概觀

私人端點是您 [VNet](../virtual-network/virtual-networks-overview.md)中 Azure 資源的特殊網路介面。 為您的認知服務資源建立私人端點，可在 VNet 中的用戶端與您的資源之間提供安全的連線能力。 私人端點會從 VNet 的 IP 位址範圍中指派 IP 位址。 私人端點與認知服務服務之間的連接會使用安全的私用連結。

VNet 中的應用程式可以順暢地透過私人端點連接到服務，並使用它們將使用的相同連接字串和授權機制。 例外狀況是語音服務，需要個別的端點。 請參閱 [使用語音服務的私人端點](#private-endpoints-with-the-speech-service)上的一節。 私人端點可與認知服務資源所支援的所有通訊協定搭配使用，包括 REST。

私人端點可以在使用 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)的子網中建立。 子網中的用戶端可以使用私人端點連接至一個認知服務資源，同時使用服務端點來存取其他服務。

當您為 VNet 中的認知服務資源建立私人端點時，會傳送同意要求以供認知服務資源擁有者核准。 如果要求建立私人端點的使用者也是資源的擁有者，則會自動核准此同意要求。

認知服務資源擁有者可以透過 [Azure 入口網站](https://portal.azure.com)中認知服務資源的 [ *私人端點* ] 索引標籤，來管理同意要求和私人端點。

### <a name="private-endpoints"></a>私人端點

建立私人端點時，您必須指定它所連接的認知服務資源。 如需建立私人端點的詳細資訊，請參閱：

* [使用 Azure 入口網站中的 Private Link 中心建立私人端點](../private-link/create-private-endpoint-portal.md)
* [使用 Azure CLI 建立私人端點](../private-link/create-private-endpoint-cli.md)
* [使用 Azure PowerShell 建立私人端點](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>連接至私人端點

使用私人端點的 VNet 上的用戶端，應該針對認知服務資源使用相同的連接字串，作為連接至公用端點的用戶端。 例外狀況是語音服務，需要個別的端點。 請參閱 [使用語音服務的私人端點](#private-endpoints-with-the-speech-service)上的一節。 我們依賴 DNS 解析，以透過私人連結，自動將 VNet 的連線路由至認知服務資源。 語音服務 

根據預設，我們會建立附加至 VNet 的 [私人 DNS 區域](../dns/private-dns-overview.md) ，並具有私人端點的必要更新。 但是，如果您使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行額外的變更。 下列 [DNS 變更](#dns-changes-for-private-endpoints) 一節說明私人端點所需的更新。

### <a name="private-endpoints-with-the-speech-service"></a>具有語音服務的私人端點

使用私人端點搭配語音服務時，您必須使用自訂端點來呼叫語音服務。 您無法使用全域端點。 端點必須遵循此模式： `{account}.{stt|tts|voice|dls}.speech.microsoft.com` 。

### <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立私人端點時，認知服務資源的 DNS CNAME 資源記錄會更新為首碼為 ' *privatelink* ' 的子域中的別名。 根據預設，我們也會建立對應至 ' *privatelink* ' 子域的 [私人 dns 區域](../dns/private-dns-overview.md)，並以 DNS 為私人端點的資源記錄。

當您從 VNet 外部將端點 URL 解析為私人端點時，它會解析為認知服務資源的公用端點。 從裝載私人端點的 VNet 解析時，端點 URL 會解析為私人端點的 IP 位址。

這種方法可讓您在 VNet 中裝載私人端點和用戶端的 VNet 中，針對用戶端使用相同的連接字串來存取認知服務資源。

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將認知服務資源端點 (FQDN) 的完整功能變數名稱解析為私人端點 IP 位址。 設定您的 DNS 伺服器，以將私人連結子域委派給 VNet 的私人 DNS 區域。

> [!TIP]
> 使用自訂或內部部署 DNS 伺服器時，您應該將 DNS 伺服器設定為將 ' privatelink ' 子域中的認知服務資源名稱解析為私人端點 IP 位址。 若要這麼做，您可以將 ' privatelink ' 子域委派給 VNet 的私人 DNS 區域，或在 DNS 伺服器上設定 DNS 區域，然後新增 DNS A 記錄。

如需有關設定您自己的 DNS 伺服器以支援私人端點的詳細資訊，請參閱下列文章：

* [Azure 虛擬網路中的資源名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [私人端點的 DNS 設定](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>後續步驟

* 探索各種 [Azure 認知服務](./what-are-cognitive-services.md)
* 深入瞭解 [Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)