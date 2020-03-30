---
title: 配置始終打開的 VPN 使用者隧道
titleSuffix: Azure Virtual WAN
description: 本文介紹如何為虛擬 WAN 配置始終處於 VPN 的使用者隧道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502863"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>為虛擬 WAN 配置始終處於 VPN 使用者通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Prerequisites

您必須創建點對點配置並編輯虛擬中心分配。 有關說明，請參閱以下部分：

* [建立 P2S 設定](virtual-wan-point-to-site-portal.md#p2sconfig)
* [編輯中心分配](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>配置使用者隧道

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>刪除設定檔

要刪除設定檔，請使用以下步驟：

1. 執行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 斷開連接，並清除 **"自動連接**"核取方塊。

   ![清理](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>後續步驟

有關虛擬 WAN 的詳細資訊，請參閱[常見問題解答](virtual-wan-faq.md)。
