---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Nintex Promapp 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Nintex Promapp 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: 3b4102b4e8dcbd5aa04e354cec0ab91bf139572c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553473"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Nintex Promapp 整合

在本教學課程中，您將了解如何整合 Nintex Promapp 與 Azure Active Directory (Azure AD)。 在整合 Nintex Promapp 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Nintex Promapp 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Nintex Promapp。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Nintex Promapp 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Nintex Promapp 支援由 **SP 和 IDP** 起始的 SSO
* Nintex Promapp 支援 **Just In Time** 使用者佈建

## <a name="adding-nintex-promapp-from-the-gallery"></a>從資源庫新增 Nintex Promapp

若要進行將 Nintex Promapp 整合到 Azure AD 中的設定，您必須從資源庫將 Nintex Promapp 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Nintex Promapp**。
1. 從結果面板中選取 [Nintex Promapp]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>設定及測試 Nintex Promapp 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Nintex Promapp 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Nintex Promapp 中相關使用者之間的連結關聯性。

若要設定及測試與 Nintex Promapp 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Nintex Promapp SSO](#configure-nintex-promapp-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Nintex Promapp 測試使用者](#create-nintex-promapp-test-user)** - 使 Nintex Promapp 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Nintex Promapp]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    1. 在 [識別碼]  方塊中，輸入以下模式的 URL：

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > 目前只能將 Azure AD 與 Nintex Promapp 的整合設定用於服務起始的驗證。 (也就是，移至 Nintex Promapp URL 會起始驗證程序。)但 [回覆 URL]  欄位是必要欄位。

    1. 在 [回覆 URL]  方塊中，輸入以下模式的 URL：

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  方塊中，輸入以下模式的 URL：`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > 這些值都是預留位置。 您需要使用實際的「識別碼」、「回覆 URL」及「登入 URL」。 請連絡 [Nintex Promapp 支援小組](https://www.promapp.com/about-us/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中的 [基本 SAML 組態]  對話方塊所顯示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Nintex Promapp]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Nintex Promapp 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Nintex Promapp]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-nintex-promapp-sso"></a>設定 Nintex Promapp SSO

1. 以系統管理員身分登入您的 Nintex Promapp 公司網站。

2. 在視窗頂端的功能表中，選取 [管理員]  ：

    ![選取管理員][12]

3. 選取 [設定]  ：

    ![選取設定][13]

4. 在 [安全性]  對話方塊中，採取下列步驟。

    ![[安全性] 對話方塊][14]

    1. 將您從 Azure 入口網站複製的 [登入 URL]  貼到 [SSO 登入 URL]  方塊中。

    1. 在 [SSO - 單一登入模式]  清單中，選取 [選用]  。 選取 [儲存]  。

       > [!NOTE]
       > 選用模式僅供測試使用。 對設定感到滿意之後，選取 [SSO - 單一登入模式]  清單中的 [必要]  ，以強制所有使用者使用 Azure AD 進行驗證。

    1. 在 [記事本] 中，開啟您在上一節下載的憑證。 複製憑證的內容，但不要複製第一行 ( **-----BEGIN CERTIFICATE-----** ) 或最後一行 ( **-----END CERTIFICATE-----** )。 將憑證內容貼到 [SSO-x.509 憑證]  方塊，然後選取 [儲存]  。

### <a name="create-nintex-promapp-test-user"></a>建立 Nintex Promapp 測試使用者

本節會在 Nintex Promapp 中建立名為 B.Simon 的使用者。 Nintex Promapp 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Nintex Promapp 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Nintex Promapp] 圖格時，應該會自動登入您已設定 SSO 的 Nintex Promapp。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Nintex Promapp](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png