---
title: 如何設定 Azure VPN 閘道的 OpenVPN 用戶端 |Microsoft Docs
description: 瞭解如何設定適用于 Azure VPN 閘道的 OpenVPN 通訊協定用戶端，包括 Windows、Mac 和 Linux 用戶端。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926207"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>設定適用于 Azure VPN 閘道的 OpenVPN 用戶端

本文可協助您設定**OpenVPN &reg; 通訊協定**用戶端。

## <a name="before-you-begin"></a>開始之前

請確認您已完成為 VPN 閘道設定 OpenVPN 的步驟。 如需詳細資訊，請參閱[設定 Azure VPN 閘道的 OpenVPN 用戶端](vpn-gateway-howto-openvpn.md)。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>後續步驟

如果您想要 VPN 用戶端能夠存取另一個 VNet 中的資源，請遵循[vnet 對 vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)一文中的指示來設定 vnet 對 vnet 連線。 請務必啟用閘道上的 BGP 和連線，否則流量無法流動。

**「OpenVPN」是 OpenVPN Inc. 的商標。**
