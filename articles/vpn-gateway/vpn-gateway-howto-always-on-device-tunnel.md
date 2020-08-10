---
title: 設定 Always On VPN 通道
titleSuffix: Azure VPN Gateway
description: 瞭解如何搭配 Windows 10 Always On 使用閘道，以建立及設定 Azure 的持續性裝置通道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: e64d2223740942fe35ae8a730303bee8893c2489
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035598"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>設定 Always On VPN 裝置通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

使用[設定點對站 VPN 連線一](vpn-gateway-howto-point-to-site-resource-manager-portal.md)文，將 VPN 閘道設定為使用 IKEv2 和憑證型驗證。

## <a name="configure-the-device-tunnel"></a>設定裝置通道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請執行下列命令：

![清除](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解，請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
