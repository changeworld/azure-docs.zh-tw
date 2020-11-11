---
title: 設定適用于 Azure 虛擬 WAN 的 OpenVPN 用戶端
description: 為 Azure 虛擬 WAN 設定 OpenVPN 用戶端的步驟
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490996"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>設定適用于 Azure 虛擬 WAN 的 OpenVPN 用戶端

本文可協助您設定 **OpenVPN &reg; 通訊協定** 用戶端。 您也可以使用 Windows 10 的 Azure VPN Client，透過 OpenVPN 通訊協定進行連接。 您可以在[這裡](openvpn-azure-ad-client.md)找到更多指示

## <a name="before-you-begin"></a>開始之前

建立使用者 VPN (點對站) 設定。 請確定您選取 [OpenVPN] 作為 [通道類型]。 如需相關步驟，請參閱 [建立 Azure 虛擬 WAN 的 P2S](virtual-wan-point-to-site-portal.md#p2sconfig)設定。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>後續步驟

如需有關使用者 VPN (點對站) 的詳細資訊，請參閱 [建立使用者 vpn 連接](virtual-wan-point-to-site-portal.md)。

**"OpenVPN" 是 OpenVPN Inc. 的商標。**
