---
title: 快速入門：使用 ExpressRoute 建立和修改線路-Azure PowerShell
description: 建立、佈建、驗證、更新、刪除和取消佈建 ExpressRoute 線路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: b5ac53c44429e23e2d22a934a9dc71bd485ec4cd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761901"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立和修改 ExpressRoute 線路

本快速入門說明如何使用 PowerShell Cmdlet 和 Azure Resource Manager 部署模型來建立 ExpressRoute 線路。 您也可以檢查狀態、更新、刪除或取消佈建線路。

## <a name="prerequisites"></a>必要條件

* 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。
* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已在本機安裝 Azure PowerShell 或 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>建立和佈建 ExpressRoute 線路
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>取得支援的提供者、位置和頻寬清單
建立 ExpressRoute 線路之前，您需要有支援的連線提供者、位置和頻寬選項的清單。

PowerShell Cmdlet **AzExpressRouteServiceProvider** 會傳回這項資訊，您將在稍後的步驟中使用這些資訊：

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

請檢查是否列出您的連線服務提供者。 記下下列資訊，稍後當您建立線路時將會用到：

* Name
* PeeringLocations
* BandwidthsOffered

您現在已經準備好建立 ExpressRoute 線路。

### <a name="create-an-expressroute-circuit"></a>建立 ExpressRoute 線路
若您還沒有資源群組，您必須在建立 ExpressRoute 線路之前建立一個。 您可以執行下列命令來這麼做：

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

以下示範如何透過矽谷的 Equinix 建立 200-Mbps ExpressRoute 線路。 如果您要使用不同的提供者和不同的設定，請在提出要求時取代該資訊。 使用下列範例來要求新服務金鑰：

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

請確定您指定正確的 SKU 層和 SKU 系列：

