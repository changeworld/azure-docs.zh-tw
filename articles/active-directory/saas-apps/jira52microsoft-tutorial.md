---
title: 教學課程：Azure Active Directory 與 JIRA SAML SSO by Microsoft (V5.2) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 JIRA SAML SSO by Microsoft (V5.2) 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b304bb35ea69906fc9576f45733134387be1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099507"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>教學課程：Azure Active Directory 與 JIRA SAML SSO by Microsoft (V5.2) 整合

在本教學課程中，您將了解如何整合 JIRA SAML SSO by Microsoft (V5.2) 與 Azure Active Directory (Azure AD)。
JIRA SAML SSO by Microsoft (V5.2) 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 JIRA SAML SSO by Microsoft (V5.2) 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 JIRA SAML SSO by Microsoft (V5.2) (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="description"></a>描述

使用 Microsoft Azure Active Directory 帳戶搭配 Atlassian JIRA 伺服器來啟用單一登入。 如此一來，您組織的所有使用者都能使用 Azure AD 認證來登入 JIRA 應用程式。 此外掛程式使用 SAML 2.0 來進行同盟。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 JIRA SAML SSO by Microsoft (V5.2) 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 應該在 Windows 64 位元版本上安裝及設定 JIRA 核心和軟體 5.2
- JIRA 伺服器已啟用 HTTPS
- 請注意下一節提及支援的 JIRA 外掛程式版本。
- JIRA 伺服器可從網際網路連上，特別是連線至 Azure AD 登入頁面來進行驗證，且應該能夠接收來自 Azure AD 的權杖
- JIRA 中已設定管理員認證
- JIRA 中已停用 WebSudo
- JIRA 伺服器應用程式中建立的測試使用者

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用 JIRA 的生產環境。 先在應用程式的開發或預備環境中測試整合，然後使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在這裡取得一個月試用：[試用供應項目](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="supported-versions-of-jira"></a>支援的 JIRA 版本

* JIRA 核心和軟體：5.2
* JIRA 也支援 6.0 至 7.12。 如需詳細資料，請按一下 [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> 請注意，我們的 JIRA 外掛程式也適用於 Ubuntu 16.04 版

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* JIRA SAML SSO by Microsoft (V5.2) 支援由 **SP** 起始的 SSO

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>從資源庫新增 JIRA SAML SSO by Microsoft (V5.2)

若要設定以將 JIRA SAML SSO by Microsoft (V5.2) 整合到 Azure AD 中，您需要從資源庫將 JIRA SAML SSO by Microsoft (V5.2) 新增到受控的 SaaS 應用程式清單。

**若要從資源庫新增 JIRA SAML SSO by Microsoft (V5.2)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **JIRA SAML SSO by Microsoft (V5.2)** ，從結果面板中選取 [JIRA SAML SSO by Microsoft (V5.2)]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 JIRA SAML SSO by Microsoft (V5.2)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 JIRA SAML SSO by Microsoft (V5.2) 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 JIRA SAML SSO by Microsoft (V5.2) 中相關使用者之間的連結關聯性。

若要使用 JIRA SAML SSO by Microsoft (V5.2) 設定和測試 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 JIRA SAML SSO by Microsoft (V5.2) 單一登入](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 JIRA SAML SSO by Microsoft (V5.2) 測試使用者](#create-jira-saml-sso-by-microsoft-v52-test-user)** - 使 JIRA SAML SSO by Microsoft (V5.2) 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 JIRA SAML SSO by Microsoft (V5.2) 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [JIRA SAML SSO by Microsoft (V5.2)]  應用程式整合頁面中，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![JIRA SAML SSO by Microsoft (V5.2) 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<domain:port>/plugins/servlet/saml/auth`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`https://<domain:port>/`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 如果連接埠是具名 URL，則為選擇性。 在設定 Jira 外掛程式 (本教學課程稍後會說明) 期間會收到這些值。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>設定 JIRA SAML SSO by Microsoft (V5.2) 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 JIRA 執行個體。

2. 將滑鼠停留在 cog 上，然後按一下 [附加元件]  。

    ![設定單一登入](./media/jira52microsoft-tutorial/addon1.png)

3. 在 [附加元件] 索引標籤區段下，按一下 [管理附加元件]  。

    ![設定單一登入](./media/jira52microsoft-tutorial/addon7.png)

4. 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=56521)下載外掛程式。 使用 [上傳附加元件]  功能表來手動上傳 Microsoft 所提供的外掛程式。 外掛程式下載受到 [Microsoft 服務合約](https://www.microsoft.com/servicesagreement/)的規範。

    ![設定單一登入](./media/jira52microsoft-tutorial/addon12.png)

5. 安裝外掛程式之後，它會出現在 [使用者安裝的附加元件]  區段中。 按一下 [設定]  來設定新的外掛程式。

    ![設定單一登入](./media/jira52microsoft-tutorial/addon13.png)

6. 在設定頁面上執行下列步驟：

    ![設定單一登入](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > 請確定只有一個對應至應用程式的憑證，解析中繼資料時就不會發生錯誤。 如果有多個憑證，則解析中繼資料時，管理員會遇到錯誤。

    a. 在 [中繼資料 URL]  文字方塊中，貼上您從 Azure 入口網站複製的**應用程式同盟中繼資料 Url**值，然後按一下 [解析]  按鈕。 這樣會讀取 IdP 中繼資料 URL 並填入所有欄位資訊。

    b. 複製 [識別碼]、[回覆 URL] 和 [登入 URL]  值，然後在 Azure 入口網站的 [基本 SAML 組態]  區段中，分別貼到 [識別碼]、[回覆 URL] 和 [登入 URL]  文字方塊中。

    c. 在 [登入按鈕名稱]  中，輸入您的組織要讓使用者在登入畫面上看到的按鈕名稱。

    d. 在 [SAML 使用者識別碼位置]  中，選取 [使用者識別碼在 Subject 陳述式的 NameIdentifier 元素中]  或 [使用者識別碼在 Attribute 元素中]  。  此識別碼必須為 JIRA 使用者識別碼。 如果使用者識別碼不相符，系統就不會允許使用者登入。

    > [!Note]
    > 預設 SAML 使用者識別碼位置是名稱識別碼。 您可以將它變更為屬性選項，並輸入適當的屬性名稱。

    e. 如果您選取 [使用者識別碼在 Attribute 元素中]  選項，請在 [屬性名稱]  文字方塊中，輸入會預期使用者識別碼的屬性名稱。 

    f. 如果您使用同盟網域 (例如 ADFS 等) 搭配 Azure AD，請按一下 [啟用主領域探索]  選項，並設定 [網域名稱]  。

    g. 在 [網域名稱]  中，如果是使用以 ADFS 為基礎的登入，請在此輸入網域名稱。

    h. 如果您想要在使用者登出 JIRA 時登出 Azure AD，請勾選 [啟用單一登出]  。 

    i. 按一下 [儲存]  按鈕以儲存設定。

    > [!NOTE]
    > 如需有關安裝和疑難排解的詳細資訊，請瀏覽 [MS JIRA SSO 連接器管理指南](../ms-confluence-jira-plugin-adminguide.md)，另外也有[常見問題集](../ms-confluence-jira-plugin-faq.md)可協助您

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon\@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com 。

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 JIRA SAML SSO by Microsoft (V5.2) 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [JIRA SAML SSO by Microsoft (V5.2)]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [JIRA SAML SSO by Microsoft (V5.2)]  。

    ![應用程式清單中的 JIRA SAML SSO by Microsoft (V5.2) 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>建立 JIRA SAML SSO by Microsoft (V5.2) 測試使用者

若要讓 Azure AD 使用者能夠登入 JIRA 內部部署伺服器，就必須將這些使用者佈建到 JIRA 內部部署伺服器。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 JIRA 內部部署伺服器。

2. 將滑鼠停留在 cog 上，然後按一下 [使用者管理]  。

    ![新增員工](./media/jira52microsoft-tutorial/user1.png)

3. 系統會將您重新導向至 [系統管理員存取] 頁面，以輸入**密碼**，然後按一下 [確認]  按鈕。

    ![新增員工](./media/jira52microsoft-tutorial/user2.png)

4. 在 [使用者管理]  索引標籤區段下，按一下 [建立使用者]  。

    ![新增員工](./media/jira52microsoft-tutorial/user3.png) 

5. 在 [建立新的使用者]  對話方塊中，執行下列步驟：

    ![新增員工](./media/jira52microsoft-tutorial/user4.png)

    a. 在 [電子郵件地址]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件地址。

    b. 在 [全名]  文字方塊中，輸入像是 Britta Simon 的使用者全名。

    c. 在 [使用者名稱]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件。

    d. 在 [密碼]  文字方塊中，輸入使用者的密碼。

    e. 按一下 [建立使用者]  。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [JIRA SAML SSO by Microsoft (V5.2)] 圖格時，應該會自動登入您已設定 SSO 的 JIRA SAML SSO by Microsoft (V5.2)。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
