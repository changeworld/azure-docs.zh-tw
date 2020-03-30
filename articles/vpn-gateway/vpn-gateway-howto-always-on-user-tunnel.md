---
title: 配置始終打開的 VPN 使用者隧道
titleSuffix: Azure VPN Gateway
description: 本文介紹如何為 VPN 閘道配置始終處於 VPN 使用者通道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502266"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>設定 Always On VPN 使用者通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

 使用["配置點對點 VPN 連接"一](vpn-gateway-howto-point-to-site-resource-manager-portal.md)文中的說明將 VPN 閘道配置為使用 IKEv2 和基於證書的身份驗證。

## <a name="configure-a-user-tunnel"></a>配置使用者隧道

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>刪除設定檔

要刪除設定檔，請使用以下步驟：

1. 執行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 斷開連接，並清除 **"自動連接**"核取方塊。

   ![清理](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>後續步驟

要排除可能發生的任何連接問題，請參閱[Azure 點對點連接問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
