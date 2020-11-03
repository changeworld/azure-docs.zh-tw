---
title: 教學課程：Azure Active Directory 與 Zscaler Two 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Two 之間的單一登入。
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
ms.openlocfilehash: a6bf6c659ff589cf693f56c6c37e7f28daa376f8
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92894180"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>教學課程：Azure Active Directory 與 Zscaler Two 整合

在本教學課程中，您將了解如何整合 Zscaler Two 與 Azure Active Directory (Azure AD)。
將 Zscaler Two 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Zscaler Two 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Zscaler Two (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Zscaler Two 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Zscaler Two 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Zscaler Two 支援 **SP** 起始的 SSO

* Zscaler Two 支援 **Just In Time** 使用者佈建

## <a name="adding-zscaler-two-from-the-gallery"></a>從資源庫新增 Zscaler Two

若要設定將 Zscaler Two 整合到 Azure AD 中，您需要從資源庫將 Zscaler Two 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Zscaler Two，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Zscaler Two** ，並從結果面板中選取 [Zscaler Two]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Zscaler Two](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Zscaler Two 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Zscaler Two 中相關使用者之間的連結關聯性。

若要使用 Zscaler Two 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Zscaler Two 單一登入](#configure-zscaler-two-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Zscaler Two 測試使用者](#create-zscaler-two-test-user)** - 使 Zscaler Two 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Zscaler Two 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Zscaler Two]  應用程式整合頁面中，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Zscaler Two 網域和 URL 單一登入資訊](common/sp-signonurl.png)

    在 [登入 URL] 文字方塊中，鍵入使用者用來登入您 Zscaler Two 的 URL。

    > [!NOTE]
    > 您會使用實際的登入 URL 來更新此值。 請連絡 [Zscaler Two 用戶端支援小組](https://www.zscaler.com/company/contact)以取得此值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. Zscaler Two 應用程式會預期特定格式的 SAML 判斷提示，這會需要您將自訂屬性對應新增至您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 **編輯** 圖示以開啟 [使用者屬性] 對話方塊。 

    ![顯示使用者屬性的螢幕擷取畫面，其中已選取 [編輯] 圖示。](common/edit-attribute.png)

6. 除了以上屬性外，Zscaler Two 應用程式還會預期 SAML 回應傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：
    
    | 名稱 | 來源屬性 |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![顯示使用者宣告的螢幕擷取畫面，其中具有新增新宣告的選項。](common/new-save-attribute.png)

    ![顯示管理使用者宣告對話方塊的螢幕擷取畫面，您可以在其中輸入所述的值。](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。
    
    f. 按一下 [檔案]  。

    > [!NOTE]
    > 請按一下[這裡](../develop/active-directory-enterprise-app-role-management.md)，以了解如何在 Azure AD 中設定角色

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

8. 在 [設定 Zscaler Two]  區段上，依據需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-zscaler-two-single-sign-on"></a>設定 Zscaler Two 單一登入

1. 若要自動執行 Zscaler Two 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Zscaler Two]  便會將您導向到 Zscaler Two 應用程式。 請從該處提供用以登入 Zscaler Two 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定 SSO](common/setup-sso.png)

3. 如果您想要手動設定 Zscaler Two，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Zscaler Two 公司網站，然後執行下列步驟：

4. 移至 管理 > 驗證 > 驗證設定  並執行下列步驟：
   
    ![螢幕擷取畫面：顯示 Zscaler One 網站，並有所述的步驟。](./media/zscaler-two-tutorial/ic800206.png "系統管理")

    a. 在 [驗證類型] 下選擇 [SAML]  。

    b. 按一下 [設定 SAML]  。

5. 在 [編輯 SAML]  視窗上執行下列步驟，然後按一下 [儲存]。  
            
    ![管理使用者和驗證](./media/zscaler-two-tutorial/ic800208.png "管理使用者和驗證")
    
    a. 在 [SAML 入口網站 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。

    b. 在 [登入名稱屬性]  文字方塊中，輸入 **NameID** 。

    c. 按一下 [上傳]  ，以上傳您從 Azure 入口網站的 [公開 SSL 憑證]  下載的 Azure SAML 簽署憑證。

    d. 切換 [啟用 SAML 自動佈建]  。

    e. 如果您想要啟用 displayName 屬性的 SAML 自動佈建，請在 [使用者顯示名稱屬性]  文字方塊中，輸入 **displayName** 。

    f. 如果您想要啟用 memberOf 屬性的 SAML 自動佈建，請在 [群組名稱屬性]  文字方塊中，輸入 **memberOf** 。

    g. 如果您想要啟用 department 屬性的 SAML 自動佈建，請在 [部門名稱屬性]  文字方塊中，輸入 **department** 。

    h. 按一下 [檔案]  。

6. 在 [設定使用者驗證]  對話方塊頁面上執行下列步驟：

    ![螢幕擷取畫面：顯示已選取 [啟用] 的 [設定使用者驗證] 對話方塊。](./media/zscaler-two-tutorial/ic800207.png)

    a. 將滑鼠停留在靠近左下方的 [啟用]  功能表上。

    b. 按一下 [啟用]  。

## <a name="configuring-proxy-settings"></a>進行 Proxy 設定
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中進行 Proxy 設定

1. 啟動 **Internet Explorer** 。

2. 從 [工具] 功能表選取 [網際網路選項] 可開啟 [網際網路選項] 對話方塊。   
    
     ![網際網路選項](./media/zscaler-two-tutorial/ic769492.png "，")

3. 按一下 [連線]  索引標籤。   
  
     ![連線](./media/zscaler-two-tutorial/ic769493.png "連接")

4. 按一下 [區域網路設定]  可開啟 [區域網路設定]  對話方塊。

5. 在 [Proxy 伺服器] 區段中，執行下列步驟：   
   
    ![Proxy 伺服器](./media/zscaler-two-tutorial/ic769494.png "Proxy 伺服器")

    a. 選取 [在您的區域網路使用 Proxy 伺服器]  。

    b. 在 [位址] 文字方塊中輸入 **gateway.Zscaler Two.net** 。

    c. 在 [連接埠] 文字方塊中輸入 **80** 。

    d. 選取 [近端網址不使用 Proxy 伺服器]  。

    e. 按一下 [確定]  關閉 [區域網路 (LAN) 設定]  對話方塊。

6. 按一下 [確定]  關閉 [網際網路選項]  對話方塊。

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

在本節中，您會將 Zscaler Two 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Zscaler Two]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler Two]  。

    ![應用程式清單中的 Zscaler Two 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊中，從清單中選取使用者 (例如 **Britta Simon** )，然後按一下畫面底部的 [選取]  按鈕。

    ![螢幕擷取畫面：顯示 [使用者和群組] 對話方塊，您可以在其中選取使用者。](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. 從 [選取角色]  對話方塊的清單中選擇適當的使用者角色，然後按一下畫面底部的 [選取]  按鈕。

    ![螢幕擷取畫面：顯示 [選取角色] 對話方塊，您可以在其中選擇使用者角色。](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. 在 [新增指派]  對話方塊中，選取 [指派]  按鈕。

    ![螢幕擷取畫面：顯示 [新增指派] 對話方塊，您可以在其中選取 [指派]。](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="create-zscaler-two-test-user"></a>建立 Zscaler Two 測試使用者

本節會在 Zscaler Two 中建立名為 Britta Simon 的使用者。 Zscaler Two 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Zscaler Two 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!Note]
>如果您需要手動建立使用者，就需要連絡 [Zscaler Two 支援小組](https://www.zscaler.com/company/contact)。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Zscaler Two 圖格時，應該會自動登入您已設定 SSO 的 Zscaler Two。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)