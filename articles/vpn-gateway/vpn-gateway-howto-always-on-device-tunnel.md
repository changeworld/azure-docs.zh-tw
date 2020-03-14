---
title: 設定 Always On VPN 通道
titleSuffix: Azure VPN Gateway
description: 設定 VPN 閘道 Always On VPN 通道的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371711"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>設定 Always On VPN 裝置通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

使用[設定點對站 VPN 連線一](vpn-gateway-howto-point-to-site-resource-manager-portal.md)文，將 VPN 閘道設定為使用 IKEv2 和憑證型驗證。

## <a name="configure-the-device-tunnel"></a>設定裝置通道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請執行下列命令：

![清理](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解，請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
