---
title: 教學課程：Azure Active Directory 與 Zscaler Beta 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Beta 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 1fec471e4047707f2b7cbaa55fdc7f2256bebe93
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519859"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>教學課程：Azure Active Directory 與 Zscaler Beta 整合

在本教學課程中，您將了解如何整合 Zscaler Beta 與 Azure Active Directory (Azure AD)。
在整合 Zscaler Beta 與 Azure AD 後，您可以︰

* 在 Azure AD 中控制可存取 Zscaler Beta 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Zscaler Beta。 此存取控制稱為單一登入 (SSO)。
* 使用 Azure 入口網站集中管理您的帳戶。

如需有關軟體即服務 (SaaS) 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Zscaler Beta 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* 使用單一登入的 Zscaler Beta 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Zscaler Beta 支援由 SP 起始的 SSO。
* Zscaler Beta 支援 Just In Time 使用者佈建。

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Zscaler Beta

若要進行將 Zscaler Beta 整合到 Azure AD 中的設定，請從 Azure Marketplace 將 Zscaler Beta 新增至受控 SaaS 應用程式清單。

若要從 Azure Marketplace 新增 Zscaler Beta，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格上，選取 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Zscaler Beta** 。 在結果面板中選取 [Zscaler Beta]  ，然後選取 [新增]  。

     ![結果清單中的 Zscaler Beta](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您將以名為 Britta Simon 的測試使用者為基礎，設定及測試與 Zscaler Beta 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，請建立 Azure AD 使用者與 Zscaler Beta 中相關使用者之間的連結關聯性。

若要設定並測試與 Zscaler Beta 搭配運作的 Azure AD 單一登入，請完成下列建置組塊：

- [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
- [設定 Zscaler Beta 單一登入](#configure-zscaler-beta-single-sign-on)，以在應用程式端設定單一登入設定。
- [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
- [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
- [建立 Zscaler Beta 測試使用者](#create-a-zscaler-beta-test-user)，使 Zscaler Beta 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
- [測試單一登入](#test-single-sign-on)，驗證設定是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Zscaler Beta 搭配運作的 Azure AD 單一登入，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Zscaler Beta]  應用程式整合頁面中，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  ，以開啟 [基本 SAML 組態]  對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段中，遵循下列步驟：

    ![Zscaler Beta 網域和 URL 單一登入資訊](common/sp-intiated.png)

    - 在 [登入 URL]  方塊中，輸入使用者用來登入您 Zscaler Beta 應用程式的 URL。

    > [!NOTE]
    > 這不是真實的值。 請使用實際的登入 URL 值更新此值。 若要取得此值，請連絡 [Zscaler Beta 用戶端支援小組](https://www.zscaler.com/company/contact)。

5. Zscaler Beta 應用程式需要特定格式的 SAML 判斷提示。 您必須將自訂屬性對應新增至 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 選取 [編輯]  ，以開啟 [使用者屬性]  對話方塊。

    ![[使用者屬性] 對話方塊](common/edit-attribute.png)

6. Zscaler Beta 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，依照下列步驟新增 SAML 權杖屬性，如下表所示。
    
    | 名稱 | 來源屬性 | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. 選取 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![使用者宣告對話方塊](common/new-save-attribute.png)

    ![[管理使用者宣告] 對話方塊](common/new-attribute-details.png)

    b. 在 [名稱]  方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  方塊保持空白。

    d. 針對 [來源]  ，選取 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 選取 [確定]  。

    g. 選取 [儲存]  。

    > [!NOTE]
    > 若要了解如何設定 Azure AD 中的角色，請參閱[設定角色宣告](../develop/active-directory-enterprise-app-role-management.md)。

7. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，選取 [下載]  以下載 [憑證 (Base64)]  。 將其儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

8. 在 [設定 Zscaler Beta]  區段中，依據您的需求複製適當的 URL：

    ![複製組態 URL](common/copy-configuration-urls.png)

    - 登入 URL
    - Azure AD 識別碼
    - 登出 URL

### <a name="configure-zscaler-beta-single-sign-on"></a>設定 Zscaler Beta 單一登入

1. 若要自動執行 Zendesk Beta 內的設定，您必須選取 [安裝擴充功能] 以安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，選取 [設定 Zscaler Beta]  會將您導向至 Zscaler Beta 應用程式。 請從該處提供用以登入 Zscaler Beta 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

3. 若要手動設定 Zscaler Beta，請開啟新的網頁瀏覽器視窗。 以系統管理員身分登入您的 Zscaler Beta 公司網站，並執行下列步驟。

4. 移至 [管理]   > [驗證]   > [驗證設定]  ，並執行下列步驟。
   
    ![管理](./media/zscaler-beta-tutorial/ic800206.png "系統管理")

    a. 在 [驗證類型]  下方，選取 [SAML]  。

    b. 選取 [設定 SAML]  。

5. 在 [編輯 SAML]  視窗中，執行下列步驟： 
            
    ![管理使用者和驗證](./media/zscaler-beta-tutorial/ic800208.png "管理使用者和驗證")
    
    a. 在 [SAML 入口網站 URL]  方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。

    b. 在 [登入名稱屬性]  方塊中，輸入 **NameID** 。

    c. 在 [公開 SSL 憑證]  方塊中選取 [上傳]  ，以上傳您從 Azure 入口網站下載的 Azure SAML 簽署憑證。

    d. 切換 [啟用 SAML 自動佈建]  。

    e. 如果您想要啟用 displayName 屬性的 SAML 自動佈建，請在 [使用者顯示名稱屬性]  方塊中輸入 **displayName** 。

    f. 如果您想要啟用 memberOf 屬性的 SAML 自動佈建，請在 [群組名稱屬性]  方塊中輸入 **memberOf** 。

    g. 如果您想要啟用 department 屬性的 SAML 自動佈建，請在 [部門名稱屬性]  方塊中輸入 **department** 。

    h. 選取 [儲存]  。

6. 在 [設定使用者驗證]  對話方塊頁面上執行下列步驟：

    ![[啟用] 功能表和 [啟用] 按鈕](./media/zscaler-beta-tutorial/ic800207.png)

    a. 將滑鼠停留在左下方的 [啟用]  功能表上。

    b. 選取 [啟用]  。

## <a name="configure-proxy-settings"></a>進行 Proxy 設定
若要在 Internet Explorer 中進行 Proxy 設定，請執行下列步驟。

1. 啟動 **Internet Explorer** 。

2. 從 [工具] 功能表選取 [網際網路選項]，以開啟 [網際網路選項] 對話方塊。 
    
     ![[網際網路選項] 對話方塊](./media/zscaler-beta-tutorial/ic769492.png "，")

3. 選取 [連線]  索引標籤。 
  
     ![連線索引標籤](./media/zscaler-beta-tutorial/ic769493.png "連接")

4. 選取 [LAN 設定]  以開啟 [區域網路 (LAN) 設定]  對話方塊。

5. 在 [Proxy 伺服器]  區段中，執行下列步驟： 
   
    ![Proxy 伺服器區段](./media/zscaler-beta-tutorial/ic769494.png "Proxy 伺服器")

    a. 選取 [在您的區域網路使用 Proxy 伺服器]  核取方塊。

    b. 在 [位址]  方塊中輸入 **gateway.Zscaler Beta.net** 。

    c. 在 [連接埠]  方塊中，輸入 **80** 。

    d. 選取 [近端網址不使用 Proxy 伺服器]  核取方塊。

    e. 選取 [確定]  以關閉 [區域網路 (LAN) 設定]  對話方塊。

6. 選取 [確定]  以關閉 [網際網路選項]  對話方塊。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在 Azure 入口網站中，建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站中，於左窗格選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

    ![[使用者] 和 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者]  對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  方塊中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  方塊中，輸入 `brittasimon@yourcompanydomain.extension`。 例如 BrittaSimon@contoso.com。

    c. 選取 [顯示密碼]  核取方塊。 記下 [密碼]  方塊中顯示的值。

    d. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

將 Zscaler Beta 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]   > [Zscaler Beta]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **Zscaler Beta** 並加以選取。

    ![應用程式清單中的 Zscaler Beta 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增使用者] 按鈕](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊中，從清單中選取 **Britta Simon** 之類的使用者。 然後，選擇畫面底部的 [選取]  按鈕。

    ![[使用者和群組] 對話方塊](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. 在 [選取角色]  對話方塊方塊中，選取清單中的適當使用者角色。 然後，選擇畫面底部的 [選取]  按鈕。

    ![[選取角色] 對話方塊](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

    ![[新增指派] 對話方塊](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>建立 Zscaler Beta 測試使用者

本節會在 Zscaler Beta 中建立使用者 Britta Simon。 Zscaler Beta 支援依預設啟用的 **Just-In-Time** 使用者佈建。 您在本節中無須執行任何動作。 如果 Zscaler Beta 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!Note]
>若要手動建立使用者，請連絡 [Zscaler Beta 支援小組](https://www.zscaler.com/company/contact)。

### <a name="test-single-sign-on"></a>測試單一登入 

使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Zscaler Beta] 圖格時，應該會自動登入您已設定 SSO 的 Zscaler Beta。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](./tutorial-list.md)
- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)