* SKU 層會決定 ExpressRoute 線路為 [本機](expressroute-faqs.md#expressroute-local)、標準或 [Premium](expressroute-faqs.md#expressroute-premium)。 您可以指定 *Local*、* Standard 或 *Premium*。 您無法將 SKU 從 *Standard/Premium* 變更為 *Local*。
* SKU 系列決定計費類型。 您可以針對計量付費資料方案指定 *[metereddata* ，並針對無限制的資料方案指定 *UnlimitedData* 。 您可以將計費類型從 *[metereddata* 變更為 *UnlimitedData*，但無法將類型從 *UnlimitedData* 變更為 *[metereddata*。 *本機*電路一律會*UnlimitedData*。

> [!IMPORTANT]
> ExpressRoute 線路會從發出服務金鑰時開始收費。 請確定在連線提供者準備好佈建線路之後，再執行這項作業。
>

回應包含服務金鑰。 您可以執行下列命令來取得所有參數的詳細描述：

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>列出所有 ExpressRoute 循環
若要取得您已建立之所有 ExpressRoute 線路的清單，請執行 **AzExpressRouteCircuit** 命令：

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

回應大致如下列範例所示：

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

您隨時可以使用 `Get-AzExpressRouteCircuit` Cmdlet 擷取此資訊。 執行呼叫時，若未指定任何參數，將會列出所有線路。 [服務金鑰]** 欄位會列出您的服務金鑰：

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

回應大致如下列範例所示：

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>將服務金鑰傳送給連線提供者以進行佈建
*ServiceProviderProvisioningState* 會提供服務提供者端目前的布建狀態相關資訊。 [狀態] 會提供 Microsoft 端的狀態。 如需線路佈建狀態的詳細資訊，請參閱[工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states)。

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

當連線提供者目前為您啟用線路時，線路會變更為下列狀態：

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

若要使用 ExpressRoute 線路，它必須處於下列狀態：

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>定期檢查線路金鑰的情況和狀態
當您的提供者布建線路時，檢查服務金鑰的狀態和狀態將可讓您知道。 設定線路之後，[ServiceProviderProvisioningState]** 會顯示為 [Provisioned]**，如下列範例所示：

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

回應大致如下列範例所示：

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="create-your-routing-configuration"></a>建立路由組態
如需逐步指示，請參閱 [ExpressRoute 線路路由組態](expressroute-howto-routing-arm.md) 一文以建立和修改線路對等。

> [!IMPORTANT]
> 這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者是提供受控第 3 層服務 (通常是 IP VPN，如 MPLS)，您的連線提供者會為您設定和管理路由。
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>將虛擬網路連結到 ExpressRoute 線路
接下來，將虛擬網路連結到 ExpressRoute 線路。 當使用 Resource Manager 部署模型時，使用[將虛擬網路連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)文章。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>取得 ExpressRoute 線路的狀態
您可以使用 **AzExpressRouteCircuit** 指令程式，隨時取得此資訊。 執行呼叫時，若未指定任何參數，將會列出所有線路。

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

回應如下列範例所示：

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

您可以透過將資源群組名稱和線路名稱做為參數傳遞至呼叫，取得特定 ExpressRoute 線路的資訊：

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

回應大致如下列範例所示：

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                        "Name": "Standard_MeteredData",
                                        "Tier": "Standard",
                                        "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

您可以執行下列命令來取得所有參數的詳細描述：

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>修改 ExpressRoute 線路
您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。

您可以執行下列工作，而無需中途停機：

* 啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。 不支援將 SKU 從 *Standard/Premium* 變更為 *Local* 。
* 如果埠上有可用的容量，請增加 ExpressRoute 線路的頻寬。 不支援將線路的頻寬降級。
* 將計量方案從 [計量付費] 變更為 [無限制]。 不支援將計量方案從 [無限制資料] 變更為 [計量付費資料]。
* 您可以啟用和停用 [允許傳統作業] **。

如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

### <a name="to-enable-the-expressroute-premium-add-on"></a>啟用 ExpressRoute 進階附加元件
您可以使用下列 PowerShell 程式碼片段，為現有的線路啟用 ExpressRoute 進階附加元件：

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

線路現在會啟用 ExpressRoute 進階附加功能。 順利執行命令之後，我們就會立即開始針對進階附加元件功能計費。

### <a name="to-disable-the-expressroute-premium-add-on"></a>停用 ExpressRoute 進階附加元件
> [!IMPORTANT]
> 如果您使用的資源超出標準線路所允許的數量，這項作業可能會失敗。
>

請注意下列資訊︰

* 從進階降級為標準之前，您必須確定連結至線路的虛擬網路數目小於 10。 如果您不這樣做，更新要求就會失敗，且我們會以進階費率計費。
* 其他地緣政治區域中的所有虛擬網路都必須先取消連結。 如果您未移除此連結，更新要求將會失敗，並會繼續以 premium 費率計費。
* 就私用對等設定而言，路由表必須少於 4000 個路由。 如果您的路由表大小大於4000路由，BGP 會話將會下降。 在通告的首碼數目低於4000時，不會重新啟用 BGP 會話。

您可以使用下列 PowerShell Cmdlet，為現有的線路停用 ExpressRoute 進階附加元件：

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>更新 ExpressRoute 線路頻寬
請查閱 [ExpressRoute 常見問題集](expressroute-faqs.md)，以取得提供者支援的頻寬選項。 您可以挑選任何比現有線路規模還大的大小。

> [!IMPORTANT]
> 如果現有的連接埠上沒有足夠的容量，您可能必須重新建立 ExpressRoute 線路。 如果該位置已無額外的容量，您無法升級線路。
>
> 降低 ExpressRoute 線路的頻寬時必須中斷運作。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。
>

一旦決定需要的大小後，可以使用下列命令來重新調整線路大小。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

您的線路將在 Microsoft 端升級。 接下來，您必須連絡連線提供者，將他們的設定更新為符合這項變更。 完成此通知之後，我們會開始以更新的頻寬選項計費。

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>將 SKU 從計量移動為無限
您可以使用下列 PowerShell 程式碼片段變更 ExpressRoute 循環的 SKU：

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>控制對傳統和 Resource Manager 環境的存取
請檢閱 [將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型](expressroute-howto-move-arm.md)中的指示。

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>解除布建 ExpressRoute 線路
請注意下列資訊︰

* 所有虛擬網路都必須從 ExpressRoute 線路取消連結。 如果此作業失敗，請確認是否有任何虛擬網路連結至循環。
* 如果 ExpressRoute 線路服務提供者佈建狀態為 **Provisioning** 或 **Provisioned**，您就必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們會繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。
* 如果服務提供者已取消布建線路，表示服務提供者布建狀態設定為 [ **未布建**]，您可以刪除線路。 線路的計費將會停止。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清除資源

您可以執行下列命令來刪除 ExpressRoute 線路：

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>後續步驟

當您建立線路並布建到您的提供者之後，請繼續進行下一個步驟來設定對等互連：

> [!div class="nextstepaction"]
> [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-arm.md)
