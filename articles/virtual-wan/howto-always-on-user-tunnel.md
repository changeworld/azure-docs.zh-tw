---
title: 設定 Always On VPN 使用者通道
titleSuffix: Azure Virtual WAN
description: 本文說明如何為您的虛擬 WAN 設定 Always On VPN 使用者通道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502863"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>設定虛擬 WAN 的 Always On VPN 使用者通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>先決條件

您必須建立點對站設定，並編輯虛擬中樞指派。 如需相關指示，請參閱下列各節：

* [建立 P2S 設定](virtual-wan-point-to-site-portal.md#p2sconfig)
* [編輯中樞指派](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>設定使用者通道

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請使用下列步驟：

1. 執行下列命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 中斷連接連線，並清除 [**自動連接]** 核取方塊。

   ![清理](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>後續步驟

如需有關虛擬 WAN 的詳細資訊，請參閱[常見問題](virtual-wan-faq.md)。
