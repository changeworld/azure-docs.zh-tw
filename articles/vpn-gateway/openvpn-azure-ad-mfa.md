---
title: 為 VPN 使用者啟用 MFA：Azure AD 身份驗證
description: 為 VPN 使用者啟用多重要素驗證
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 34ef1b73b06870fd4eaabe88147cd98b281c1f11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472344"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>為 VPN 使用者啟用 Azure 多重要素驗證 （MFA）

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>啟用驗證

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>配置登錄設置

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>選項 1 - 每個使用者訪問

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>選項 2 - 條件訪問

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>後續步驟

要連接到虛擬網路，必須創建和配置 VPN 用戶端設定檔。 請參閱[為 P2S VPN 連接配置 VPN 用戶端](openvpn-azure-ad-client.md)。