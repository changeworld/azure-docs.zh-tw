---
title: 教學課程：Azure Active Directory 與 Cezanne HR Software 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cezanne HR Software 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: b5739ea49bb9f5823538e9a41660a572a3cede04
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760714"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>教學課程：Azure Active Directory 與 Cezanne HR Software 整合

在此教學課程中，您將了解如何整合 Cezanne HR Software 與 Azure Active Directory (Azure AD)。
Cezanne HR Software 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Cezanne HR Software 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Cezanne HR Software (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Cezanne HR Software 整合，您需要以下項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Cezanne HR Software 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Cezanne HR Software 支援由 **SP** 起始的 SSO

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>從資源庫加入 Cezanne HR Software

若要設定 Cezanne HR Software 與 Azure AD 整合，您需要從資源庫將 Cezanne HR Software 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Cezanne HR Software，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Cezanne HR Software**，從結果面板中選取 [Cezanne HR Software]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 [Cezanne HR Software]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Cezanne HR Software 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Cezanne HR Software 中相關使用者之間的連結關聯性。

若要使用 Cezanne HR Software 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Cezanne HR Software 單一登入](#configure-cezanne-hr-software-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Cezanne HR Software 測試使用者](#create-cezanne-hr-software-test-user)** - 在 Cezanne HR Software 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Cezanne HR Software 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Cezanne HR Software]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Cezanne HR Software 網域與 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入 URL：`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「回覆 URL」來更新這些值。 若要取得這些值，請連絡 [Cezanne HR Software 用戶端支援小組](https://cezannehr.com/services/support/)。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Cezanne HR Software]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>設定 Cezanne HR Software 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Cezanne HR Software 租用戶。

2. 在左側的導覽窗格上，按一下 [系統設定]  。 移至 [安全性設定]  。 然後瀏覽至 [單一登入設定]  。

    ![顯示 Cezanne HR Software 租用戶的螢幕擷取畫面，其中已選取 [安全性設定] 和 [單一登入設定]。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. 在 [允許使用者使用下列的單一登入 (SSO) 服務來登入]  面板中檢查 [SAML 2.0]  方塊，然後選取 [進階組態]  選項。

    ![顯示 [允許使用者] 窗格的螢幕擷取畫面，其中已選取 [SAML 2.0] 和 [進階設定]。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. 按一下 [新增]  按鈕。

    ![顯示 [新增] 按鈕的螢幕擷取畫面。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. 在 [SAML 2.0 身分識別提供者]  區段中執行下列步驟。

    ![顯示窗格的螢幕擷取畫面，您可以在其中輸入此步驟中所述的值。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. 輸入識別提供者名稱做為**顯示名稱**。

    b. 在 [實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    c. 將 [SAML 繫結]  變更為 'POST'。

    d. 在 [Security Token Service 端點]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    e. 在 [使用者識別碼屬性名稱] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    f. 按一下 [上傳]  圖示來上傳從 Azure 入口網站下載的憑證。

    g. 按一下 [確定]  按鈕。

6. 按一下 [儲存]  按鈕。

    ![螢幕擷取畫面，顯示 [單一登入設定] 的 [儲存] 按鈕。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Cezanne HR Software 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Cezanne HR Software]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Cezanne HR Software]  。

    ![應用程式清單中的 [Cezanne HR Software] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-cezanne-hr-software-test-user"></a>建立 Cezanne HR Software 測試使用者

為了讓 Azure AD 使用者能夠登入 Cezanne HR Software，必須將他們佈建到 Cezanne HR Software 中。 Cezanne HR Software 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Cezanne HR Software 公司網站。

2. 在左側的導覽窗格上，按一下 [系統設定]  。 移至 [管理使用者]  。 然後瀏覽至 [新增使用者]  。

    ![顯示 Cezanne HR Software 租用戶的螢幕擷取畫面，其中已選取 [管理使用者] 和 [新增使用者]。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "新增使用者")

3. 在 [人員詳細資料]  區段中，執行下列步驟︰

    ![顯示 [人員詳細資料] 區段的螢幕擷取畫面，您可以在其中輸入此步驟中所述的值。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "新增使用者")

    a. 將 [內部使用者]  設定為 OFF。

    b. 在 [名字]  文字方塊中，輸入使用者的名字，例如 **Britta**。  

    c. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    d. 在 [電子郵件]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件地址。

4. 在 [帳戶資訊]  區段中，執行下列步驟︰

    ![顯示 [帳戶資訊] 區段的螢幕擷取畫面，您可以在其中輸入此步驟中所述的值。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "新增使用者")

    a. 在 [使用者名稱]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件。

    b. 在 [密碼]  文字方塊中，輸入使用者的密碼。

    c. 選取 [HR Professional]  做為 [安全性角色]  。

    d. 按一下 [確定]  。

5. 瀏覽至 [單一登入] 索引標籤並選取 [SAML 2.0 識別碼] 區域中的 [新增]。

    ![顯示 [單一登入] 索引標籤的螢幕擷取畫面，您可以在其中選取 [新增]。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")

6. 為 [識別提供者]  選擇識別提供者，並在 [使用者識別碼]  文字方塊中輸入 Britta Simon 帳戶的電子郵件地址。

    ![顯示 SAML 2.0 識別碼的螢幕擷取畫面，您可以在其中選取您的識別提供者和使用者識別碼。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")

7. 按一下 [儲存]  按鈕。

    ![螢幕擷取畫面，顯示 [使用者設定] 的 [儲存] 按鈕。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Cezanne HR Software] 圖格時，應該會自動登入您已設定 SSO 的 Cezanne HR Software。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
