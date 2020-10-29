---
title: 教學課程：Azure Active Directory 與 Kantega SSO for Bamboo 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Kantega SSO for Bamboo 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: aa5f908cdf25925db63054adaf1e6dab15f5260b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459300"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>教學課程：Azure Active Directory 與 Kantega SSO for Bamboo 整合

在本教學課程中，您會了解如何整合 Kantega SSO for Bamboo 與 Azure Active Directory (Azure AD)。
Kantega SSO for Bamboo 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Kantega SSO for Bamboo 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Kantega SSO for Bamboo (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Kantega SSO for Bamboo 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Kantega SSO for Bamboo 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Kantega SSO for Bamboo 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>從資源庫新增 Kantega SSO for Bamboo

若要設定將 Kantega SSO for Bamboo 整合到 Azure AD 中，您需要從資源庫將 Kantega SSO for Bamboo 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Kantega SSO for Bamboo，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Kantega SSO for Bamboo** ，從結果面板中選取 [Kantega SSO for Bamboo]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Kantega SSO for Bamboo](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Kantega SSO for Bamboo 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Kantega SSO for Bamboo 中相關使用者之間的連結關聯性。

若要設定及測試與 Kantega SSO for Bamboo 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Kantega SSO for Bamboo 單一登入](#configure-kantega-sso-for-bamboo-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Kantega SSO for Bamboo 測試使用者](#create-kantega-sso-for-bamboo-test-user)** - 使 Kantega SSO for Bamboo 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Kantega SSO for Bamboo 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Kantega SSO for Bamboo]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![顯示基本 SAML 設定的螢幕擷取畫面，您可以在其中輸入識別碼、回覆 URL 以及選取 [儲存]。](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![顯示您可以在其中輸入登入 URL 的設定額外 URL 螢幕擷取畫面。](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 在設定 Bamboo 外掛程式 (本教學課程稍後會說明) 期間會收到這些值。

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML** ，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [設定 Kantega SSO for Bamboo]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>設定 Kantega SSO for Bamboo 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Bamboo 內部部署伺服器。

1. 將滑鼠停留在 cog 上，然後按一下 [附加元件]  。

    ![此螢幕擷取畫面顯示已從 [設定] 功能表中選取附加元件。](./media/kantegassoforbamboo-tutorial/addon1.png)

1. 在附加元件索引標籤區段下，按一下 [尋找新的附加元件]  。 搜尋 **Kantega SSO for Bamboo (SAML & Kerberos)** ，然後按一下 [安裝]  按鈕以安裝新的 SAML 外掛程式。

    ![此螢幕擷取畫面顯示已選取 Kantega SSO for Bamboo 的 Bamboo 系統管理。](./media/kantegassoforbamboo-tutorial/addon2.png)

1. 外掛程式將會開始安裝。

    ![此螢幕擷取畫面顯示 Kantega SSO for Bamboo 的安裝進度。](./media/kantegassoforbamboo-tutorial/addon21.png)

1. 當安裝完成時。 按一下 [關閉]  。

    ![顯示 [關閉] 按鈕的螢幕擷取畫面。](./media/kantegassoforbamboo-tutorial/addon33.png)

1. 按一下 [管理]  。

    ![顯示 [管理] 按鈕的螢幕擷取畫面。](./media/kantegassoforbamboo-tutorial/addon34.png)

1. 按一下 [設定]  來設定新的外掛程式。

    ![此螢幕擷取畫面顯示已選取 [設定] 的使用者安裝附加元件。](./media/kantegassoforbamboo-tutorial/addon3.png)

1. 在 [SAML]  區段中。 從 [新增識別提供者] 下拉式清單中，選取 [Azure Active Directory]\(Azure AD\)。

    ![此螢幕擷取畫面顯示已選取 Azure AD 作為身分識別提供者的 Kantega 單一登入。](./media/kantegassoforbamboo-tutorial/addon4.png)

1. 選取 [基本]  作為訂用帳戶層級。

    ![此螢幕擷取畫面顯示已選取 [基本] 的 [準備 Azure AD]。](./media/kantegassoforbamboo-tutorial/addon5.png)

1. 在 [應用程式屬性]  區段中，執行下列步驟：

    ![此螢幕擷取畫面顯示用來在此步驟中提供資訊的 [應用程式屬性] 區段。](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. 複製 [應用程式識別碼 URI] 值，然後在 Azure 入口網站的 [基本 SAML 設定] 區段上，使用此 URI 作為 [識別碼]、[回覆 URL] 和 [登入 URL]。

    b. 按 [下一步]  。

1. 在 [中繼資料匯入]  區段中，執行下列步驟：

    ![此螢幕擷取畫面顯示您可以在其中瀏覽至中繼資料檔案的 [中繼資料匯入] 區段。](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. 選取 [我的電腦上的中繼資料檔案]  ，上傳您從 Azure 入口網站下載的中繼資料檔案。

    b. 按 [下一步]  。

1. 在 [名稱和 SSO 位置]  區段中，執行下列步驟：

    ![此螢幕擷取畫面顯示其中 Azure AD 是身分識別提供者名稱的 [名稱和 SSO 位置]。](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. 在 [識別提供者名稱]  文字方塊中，新增識別提供者的名稱 (例如 Azure AD)。

    b. 按 [下一步]  。

1. 確認簽署憑證，然後按 [下一步]  。

    ![此螢幕擷取畫面會顯示簽章驗證。](./media/kantegassoforbamboo-tutorial/addon9.png)

1. 在 [Bamboo 使用者帳戶]  區段中，執行下列步驟：

    ![此螢幕擷取畫面顯示其中具有建立使用者選項的 Bamboo 使用者帳戶。](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. 選取 [如果需要，在 Bamboo 的內部目錄中建立使用者]  ，然後輸入使用者群組的適當名稱 (可以是多個 群組，以逗號分隔)。

    b. 按 [下一步]  。

1. 按一下 [完成]  。

    ![顯示 [摘要] 頁面的螢幕擷取畫面。](./media/kantegassoforbamboo-tutorial/addon11.png)

1. 在 [Azure AD 的已知網域]  區段中，執行下列步驟：

    ![此螢幕擷取畫面顯示您可以在其中執行這些步驟的 Azure AD 已知網域。](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. 從頁面的左面板中，選取 [已知網域]  。

    b. 在 [已知網域]  文字方塊中，輸入網域名稱。

    c. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Kantega SSO for Bamboo 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Kantega SSO for Bamboo]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Kantega SSO for Bamboo]  。

    ![應用程式清單中的 [Kantega SSO for Bamboo] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-kantega-sso-for-bamboo-test-user"></a>建立 Kantega SSO for Bamboo 測試使用者

若要讓 Azure AD 使用者能夠登入 Bamboo，必須將他們佈建到 Bamboo。 在 Kantega SSO for Bamboo 的案例中，佈建是手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Bamboo 內部部署伺服器。

1. 將滑鼠停留在 cog 上，然後按一下 [使用者管理]  。

    ![顯示已從 [設定] 功能表選取 [使用者管理] 的螢幕擷取畫面。](./media/kantegassoforbamboo-tutorial/user1.png)

1. 按一下 [使用者]  。 在 [新增使用者]  區段下，執行下列步驟：

    ![此螢幕擷取畫面顯示您可以在其中執行這些步驟的 [新增使用者] 窗格。](./media/kantegassoforbamboo-tutorial/user2.png)

    a. 在 [使用者名稱]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件。

    b. 在 [密碼]  文字方塊中，輸入使用者的密碼。

    c. 在 [確認密碼]  文字方塊中，再次輸入使用者的密碼。

    d. 在 [全名]  文字方塊中，輸入像是 Britta Simon 的使用者全名。

    e. 在 [電子郵件]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件地址。

    f. 按一下 [檔案]  。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Kantega SSO for Bamboo] 圖格時，應該會自動登入您已設定 SSO 的 Kantega SSO for Bamboo。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)