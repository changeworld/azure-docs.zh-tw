---
title: 為 VPN 使用者啟用 MFA： Azure AD 驗證
description: 為 VPN 使用者啟用多重要素驗證
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 4dcda4d553eba2829aba4626e1020a27d5215850
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84752783"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>為 VPN 使用者啟用 Azure 多重要素驗證（MFA）

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>啟用驗證

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>設定登入設定

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>選項 1-每位使用者存取

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>選項 2-條件式存取

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[針對 Azure 的點對站連線設定 Azure AD 驗證](virtual-wan-point-to-site-azure-ad.md)。
