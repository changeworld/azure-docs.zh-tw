---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 F5 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 F5 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fc56fe040528df5b4bd5e9217bd614c4a640c15
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85608042"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 F5 整合

在本教學課程中，您會了解如何整合 F5 與 Azure Active Directory (Azure AD)。 在整合 F5 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 F5 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 F5。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 F5 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* F5 支援 **SP 和 IDP** 起始的 SSO
* 您可以使用三種不同的方式設定 F5 SSO。

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [設定適用於標頭式應用程式的 F5 單一登入](headerf5-tutorial.md)

- [設定適用於 Kerberos 應用程式的 F5 單一登入](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>從資源庫新增 F5

若要設定將 F5 整合到 Azure AD 中，您需要從資源庫將 F5 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **F5**。
1. 從結果面板選取 [F5]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>設定及測試 F5 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 F5 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 F5 中相關使用者之間的連結關聯性。

若要設定及測試與 F5 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 F5-SSO](#configure-f5-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 F5 測試使用者](#create-f5-test-user)** - 使 F5 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [F5] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<YourCustomFQDN>.f5.com/`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<YourCustomFQDN>.f5.com/`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [F5 用戶端支援小組](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 [同盟中繼資料 XML]，然後選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 F5] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 F5 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取[F5]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-f5-sso"></a>設定 F5 SSO

- [設定適用於標頭式應用程式的 F5 單一登入](headerf5-tutorial.md)

- [設定適用於 Kerberos 應用程式的 F5 單一登入](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>設定適用於進階 Kerberos 應用程式的 F5 單一登入

1. 開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 F5 (進階 Kerberos) 公司網站，然後執行下列步驟：

1. 您必須將中繼資料憑證匯入稍後將在安裝程式中使用的 F5 (進階 Kerberos)。 移至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]。 按一下右上角的 [匯入]。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure01.png)
 
1. 若要設定 SAML IDP，請移至 [存取] > [同盟] > [SAML 服務提供者] > [建立] > [從中繼資料]。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure05.png)
 
1. 指定從工作 3 上傳的憑證

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure07.png)

 1. 若要設定 SAML SP，請移至 [存取] > [同盟] > [SAML 服務同盟] > [本機 SP 服務] > [建立]。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure08.png)
 
1. 按一下 [確定]。

1. 選取 [SP 設定]，然後按一下 [繫結/取消繫結 IdP 連接器]。

     ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. 按一下 [加入新資料列]，然後選取上一個步驟中建立的**外部 IdP 連接器**。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure10.png)
 
1. 若要設定 Kerberos SSO，請移至 [存取] > [單一登入] > [Kerberos]

    >[!Note]
    >您將需要建立和指定 Kerberos 委派帳戶。 請參閱 KCD 區段 (參閱變數參考的附錄)

    •   使用者名稱來源  `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   使用者領域來源  `session.logon.last.domain`

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure11.png)

1. 若要設定存取設定檔，請移至 [存取] > [設定檔/原則] > [存取設定檔 (遵循工作階段原則)]。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. 若要加入新的節點，請移至 [本機流量] > [節點] > [結點清單] > [+]。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure21.png)
 
1. 若要建立新的集區，請移至 [本機流量] > [集區] > [集區清單] > [建立]。

     ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure22.png)

 1. 若要建立新的虛擬伺服器，請移至 [本機流量] > [虛擬伺服器]> [虛擬伺服器清單] > [+]。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure23.png)

1. 指定上一個步驟中建立的存取設定檔。

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>設定 Kerberos 委派 

>[!Note]
>如需詳細資訊，請參閱[這裡](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **步驟 1：建立委派帳戶**

    * 範例
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **步驟 2：設定 SPN (在 APM 委派帳戶上)**

    *  範例
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **步驟 3：SPN 委派 (適用於 App Service 帳戶)**

    * 為 F5 委派帳戶設定適當的委派。
    * 下列範例會為 FRP-App1.superdemo.live 應用程式設定適用於 KCD 的 APM 委派帳戶。

        ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure25.png)

1. 在[此處](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)下方，提供上方參考文件中所述的詳細資料

1. 附錄 - SAML – F5 BIG IP 變數對應，如下所示：

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 (進階 Kerberos) 設定](./media/advance-kerbf5-tutorial/configure27.png) 

1. 以下是預設 SAML 屬性的完整清單。 GivenName 會使用下列字串來表示。
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| 工作階段 | 屬性 |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>建立 F5 測試使用者

在本節中，您會在 F5 中建立名為 B.Simon 的使用者。 請與 [F5 客戶支援小組](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)合作，在 F5 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [F5] 圖格時，應該會自動登入您已設定 SSO 的 F5。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 F5](https://aad.portal.azure.com/)

- [設定適用於標頭式應用程式的 F5 單一登入](headerf5-tutorial.md)

- [設定適用於 Kerberos 應用程式的 F5 單一登入](kerbf5-tutorial.md)

