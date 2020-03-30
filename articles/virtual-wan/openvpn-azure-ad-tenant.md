---
title: 用於使用者 VPN 連接的 Azure AD 租戶：Azure AD 身份驗證
description: 您可以使用 Azure 虛擬 WAN 使用者 VPN（點對點）使用 Azure AD 身份驗證連接到 VNet
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059449"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>為使用者 VPN OpenVPN 協定連接創建 Azure 活動目錄租戶

連接到 VNet 時，可以使用基於證書的身份驗證或 RADIUS 身份驗證。 但是，當您使用 Open VPN 協定時，也可以使用 Azure 活動目錄身份驗證。 本文可説明您為虛擬 WAN 使用者 VPN（點對點）開放 VPN 身份驗證設置 Azure AD 租戶。

> [!NOTE]
> Azure AD 身份驗證僅支援 OpenVPN&reg;協定連接。
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. 創建 Azure AD 租戶

使用["創建新租戶](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)"文章中的步驟創建 Azure AD 租戶：

* 組織名稱
* 初始網域名稱

範例：

   ![新的 Azure AD 租戶](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. 創建 Azure AD 租戶使用者

接下來，創建兩個使用者帳戶。 創建一個全域管理員帳戶和一個主使用者帳戶。 主使用者帳戶用作主嵌入帳戶（服務帳戶）。 創建 Azure AD 租戶使用者帳戶時，將調整要創建的使用者類型的"目錄"角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟為 Azure AD 租戶創建至少兩個使用者。 請務必更改**目錄角色**以創建帳戶類型：

* 全域管理員
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. 在 VPN 閘道上啟用 Azure AD 身份驗證

1. 找到要用於身份驗證的目錄的目錄 ID。 它列在"活動目錄"頁的屬性部分中。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 複製 [目錄識別碼]。

3. 以分配**全域管理員**角色的使用者身份登錄到 Azure 門戶。

4. 接下來，給予管理員同意。 複製並粘貼與您的部署位置相關的 URL 在瀏覽器的網址列中：

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

5. 如果出現提示，請選擇**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 選擇"在提示時**接受**"。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在 Azure AD 下，在**企業應用程式中**，您將看到列出的**Azure VPN。**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. 為使用者 VPN 配置 Azure AD 身份驗證，並按照配置 Azure AD 身份驗證中的步驟將其分配給虛擬中心[，以便從點對點連接到 Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>後續步驟

要連接到虛擬網路，必須創建和配置 VPN 用戶端設定檔並將其關聯到虛擬中心。 有關[指向網站連接到 Azure，請參閱配置 Azure AD 身份驗證](virtual-wan-point-to-site-azure-ad.md)。
