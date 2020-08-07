---
title: 設定 Always On VPN 通道
titleSuffix: Azure VPN Gateway
description: 瞭解如何設定 Always On，以根據觸發程式來維護 VPN 連線，例如使用者登入、網路狀態變更或裝置螢幕作用中。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 24043576fca4910631ccddb3924303dd642c6842
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927040"
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
