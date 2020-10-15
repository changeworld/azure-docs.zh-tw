---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Ekarda 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Ekarda 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: cfd7a007c8f26a96d929026e231cb412cdc70279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440842"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Ekarda 整合

在本教學課程中，您會了解如何將Ekarda 與 Azure Active Directory (Azure AD) 進行整合。 在整合 Ekarda 與 Azure AD 後，您可以︰

* 在 Azure AD 中控制可存取 Ekarda 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Ekarda。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是單一登入 (SSO)？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Ekarda 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Ekarda 支援 SP 和 IDP 起始的 SSO。
* Ekarda 支援 Just-In-Time 使用者佈建。
* 設定 Ekarda 後，您可以強制執行工作階段控制項。 這項預防措施可即時保護貴組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取應用程式控制延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-ekarda-from-the-gallery"></a>從資源庫新增 Ekarda

若要設定將 Ekarda 整合到 Azure AD 中，請從資源庫將 Ekarda 新增到受控 SaaS 應用程式清單：

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Ekarda**。
1. 從結果面板選取 [Ekarda]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>設定及測試 Ekarda 的 Azure AD 單一登入

使用名為 **B.Simon** 的測試使用者，設定及測試與 Ekarda 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Ekarda 中相關使用者之間的連結關聯性。

若要設定及測試與 Ekarda 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。

    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. [設定 Ekarda SSO](#configure-ekarda-sso)以在應用程式端設定單一登入設定。
    * [建立 Ekarda 測試使用者](#create-an-ekarda-test-user)，以使 Ekarda 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照 Azure 入口網站中的下列步驟，啟用 Azure AD SSO：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在 **Ekarda** 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取鉛筆圖示以編輯**基本 SAML 組態**的設定。

   ![以 SAML 設定單一登入頁面的螢幕擷取畫面，其中的鉛筆圖示已醒目提示。](common/edit-urls.png)

1. 如果您看見**服務提供者中繼資料檔案**，請在 [基本 SAML 設定] 區段中遵循下列步驟：
    1. 選取 [上傳中繼資料檔案]****。
    1. 選取資料夾圖示以選取中繼資料檔案，然後選取 [上傳]。
    1. 成功上傳中繼資料檔案後，Ekarda 區段文字方塊中會自動出現 [識別碼] 和 [回覆 URL]。

    > [!Note]
    > 如果 [識別碼] 和 [回覆 URL] 值未自動出現，請根據您的需求手動填入這些值。

1. 如果您在 [基本 SAML 設定] 區段中看不到**服務提供者中繼資料檔案**，而且想要以 IDP 起始模式設定應用程式，請輸入下列欄位的值：

    1. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL︰`https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL] 並執行下列動作：

    * 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > 上述兩個步驟中的值不是真實的。 請使用實際的識別碼、回覆 URL 及登入 URL 值來更新它們。 請連絡 [Ekarda 用戶端支援小組](mailto:contact@ekarda.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取 [下載] 以在電腦上儲存 [憑證 (Base64)]。

    ![[以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段的螢幕擷取畫面，其中已針對 Base64 憑證醒目提示下載連結。](common/certificatebase64.png)

1. 在 [設定 Ekarda] 區段中，根據您的需求複製適當的 URL。

    ![[以 SAML 設定單一登入] 頁面的 [設定 Ekarda] 區段的螢幕擷取畫面，其中已醒目提示 URL 複製連結。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將使用 Azure 入口網站來建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如，輸入 `B.Simon@contoso.com`。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Ekarda 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [Ekarda]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![管理區段的螢幕擷取畫面，醒目提示使用者和群組。](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[使用者和群組] 區段的螢幕擷取畫面，其中已醒目提示 [新增使用者]。](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 然後，選擇畫面底部的 [選取] 按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請從 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後，選擇畫面底部的 [選取] 按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-ekarda-sso"></a>設定 Ekarda SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Ekarda 公司網站。
1. 選取 [系統管理員] > [我的帳戶]。

    ![[管理] 功能表上已醒目提示 [我的帳戶] 的 Ekarda 網站 UI 螢幕擷取畫面。](./media/ekarda-tutorial/ekarda.png)

1. 在頁面底部，找出 [SAML 設定] 區段。 這一節是您設定 SAML 整合的地方。
1. 在 [SAML 設定] 區段中，依照下列步驟：

    ![已反白顯示 SAML 設定欄位之 Ekarda SAML 設定頁面的螢幕擷取畫面。](./media/ekarda-tutorial/ekarda1.png)

    1. 選取 [服務提供者中繼資料] 連結，並將其儲存為您電腦中的檔案。
    1. 選取 [啟用 SAML] 核取方塊。
    1. 在 [IDP 實體識別碼] 文字方塊中，貼上您稍早從 Azure 入口網站複製的 [Azure AD 識別碼] 值。
    1. 在 [IDP 登入 URL] 文字方塊中，貼上您稍早從 Azure 入口網站複製的 [登入 URL] 值。
    1. 在 [IDP 登出 URL] 文字方塊中，貼上您稍早從 Azure 入口網站複製的 [登出 URL] 值。
    1. 使用 [記事本] 來開啟您從 Azure 入口網站下載的 [憑證 (Base64)] 檔案。 將該內容貼入 [IDP x509 憑證] 文字方塊中。
    1. 在 [選項] 區段中，選取 [啟用 SLO] 核取方塊。
    1. 選取 [更新]。

### <a name="create-an-ekarda-test-user"></a>建立 Ekarda 測試使用者

本節會在 Ekarda 中建立名為 B.Simon 的使用者。 Ekarda 支援依預設啟用的 Just-In-Time 使用者佈建。 您在此區段中不需採取任何動作。 如果 Ekarda 中還沒有名為 B. Simon 的使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在 [我的應用程式] 入口網站中選取 Ekarda 圖格時，應該會自動登入您設定 SSO 的 Ekarda 網站。 如需「我的應用程式」入口網站的詳細資訊，請參閱[我的應用程式入口網站簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

* [用於整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
* [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
* [嘗試搭配 Azure AD 使用 Ekarda](https://aad.portal.azure.com/)
* 使用 [Ekarda 的企業 eCard 解決方案](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise)來佈建任何數量的人員，以將加上您公司標誌的 eCard 傳送給用戶端和同事。 深入了解如何將 [Ekarda 佈建為 SSO 解決方案](https://support.ekarda.com/#SSO-Implementation)。
* [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [如何使用進階可見性和控制項保護 Ekarda](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
