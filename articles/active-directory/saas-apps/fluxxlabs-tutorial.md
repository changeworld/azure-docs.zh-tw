---
title: 教學課程：Azure Active Directory 與 Fluxx Labs 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Fluxx Labs 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: d201f80aea2c22e1bee06c776d2a84fb69f2bd7d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92452578"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Fluxx Labs 整合

在本教學課程中，您將了解如何整合 Fluxx Labs 與 Azure Active Directory (Azure AD)。 在整合 Fluxx Labs 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Fluxx Labs 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Fluxx Labs。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Fluxx Labs 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Fluxx Labs 支援由 **IDP** 起始的 SSO
* 設定 Fluxx Labs 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-fluxx-labs-from-the-gallery"></a>從資源庫新增 Fluxx Labs

若要進行將 Fluxx Labs 整合到 Azure AD 中的設定，您必須從資源庫將 Fluxx Labs 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Fluxx Labs** 。
1. 從結果面板中選取 [Fluxx Labs]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>設定及測試 Fluxx Labs 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Fluxx Labs 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Fluxx Labs 中相關使用者之間的連結關聯性。

若要設定及測試與 Fluxx Labs 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Fluxx Labs SSO](#configure-fluxx-labs-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Fluxx Labs 測試使用者](#create-fluxx-labs-test-user)** - 在 Fluxx Labs 中建立 B.Simon 的對應項目，且該項目須與 Azure AD 中代表使用者的項目連結。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Fluxx Labs] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入] 頁面上，執行下列步驟：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：

    | 環境 | URL 模式|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | 生產前 | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：

    | 環境 | URL 模式|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 生產前 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Fluxx Labs 用戶端支援小組](https://fluxx.zendesk.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，尋找 [憑證 (Base64)] 並選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Fluxx Labs] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Fluxx Labs 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Fluxx Labs]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-fluxx-labs-sso"></a>設定 Fluxx Labs SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Fluxx Labs 公司網站。

2. 選取 [設定] 區段下方的 [管理員]。

    ![顯示 [設定] 區段的螢幕擷取畫面，其中已選取 [管理員]。](./media/fluxxlabs-tutorial/config1.png)

3. 在管理面板中選取 [外掛程式] > [整合]，然後選取 [SAML SSO - (已停用)]

    ![顯示 [整合] 索引標籤的螢幕擷取畫面，其中已選取 [SAML SSO - (已停用)]。](./media/fluxxlabs-tutorial/config2.png)

4. 在 [屬性] 區段中，執行下列步驟：

    ![顯示 [屬性] 區段的螢幕擷取畫面，其中已核取 [SAML SSO]、在欄位中輸入值，並且已選取 [儲存] 按鈕。](./media/fluxxlabs-tutorial/config3.png)

    a. 選取 [SAML SSO] 核取方塊。

    b. 在 [要求路徑] 文字方塊中，輸入 **/auth/saml** 。

    c. 在 [回呼路徑] 文字方塊中，輸入 **/auth/saml/callback** 。

    d. 在 [判斷提示取用者服務 Url (單一登入 URL)] 文字方塊中，輸入您在 Azure 入口網站中輸入的 [回覆 URL] 值。

    e. 在 [對象 (SP 實體識別碼)] 文字方塊中，輸入您在 Azure 入口網站中輸入的 [識別碼] 值。

    f. 在 [識別提供者 SSO 目標 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    g. 在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼入 [識別提供者憑證] 文字方塊。

    h. 在 [名稱識別碼格式] 文字方塊中，輸入 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` 值。

    i. 按一下 [檔案] 。

    > [!NOTE]
    > 在儲存內容後，欄位基於安全考量會呈現為空白，但實際上其值已儲存到組態中。

### <a name="create-fluxx-labs-test-user"></a>建立 Fluxx Labs 測試使用者

若要讓 Azure AD 使用者能夠登入 Fluxx Labs，必須將他們佈建到 Fluxx Labs 中。 Fluxx Labs 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Fluxx Labs 公司網站。

2. 按一下下方顯示的 **圖示** 。

    ![顯示系統管理員選項的螢幕擷取畫面，其中已在 [您的儀表板] 底下選取「加號」圖示。](./media/fluxxlabs-tutorial/config6.png)

3. 在儀表板上，按一下下方顯示的圖示，以開啟 [新增人員] 卡片。

    ![顯示 [連絡人管理] 功能表的螢幕擷取畫面，其中已選取 [人員] 旁邊的「加號」圖示。](./media/fluxxlabs-tutorial/config4.png)

4. 在 [新增人員] 區段上，執行下列步驟：

    ![Fluxx Labs 組態](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs 會使用電子郵件作為 SSO 登入的唯一識別碼。 在 [SSO UID] 欄位中，填入與使用者用作 SSO 登入的電子郵件地址相符的使用者電子郵件地址。

    b. 按一下 [檔案] 。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Fluxx Labs] 圖格時，應該會自動登入您已設定 SSO 的 Fluxx Labs。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試使用 Fluxx Labs 搭配 Azure AD](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Fluxx Labs](/cloud-app-security/proxy-intro-aad)