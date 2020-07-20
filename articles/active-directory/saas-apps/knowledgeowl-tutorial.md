---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 KnowledgeOwl 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 KnowledgeOwl 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e3931baa8bab6f21d0edfefd17b95f7ecf3f7be
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85799814"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 KnowledgeOwl 整合

在本教學課程中，您將了解如何整合 KnowledgeOwl 與 Azure Active Directory (Azure AD)。 在整合 KnowledgeOwl 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 KnowledgeOwl 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 KnowledgeOwl。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 KnowledgeOwl 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* KnowledgeOwl 支援由 **SP 和 IDP** 起始的 SSO
* KnowledgeOwl 支援 **Just In Time** 使用者佈建

## <a name="adding-knowledgeowl-from-the-gallery"></a>從資源庫新增 KnowledgeOwl

若要設定 KnowledgeOwl 與 Azure AD 整合，您需要從資源庫將 KnowledgeOwl 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **KnowledgeOwl**。
1. 從結果面板中選取 [KnowledgeOwl]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>設定及測試 KnowledgeOwl 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 KnowledgeOwl 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 KnowledgeOwl 中相關使用者之間的連結關聯性。

若要設定及測試與 KnowledgeOwl 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 KnowledgeOwl SSO](#configure-knowledgeowl-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 KnowledgeOwl 測試使用者](#create-knowledgeowl-test-user)** - 使 KnowledgeOwl 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [KnowledgeOwl]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > 這些都不是真正的值。 您將需要從實際的識別碼、回覆 URL 及登入 URL 更新這些值，本教學課程稍後會說明這些值。

1. KnowledgeOwl 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，KnowledgeOwl 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性 | 命名空間 |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (原始)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificateraw.png)

1. 在 [設定 KnowledgeOwl]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 KnowledgeOwl 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [KnowledgeOwl]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-knowledgeowl-sso"></a>設定 KnowledgeOwl SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 KnowledgeOwl 公司網站。

1. 按一下 [設定]  ，然後選取 [安全性]  。

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure1.png)

1. 捲動至 [SAML SSO 整合]  ，然後執行下列步驟：

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure2.png)

    a. 選取 [啟用 SAML SSO]  。

    b. 複製 [SP 實體識別碼]  值，並將它貼至 Azure 入口網站中 [基本 SAML 設定]  區段上的 [識別碼 (實體識別碼)]  中。

    c. 複製 [SP 登入 URL]  值，並將它貼至 Azure 入口網站上 [基本 SAML 設定]  區段中的 [登入 URL 和回覆 URL]  文字方塊。

    d. 在 [IdP entityID]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    e. 在 [IdP 登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    f. 在 [IdP 登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值

    g. 按一下 [上傳 IdP 憑證]  ，以上傳從 Azure 入口網站下載的憑證。

    h. 按一下 [對應 SAML 屬性]  以對應屬性，然後執行下列步驟：

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure3.png)

    * 在 [SSO 識別碼]  文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`。
    * 在 [使用者名稱/電子郵件]  文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。
    * 在 [名字]  文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。
    * 在 [姓氏]  文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。
    * 按一下 [儲存] 

    i. 按一下頁面底部的 [儲存]  。

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>建立 KnowledgeOwl 測試使用者

本節會在 KnowledgeOwl 中建立名為 B.Simon 的使用者。 KnowledgeOwl 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 KnowledgeOwl 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!Note]
> 如果您需要手動建立使用者，請連絡 [KnowledgeOwl 支援小組](mailto:support@knowledgeowl.com)。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [KnowledgeOwl] 圖格時，應該會自動登入您已設定 SSO 的 KnowledgeOwl。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 KnowledgeOwl](https://aad.portal.azure.com/)