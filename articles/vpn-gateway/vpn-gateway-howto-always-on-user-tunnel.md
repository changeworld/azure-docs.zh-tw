---
title: 設定 Always On VPN 使用者通道
titleSuffix: Azure VPN Gateway
description: 本文說明如何為您的 VPN 閘道設定 Always On VPN 使用者通道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8cfe1d6434c0f5765196776ae9f6712fe14c52a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984126"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>設定 Always On VPN 使用者通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

 使用[設定點對站 VPN 連線一](vpn-gateway-howto-point-to-site-resource-manager-portal.md)文中的指示，將 VPN 閘道設定為使用 IKEv2 和以憑證為基礎的驗證。

## <a name="configure-a-user-tunnel"></a>設定使用者通道

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請使用下列步驟：

1. 執行下列命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 中斷連接連線，並清除 [**自動連接]** 核取方塊。

   ![清理](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>後續步驟

若要對任何可能發生的連線問題進行疑難排解，請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
