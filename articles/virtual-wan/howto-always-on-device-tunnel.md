---
title: 設定 Always On VPN 通道
titleSuffix: Azure Virtual WAN
description: 設定虛擬 WAN Always On VPN 裝置通道的步驟
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 7adeb9682336b19cc87d8c73a7f9ad8bda5e7828
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564059"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>設定虛擬 WAN 的 Always On VPN 裝置通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>必要條件

您必須建立點對站設定，並編輯虛擬中樞指派。 如需相關指示，請參閱下列各節：

* [建立 P2S 設定](virtual-wan-point-to-site-portal.md#p2sconfig)
* [建立具有 P2S 閘道的中樞](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>設定裝置通道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請執行下列命令：

![清理](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。