---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 JIRA SAML SSO by Microsoft 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 JIRA SAML SSO by Microsoft 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.openlocfilehash: 621bbac6bc9ff3cb147110942060c8268fd86989
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511173"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 JIRA SAML SSO by Microsoft 整合

在本教學課程中，您會了解如何整合 JIRA SAML SSO by Microsoft 與 Azure Active Directory (Azure AD)。 當您整合 JIRA SAML SSO by Microsoft 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 JIRA SAML SSO by Microsoft 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 JIRA SAML SSO by Microsoft。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="description"></a>描述

使用 Microsoft Azure Active Directory 帳戶搭配 Atlassian JIRA 伺服器來啟用單一登入。 如此一來，您組織的所有使用者都能使用 Azure AD 認證來登入 JIRA 應用程式。 此外掛程式使用 SAML 2.0 來進行同盟。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 JIRA SAML SSO by Microsoft 整合，您需要下列項目：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
- 應該在 Windows 64 位元版本上安裝及設定 JIRA Core 和 JIRA Software 6.4 到 8.14.0 或 JIRA Service Desk 3.0 到 4.11.1
- JIRA 伺服器已啟用 HTTPS
- 請注意下一節提及支援的 JIRA 外掛程式版本。
- JIRA 伺服器可從網際網路連上，特別是連線至 Azure AD 登入頁面來進行驗證，且應該能夠接收來自 Azure AD 的權杖
- JIRA 中已設定管理員認證
- JIRA 中已停用 WebSudo
- JIRA 伺服器應用程式中建立的測試使用者

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用 JIRA 的生產環境。 先在應用程式的開發或預備環境中測試整合，然後使用生產環境。

若要開始，您需要下列項目：

* 除非必要，否則請勿使用生產環境。
* 已啟用 JIRA SAML SSO by Microsoft 單一登入 (SSO) 的訂用帳戶。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="supported-versions-of-jira"></a>支援的 JIRA 版本

* JIRA 核心和軟體：6.4 到 8.14.0
* JIRA Service Desk 3.0.0 到 4.11.1
* JIRA 也支援 5.2。 如需詳細資訊，請按一下[適用於 JIRA 5.2 的 Microsoft Azure Active Directory 單一登入](jira52microsoft-tutorial.md)

> [!NOTE]
> 請注意，我們的 JIRA 外掛程式也適用於 Ubuntu 16.04 版及 Linux。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* JIRA SAML SSO by Microsoft 支援由 **SP** 起始的 SSO

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>從資源庫新增 JIRA SAML SSO by Microsoft

如要設定將 JIRA SAML SSO by Microsoft 整合到 Azure AD 中，您需要從資源庫將 JIRA SAML SSO by Microsoft 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 [JIRA SAML SSO by Microsoft]  。
1. 從結果面板中選取 [JIRA SAML SSO by Microsoft]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>設定並測試 JIRA SAML SSO by Microsoft 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 JIRA SAML SSO by Microsoft 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 JIRA SAML SSO by Microsoft 中相關使用者之間的連結關聯性。

