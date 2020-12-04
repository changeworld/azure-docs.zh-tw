---
title: 為虛擬網路對等互連設定 VPN 閘道傳輸
description: 設定虛擬網路對等互連的閘道傳輸，以順暢地將兩個 Azure 虛擬網路連接到一個以供連線之用。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 2fc12385c78135269b6a73038fd0ad810ebaedd6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576150"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>為虛擬網路對等互連設定 VPN 閘道傳輸

本文將協助您為虛擬網路對等互連設定閘道傳輸。 [虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)可緊密連接兩個 Azure 虛擬網路，將兩個虛擬網路合併為一個以用於連線。 [閘道傳輸](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) 是一種對等互連屬性，可讓一個虛擬網路使用對等互連虛擬網路中的 VPN 閘道，來進行跨單位或 VNet 對 vnet 連線。 下圖顯示閘道傳輸搭配虛擬網路對等互連的運作方式。

![閘道傳輸圖表](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

在圖表中，閘道傳輸可讓對等的虛擬網路在 Hub-RM 中使用 Azure VPN 閘道。 VPN 閘道上可用的連線 (包括 S2S、P2S 和 VNet 對 VNet 連線) 皆適用於這三個虛擬網路。 傳輸選項可用於相同或不同部署模型之間的對等互連。 如果您要設定不同部署模型之間的傳輸，中樞虛擬網路和虛擬網路閘道必須位於 Resource Manager 部署模型中，而不是傳統部署模型。
>

在中樞與輪輻式 (hub-and-spoke) 網路架構中，閘道傳輸會讓輪輻虛擬網路共用中樞內的 VPN 閘道，而不是將 VPN 閘道部署在每個輪輻虛擬網路中。 通往閘道連線虛擬網路或內部部署網路的路由，將會傳播到使用閘道傳輸的對等虛擬網路路由表。 您可以從 VPN 閘道中停用自動路由傳播 建立具有「**停用 BGP 路由傳播**」選項的路由表，然後讓路由表與子網路產生關聯，以防止路由發佈至這些子網路。 如需詳細資訊，請參閱[虛擬網路由表](../virtual-network/manage-route-table.md)。

本文有兩種案例：

* **相同的部署模型**：兩個虛擬網路都是在 Resource Manager 部署模型中建立。
* **不同的部署模型**：輪輻虛擬網路是在傳統部署模型中建立，而中樞虛擬網路和閘道則位於 Resource Manager 部署模型中。

>[!NOTE]
> 如果您對網路拓撲進行變更並擁有 Windows VPN 用戶端，則必須重新下載並安裝 Windows 用戶端的 VPN 用戶端套件，才能將變更套用至用戶端。
>

## <a name="prerequisites"></a>先決條件

開始之前，請確認您有下列虛擬網路和許可權：

### <a name="virtual-networks"></a><a name="vnet"></a>虛擬網路

|VNet|部署模型| 虛擬網路閘道|
|---|---|---|---|
| Hub-RM| [Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| [是](tutorial-create-gateway-portal.md)|
| 輪輻-RM | [Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| 否 |
| Spoke-Classic | [傳統](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | 否 |

### <a name="permissions"></a><a name="permissions"></a>Permissions

您用於建立虛擬網路對等互連的帳戶必須具有必要角色或權限。 在下列範例中，如果您將兩個虛擬網路（名為 **Hub-RM** 和 **輪輻-傳統**）對等互連，則您的帳戶必須具有下列每個虛擬網路的角色或許可權：

|VNet|部署模型|角色|權限|
|---|---|---|---|
|Hub-RM|Resource Manager|[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |傳統|[傳統網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|Spoke-Classic|Resource Manager|[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||傳統|[傳統網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

深入了解[內建角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及如何對[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)指派特定權限 (僅限 Resource Manager)。

## <a name="same-deployment-model"></a><a name="same"></a>相同的部署模型

在此案例中，虛擬網路位於 Resource Manager 部署模型中。 使用下列步驟來建立或更新虛擬網路對等互連，以啟用閘道傳輸。

### <a name="to-add-a-peering-and-enable-transit"></a>新增對等互連並啟用傳輸

1. 在 [Azure 入口網站](https://portal.azure.com)中，建立或更新來自中樞-RM 的虛擬網路對等互連。 流覽至 **中樞-RM** 虛擬網路。 選取 [ **對等互連**]，然後按一下 [ **+ 新增** ] 開啟 [ **新增對等互連**]。
1. 在 [ **新增對等互連** ] 頁面上，設定 **此虛擬網路** 的值。

   * 對等互連連結名稱：為連結命名。 範例： **HubRMToSpokeRM**
   * 遠端虛擬網路的流量： **允許**
   * 從遠端虛擬網路轉送的流量： **允許**
   * 虛擬網路閘道： **使用此虛擬網路的閘道**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="螢幕擷取畫面顯示新增對等互連。":::

1. 在相同的頁面上，繼續設定 **遠端虛擬網路** 的值。

   * 對等互連連結名稱：為連結命名。 範例： **SpokeRMtoHubRM**
   * 部署模型： **Resource Manager**
   * 虛擬網路： **輪輻-RM**
   * 遠端虛擬網路的流量： **允許**
   * 從遠端虛擬網路轉送的流量： **允許**
   * 虛擬網路閘道： **使用遠端虛擬網路的閘道**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="螢幕擷取畫面顯示遠端虛擬網路的值。":::

1. 選取 [ **新增** ] 以建立對等互連。
1. 確認兩個虛擬網路上的對等互連狀態為 [ **已連線** ]。

### <a name="to-modify-an-existing-peering-for-transit"></a>修改現有的對等互連以進行傳輸

如果已建立對等互連，您可以修改傳送的對等互連。

1. 流覽至虛擬網路。 選取 [ **對等互連** ]，然後選取您想要修改的對等互連。

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="螢幕擷取畫面會顯示 select 對等互連。":::

1. 更新 VNet 對等互連。

   * 遠端虛擬網路的流量： **允許**
   * 轉送至虛擬網路的流量; **允許**
   * 虛擬網路閘道： **使用遠端虛擬網路的閘道**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="螢幕擷取畫面顯示 [修改對等互連閘道]。":::

1. **儲存** 對等互連設定。

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell 範例

您也可以使用 PowerShell 來建立或更新上述範例的對等互連。 使用您虛擬網路和資源群組的名稱取代變數。

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>不同的部署模型

在此設定中，輪輻 VNet **輪輻-傳統** 是在傳統部署模型中，而中樞 vnet **中樞-RM** 位於 Resource Manager 部署模型中。 設定部署模型之間的傳輸時，必須針對 Resource Manager VNet （而不是傳統 VNet）設定虛擬網路閘道。

針對此設定，您只需要設定 **中樞-RM** 虛擬網路。 您不需要在 **輪輻傳統** VNet 上進行任何設定。

1. 在 Azure 入口網站中，流覽至 **中樞-RM** 虛擬網路，選取 [ **對等互連**]，然後選取 [ **+ 新增**]。
1. 在 [ **新增對等互連** ] 頁面上，設定下列值：

   * 對等互連連結名稱：為連結命名。 範例： **HubRMToClassic**
   * 遠端虛擬網路的流量： **允許**
   * 從遠端虛擬網路轉送的流量： **允許**
   * 虛擬網路閘道： **使用此虛擬網路的閘道**
   * 遠端虛擬網路： **傳統**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="新增輪輻的對等互連頁面-傳統":::

1. 確認訂用帳戶正確無誤，然後從下拉式清單中選取虛擬網路。
1. 選取 [ **新增** ] 以新增對等互連。
1. 確認在中樞-RM 虛擬網路上 **連線** 的對等互連狀態。 

針對此設定，您不需要在 **輪輻傳統** 虛擬網路上設定任何設定。 當狀態顯示為 [ **已連線**] 時，輪輻虛擬網路可以透過中樞虛擬網路中的 VPN 閘道來使用連線能力。

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell 範例

您也可以使用 PowerShell 來建立或更新上述範例的對等互連。 使用您虛擬網路、資源群組和訂用帳戶的值，來取代變數和訂用帳戶識別碼。 您只需要在中樞虛擬網路上建立虛擬網路對等互連。

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>後續步驟

* 請先深入了解[虛擬網路對等互連的條件約束和行為](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)及[虛擬網路對等互連設定](../virtual-network/virtual-network-manage-peering.md#create-a-peering)，再建立虛擬網路對等互連以供生產環境使用。
* 了解如何透過虛擬網路對等互連和閘道傳輸來[建立中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)。
* [使用相同的部署模型建立虛擬網路對等互連](../virtual-network/tutorial-connect-virtual-networks-portal.md)。
* [使用不同的部署模型建立虛擬網路對等互連](../virtual-network/create-peering-different-deployment-models.md)。