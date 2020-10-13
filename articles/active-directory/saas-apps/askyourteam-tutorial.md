---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 AskYourTeam 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 AskYourTeam 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: 6ab6a355b986daeca41ffd18a51689cd9d880dcd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 AskYourTeam 整合

在本教學課程中，您將了解如何整合 AskYourTeam 與 Azure Active Directory (Azure AD)。 在整合 AskYourTeam 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 AskYourTeam 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 AskYourTeam。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 AskYourTeam 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* AskYourTeam 支援由 **SP 和 IDP** 起始的 SSO。
* 設定 AskYourTeam 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-askyourteam-from-the-gallery"></a>從資源庫新增 AskYourTeam

若要設定 AskYourTeam 與 Azure AD 整合，您必須從資源庫將 AskYourTeam 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **AskYourTeam**。
1. 從結果面板選取 [AskYourTeam]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>設定及測試 AskYourTeam 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 AskYourTeam 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 AskYourTeam 中相關使用者之間的連結關聯性。

若要設定及測試與 AskYourTeam 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 AskYourTeam SSO](#configure-askyourteam-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 AskYourTeam 測試使用者](#create-askyourteam-test-user)** - 使 AskYourTeam 中 B.Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [AskYourTeam]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > 這些都不是真正的值。 本教學課程稍後會說明如何使用實際的「回覆 URL」及「登入 URL」值來更新這些值。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 AskYourTeam]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 AskYourTeam 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [AskYourTeam]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-askyourteam-sso"></a>設定 AskYourTeam SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 AskYourTeam 網站。

1. 按一下 [我的組織]  。

    ![顯示我的組織連結的螢幕擷取畫面。](./media/askyourteam-tutorial/user1.png)

1. 按一下 [整合]  。

    ![顯示整合連結的螢幕擷取畫面。](./media/askyourteam-tutorial/configure1.png)

1. 按一下 [編輯設定]  。

    ![顯示單一登入訊息的螢幕擷取畫面，其中具有 [編輯設定] 按鈕。](./media/askyourteam-tutorial/configure2.png)

1. 在 [編輯單一登入整合]  頁面執行下列步驟： 

    ![顯示編輯單一登入整合的螢幕擷取畫面，您可以在其中針對此步驟輸入值。](./media/askyourteam-tutorial/configure3.png)

    a. 在 [SAML 單一登入服務 URL]  文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    b. 在 [SAML 實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    c. 在 [登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    d. 從 Azure 入口網站將所下載的**憑證(Base64)** 以記事本開啟，然後將內容貼至 [SAML 簽署憑證 - Base64]  文字方塊。

    > [!NOTE]
    > 您也可以按一下 [選擇檔案]  選項來上傳**同盟中繼資料 XML**。

    e. 複製**回覆 URL (判斷提示取用者服務 URL)** 值，在 Azure 入口網站的 [基本 SAML 組態]  區段中的 [回覆 URL]  文字方塊內貼上該值。

    f. 複製 [登入 URL]  值，並將此值貼到 Azure 入口網站的 [基本 SAML 組態]  區段上的 [登入 URL]  文字方塊中。

    g. 按一下 [檔案]  。

### <a name="create-askyourteam-test-user"></a>建立 AskYourTeam 測試使用者

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 AskYourTeam 網站。

1. 按一下 [我的組織]  。

    ![顯示我的組織連結的螢幕擷取畫面，您會在其中開始此工作。](./media/askyourteam-tutorial/user1.png)

1. 按一下 [使用者]  ，然後選取 [新增使用者]  。

    ![顯示具有新增使用者的使用者連結螢幕擷取畫面。](./media/askyourteam-tutorial/user2.png)

1. 在 [新增使用者]  區段中，執行下列步驟：

    ![顯示 [新增使用者] 區段的螢幕擷取畫面，您會在其中輸入使用者資訊。](./media/askyourteam-tutorial/user3.png)

    1. 在 [名字]  文字方塊中，輸入使用者的名字。

    1. 在 [姓氏]  文字方塊中，輸入使用者的姓氏。

    1. 在 [電子郵件]  文字方塊中，輸入使用者的電子郵件地址，例如 B.Simon@contoso.com。

    1. 根據您的組織需求，選取使用者的 [角色]  。

    1. 按一下 [檔案]  。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [AskYourTeam] 圖格時，應該會自動登入您已設定 SSO 的 AskYourTeam。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [試用 AskYourTeam 搭配 Azure AD](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
