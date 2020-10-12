---
title: 設定 Always-On VPN 使用者通道
titleSuffix: Azure VPN Gateway
description: 本文說明如何為您的 VPN 閘道設定 Always On VPN 使用者通道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99d21222a62ed6b27a6a1b2a73b704f4cb26457b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435791"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>設定 Always On VPN 使用者通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

 使用「 [設定點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 連線」一文中的指示，將 VPN 閘道設定為使用 IKEv2 和憑證型驗證。

## <a name="configure-a-user-tunnel"></a>設定使用者通道

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請使用下列步驟：

1. 執行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 中斷連接，並清除 [ **自動連線]** 核取方塊。

   ![清理](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>後續步驟

若要針對可能發生的任何連接問題進行疑難排解，請參閱 [Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
