---
title: 關於 ExpressRoute 虛擬網路閘道 - Azure | Microsoft Docs
description: 了解 ExpressRoute 的虛擬網路閘道。 本文包含閘道 SKU 和類型的相關資訊。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: duau
ms.openlocfilehash: b721a9b8d8bdff3f3aaf05f15857c00347e7abb4
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202373"
---
# <a name="about-expressroute-virtual-network-gateways"></a>關於 ExpressRoute 虛擬網路閘道

若要透過 ExpressRoute 連接您的 Azure 虛擬網路和內部部署網路，您必須先建立虛擬網路閘道。 虛擬網路閘道有兩個用途：在網路之間交換 IP 路由，以及路由網路流量。 本文說明閘道類型、閘道 Sku，以及 SKU 的預估效能。 本文也說明 ExpressRoute [FastPath](#fastpath)，這項功能可讓您的內部部署網路中的網路流量略過虛擬網路閘道，以改善效能。

## <a name="gateway-types"></a>閘道類型

當您建立虛擬網路閘道時，您必須指定數項設定。 其中一個必要設定 '-GatewayType' 會指定是否要對 ExpressRoute 或 VPN 閘道流量使用閘道。 兩種閘道類型如下：

* **Vpn** - 若要透過公用網際網路傳送已加密的流量，請使用 'Vpn' 閘道類型。 也稱之為 VPN 閘道。 站對站、點對站和 VNet 對 VNet 連線都使用 VPN 閘道。

* **ExpressRoute** - 若要在私人連線上傳送網路流量，請使用 'ExpressRoute' 閘道類型。 這也稱為 ExpressRoute 閘道，而且是您設定 ExpressRoute 時所用的閘道類型。

對於每種閘道類型，每個虛擬網路只能有一個虛擬網路閘道。 例如，您可以有一個使用 -GatewayType Vpn 的虛擬網路閘道，以及一個使用 -GatewayType ExpressRoute 的虛擬網路閘道。

## <a name="gateway-skus"></a><a name="gwsku"></a>閘道 SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

如果您想要將閘道升級為更強大的閘道 SKU，在大部分情況下，您可以使用 Set-azvirtualnetworkgateway 的 PowerShell Cmdlet。 這適用於升級至 Standard 和 HighPerformance SKU。 不過，若要升級至 UltraPerformance SKU，您必須重新建立閘道器。 重新建立閘道時會導致停機。

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>閘道 SKU 預估的效能
下表顯示閘道類型和預估的效能。 此資料表適用於資源管理員與傳統部署模型。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> 應用程式效能取決於多項因素，例如端對端延遲以及應用程式開啟之流量的數目。 表格中的數字代表應用程式在理想的環境中，理論上可以達成的最高上限。
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>閘道子網路

建立 ExpressRoute 閘道之前，您必須先建立閘道子網。 閘道子網路包含虛擬網路閘道 VM 與服務所使用的 IP 位址。 當您建立虛擬網路閘道時，閘道 Vm 會部署至閘道子網，並使用必要的 ExpressRoute 閘道設定進行設定。 絕不部署任何其他 (例如，其他 Vm) 至閘道子網。 此閘道子網路必須命名為 'GatewaySubnet' 才能正常運作。 將閘道子網路命名為 'GatewaySubnet' 可讓 Azure 知道這是要用來部署虛擬網路閘道 VM 和服務的子網路。

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

當您建立閘道子網路時，您可指定子網路包含的 IP 位址數目。 閘道子網路中的 IP 位址會配置給閘道 VM 和閘道服務。 有些組態需要的 IP 位址比其他組態多。 

當您規劃閘道子網大小時，請參閱您打算建立的設定檔。 例如，ExpressRoute/VPN 閘道並存設定需要較大的閘道子網，而不是大部分的其他設定。 此外，您可能會想要確定閘道子網路包含足夠的 IP 位址，以因應未來可能的額外組態需求。 雖然您可以建立小至/29 的閘道子網，但建議您建立/27 或更大 (/27、/26 等閘道子網，) 是否有可用的位址空間。 這會容納大部分的設定。

下列 Resource Manager PowerShell 範例顯示名為 GatewaySubnet 的閘道子網路。 您可以看到 CIDR 標記法指定 /27，這可提供足以供大多數現有組態使用的 IP 位址。

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>區域備援閘道 SKU

您也可以在 Azure 可用性區域中部署 ExpressRoute 閘道。 這能夠在實際上和邏輯上將閘道分隔為不同的可用性區域，同時還能在發生區域層級的失敗時，保護內部部署項目與 Azure 的網路連線。

![區域備援 ExpressRoute 閘道](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

區域備援閘道會使用適用於 ExpressRoute 閘道的特定新式閘道 SKU。

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

新的閘道 SKU 也支援其他部署選項，以充分符合您的需求。 使用新的閘道 SKU 建立虛擬網路閘道時，您也可選擇在特定區域中部署閘道。 稱之為區域閘道。 當您部署分區閘道時，閘道的所有執行個體都會部署在相同的可用性區域中。

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute 虛擬網路閘道的設計目的是要切換式網路路由及路由網路流量。 FastPath 的設計目的是要改善您的內部部署網路與虛擬網路之間的資料路徑效能。 啟用時，FastPath 會將網路流量直接傳送到虛擬網路中的虛擬機器，略過閘道。

如需 FastPath 的詳細資訊，包括限制和需求，請參閱 [關於 FastPath](about-fastpath.md)。

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API 和 PowerShell Cmdlet
如需將 REST API 和 PowerShell Cmdlet 使用於虛擬網路閘道組態時的其他技術資源和特定語法需求，請參閱下列頁面︰

| **傳統** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>後續步驟

如需可用連接設定的詳細資訊，請參閱 [ExpressRoute 總覽](expressroute-introduction.md)。

如需建立 ExpressRoute 閘道的詳細資訊，請參閱 [建立 expressroute 的虛擬網路閘道](expressroute-howto-add-gateway-resource-manager.md)。

如需設定區域冗余閘道的詳細資訊，請參閱 [建立區域冗余的虛擬網路閘道](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)。

如需 FastPath 的詳細資訊，請參閱 [關於 FastPath](about-fastpath.md)。