---
title: 為 VPN 使用者啟用 MFA： Azure AD 驗證
description: 為 VPN 使用者啟用多重要素驗證
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964689"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>為 VPN 使用者啟用 Azure 多重要素驗證（MFA）

如果您想要在授與存取權之前提示使用者提供第二個驗證因素，您可以設定每個使用者的 Azure 多重要素驗證（MFA），或透過[條件式存取](../active-directory/conditional-access/overview.md)來運用多重要素驗證（mfa），以進行更精細的控制。 為每位使用者設定多重要素驗證可以免費啟用，不過，當每位使用者啟用 MFA 時，系統會提示使用者針對所有系結至 Azure AD 租使用者的應用程式進行第二因素驗證。 條件式存取可讓您更精確地控制第二個因素的升級方式，而且只允許將 MFA 指派給 VPN，而不是其他系結至 Azure AD 租使用者的應用程式。

## <a name="enableauth"></a>啟用驗證

1. 流覽至**Azure Active Directory-> 企業應用程式-> 所有應用程式**。
2. 在 [**企業應用程式-所有應用程式**] 頁面上，選取 [ **Azure VPN**]。

   ![目錄識別碼](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>設定登入設定

在 [ **AZURE VPN-屬性**] 頁面上，設定 [登入設定]。

1. 將 [**為使用者啟用登入嗎？** ] 設定為 **[是]** 。 這可讓 AD 租使用者中的所有使用者成功連接到 VPN。
2. **需要設定使用者指派嗎？** 如果您想要將登入限制為僅有 Azure VPN 許可權的使用者，則為 **[是]** 。
3. 儲存您的變更。

   ![使用權限](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>選項 1-透過條件式存取啟用多重要素驗證（MFA）

條件式存取可讓您以每個應用程式為基礎，進行更細緻的存取控制。  請注意，若要利用條件式存取，您應該將 Azure AD Premium 1 或更高的授權套用至將受條件式存取規則規範的使用者。

1. 在 [**企業應用程式-所有應用程式**] 頁面上，選取 [ **Azure VPN**]、[**條件式存取**]，然後按一下 [**新增原則**]。
2. 在 [使用者和群組] 底下的 [*包含*] 索引標籤上，**選取 [使用者和群組**]、[檢查**使用者和群組**]，然後選取一組或應受 MFA 的使用者集合。  按一下 [完成]。
![指派](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. 在 **[授**與] 底下，核取 [**授與存取權**]、[檢查**需要多重要素驗證**]、 **[需要所有選取的控制項**] 和 [**選取**] 按鈕。
![授與存取權-MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. 在 [**啟用原則** **] 下核**取，然後按一下 [**建立**] 按鈕。
![啟用原則](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>選項 2-啟用每位使用者的多重要素驗證（MFA）

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
