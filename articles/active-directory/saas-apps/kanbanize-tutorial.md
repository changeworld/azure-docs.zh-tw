---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Kanbanize 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Kanbanize 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 686a186650bfe4066533b0f086c080b5ffb14eb6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546826"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Kanbanize 整合

在本教學課程中，您將了解如何整合 Kanbanize 與 Azure Active Directory (Azure AD)。 在整合 Kanbanize 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Kanbanize 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Kanbanize。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Kanbanize 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Kanbanize 支援 **SP 和 IDP** 起始的 SSO
* Kanbanize 支援 **Just In Time** 使用者佈建

## <a name="adding-kanbanize-from-the-gallery"></a>從資源庫新增 Kanbanize

若要設定 Kanbanize 與 Azure AD 整合，您需要從資源庫將 Kanbanize 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Kanbanize**。
1. 從結果面板選取 [Kanbanize]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>設定及測試 Kanbanize 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Kanbanize 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Kanbanize 中相關使用者之間的連結關聯性。

若要設定及測試與 Kanbanize 搭配運作的 Azure AD SSO，請完成下列建構元素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Kanbanize SSO](#configure-kanbanize-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Kanbanize 測試使用者](#create-kanbanize-test-user)** - 讓 Kanbanize 中對應的 B.Simon 連結到使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Kanbanize]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

     a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.kanbanize.com/`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.kanbanize.com/saml/acs`

    c. 按一下 [設定額外的 URL]  。

    d. 在 [轉送狀態]  文字方塊中，輸入 URL：`/ctrl_login/saml_login`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [Kanbanize 用戶端支援小組](mailto:support@ms.kanbanize.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. Kanbanize 應用程式需要特定格式的 SAML 判斷提示，因此您需要新增可對應到 SAML 權杖屬性設定的自訂屬性。 下列螢幕擷取畫面顯示預設屬性清單，其中的 nameidentifier 與 **user.userprincipalname** 相對應。 Kanbanize 應用程式要求 nameidentifier 需與 **user.mail** 相對應，因此您必須按一下 [編輯] 圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Kanbanize]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Kanbanize 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Kanbanize]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-kanbanize-sso"></a>設定 Kanbanize SSO

1. 若要自動執行 Kanbanize 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Kanbanize]  便會將您導向到 Kanbanize 應用程式。 請從該處提供用以登入 Kanbanize 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Kanbanize，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Kanbanize 公司網站，然後執行下列步驟：

4. 移至頁面的右上方，按一下 [設定]  標誌。

    ![Kanbanize 設定](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. 從功能表左側的 [系統管理面板] 頁面上，按一下 [整合]  ，然後再啟用 [單一登入]  。

    ![Kanbanize 整合](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. 在 [整合] 區段下，按一下 [設定]  來開啟 [單一登入整合]  頁面。

    ![Kanbanize 組態](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. 在 [單一登入整合]  頁面的 [設定]  底下，執行下列步驟：

    ![Kanbanize 整合](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. 在 [Idp 實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    b. 在 [Idp 登入端點]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 在 [Idp 登出端點]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    d. 在 [電子郵件的屬性名稱]  文字方塊中，輸入此值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. 在 [名字的屬性名稱]  文字方塊中，輸入此值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. 在 [姓氏的屬性名稱]  文字方塊中，輸入此值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > 您可以從 Azure 入口網站的 [使用者屬性] 區段中，結合命名空間與個別屬性的名稱值來取得這些值。

    g. 在「記事本」中開啟從 Azure 入口網站下載的 Base 64 編碼憑證，複製其內容 (不含開始和結束標記)，然後貼到 [Idp X.509 憑證]  方塊中。

    h. 勾選 [使用 SSO 和 Kanbanize 啟用登入]  。

    i. 按一下 [儲存設定]  。

### <a name="create-kanbanize-test-user"></a>建立 Kanbanize 測試使用者

本節會在 Kanbanize 中建立名為 B.Simon 的使用者。 Kanbanize 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Kanbanize 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。 如果您需要手動建立使用者，請連絡 [Kanbanize 用戶端支援小組](mailto:support@ms.kanbanize.com)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Kanbanize 圖格時，應該會自動登入您設定 SSO 的 Kanbanize。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Kanbanize](https://aad.portal.azure.com/)

