---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: df3d1c34fe0fc045e829c2ea9166664f06fd3127
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116795"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.建立 Azure AD 租用戶

使用[建立新的租用戶](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步驟，建立 Azure AD 租用戶：

* 組織名稱
* 初始網域名稱

  範例：

   ![新增 Azure AD 租用戶](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2.建立租用戶使用者

在此步驟中，您會建立兩個 Azure AD 租用戶使用者：一個是全域管理員帳戶，一個是主要使用者帳戶。 主要使用者帳戶會當做您的主要內嵌帳戶 (服務帳戶) 使用。 當您建立 Azure AD 租用戶使用者帳戶時，您會針對您想要建立的使用者類型調整目錄角色。 使用[本文](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟，為您的 Azure AD 租用戶建立至少兩個使用者。 請務必變更**目錄角色**以建立帳戶類型：

* 全域管理員
* User

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3.註冊 VPN 用戶端

在 Azure AD 租用戶中註冊 VPN 用戶端。

1. 找出目錄 (您要用於驗證) 的目錄識別碼。 會列在 Active Directory 頁面的屬性區段中。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 複製目錄識別碼。

3. 以獲指派**全域管理員**角色的使用者身分登入 Azure 入口網站。

4. 接下來，給予管理員同意。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

    公開

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> 如果您使用的全域系統管理員帳戶不是 Azure AD 租使用者的原生管理員帳戶來提供同意，請以 URL 中的 Azure AD 目錄識別碼取代「一般」。 在某些其他情況下，您可能也必須將「通用」取代為您的目錄識別碼。
>

5. 若出現提示，請選取**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 出現提示時選取 [接受]。

    ![螢幕擷取畫面顯示一個視窗，內含要求接受您組織的訊息許可權，以及要求的相關資訊。](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在您的 Azure AD 底下的 [企業應用程式] 中，您會看到列出 **Azure VPN**。

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4.註冊其他應用程式

在此步驟中，您會針對各個使用者和群組註冊其他應用程式。

1. 在您的 Azure Active Directory 底下，按一下 [應用程式註冊]，然後按一下 [+ 新增註冊]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 在 [註冊應用程式] 頁面上，輸入 [名稱]。 選取所需的 [支援的帳戶類型]，然後按一下 [註冊]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 註冊新的應用程式之後，按一下應用程式刀鋒視窗底下的 [公開 API]。

4. 按一下 [+ 新增範圍]。

5. 保留預設的 [應用程式識別碼 URI]。 按一下 [儲存並繼續]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 填寫必要欄位，並確定 [狀態] 為 [已啟用]。 按一下 [新增範圍]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. 按一下 [公開 API]， 然後按一下 [+ 新增用戶端應用程式]。  針對 [用戶端識別碼]，請根據雲端輸入下列值：

    - 針對 Azure **公用**輸入 **41b23e61-6c1e-4545-b367-cd054e0ed4b4**
    - 針對 Azure **Government** 輸入 **51bb15d4-3a4f-4ebf-9dca-40096fe32426**
    - 針對 Azure **德國**輸入 **538ee9e6-310a-468d-afef-ea97365856a9**
    - 針對 Azure **China 21Vianet** 輸入 **49f817b6-84ae-4cc0-928c-73f27289b3aa**

8. 按一下 [新增應用程式]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. 從 [概觀] 頁面複製 [應用程式 (用戶端) 識別碼]。 您在設定 VPN 閘道時需要此資訊。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 重複此[註冊其他應用程式](#register-apps)一節中的步驟，以建立您的安全性需求所需數量的應用程式。 每個應用程式都會與 VPN 閘道相關聯，而且可以有一組不同的使用者。 只有一個應用程式可以與閘道建立關聯。

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5.將使用者指派至應用程式

將使用者指派至您的應用程式。

1. 在 [Azure AD -> 企業應用程式] 底下，選取新註冊的應用程式，然後按一下 [屬性]。 務必將 [需要使用者指派嗎？] 設定為 [是]。 按一下 [檔案] 。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 在應用程式頁面上，按一下 [使用者和群組]，然後按一下 [+ 新增使用者]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. 在 [新增指派] 底下，按一下 [使用者和群組]。 選取您想要讓其存取此 VPN 應用程式的使用者。 按一下 [選取]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