若要設定及測試與 JIRA SAML SSO by Microsoft 搭配運作的 Azure AD SSO，您需要完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 JIRA SAML SSO by Microsoft 的 SSO](#configure-jira-saml-sso-by-microsoft-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 JIRA SAML SSO by Microsoft 測試使用者](#create-jira-saml-sso-by-microsoft-test-user)** - 使 JIRA SAML SSO by Microsoft 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [JIRA SAML SSO by Microsoft]  應用程式整合分頁上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<domain:port>/plugins/servlet/saml/auth`

    b. 在 [識別碼] 方塊中，使用下列模式輸入 URL：`https://<domain:port>/`

    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 如果連接埠是具名 URL，則為選擇性。 在設定 Jira 外掛程式 (本教學課程稍後會說明) 期間會收到這些值。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 JIRA SAML SSO by Microsoft 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [JIRA SAML SSO by Microsoft]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>設定 JIRA SAML SSO by Microsoft 的 SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 JIRA 執行個體。

2. 將滑鼠停留在 cog 上，然後按一下 [附加元件]  。

    ![此螢幕擷取畫面顯示已從 [設定] 功能表中選取附加元件。](./media/jiramicrosoft-tutorial/addon1.png)

3. 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=56506)下載外掛程式。 使用 [上傳附加元件] 功能表來手動上傳 Microsoft 所提供的外掛程式。 外掛程式下載受到 [Microsoft 服務合約](https://www.microsoft.com/servicesagreement/)的規範。

    ![此螢幕擷取畫面顯示 [管理附加元件] 並指出上傳附加元件的連結。](./media/jiramicrosoft-tutorial/addon12.png)

4. 若要執行 JIRA 反向 Proxy 案例或是負載平衡器案例，請執行以下步驟：

    > [!NOTE]
    > 請先按照以下說明來設定伺服器，再安裝外掛程式。

    a. 在 JIRA 伺服器應用程式的 **server.xml** 檔案中，於 **連接器** 連接埠新增下列屬性。

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![此螢幕擷取畫面顯示編輯器中已加入新行的 server.xml 檔案。](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. 根據 Proxy/負載平衡器，變更 **系統設定** 中的 **基底 URL**。

    ![此螢幕擷取畫面顯示您可以用來變更基底 URL 的系統管理設定。](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. 安裝外掛程式之後，它會出現在 [管理附加元件] 區段的 [使用者安裝的附加元件] 區段中。 按一下 [設定] 來設定新的外掛程式。

    ![此螢幕擷取畫面顯示已選取 [設定] 的 [適用於 Jira 的 Azure AD SAML 單一登入] 區段。](./media/jiramicrosoft-tutorial/addon14.png)

6. 在設定頁面上執行下列步驟：

    ![此螢幕擷取畫面顯示適用於 Jira 的 Microsoft Azure Active Directory 單一登入設定頁面。](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > 請確定只有一個對應至應用程式的憑證，解析中繼資料時就不會發生錯誤。 如果有多個憑證，則解析中繼資料時，管理員會遇到錯誤。

    1. 在 [中繼資料 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 **應用程式同盟中繼資料 Url** 值，然後按一下 [解析]  按鈕。 這樣會讀取 IdP 中繼資料 URL 並填入所有欄位資訊。

    1. 複製 [識別碼]、[回覆 URL] 和 [登入 URL]  值，然後在 Azure 入口網站的 [JIRA SAML SSO by Microsoft 網域及 URL]  中，分別貼到 [識別碼]、[回覆 URL] 和 [登入 URL]  文字方塊。

    1. 在 [登入按鈕名稱]  中，輸入您的組織要讓使用者在登入畫面上看到的按鈕名稱。
    
    1. 在 [登入按鈕描述]  中，輸入組織要讓使用者在登入畫面上看到的按鈕描述。

    1. 在 [SAML 使用者識別碼位置]  中，選取 [使用者識別碼在 Subject 陳述式的 NameIdentifier 元素中]  或 [使用者識別碼在 Attribute 元素中]  。  此識別碼必須為 JIRA 使用者識別碼。 如果使用者識別碼不相符，系統就不會允許使用者登入。

       > [!Note]
       > 預設 SAML 使用者識別碼位置是名稱識別碼。 您可以將它變更為屬性選項，並輸入適當的屬性名稱。

    1. 如果您選取 [使用者識別碼在 Attribute 元素中] 選項，請在 [屬性名稱] 文字方塊中，輸入會預期使用者識別碼的屬性名稱。

    1. 如果您使用同盟網域 (例如 ADFS 等) 搭配 Azure AD，請按一下 [啟用主領域探索] 選項，並設定 [網域名稱]。

    1. 在 [網域名稱]  中，如果是使用以 ADFS 為基礎的登入，請在此輸入網域名稱。

    1. 如果您想要在使用者登出 JIRA 時登出 Azure AD，請勾選 [啟用單一登出]  。
    
    1. 如果您只要透過 Azure AD 認證登入，請啟用 [強制執行 Azure 登入]  核取方塊。
    
       > [!Note]
       > 若要在啟用強制執行 Azure 登入時，於登入頁面上啟用系統管理員登入的預設登入表單，請在瀏覽器 URL 中加入查詢參數。
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. 按一下 [儲存]  按鈕以儲存設定。

       > [!NOTE]
       > 如需有關安裝和疑難排解的詳細資訊，請瀏覽 [MS JIRA SSO 連接器管理指南](./ms-confluence-jira-plugin-adminguide.md)。 另外也有[常見問題集](./ms-confluence-jira-plugin-adminguide.md)可協助您。

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>建立 JIRA SAML SSO by Microsoft 測試使用者

若要讓 Azure AD 使用者能夠登入 JIRA 內部部署伺服器，必須將他們佈建到 JIRA SAML SSO by Microsoft。 對於 JIRA SAML SSO by Microsoft，佈建是手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 JIRA 內部部署伺服器。

2. 將滑鼠停留在 cog 上，然後按一下 [使用者管理]  。

    ![顯示已從 [設定] 功能表選取 [使用者管理] 的螢幕擷取畫面。](./media/jiramicrosoft-tutorial/user1.png)

3. 系統會將您重新導向至 [系統管理員存取] 頁面，以輸入 **密碼**，然後按一下 [確認]  按鈕。

    ![此螢幕擷取畫面顯示您在其中輸入認證的 [系統管理員] 存取頁面。](./media/jiramicrosoft-tutorial/user2.png)

4. 在 [使用者管理]  索引標籤區段下，按一下 [建立使用者]  。

    ![此螢幕擷取畫面顯示您可以在其中建立使用者的 [使用者管理] 索引標籤。](./media/jiramicrosoft-tutorial/user3.png) 

5. 在 [建立新的使用者]  對話方塊中，執行下列步驟：

    ![此螢幕擷取畫面顯示您可以在此步驟中輸入資訊的 [建立新使用者] 對話方塊。](./media/jiramicrosoft-tutorial/user4.png) 

    a. 在 [電子郵件地址]  文字方塊中，輸入像是 B.simon@contoso.com 的使用者電子郵件地址。

    b. 在 [全名]  文字方塊中，輸入像是 B.Simon 的使用者全名。

    c. 在 [使用者名稱]  文字方塊中，輸入像是 B.simon@contoso.com 的使用者電子郵件。

    d. 在 [密碼]  文字方塊中，輸入使用者的密碼。

    e. 按一下 [建立使用者]  。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [JIRA SAML SSO by Microsoft] 圖格時，應該會自動登入您已設定 SSO 的 JIRA SAML SSO by Microsoft。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 JIRA SAML SSO by Microsoft](https://aad.portal.azure.com/)
