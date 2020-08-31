---
title: 關於 Azure VPN 閘道
description: 了解什麼是 VPN 閘道，以及如何使用 VPN 閘道連線到 IPsec IKE 站對站、VNet 對 VNet 和點對站 VPN 虛擬網路。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/25/2020
ms.author: cherylmc
ms.openlocfilehash: c7fbea977904145aa2e8851f45a4b70f9ce0c560
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855601"
---
# <a name="what-is-vpn-gateway"></a>什麼是 VPN 閘道？

VPN 閘道是特定的虛擬網路閘道類型，可透過公用網際網路在 Azure 虛擬網路與內部部署位置之間傳送加密流量。 您也可以使用 VPN 閘道，透過 Microsoft 網路來傳送 Azure 虛擬網路之間的已加密流量。 每個虛擬網路只能有一個 VPN 閘道。 不過，您可以對相同的 VPN 閘道建立多個連線。 當您對相同的 VPN 閘道建立多個連線時，所有 VPN 通道都會共用可用的閘道頻寬。

您可以在 Azure 可用性區域中部署 VPN 閘道。 此方式可為虛擬網路閘道帶來復原力、延展性和更高的可用性。 在 Azure 可用性區域中部署閘道可從根本上和邏輯上分隔區域內的閘道，同時還能在發生區域層級的失敗時，保護您內部部署項目與 Azure 的網路連線。 請參閱[關於在 Azure 可用性區域中的區域備援虛擬網路閘道](about-zone-redundant-vnet-gateways.md) (機器翻譯)。

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>什麼是虛擬網路閘道？

虛擬網路閘道是由部署到特定子網路 (由您所建並稱為「閘道子網路」) 的兩部或多部 VM 所組成。 虛擬網路閘道 VM 包含路由表，並且會執行特定的閘道服務。 當您建立虛擬網路閘道時，這些 VM 也會隨之建立。 您無法直接設定屬於虛擬網路閘道的 VM。

當您設定虛擬網路閘道時，可以進行指定閘道類型的設定。 閘道類型會決定使用虛擬網路閘道的方式，以及閘道所採取的動作。 閘道類型 'Vpn' 會指定所建立虛擬網路閘道的類型是「VPN 閘道」。 這與使用不同閘道類型的 ExpressRoute 閘道不同。 虛擬網路可以有兩個虛擬網路閘道；一個是 VPN 閘道和一個 ExpressRoute 閘道。 如需詳細資訊，請參閱[閘道類型](vpn-gateway-about-vpn-gateway-settings.md#gwtype)。

建立虛擬網路閘道最多可能需要花費 45 分鐘的時間來完成。 建立虛擬網路閘道時，閘道 VM 會部署到閘道子網路，並使用您指定的設定進行設定。 建立 VPN 閘道之後，您可以在 VPN 閘道與另一個 VPN 閘道 (VNet 對 VNet) 之間建立 IPsec/IKE VPN 通道連線，或在 VPN 閘道與內部部署 VPN 裝置 (站對站) 之間建立跨單位 IPsec/IKE VPN 通道連線。 您也可以建立點對站 VPN 連線 (透過 OpenVPN、IKEv2 或 SSTP 的 VPN)，它可讓您從遠端位置連線到您的虛擬網路，例如從會議或住家。

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>設定 VPN 閘道

VPN 閘道連線需仰賴多個具有特定設定的資源。 大部分的資源可以分別進行設定，雖然必須以特定順序設定某些資源。

### <a name="design-connection-topology-diagrams"></a><a name="diagrams"></a>設計：連線拓撲圖表

請務必知道 VPN 閘道連線有不同的組態可用。 您必須決定哪個組態最符合您的需求。 例如，點對站、站對站及共存的 ExpressRoute/站對站連線，都有不同的指示與設定需求。 如需設計和連線拓撲圖表的相關資訊，請參閱[設計](design.md)。

### <a name="settings"></a><a name="settings"></a>設定

您為每個資源選擇的設定，對於建立成功連線而言極為重要。 如需 VPN 閘道個別資源和設定的資訊，請參閱 [關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md)。 本文包含的資訊可協助您了解閘道類型、閘道 SKU、VPN 類型、連線類型、閘道子網路、區域網路閘道，以及您需要考量的各種其他資源設定。

### <a name="deployment-tools"></a><a name="tools"></a>部署工具

您可以使用設定工具 (例如 Azure 入口網站) 開始建立及設定資源。 您可以稍後再決定切換到另一個工具 (如 PowerShell) 來設定其他資源，或是在適用的時機修改現有資源。 您目前無法在 Azure 入口網站中進行每一項資源和資源設定。 文章中各連線拓撲的指示會指定何時需要特定組態工具。

### <a name="planning-table"></a><a name="planningtable"></a>規劃表

下表可以協助您為您的解決方案決定最佳的連線選項。

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gateway-skus"></a><a name="gwsku"></a>閘道 SKU

建立虛擬網路閘道時，您必須指定想要使用的閘道 SKU。 根據工作負載、輸送量、功能和 SLA 的類型，選取符合您需求的 SKU。

* 如需閘道 SKU 的詳細資訊，包括支援功能、實際執行環境和開發測試和設定步驟，請參閱 [VPN 閘道設定 - 閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) 一文。
* 如需舊版 SKU 的資訊，請參閱[使用舊版 SKU](vpn-gateway-about-skus-legacy.md)。

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>依通道、連線和輸送量區分的閘道 SKU

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="pricing"></a><a name="pricing"></a>定價

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

如需 VPN 閘道之閘道 SKU 的詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

## <a name="faq"></a><a name="faq"></a>常見問題集

如需 VPN 閘道的常見問題集，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)。

## <a name="whats-new"></a><a name="new"></a>新功能

訂閱 RSS 摘要，並在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway)頁面上檢視最新的 VPN 閘道功能更新。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)。
- 檢視[訂用帳戶與服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。
- 了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。
