---
title: 虛擬網路
titleSuffix: Azure Cognitive Services
description: 為您的認知服務資源設定多層式網路安全性。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 96108053e6b68a71532d1cf25f8a352b3e0e5ca7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202082"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>設定 Azure 認知服務虛擬網路

Azure 認知服務提供多層式安全性模型。 此模型可讓您將認知服務帳戶保護到特定的網路子集。 設定網路規則時，只有透過一組指定網路要求資料的應用程式才能存取該帳戶。 您可以使用要求篩選來限制資源的存取權。 只允許來自指定 IP 位址、IP 範圍或[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)中子網清單的要求。 如果您對此供應專案感興趣，則需要[要求預覽存取權](https://aka.ms/cog-svc-vnet-signup)。

當網路規則生效時，存取認知服務資源的應用程式需要授權。 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) （Azure AD）認證或有效的 API 金鑰支援授權。

> [!IMPORTANT]
> 開啟認知服務帳戶的防火牆規則預設會封鎖傳入的資料要求。 為了允許要求通過，必須符合下列其中一個條件：
> * 此要求應來自目標認知服務帳戶的 [允許的子網] 清單中，在 Azure 虛擬網路（VNet）內運作的服務。 來自 VNet 的要求中的端點必須設定為您認知服務帳戶的[自訂子域](cognitive-services-custom-subdomains.md)。
> * 或者，要求應該來自允許的 IP 位址清單。
>
> 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>案例

若要保護您的認知服務資源，您應該先設定規則，以拒絕從所有網路（包括網際網路流量）存取流量的預設值。 然後，您應該設定規則，以授與來自特定 Vnet 之流量的存取權。 此設定可讓您為應用程式設定安全的網路界限。 您也可以設定規則，以授與從選取的公用網際網路 IP 位址範圍存取流量的許可權，啟用來自特定網際網路或內部部署用戶端的連線。

所有網路通訊協定的網路規則都會強制執行至 Azure 認知服務，包括 REST 和 WebSocket。 若要使用 Azure 測試主控台之類的工具來存取資料，必須設定明確的網路規則。 您可以將網路規則套用到現有的認知服務資源，或在建立新的認知服務資源時套用。 一旦套用網路規則，就會對所有要求執行。

## <a name="supported-regions-and-service-offerings"></a>支援的區域和服務供應專案

以下所列認知服務的虛擬網路支援僅限於*美國中部 EUAP*、美國*中南部*、美國*東部*、*美國西部 2*、*歐洲北部*、*南非北部*、*西歐*、*印度中部*、*澳大利亞東部*、*美國西部*和*US Gov 維吉尼亞州*Azure 區域。 如果此處未列出服務供應專案，則不支援虛擬網路。

