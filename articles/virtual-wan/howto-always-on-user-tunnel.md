---
title: 設定 Always-On VPN 使用者通道
titleSuffix: Azure Virtual WAN
description: 本文說明如何為您的虛擬 WAN 設定 Always On VPN 使用者通道
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e83ca64d2b0e50ec02007a3cd878e6bf034d0961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313581"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>設定虛擬 WAN 的 Always On VPN 使用者通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>必要條件

您必須建立點對站設定，並編輯虛擬中樞指派。 如需相關指示，請參閱下列各節：

* [建立 P2S 設定](virtual-wan-point-to-site-portal.md#p2sconfig)
* [使用 P2S 閘道建立中樞](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>設定使用者通道

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請使用下列步驟：

1. 執行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 中斷連接，並清除 [ **自動連線]** 核取方塊。

   ![清理](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>後續步驟

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
