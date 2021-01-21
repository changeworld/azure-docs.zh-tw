---
title: 教學課程：Azure Active Directory 與 TOPdesk - Secure 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 TOPdesk - Secure 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 93b4030101ab273182a8f9207bc40aa46dbb11c3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622338"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>教學課程：Azure Active Directory 與 TOPdesk - Secure 整合

在本教學課程中，您將瞭解如何整合 TOPdesk-Secure 與 Azure Active Directory (Azure AD) 。 在整合 TOPdesk-Secure 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 TOPdesk-Secure 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 TOPdesk-Secure (單一登入) 。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：
 
 * Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* TOPdesk-Secure 單一登入 (SSO) 啟用的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* TOPdesk - Secure 支援 **SP** 起始的 SSO

## <a name="add-topdesk---secure-from-the-gallery"></a>從資源庫新增 TOPdesk-Secure

若要設定將 TOPdesk - Secure 整合到 Azure AD 中，您需要將 TOPdesk - Secure 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **TOPdesk-Secure** 。
1. 從結果面板選取 [ **TOPdesk-Secure** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>設定及測試 Azure AD SSO for TOPdesk-Secure

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 TOPdesk - Secure 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 TOPdesk - Secure 中相關使用者之間的連結關聯性。

若要設定及測試與 TOPdesk-Secure 搭配 Azure AD 單一登入，您需要執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    2. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 TOPdesk-SECURE SSO](#configure-topdesk---secure-sso)** -在應用程式端設定單一 Sign-On 設定。
    1. **[建立 TOPdesk - Secure 測試使用者](#create-topdesk---secure-test-user)** - 使 TOPdesk - Secure 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 TOPdesk - Secure 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 Azure 入口網站的 [ **TOPdesk-Secure** 應用程式整合] 頁面上，選取 [ **單一登入**]。

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

3. 在 [ **以 SAML 設定單一 Sign-On** ] 頁面上，按一下 [鉛筆] 圖示以開啟 [ **基本 SAML** 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<companyname>.topdesk.net`

    b. 在 [識別碼 URL] 方塊中，填入您可以從 TOPdesk 設定中擷取的 TOPdesk 中繼資料 URL。 它應該使用下列模式：`https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [TOPdesk - Secure 用戶端支援小組](https://www.topdesk.com/us/support/) \(英文\) 以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 TOPdesk - Secure] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 TOPdesk-Secure 的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [TOPdesk - Secure]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="configure-topdesk---secure-sso"></a>設定 TOPdesk-Secure SSO

1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。

2. 在 [TOPdesk - Secure] 功能表中，按一下 [設定]。

    ![設定](./media/topdesk-secure-tutorial/ic790598.png "設定")

3. 按一下 [登入設定]。

    ![登入設定](./media/topdesk-secure-tutorial/ic790599.png "登入設定")

4. 展開 [登入設定] 功能表，然後按一下 [一般]。

    ![一般](./media/topdesk-secure-tutorial/ic790600.png "一般")

5. 在 [SAML 登入] 組態區段的 [Secure] 區段中，執行下列步驟：

    ![技術設定](./media/topdesk-secure-tutorial/ic790855.png "技術設定")

    a. 按一下 [下載] 來下載公用中繼資料檔案，然後將它儲存在本機電腦上。

    b. 開啟中繼資料檔案，然後找到 **AssertionConsumerService** 節點。

    ![判斷提示取用者服務](./media/topdesk-secure-tutorial/ic790856.png "判斷提示取用者服務")

    c. 複製 **AssertionConsumerService** 值，在 [TOPdesk - Secure 網域及 URL] 區段的 [回覆 URL] 文字方塊中，貼上此值。

6. 若要建立憑證檔案，請執行下列步驟：

    ![[MSSQLSERVER 的通訊協定內容]](./media/topdesk-secure-tutorial/ic790606.png "憑證")

    a. 從 Azure 入口網站開啟下載的中繼資料檔案。

    b. 展開 **fed:ApplicationServiceType** 為 **xsi:type** 的 **RoleDescriptor** 節點。

    c. 複製 **X509Certificate** 節點的值。

    d. 將複製的 **X509Certificate** 值儲存在本機電腦上的檔案中。

7. 在 [公用] 區段中，按一下 [加入]。

    ![加入](./media/topdesk-secure-tutorial/ic790607.png "加")

8. 在 [SAML 設定助理] 對話方塊頁面上，執行下列步驟：

    ![SAML 組態輔助程式](./media/topdesk-secure-tutorial/ic790608.png "SAML 組態輔助程式")

    a. 若要上傳您從 Azure 入口網站下載的中繼資料檔案，請在 [同盟中繼資料] 下按一下 [瀏覽]。

    b. 若要上傳憑證檔案，請按一下 [憑證 (RSA] 下的 [瀏覽]。

    c. 針對 **私密金鑰 (RSA、PKCS8、DER)**，您可以上傳您自己的私密金鑰，也可以連絡 [TOPdesk - Secure 用戶端支援小組](https://www.topdesk.com/us/support)以取得私密金鑰。

    d. 若要將 TOPdesk 支援小組提供的標誌檔案上傳，請按一下 [商標圖示] 下的 [瀏覽]。

    e. 在 [使用者名稱屬性] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    f. 在 [顯示名稱] 文字方塊中，輸入您的組態名稱。

    g. 按一下 [檔案] 。

### <a name="create-topdesk---secure-test-user"></a>建立 TOPdesk - Secure 測試使用者

若要讓 Azure AD 使用者可以登入 TOPdesk - Secure，則必須將他們佈建到 TOPdesk - Secure。  
TOPdesk - Secure 需以手動的方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：

1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。

2. 在上方功能表中按一下 [TOPdesk] \> [新增] \> [支援檔案] \> [操作員]。

    ![運算子](./media/topdesk-secure-tutorial/ic790610.png "運算子")

3. 在 [新增操作員] 對話方塊中，執行下列步驟：

    ![New 運算子](./media/topdesk-secure-tutorial/ic790611.png "New 運算子")

    a. 按一下 [General] \(一般\) 索引標籤。

    b. 在 [ **姓氏** ] 文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [位置] 區段中，選取帳戶的 [站台]。

    d. 在 [TOPdesk 登入] 區段的 [登入名稱] 文字方塊中，輸入使用者的登入名稱。

    e. 按一下 [檔案] 。

> [!NOTE]
> 您可以使用任何其他的 TOPdesk - Secure 使用者帳戶建立工具或 TOPdesk - Secure 提供的 API，來佈建 Azure AD 使用者帳戶。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 TOPdesk-Secure 登入 URL，您可以在其中起始登入流程。 

* 直接前往 TOPdesk-Secure 登入 URL，並從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [TOPdesk-Secure] 圖格時，應該會自動登入您已設定 SSO 的 TOPdesk-Secure。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 TOPdesk-Secure 之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