> [!div class="checklist"]
> * [異常偵測器](./anomaly-detector/index.yml)
> * [電腦視覺](./computer-vision/index.yml)
> * [內容仲裁](./content-moderator/index.yml)
> * [自訂視覺](./custom-vision-service/index.yml)
> * [臉部](./face/index.yml)
> * [表單辨識器](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [個人化工具](./personalizer/index.yml)
> * [文字分析](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

以下所列認知服務的虛擬網路支援僅限於*美國中部 EUAP*、美國*中南部*、*美國東部*、*美國西部 2*、*全球*和*US Gov 維吉尼亞州*Azure 區域。
> [!div class="checklist"]
> * [翻譯文字](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#virtual-network-support)

## <a name="service-tags"></a>服務標籤
除了支援上述服務的虛擬網路服務端點之外，認知服務也支援輸出網路規則設定的服務標記。 下列服務會包含在 CognitiveServicesManagement 服務標記中。
> [!div class="checklist"]
> * [異常偵測器](./anomaly-detector/index.yml)
> * [電腦視覺](./computer-vision/index.yml)
> * [內容仲裁](./content-moderator/index.yml)
> * [自訂視覺](./custom-vision-service/index.yml)
> * [臉部](./face/index.yml)
> * [表單辨識器](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [個人化工具](./personalizer/index.yml)
> * [文字分析](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [翻譯文字](./translator/index.yml)
> * [語音服務](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>變更預設的網路存取規則

根據預設，認知服務資源會接受來自任何網路上用戶端的連接。 若要限制對所選網路的存取，您必須先變更預設動作。

> [!WARNING]
> 變更網路規則可能會影響您的應用程式連線到 Azure 認知服務的能力。 將預設的網路規則設定為 [**拒絕**] 會封鎖對資料的所有存取，除非也套用**授**與存取權的特定網路規則。 請務必先將存取權授與任何使用網路規則的允許網路，再變更預設規則以拒絕存取。 如果您允許列出內部部署網路的 IP 位址，請務必從您的內部部署網路新增所有可能的連出公用 IP 位址。

### <a name="managing-default-network-access-rules"></a>管理預設的網路存取規則

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來管理認知服務資源的預設網路存取規則。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 移至您想要保護的認知服務資源。

1. 選取名為 [**虛擬網路**] 的 [**資源管理**] 功能表。

   ![虛擬網路選項](media/vnet/virtual-network-blade.png)

1. 若要預設拒絕存取，請選擇允許從**選取的網路**進行存取。 使用 [**選取的網路**] 設定，依設定的**虛擬網路**或**位址範圍**來 unaccompanied-所有存取都會有效拒絕。 當所有存取都遭到拒絕時，不允許嘗試使用認知服務資源的要求。 Azure 入口網站、Azure PowerShell 或 Azure CLI 仍然可以用來設定認知服務資源。
1. 若要允許來自所有網路的流量，請選擇允許來自**所有網路**的存取。

   ![虛擬網路拒絕](media/vnet/virtual-network-deny.png)

1. 選取 [儲存]**** 以套用變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安裝[Azure PowerShell](/powershell/azure/install-az-ps)並登[入](/powershell/azure/authenticate-azureps)，或選取 [**試試看**]。

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

1. 安裝[Azure CLI](/cli/azure/install-azure-cli)並登[入](/cli/azure/authenticate-azure-cli)，或選取 [**試試看**]。

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

***

## <a name="grant-access-from-a-virtual-network"></a>授與虛擬網路存取權

您可以設定認知服務資源，以只允許來自特定子網的存取。 允許的子網可能屬於相同訂用帳戶或不同訂用帳戶中的 VNet，包括屬於不同 Azure Active Directory 租使用者的訂閱。

在 VNet 內啟用 Azure 認知服務的[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 服務端點會透過 Azure 認知服務服務的最佳路徑，將流量路由傳送至 VNet。 子網和虛擬網路的身分識別也會隨每個要求傳送。 然後，系統管理員可以設定認知服務資源的網路規則，以允許從 VNet 中的特定子網接收要求。 透過這些網路規則授與存取權的用戶端，必須繼續符合認知服務資源的授權需求，才能存取資料。

每個認知服務資源最多支援100個虛擬網路規則，可能會結合[IP 網路規則](#grant-access-from-an-internet-ip-range)。

### <a name="required-permissions"></a>所需的權限

若要將虛擬網路規則套用至認知服務資源，使用者必須擁有所要新增之子網的適當許可權。 必要許可權是「預設*參與者*」角色，或「*認知服務參與者*」角色。 您也可以將必要的許可權新增至自訂角色定義。

認知服務資源和已授與存取權的虛擬網路可能位於不同的訂用帳戶中，包括屬於不同 Azure AD 租使用者的訂用帳戶。

> [!NOTE]
> 目前只有透過 Powershell、CLI 和 REST Api 才支援設定規則，以授與屬於不同 Azure Active Directory 租使用者之虛擬網路中子網的存取權。 這類規則無法透過 Azure 入口網站進行設定，但可以在入口網站中看到。

### <a name="managing-virtual-network-rules"></a>管理虛擬網路規則

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來管理認知服務資源的虛擬網路規則。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 移至您想要保護的認知服務資源。

1. 選取名為 [**虛擬網路**] 的 [**資源管理**] 功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 若要使用現有的網路規則授與虛擬網路的存取權，請在 [**虛擬網路**] 下選取 [**新增現有的虛擬網路**]。

   ![新增現有的 vNet](media/vnet/virtual-network-add-existing.png)

1. 選取 [**虛擬網路**] 和 [**子網**] 選項，然後選取 [**啟用**]。

   ![新增現有的 vNet 詳細資料](media/vnet/virtual-network-add-existing-details.png)

1. 若要建立新的虛擬網路並將存取權授與它，請選取 [**新增虛擬網路**]。

   ![加入新的 vNet](media/vnet/virtual-network-add-new.png)

1. 提供建立新虛擬網路所需的資訊，然後選取 [**建立**]。

   ![建立 vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > 如果先前未針對所選虛擬網路和子網設定 Azure 認知服務的服務端點，您可以將它設定為這項作業的一部分。
    >
    > 目前，只有屬於相同 Azure Active Directory 租使用者的虛擬網路，才會在建立規則時顯示選取範圍。 若要授與屬於另一個租使用者之虛擬網路中子網的存取權，請使用 Powershell、CLI 或 REST Api。

1. 若要移除虛擬網路或子網規則，請選取 [ **...** ] 開啟虛擬網路或子網的內容功能表，然後選取 [**移除**]。

   ![移除 vNet](media/vnet/virtual-network-remove.png)

1. 選取 [儲存]**** 以套用變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安裝[Azure PowerShell](/powershell/azure/install-az-ps)並登[入](/powershell/azure/authenticate-azureps)，或選取 [**試試看**]。

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
    > 若要為屬於另一個 Azure AD 租使用者之 VNet 中的子網新增網路規則，請使用 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 格式的完整**VirtualNetworkResourceId**參數。

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

1. 安裝[Azure CLI](/cli/azure/install-azure-cli)並登[入](/cli/azure/authenticate-azure-cli)，或選取 [**試試看**]。

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
    > 若要在屬於另一個 Azure AD 租使用者的 VNet 中新增子網的規則，請使用 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 格式的完整子網識別碼。
    > 
    > 您可以使用**訂**用帳戶參數來抓取屬於另一個 Azure AD 租使用者之 VNet 的子網識別碼。

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
***

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則網路規則不會生效。

## <a name="grant-access-from-an-internet-ip-range"></a>授與網際網路 IP 範圍存取權

您可以設定認知服務資源，以允許來自特定公用網際網路 IP 位址範圍的存取。 此設定會授與特定服務和內部部署網路的存取權，並有效地封鎖一般網際網路流量。

請在表單中使用[CIDR 標記法](https://tools.ietf.org/html/rfc4632) `16.17.18.0/24` 或個別 IP 位址（例如），以提供允許的網際網路位址範圍 `16.17.18.19` 。

   > [!Tip]
   > 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 這些範圍應該使用個別的 IP 位址規則設定。

只有**公用網際網路** IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定義)。 私人網路包括以、和開頭的位址 `10.*` `172.16.*`  -  `172.31.*` `192.168.*` 。

   > [!NOTE]
   > IP 網路規則不會影響源自相同 Azure 區域做為認知服務資源的要求。 使用[虛擬網路規則](#grant-access-from-a-virtual-network)來允許同一個區域的要求。

目前僅支援 IPV4 位址。 每個認知服務資源最多支援100個 IP 網路規則，這可能會與[虛擬網路規則](#grant-access-from-a-virtual-network)結合。

### <a name="configuring-access-from-on-premises-networks"></a>設定內部部署網路存取權

若要使用 IP 網路規則，將內部部署網路存取權授與認知服務資源，您必須識別網路所使用的網際網路對應 IP 位址。 請連絡網路系統管理員尋求協助。

如果您使用[ExpressRoute](../expressroute/expressroute-introduction.md)內部部署來進行公用對等互連或 Microsoft 對等互連，您將需要識別 NAT IP 位址。 針對公用對等互連，每個 ExpressRoute 線路預設都會使用兩個 NAT IP 位址。 當流量進入 Microsoft Azure 網路骨幹時，每個都會套用至 Azure 服務流量。 針對 Microsoft 對等互連，所使用的 NAT IP 位址是由客戶提供，或由服務提供者提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 深入了解 [ExpressRoute 公用與 Microsoft 對等互連的 NAT。](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>管理 IP 網路規則

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來管理認知服務資源的 IP 網路規則。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 移至您想要保護的認知服務資源。

1. 選取名為 [**虛擬網路**] 的 [**資源管理**] 功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 若要授與網際網路 IP 範圍的存取權，請在 [**防火牆**位址範圍] 底下輸入 IP 位址或位址範圍（採用[CIDR 格式](https://tools.ietf.org/html/rfc4632)）  >  ** **。 只接受有效的公用 IP （非保留）位址。

   ![新增 IP 範圍](media/vnet/virtual-network-add-ip-range.png)

1. 若要移除 IP 網路規則，請選取 <span class="docon docon-delete x-hidden-focus"></span> 位址範圍旁的垃圾桶圖示。

   ![刪除 IP 範圍](media/vnet/virtual-network-delete-ip-range.png)

1. 選取 [儲存]**** 以套用變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安裝[Azure PowerShell](/powershell/azure/install-az-ps)並登[入](/powershell/azure/authenticate-azureps)，或選取 [**試試看**]。

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

1. 安裝[Azure CLI](/cli/azure/install-azure-cli)並登[入](/cli/azure/authenticate-azure-cli)，或選取 [**試試看**]。

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

***

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]****，否則網路規則不會生效。

## <a name="next-steps"></a>後續步驟

* 探索各種[Azure 認知服務](welcome.md)
* 深入瞭解[Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)