---
title: 設定 Always On VPN 通道
titleSuffix: Azure Virtual WAN
description: 設定虛擬 WAN Always On VPN 裝置通道的步驟
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983691"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>設定虛擬 WAN 的 Always On VPN 裝置通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>必要條件

您必須建立點對站設定，並編輯虛擬中樞指派。 如需相關指示，請參閱下列各節：

* [建立 P2S 設定](virtual-wan-point-to-site-portal.md#p2sconfig)
* [使用 P2S 閘道建立中樞](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>設定裝置通道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請執行下列命令：

![螢幕擷取畫面顯示執行 VpnConnection-Name MachineCertTest 命令的 PowerShell 視窗。](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。