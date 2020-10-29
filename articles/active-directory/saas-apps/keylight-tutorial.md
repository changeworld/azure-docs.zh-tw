---
title: 教學課程：Azure Active Directory 與 LockPath Keylight 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 LockPath Keylight 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459079"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>教學課程：Azure Active Directory 與 LockPath Keylight 整合

在本教學課程中，您會了解如何整合 LockPath Keylight 與 Azure Active Directory (Azure AD)。
LockPath Keylight 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中管控可存取 LockPath Keylight 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 LockPath Keylight (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 LockPath Keylight 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 啟用 LockPath Keylight 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* LockPath Keylight 支援 **SP** 起始的 SSO
* LockPath Keylight 支援 **Just In Time** 使用者佈建

## <a name="adding-lockpath-keylight-from-the-gallery"></a>從資源庫新增 LockPath Keylight

若要設定將 LockPath Keylight 整合到 Azure AD 中，您需要從資源庫將 LockPath Keylight 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 LockPath Keylight，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **LockPath Keylight** ，從結果面板中選取 [LockPath Keylight]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 LockPath Keylight](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 LockPath Keylight 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 LockPath Keylight 中相關使用者之間的連結關聯性。

若要設定及測試與 LockPath Keylight 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 LockPath Keylight 單一登入](#configure-lockpath-keylight-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 LockPath Keylight 測試使用者](#create-lockpath-keylight-test-user)** - 使 LockPath Keylight 中對應的 Britta Simon 連結到該該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 LockPath Keylight 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [LockPath Keylight]  應用程式整合頁面上，按一下 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![LockPath Keylight 網域與 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<company name>.keylightgrc.com/`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<company name>.keylightgrc.com`

    c. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」、「識別碼」和「回覆 URL」來更新這些值。 請連絡 [LockPath Keylight 客戶支援小組](https://www.lockpath.com/contact/)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (原始)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificateraw.png)

6. 在 [安裝 LockPath Keylight]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>設定 LockPath Keylight 單一登入

1. 若要在 LockPath Keylight 中啟用 SSO，請執行下列步驟：

    a. 以系統管理員身分登入 LockPath Keylight 帳戶。

    b. 在頂端功能表中，按一下 [人員]  ，然後選取 [Keylight 安裝]  。

    ![顯示已選取 [人員] 圖示，並已從下拉式清單中選取 [Keylight 設定] 的螢幕擷取畫面。](./media/keylight-tutorial/401.png)

    c. 在左側樹狀檢視中，按一下 [SAML]  。

    ![顯示已在樹狀檢視中選取 [S A M L] 的螢幕擷取畫面。](./media/keylight-tutorial/402.png)

    d. 在 [SAML 設定]  對話方塊中，按一下 [編輯]  。

    ![顯示 [S A M L 設定] 視窗的螢幕擷取畫面，其中已選取 [編輯] 按鈕。](./media/keylight-tutorial/404.png)

1. 在 [編輯 SAML 設定]  對話方塊頁面上，執行下列步驟：

    ![設定單一登入](./media/keylight-tutorial/405.png)

    a. 將 [SAML 驗證]  設為 **作用中** 。

    b. 在 [識別提供者登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 在 [識別提供者登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    d. 按一下 [選擇檔案]  來選取下載的 LockPath Keylight 憑證，然後按一下 [開啟]  以上傳憑證。

    e. 將 [SAML 使用者識別碼位置]  設定為 [Subject 陳述式的 NameIdentifier 元素]  。

    f. 使用下列模式提供 [Keylight 服務提供者]  ︰`https://<CompanyName>.keylightgrc.com`。

    g. 將 [自動佈建使用者]  設定為 [作用中]  。

    h. 將 [自動佈建帳戶類型]  設定為 [完整使用者]  。

    i. 設定 [自動佈建安全性角色]  ，選取 [具備 SAML 的標準使用者]  。

    j. 設定 [自動佈建安全性設定]  ，選取 [標準使用者設定]  。

    k. 在 [電子郵件屬性]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    l. 在 [名字屬性]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    m. 在 [姓氏屬性]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

    n. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 LockPath Keylight 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [LockPath Keylight]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [LockPath Keylight]  。

    ![應用程式清單中的 LockPath Keylight 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-lockpath-keylight-test-user"></a>建立 LockPath Keylight 測試使用者

在本節中，您會在 LockPath Keylight 中建立名為 Britta Simon 的使用者。 LockPath Keylight 支援預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 LockPath Keylight 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。 如果您需要手動建立使用者，則必須連絡 [LockPath Keylight 客戶支援小組](https://www.lockpath.com/contact/)。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [LockPath Keylight] 圖格時，應該會自動登入您設定 SSO 的 LockPath Keylight。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)