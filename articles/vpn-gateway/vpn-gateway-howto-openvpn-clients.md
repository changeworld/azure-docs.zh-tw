---
title: 如何設定 Azure VPN 閘道的 OpenVPN 用戶端 |Microsoft Docs
description: 為 Azure VPN 閘道設定 OpenVPN 用戶端的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066149"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>為 Azure VPN 閘道配置 OpenVPN 用戶端

本文可説明您配置**OpenVPN&reg;協定**用戶端。

## <a name="before-you-begin"></a>開始之前

請確認您已完成為 VPN 閘道設定 OpenVPN 的步驟。 如需詳細資訊，請參閱[設定 Azure VPN 閘道的 OpenVPN 用戶端](vpn-gateway-howto-openvpn.md)。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>後續步驟

如果希望 VPN 用戶端能夠訪問另一個 VNet 中的資源，請按照[VNet 到 VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)文章上的說明設置 Vnet 到 vnet 連接。 請務必啟用閘道上的 BGP 和連線，否則流量無法流動。

**"OpenVPN"是OpenVPN公司的商標。**
