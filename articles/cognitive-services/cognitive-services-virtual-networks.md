---
title: 虛擬網路
titleSuffix: Azure Cognitive Services
description: 為您的認知服務資源配置分層網路安全。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371217"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>配置 Azure 認知服務虛擬網路

Azure 認知服務提供分層安全模型。 此模型使您能夠將認知服務帳戶保護到特定的網路子集。 配置網路規則後，只有通過指定網路集請求資料的應用程式才能訪問該帳戶。 您可以通過要求篩選限制對資源的訪問。 僅允許來自指定 IP 位址、IP 範圍或[Azure 虛擬網路中](../virtual-network/virtual-networks-overview.md)子網清單的請求。 如果您對此產品感興趣，則需要[請求預覽訪問](https://aka.ms/cog-svc-vnet-signup)。

當網路規則生效時訪問認知服務資源的應用程式需要授權。 [Azure 活動目錄](../active-directory/fundamentals/active-directory-whatis.md)（Azure AD） 憑據或有效的 API 金鑰支援授權。

> [!IMPORTANT]
> 預設情況下，打開認知服務帳戶的防火牆規則會阻止傳入的資料請求。 為了允許請求通過，需要滿足以下條件之一：
> * 請求應源自目標認知服務帳戶的允許子網清單上的 Azure 虛擬網路 （VNet） 中的服務。 源自 VNet 的請求中的終結點需要設置為認知服務帳戶的[自訂子域](cognitive-services-custom-subdomains.md)。
> * 或者請求應源自允許的 IP 位址清單。
>
> 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>案例

要保護認知服務資源，應首先配置一條規則，以預設拒絕從所有網路（包括互聯網流量）訪問流量。 然後，應配置授予從特定 VNet 訪問流量的規則。 此設定可讓您為應用程式設定安全的網路界限。 您還可以配置規則以授予從選定的公共 Internet IP 位址範圍訪問流量的許可權，從而啟用來自特定 Internet 或本地用戶端的連接。

網路規則在 Azure 認知服務的所有網路通訊協定（包括 REST 和 WebSocket）上強制執行。 要使用 Azure 測試主控台等工具訪問資料，必須配置顯式網路規則。 您可以將網路規則應用於現有的認知服務資源，或者在創建新的認知服務資源時。 一旦套用網路規則，就會對所有要求執行。

## <a name="supported-regions-and-service-offerings"></a>支援的區域和服務產品

下面列出的認知服務的虛擬網路支援僅限於*美國中部歐盟、**美國中南部*、*美國東部*、*美國西部 2、**北歐*、*南非北部*、*西歐*、*印度中部*、*澳大利亞東部*、*美國西部*和*美國政府佛吉尼亞*Azure 地區。 如果此處未列出服務產品/服務，則不支援虛擬網路。

> [!div class="checklist"]
> * [異常偵測器](./anomaly-detector/index.yml)
> * [電腦視覺](./computer-vision/index.yml)
> * [內容檢閱者](./content-moderator/index.yml)
> * [自訂願景](./custom-vision-service/index.yml)
> * [臉部](./face/index.yml)
> * [表單辨識器](./form-recognizer/index.yml)
> * [路易士](./luis/index.yml)
> * [個人化器](./personalizer/index.yml)
> * [文本分析](./text-analytics/index.yml)
> * [QnA 製造商](./qnamaker/index.yml)

下面列出的認知服務的虛擬網路支援僅限於*美國中部歐盟、**美國中南部*、*美國東部*、*美國西部 2、**全球*和*美國政府佛吉尼亞*Azure 區域。
> [!div class="checklist"]
> * [翻譯文本](./translator/index.yml)

## <a name="service-tags"></a>服務標籤
除了支援上述服務的虛擬網路服務終結點外，認知服務還支援用於出站網路規則配置的服務標記。 以下服務包含在認知服務管理服務標記中。
> [!div class="checklist"]
> * [異常偵測器](./anomaly-detector/index.yml)
> * [電腦視覺](./computer-vision/index.yml)
> * [內容檢閱者](./content-moderator/index.yml)
> * [自訂願景](./custom-vision-service/index.yml)
> * [臉部](./face/index.yml)
> * [表單辨識器](./form-recognizer/index.yml)
> * [路易士](./luis/index.yml)
> * [個人化器](./personalizer/index.yml)
> * [文本分析](./text-analytics/index.yml)
> * [QnA 製造商](./qnamaker/index.yml)
> * [翻譯文本](./translator/index.yml)
> * [語音服務](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>變更預設的網路存取規則

預設情況下，認知服務資源接受來自任何網路上的用戶端的連接。 若要限制對所選網路的存取，您必須先變更預設動作。

> [!WARNING]
> 更改網路規則可能會影響應用程式連接到 Azure 認知服務的能力。 將預設網路規則設置為**拒絕**阻止對資料的所有訪問，除非還應用了**授予**存取權限的特定網路規則。 請務必先將存取權授與任何使用網路規則的允許網路，再變更預設規則以拒絕存取。 如果允許列出本地網路的 IP 位址，請確保從本地網路添加所有可能的傳出公共 IP 位址。

### <a name="managing-default-network-access-rules"></a>管理預設的網路存取規則

您可以通過 Azure 門戶、PowerShell 或 Azure CLI 管理認知服務資源的預設網路訪問規則。

# <a name="azure-portal"></a>[Azure 門戶](#tab/portal)

1. 轉到要保護的認知服務資源。

1. 選擇名為 **"虛擬網路**"**的資源管理**功能表。

   ![虛擬網路選項](media/vnet/virtual-network-blade.png)

1. 預設情況下，要拒絕訪問，請選擇允許從**選定網路**進行訪問。 單獨設置 **"選定網路**"，僅由配置的**虛擬網路**或**位址範圍**陪同 ，因此所有訪問都有效被拒絕。 當所有訪問被拒絕時，不允許嘗試使用認知服務資源的請求。 Azure 門戶、Azure PowerShell 或 Azure CLI 仍可用於配置認知服務資源。
1. 要允許來自所有網路的流量，請選擇允許從**所有網路**進行訪問。

   ![虛擬網路拒絕](media/vnet/virtual-network-deny.png)

1. 選取 [儲存]**** 以套用變更。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

1. 安裝[Azure PowerShell](/powershell/azure/install-az-ps)並[登錄](/powershell/azure/authenticate-azureps)，或選擇 **"試用"。**

1. 顯示認知服務資源的預設規則的狀態。

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

1. 安裝[Azure CLI](/cli/azure/install-azure-cli)並[登錄](/cli/azure/authenticate-azure-cli)，或選擇 **"試用"。**

1. 顯示認知服務資源的預設規則的狀態。

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

您可以將認知服務資源配置為僅允許從特定子網進行訪問。 允許的子網可能屬於同一訂閱中的 VNet 或其他訂閱，包括屬於其他 Azure 活動目錄租戶的訂閱。

在 VNet 中為 Azure 認知服務啟用[服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)。 服務終結點通過 Azure 認知服務服務的最佳路徑路由流量。 子網和虛擬網路的標識也會隨每個請求一起傳輸。 然後，管理員可以為認知服務資源配置網路規則，以便從 VNet 中的特定子網接收請求。 通過這些網路規則授予存取權限的用戶端必須繼續滿足認知服務資源訪問資料的授權要求。

每個認知服務資源最多支援 100 個虛擬網路規則，這些規則可與[IP 網路規則](#grant-access-from-an-internet-ip-range)結合使用。

### <a name="required-permissions"></a>所需的權限

要將虛擬網路規則應用於認知服務資源，使用者必須具有要添加的子網的適當許可權。 所需的許可權是預設*的"參與者"* 角色或*認知服務參與者*角色。 如果需要的許可權也可以添加到自訂角色定義中。

認知服務資源和授予存取權限的虛擬網路可能位於不同的訂閱中，包括屬於其他 Azure AD 租戶的訂閱。

> [!NOTE]
> 授予屬於其他 Azure 活動目錄租戶的虛擬網路中子網的存取權限的規則配置目前只能通過 Powershell、CLI 和 REST API 進行支援。 無法通過 Azure 門戶配置此類規則，儘管可以在門戶中查看這些規則。

### <a name="managing-virtual-network-rules"></a>管理虛擬網路規則

您可以通過 Azure 門戶、PowerShell 或 Azure CLI 管理認知服務資源的虛擬網路規則。

# <a name="azure-portal"></a>[Azure 門戶](#tab/portal)

1. 轉到要保護的認知服務資源。

1. 選擇名為 **"虛擬網路**"**的資源管理**功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 要授予使用現有網路規則訪問虛擬網路的許可權，請在**虛擬網路**下，選擇 **"添加現有虛擬網路**"。

   ![添加現有 vNet](media/vnet/virtual-network-add-existing.png)

1. 選擇**虛擬網路和****子網**選項，然後選擇**啟用**。

   ![添加現有 vNet 詳細資訊](media/vnet/virtual-network-add-existing-details.png)

1. 要創建新的虛擬網路並授予其存取權限，請選擇"**添加新虛擬網路**"。

   ![添加新 vNet](media/vnet/virtual-network-add-new.png)

1. 提供創建新虛擬網路所需的資訊，然後選擇 **"創建**"。

   ![創建 vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > 如果以前未為所選虛擬網路和子網配置 Azure 認知服務的服務終結點，則可以將其配置為此操作的一部分。
    >
    > 目前，在創建規則期間，僅顯示屬於同一 Azure 活動目錄租戶的虛擬網路才能選擇。 要授予對屬於其他租戶的虛擬網路中子網的存取權限，請使用 Powershell、CLI 或 REST API。

1. 要刪除虛擬網路或子網規則，請選擇 **..."** 以打開虛擬網路或子網的內容功能表，然後選擇 **"刪除**"。

   ![刪除 vNet](media/vnet/virtual-network-remove.png)

1. 選取 [儲存]**** 以套用變更。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

1. 安裝[Azure PowerShell](/powershell/azure/install-az-ps)並[登錄](/powershell/azure/authenticate-azureps)，或選擇 **"試用"。**

1. 列出虛擬網路規則。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 在現有虛擬網路和子網上為 Azure 認知服務啟用服務終結點。

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
    > 要在屬於另一個 Azure AD 租戶的 VNet 中添加子網的網路規則，請使用"/訂閱/訂閱 ID/資源組/資源組/資源組名稱/供應商/Microsoft.Network/虛擬網路/vNet 名稱/子網/子網名稱"中的完全限定**的虛擬網路資源 Id**參數。

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

1. 安裝[Azure CLI](/cli/azure/install-azure-cli)並[登錄](/cli/azure/authenticate-azure-cli)，或選擇 **"試用"。**

1. 列出虛擬網路規則。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 在現有虛擬網路和子網上為 Azure 認知服務啟用服務終結點。

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
    > 要在屬於其他 Azure AD 租戶的 VNet 中添加子網的規則，請使用"/訂閱/訂閱 ID/資源組/資源組/資源組名稱/提供程式/Microsoft.Network/虛擬網路/vNet 名稱/子網/子網名稱"中的完全限定子網 ID。
    > 
    > 可以使用**訂閱**參數檢索屬於另一個 Azure AD 租戶的 VNet 的子網 ID。

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

您可以配置認知服務資源以允許從特定的公共互聯網 IP 位址範圍進行訪問。 此配置允許訪問特定服務和本地網路，從而有效阻止一般互聯網流量。

使用表單`16.17.18.0/24`中的[CIDR 標記法](https://tools.ietf.org/html/rfc4632)或作為單個 IP 位址（如`16.17.18.19`）提供允許的互聯網位址範圍。

   > [!Tip]
   > 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 這些範圍應該使用個別的 IP 位址規則設定。

只有**公用網際網路** IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定義)。 私人網路絡`10.*`包括以 和`172.16.*` - `172.31.*`開頭的位址。 `192.168.*`

   > [!NOTE]
   > IP 網路規則對來自與認知服務資源相同的 Azure 區域的請求沒有影響。 使用[虛擬網路規則](#grant-access-from-a-virtual-network)來允許同一個區域的要求。

目前僅支援 IPV4 位址。 每個認知服務資源最多支援 100 個 IP 網路規則，這些規則可能與[虛擬網路規則](#grant-access-from-a-virtual-network)結合使用。

### <a name="configuring-access-from-on-premises-networks"></a>設定內部部署網路存取權

要使用 IP 網路規則從本地網路授予對認知服務資源的存取權限，必須標識網路使用的面向 Internet 的 IP 位址。 請連絡網路系統管理員尋求協助。

如果您正在本地使用[ExpressRoute](../expressroute/expressroute-introduction.md)進行公共對等互連或 Microsoft 對等互連，則需要標識 NAT IP 位址。 對於公共對等互連，預設情況下每個 ExpressRoute 電路使用兩個 NAT IP 位址。 當流量進入 Microsoft Azure 網路骨幹時，每個都應用於 Azure 服務流量。 對於 Microsoft 對等互連，使用的 NAT IP 位址是客戶提供的，或者由服務提供者提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 深入了解 [ExpressRoute 公用與 Microsoft 對等互連的 NAT。](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>管理 IP 網路規則

您可以通過 Azure 門戶、PowerShell 或 Azure CLI 管理認知服務資源的 IP 網路規則。

# <a name="azure-portal"></a>[Azure 門戶](#tab/portal)

1. 轉到要保護的認知服務資源。

1. 選擇名為 **"虛擬網路**"**的資源管理**功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 要授予對互聯網 IP 範圍的存取權限，請在**防火牆** > **位址範圍**下輸入 IP 位址或位址範圍[（CIDR 格式](https://tools.ietf.org/html/rfc4632)）。 僅接受有效的公共 IP（非保留）位址。

   ![添加 IP 範圍](media/vnet/virtual-network-add-ip-range.png)

1. 要刪除 IP 網路規則，請選擇位址範圍<span class="docon docon-delete x-hidden-focus"></span>旁邊的垃圾罐圖示。

   ![刪除 IP 範圍](media/vnet/virtual-network-delete-ip-range.png)

1. 選取 [儲存]**** 以套用變更。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

1. 安裝[Azure PowerShell](/powershell/azure/install-az-ps)並[登錄](/powershell/azure/authenticate-azureps)，或選擇 **"試用"。**

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

1. 安裝[Azure CLI](/cli/azure/install-azure-cli)並[登錄](/cli/azure/authenticate-azure-cli)，或選擇 **"試用"。**

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
* 瞭解有關[Azure 虛擬網路服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)的更多資訊