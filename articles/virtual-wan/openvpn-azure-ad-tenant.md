---
title: 使用者 VPN 連線的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 Azure 虛擬 WAN 使用者 VPN （點對站）來連接到使用 Azure AD authentication 的 VNet
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753366"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>建立使用者 VPN OpenVPN 通訊協定連線的 Azure Active Directory 租使用者

連線到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用 Open VPN 通訊協定時，您也可以使用 Azure Active Directory 驗證。 本文可協助您設定虛擬 WAN 使用者 VPN （點對站） Open VPN 驗證的 Azure AD 租使用者。

> [!NOTE]
> 只有 OpenVPN 通訊協定連線支援 Azure AD 驗證 &reg; 。
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.建立 Azure AD 租用戶

使用[建立新的租用戶](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步驟，建立 Azure AD 租用戶：

* 組織名稱
* 初始網域名稱

範例：

   ![新增 Azure AD 租用戶](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. 建立 Azure AD 租使用者使用者

接下來，建立兩個使用者帳戶。 建立一個全域系統管理員帳戶和一個主要使用者帳戶。 主要使用者帳戶會當做您的主要內嵌帳戶 (服務帳戶) 使用。 當您建立 Azure AD 租用戶使用者帳戶時，您會針對您想要建立的使用者類型調整目錄角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟，為您的 Azure AD 租用戶建立至少兩個使用者。 請務必變更**目錄角色**以建立帳戶類型：

* 全域管理員
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. 在 VPN 閘道上啟用 Azure AD 驗證

1. 找出目錄 (您要用於驗證) 的目錄識別碼。 會列在 Active Directory 頁面的屬性區段中。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 複製目錄識別碼。

3. 以獲指派**全域管理員**角色的使用者身分登入 Azure 入口網站。

4. 接下來，給予管理員同意。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

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

5. 若出現提示，請選取**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 出現提示時選取 [接受]。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在您的 Azure AD 下的 [**企業應用程式**] 中，您會看到列出**Azure VPN** 。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. 依照[設定對 Azure 的點對站連線的 Azure AD 驗證](virtual-wan-point-to-site-azure-ad.md)中的步驟，為使用者 VPN 設定 Azure AD 驗證，並將其指派給虛擬中樞

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔，並將它與虛擬中樞產生關聯。 請參閱[針對 Azure 的點對站連線設定 Azure AD 驗證](virtual-wan-point-to-site-azure-ad.md)。
