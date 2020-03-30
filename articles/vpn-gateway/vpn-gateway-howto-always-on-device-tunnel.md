---
title: 配置始終打開的 VPN 隧道
titleSuffix: Azure VPN Gateway
description: 為 VPN 閘道配置始終打開 VPN 隧道的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371711"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>設定 Always On VPN 裝置通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

使用["配置點對點 VPN 連接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)"一文將 VPN 閘道配置為使用 IKEv2 和基於證書的身份驗證。

## <a name="configure-the-device-tunnel"></a>配置設備隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>刪除設定檔

要刪除設定檔，請運行以下命令：

![清理](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

有關故障排除，請參閱[Azure 點對點連接問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
