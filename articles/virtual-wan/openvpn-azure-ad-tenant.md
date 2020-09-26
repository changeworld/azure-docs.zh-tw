---
title: 適用于使用者 VPN 連接的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 Azure 虛擬 WAN 使用者 VPN (點對站) ，以使用 Azure AD 驗證連接到您的 VNet
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91367847"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>準備 Azure Active Directory 租使用者以進行使用者 VPN OpenVPN 通訊協定連線

透過 IKEv2 通訊協定連接到您的虛擬中樞時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 但是，當您使用 OpenVPN 通訊協定時，也可以使用 Azure Active Directory authentication。 本文可協助您使用 OpenVPN authentication 來設定虛擬 WAN 使用者 VPN (點對站) 的 Azure AD 租使用者。

> [!NOTE]
> 只有 OpenVPN &reg; 通訊協定連接支援 Azure AD authentication。
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.建立 Azure AD 租用戶

確認您有 Azure AD 的租使用者。 如果您沒有 Azure AD 租使用者，您可以使用 [建立新的租](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 使用者文章中的步驟建立一個租使用者：

* 組織名稱
* 初始網域名稱

範例：

   ![新增 Azure AD 租用戶](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. 建立 Azure AD 租使用者使用者

接下來，在新建立的 Azure AD 租使用者中建立兩個使用者帳戶，一個全域管理員帳戶和一個使用者帳戶。 使用者帳戶可用來測試 OpenVPN authentication，而全域管理員帳戶將用來授與 Azure VPN 應用程式註冊的同意。 建立 Azure AD 的使用者帳戶之後，您可以將 **目錄角色** 指派給使用者，以便委派系統管理許可權。

使用 [本文中的步驟，為您](../active-directory/fundamentals/add-users-azure-active-directory.md) 的 Azure AD 租使用者建立兩位使用者。 務必將其中一個已建立帳戶的 **目錄角色** 變更為 **全域管理員**。

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. 將同意授與 Azure VPN 應用程式註冊

1. 以已獲指派 **全域管理員** 角色的使用者身分登入 Azure 入口網站。

2. 接下來，為您的組織授與系統管理員同意，這可讓 Azure VPN 應用程式登入和讀取使用者設定檔。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

    公開

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. 如果出現提示，請選取 **全域系統管理員** 帳戶。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/pick.png)

4. 出現提示時選取 [接受]。

    ![螢幕擷取畫面顯示對話方塊，其中包含要求您的組織接受的訊息許可權，以及其他資訊。](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. 在您的 Azure AD 下，在 **企業應用程式**中，您現在應該會看到列出的 **Azure VPN** 。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>後續步驟

若要使用 Azure AD authentication 連接到您的虛擬網路，您必須建立使用者 VPN 設定，並將其與虛擬中樞建立關聯。 請參閱 [設定 Azure 點對站連線的 Azure AD 驗證](virtual-wan-point-to-site-azure-ad.md)。
