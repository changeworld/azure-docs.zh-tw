---
title: 設定 Always On VPN 通道
titleSuffix: Azure VPN Gateway
description: 瞭解如何使用閘道搭配 Windows 10 Always On 來建立並設定 Azure 的持續性裝置通道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7688e42175b2b4e35b63979f5df25702f3bb869d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986591"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>設定 Always On VPN 裝置通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>設定閘道

使用「 [設定點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 連線」一文，將 VPN 閘道設定為使用 IKEv2 和以憑證為基礎的驗證。

## <a name="configure-the-device-tunnel"></a>設定裝置通道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>移除設定檔

若要移除設定檔，請執行下列命令：

![螢幕擷取畫面顯示執行 VpnConnection-Name MachineCertTest 命令的 PowerShell 視窗。](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>下一步

如需疑難排解，請參閱 [Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
