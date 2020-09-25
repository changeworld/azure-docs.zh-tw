---
title: 使用 Azure AD authentication 為 VPN 使用者啟用 MFA
description: 瞭解如何使用 Azure AD 驗證，為 VPN 使用者啟用 Azure Multi-Factor Authentication (MFA) 。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: efe01c9e0907fef4d33d2a70b3e479b30c471a7c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267885"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>使用 Azure AD 驗證為 VPN 使用者啟用 Azure Multi-Factor Authentication (MFA) 

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

若要連接到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱 [設定 Azure 點對站連線的 Azure AD 驗證](virtual-wan-point-to-site-azure-ad.md)。
