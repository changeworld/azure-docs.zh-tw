---
title: 教學課程：Azure Active Directory 與 Blackboard Learn - Shibboleth 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Blackboard Learn - Shibboleth 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 0355c8dc682ed45865a65d59b8b4810a85d588fa
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621193"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>教學課程：Azure Active Directory 與 Blackboard Learn - Shibboleth 整合

在本教學課程中，您將瞭解如何整合「Blackboard 學習-Shibboleth」與 Azure Active Directory (Azure AD) 。 當您將 Blackboard 學習 Shibboleth 與 Azure AD 整合時，您可以：

* 在 Azure AD 中控制可存取 Blackboard 學習 Shibboleth 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Blackboard 學習-Shibboleth。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：
 
* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* Shibboleth 單一登入 (SSO) 啟用的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Blackboard Learn - Shibboleth 支援由 **SP** 起始的 SSO

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>從資源庫新增 Blackboard 學習-Shibboleth

若要設定將 Blackboard Learn - Shibboleth 整合到 Azure AD 中，您需要從資源庫將 Blackboard Learn - Shibboleth 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中輸入 **黑板學習-Shibboleth** 。
1. 從結果面板選取 [ **Blackboard 學習-Shibboleth** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>設定及測試適用于 Blackboard 的 Azure AD SSO 學習-Shibboleth

以名為 **Simon** 的測試使用者，設定及 Azure AD 測試與 Blackboard Shibboleth 搭配的 SSO 學習-。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Blackboard 學習-Shibboleth 中相關使用者之間的連結關聯性。

若要設定及測試 Azure AD 與 Blackboard 的 SSO 學習 Shibboleth，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Blackboard 學習-SHIBBOLETH SSO](#configure-blackboard-learn---shibboleth-sso)** -在應用程式端設定單一登入設定。
    1. **[建立 Blackboard Shibboleth 測試使用者](#create-blackboard-learn---shibboleth-test-user)** -使在 Blackboard 中 Simon 的對應使用者連結到使用者的 Azure AD 標記法。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Blackboard Learn - Shibboleth 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 Azure 入口網站的 [ **Blackboard Shibboleth** 應用程式整合] 頁面上，選取 [ **單一登入**]。

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

3. 在 [ **以 SAML 設定單一 Sign-On** ] 頁面上，按一下 [鉛筆] 圖示以開啟 [ **基本 SAML** 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. 在 [識別碼] 方塊中，使用下列模式輸入 URL：`https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Blackboard Learn - Shibboleth 用戶端支援小組](https://www.blackboard.com/forms/contact-us_form.aspx)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Blackboard Learn - Shibboleth]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Blackboard 學習-Shibboleth 的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Blackboard Learn - Shibboleth]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="configure-blackboard-learn---shibboleth-sso"></a>設定 Blackboard 學習-Shibboleth SSO

若要在 **Blackboard Learn - Shibboleth** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和複製的適當 URL 傳送給 [Blackboard Learn - Shibboleth 支援小組](https://www.blackboard.com/forms/contact-us_form.aspx)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-blackboard-learn---shibboleth-test-user"></a>建立 Blackboard Learn - Shibboleth 測試使用者

在本節中，您會在 Blackboard Learn - Shibboleth 中建立名為 Britta Simon 的使用者。 請與 [Blackboard Learn - Shibboleth 支援小組](https://www.blackboard.com/forms/contact-us_form.aspx)合作，在 Blackboard Learn - Shibboleth 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以起始登入流程的黑板學習 Shibboleth 登入 URL。 

* 直接前往 [Blackboard 學習-Shibboleth 登入 URL]，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [Blackboard 學習-Shibboleth] 圖格時，應該會自動登入您已設定 SSO 的 [Blackboard 學習-Shibboleth]。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Blackboard 學習 Shibboleth 之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
