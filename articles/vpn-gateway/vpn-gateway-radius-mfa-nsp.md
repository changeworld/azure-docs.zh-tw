---
title: 整合 NPS 與 MFA 的 VPN 閘道 RADIUS 驗證
description: 描述整合 Azure 閘道 RADIUS 驗證與 NPS 伺服器以進行多重要素驗證。
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 8c439113907c2eb28c41aed3c21c1d27398d5207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987076"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>整合 Azure VPN 閘道 RADIUS 驗證與 NPS 伺服器以進行多重要素驗證 

本文描述如何整合網路原則伺服器 (NPS) 和 Azure VPN 閘道 RADIUS 驗證，為點對站 VPN 連線提供多重要素驗證 (MFA)。 

## <a name="prerequisite"></a>必要條件

若要啟用 MFA，使用者必須使用 Azure Active Directory (Azure AD)，必須從內部部署或雲端環境同步。 而且，使用者必須已經完成 MFA 的自動註冊程序。  如需詳細資訊，請參閱[對我的帳戶進行雙步驟驗證設定](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>詳細步驟

### <a name="step-1-create-a-virtual-network-gateway"></a>步驟 1：建立虛擬網路閘道

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在將裝載虛擬網路閘道的虛擬網路中，依序選取 [子網路]**** 和 [閘道子網路]**** 以建立一個子網路。 

    ![關於如何新增閘道子網路的影像](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. 藉由指定下列設定建立虛擬網路閘道：

    - 閘道類型****︰選取 [VPN]****。
    - **VPN 類型**：選取 [路由型]****。
    - **SKU**：依序需求選取 SKU 類型。
    - **虛擬網路**：選取您在其中建立閘道子網路的虛擬網路。

        ![關於虛擬網路閘道設定的影像](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>步驟 2：設定 Azure MFA 的 NPS

1. 在 NPS 伺服器上，[安裝 Azure MFA 的 NPS 延伸模組](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)。
2. 開啟 NPS 主控台，以滑鼠右鍵按一下 [ **RADIUS 用戶端**]，然後選取 [**新增**]。 藉由指定下列設定來建立 RADIUS 用戶端：

    - **易記名稱**：輸入任何名稱。
    - **位址 (IP 或 DNS)**：輸入您在步驟 1 建立的閘道子網路。
    - **共用密碼**：輸入任何秘密金鑰，並記住它以方便之後使用。

      ![RADIUS 用戶端設定的相關影像](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  在 [進階]**** 索引標籤上，將廠商名稱設定為 [RADIUS 標準]**** 並確定未選取 [其他選項]**** 和取方塊。

    ![RADIUS 用戶端 Advanced 設定的相關影像](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. 移至 [**原則**] [  >  **網路原則**]，按兩下 [**連接到 Microsoft 路由及遠端存取服務器**原則]，選取 **[授與存取權**]，然後按一下 **[確定]**。

### <a name="step-3-configure-the-virtual-network-gateway"></a>步驟 3：設定虛擬網路閘道

1. 登入[Azure 入口網站](https://portal.azure.com)。
2. 開啟您建立的虛擬網路閘道。 確定閘道類型已設定為 **VPN****路由型**。
3. 按一下 [**點對站**設定] [  >  **立即設定**]，然後指定下列設定：

    - **位址集區**：輸入您在步驟 1 建立的閘道子網路。
    - **驗證類型**：選取 [RADIUS 驗證]****。
    - **伺服器 IP 位址**：輸入 NPS 伺服器的 IP 位址。

      ![關於點對站設定的影像](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>後續步驟

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [將現有的 NPS 基礎結構與 Azure Multi-Factor Authentication 整合](../active-directory/authentication/howto-mfa-nps-extension.md)
