---
title: 如何在 Azure VPN 閘道上設定 OpenVPN： PowerShell
description: 為 Azure VPN 閘道設定 OpenVPN 的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162402"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>設定 OpenVPN 以進行 Azure 點對站 VPN 閘道

本文可協助您在 Azure VPN 閘道上設定**OpenVPN®通訊協定**。 本文假設您已具有可用的點對站環境。 如果還沒有，請使用步驟 1 中的指示來建立點對站 VPN。



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. 建立點對站 VPN

如果您還沒有正常運作的點對站台環境，請依照指示來建立一個。 請參閱[建立點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，來建立及設及使用原生 Azure 憑證驗證的點對站 VPN 閘道。 

> [!IMPORTANT]
> OpenVPN 不支援基本 SKU。

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. 在閘道上啟用 OpenVPN

在閘道上啟用 OpenVPN。 執行下列命令之前，務必確認閘道已針對點對站 (IKEv2 或 SSTP) 進行設定：

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>後續步驟

若要設定 OpenVPN 的用戶端，請參閱[設定 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md)。

**「OpenVPN」是 OpenVPN Inc. 的商標。**
