---
title: 設定適用于 Azure 虛擬 WAN 的 OpenVPN 用戶端
description: 為 Azure 虛擬 WAN 設定 OpenVPN 用戶端的步驟
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3e9674916ea9bb5e756a5e57ff18517f53ca7497
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427552"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>設定適用于 Azure 虛擬 WAN 的 OpenVPN 用戶端

本文可協助您設定 **OpenVPN &reg; 通訊協定** 用戶端。 您也可以使用 Azure VPN Client Windows 10 透過 OpenVPN 通訊協定連線 

## <a name="before-you-begin"></a>開始之前

建立使用者 VPN (點對站) 設定。 請確定您選取 [OpenVPN] 作為 [通道類型]。 如需相關步驟，請參閱 [建立 Azure 虛擬 WAN 的 P2S](virtual-wan-point-to-site-portal.md#p2sconfig)設定。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>後續步驟

如需有關使用者 VPN (點對站) 的詳細資訊，請參閱 [建立使用者 vpn 連接](virtual-wan-point-to-site-portal.md)。

**"OpenVPN" 是 OpenVPN Inc. 的商標。**
