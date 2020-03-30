---
title: 配置始終打開的 VPN 隧道
titleSuffix: Azure Virtual WAN
description: 為虛擬 WAN 配置始終位於 VPN 設備隧道的步驟
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502850"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>為虛擬 WAN 配置始終位於 VPN 上的設備隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Prerequisites

您必須創建點對點配置並編輯虛擬中心分配。 有關說明，請參閱以下部分：

* [建立 P2S 設定](virtual-wan-point-to-site-portal.md#p2sconfig)
* [編輯中心分配](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>配置設備隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>刪除設定檔

要刪除設定檔，請運行以下命令：

![清理](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

有關虛擬 WAN 的詳細資訊，請參閱[常見問題解答](virtual-wan-faq.md)。