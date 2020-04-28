---
title: 舊版 Azure 虛擬網路 VPN 閘道 Sku
description: 如何使用舊的虛擬網路閘道 SKU；基本、標準和高效能。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279386"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>使用虛擬網路閘道 SKU (舊版 SKU)

此文章包含舊版虛擬網路閘道 SKU 的相關資訊。 舊版 SKU 仍然可以在針對 VPN 閘道建立的兩個部署模型中運作。 傳統 VPN 閘道仍繼續針對現有閘道以及新閘道使用舊版 SKU。 在建立新的資源管理員 VPN 閘道時，請使用新的閘道 SKU。 如需最新 SKU 的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## <a name="gateway-skus"></a><a name="gwsku"></a>閘道 SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

您可以在[ExpressRoute 定價頁面](https://azure.microsoft.com/pricing/details/expressroute)上的 [**虛擬網路閘道**] 區段中，查看舊版閘道定價。

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>依 SKU 列出的估計彙總輸送量

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>依 SKU 和 VPN 類型列出的支援組態

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>調整閘道器大小

您可以將相同 SKU 系列內的閘道大小調整為閘道 SKU。 例如，如果您有標準 SKU，則可以調整為高效能 SKU。 但是，您無法調整舊 SKU 與新 SKU 系列之間的 VPN 閘道大小。 例如，您不能從標準 SKU 變成 VpnGw2 SKU；也不能從標準 SKU 變成 VpnGw1。

### <a name="resource-manager"></a>Resource Manager

若要使用 PowerShell 調整資源管理員部署模型的閘道大小，請使用下列命令：

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

您也可以在 Azure 入口網站中調整閘道。

### <a name="classic"></a><a name="classicresize"></a>傳統

若要調整傳統部署模型的閘道大小，您必須使用服務管理 PowerShell Cmdlet。 使用下列命令：

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>變更為新的閘道 SKU

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>後續步驟

如需新式閘道 SKU 的相關資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gwsku)。

如需組態設定的詳細資訊，請參閱[關於 VPN 閘道組態設定](vpn-gateway-about-vpn-gateway-settings.md)。
