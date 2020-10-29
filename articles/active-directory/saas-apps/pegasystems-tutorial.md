---
title: 教學課程：Azure Active Directory 與 Pega Systems 整合 | Microsoft Docs
description: 在本教學課程中，您會了解如何設定 Azure Active Directory 與 Pega Systems 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 4fb117b7f7b9a0c7a6a67e2714380a01cd53a4e0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515631"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>教學課程：Azure Active Directory 與 Pega Systems 整合

在本教學課程中，您會了解如何整合 Pega Systems 與 Azure Active Directory (Azure AD)。

此整合有下列優點：

* 您可以使用 Azure AD 來控制可存取 Pega Systems 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Pega Systems (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](../manage-apps/what-is-single-sign-on.md)。

如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定與 Pega Systems 的 Azure AD 整合，您需要具備：

* Azure AD 訂用帳戶。 如果沒有 Azure AD 環境，您可以註冊[一個月的試用版](https://azure.microsoft.com/pricing/free-trial/)。
* 已啟用單一登入的 Pega Systems 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Pega Systems 支援 SP 和 IDP 起始的 SSO。

## <a name="add-pega-systems-from-the-gallery"></a>從資源庫新增 Pega Systems

若要設定將 Pega Systems 整合到 Azure AD 中，您需要從資源庫將 Pega Systems 新增到受控 SaaS 應用程式清單。

1. 在 [Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [Azure Active Directory]  ：

    ![選取 Azure Active Directory](common/select-azuread.png)

2. 移至 [企業應用程式]   > [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取視窗頂端的 [新增應用程式]  ：

    ![選取 [新增應用程式]](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Pega Systems** 。 在搜尋結果中，選取 [Pega Systems]  ，然後選取 [新增]  。

     ![搜尋結果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會使用名為 Britta Simon 的測試使用者，設定及測試與 Pega Systems 搭配運作的 Azure AD 單一登入。
若要啟用單一登入，您必須建立 Azure AD 使用者與 Pega Systems 中相關使用者之間的關聯性。

若要設定及測試與 Pega Systems 搭配運作的 Azure AD 單一登入，您需要完成下列步驟：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** ，讓您的使用者能夠使用此功能。
2. 在應用程式端 **[設定 Pega Systems 單一登入](#configure-pega-systems-single-sign-on)** 。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，為使用者啟用 Azure AD 單一登入。
5. **[建立 Pega Systems 測試使用者](#create-a-pega-systems-test-user)** ，其會連結至 Azure AD 中代表該使用者的項目。
6. **[測試單一登入](#test-single-sign-on)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Pega Systems 搭配運作的 Azure AD 單一登入，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Pega Systems]  應用程式整合頁面上，選取 [單一登入]  ：

    ![選取單一登入](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入：

    ![選取單一登入方法](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  圖示，以開啟 [基本 SAML 組態]  對話方塊：

    ![編輯圖示](common/edit-urls.png)

4. 在 [基本 SAML 組態]  對話方塊中，若您想要以 IDP 起始模式設定應用程式，請完成下列步驟。

    ![[基本 SAML 組態] 對話方塊](common/idp-intiated.png)

    1. 在 [識別碼]  方塊中，輸入以下模式的 URL：

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. 在 [回覆 URL]  方塊中，輸入以下模式的 URL：

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL]  ，然後完成下列步驟。

    ![Pega Systems 網域及 URL 單一登入資訊](common/both-advanced-urls.png)

    1. 在 [登入 URL]  方塊中，輸入登入 URL 值。

    1. 在 [轉送狀態]  方塊中，輸入以下模式的 URL：`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > 此處提供的值是預留位置。 您需要使用實際的「識別碼」、「回覆 URL」、「登入 URL」及「轉送狀態 URL」。 您可以從本教學課程稍後所述的 Pega 應用程式取得「識別碼」和「回覆 URL」的值。 若要取得轉送狀態的值，請連絡 [Pega Systems 支援小組](https://www.pega.com/contact-us)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

6. Pega Systems 應用程式需要特定格式的 SAML 判斷提示。 若要取得正確的格式，您需要將自訂屬性對應新增至您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性。 選取 [編輯]  圖示，以開啟 [使用者屬性]  對話方塊：

    ![使用者屬性](common/edit-attribute.png)

7. 除了先前螢幕擷取畫面所示的屬性，Pega Systems 應用程式還需要在 SAML 回應中傳回的幾個其他屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，完成下列步驟來新增這些 SAML 權杖屬性：

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > 這些值會是您組織特有的。 請提供適當的值。

    1. 選取 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊：

    ![選取 [新增宣告]](common/new-save-attribute.png)

    ![[管理使用者宣告] 對話方塊](common/new-attribute-details.png)

    1. 在 [名稱]  方塊中，輸入該資料列所顯示的屬性名稱。

    1. 讓 [命名空間]  方塊保持空白。

    1. 針對 [來源]  ，選取 [屬性]  。

    1. 在 [來源屬性]  清單中，選取該資料列所顯示的屬性值。

    1. 選取 [確定]  。

    1. 選取 [儲存]  。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，依據您的需求選取 [同盟中繼資料 XML] 旁邊的 [下載] 連結，並將憑證儲存在您的電腦上：

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 Pega Systems]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    1. **登入 URL** 。

    1. **Azure AD 識別碼** 。

    1. **登出 URL** 。

### <a name="configure-pega-systems-single-sign-on"></a>設定 Pega Systems 單一登入

1. 若要設定 **Pega Systems** 端的單一登入，請在其他瀏覽器視窗中以管理帳戶登入 Pega 入口網站。

2. 選取 [建立]   >  [SysAdmin]   >  [驗證服務]  ：

    ![選取驗證服務](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. 完成 [建立驗證服務]  畫面上的下列步驟。

    ![建立驗證服務畫面](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. 在 [類型]  清單中，選取 [SAML 2.0]  。

    1. 在 [名稱]  方塊中輸入任何名稱 (例如 **Azure AD SSO** )。

    1. 在 [簡短描述]  方塊中輸入描述。  

    1. 選取 [建立並開啟]  。
    
4. 在 [識別提供者 (IdP) 資訊]  區段中，選取 [匯入 IdP 中繼資料]  ，並瀏覽您從 Azure 入口網站下載的中繼資料檔案。 按一下 [提交]  以載入中繼資料：

    ![識別提供者 (IdP) 資訊區段](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    此匯入會填入 IdP 資料，如下所示：

    ![匯入的 IdP 資料](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. 在 [服務提供者 (SP) 設定]  區段中，完成下列步驟。

    ![服務提供者設定](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. 複製 [實體識別碼] 值，並將其貼至 Azure 入口網站中 [基本 SAML 組態] 區段中的 [識別碼] 方塊內。

    1. 複製 [判斷提示取用者服務 (ACS) 位置] 值，並將其貼至 Azure 入口網站中 [基本 SAML 組態] 區段中的 [回覆 URL] 方塊內。

    1. 選取 [停用要求簽署]  。

7. 選取 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  ：

    ![選取 [所有使用者]](common/users.png)

2. 在畫面頂端選取 [新增使用者]  ：

    ![選取 [新增使用者]](common/new-user.png)

3. 在 [使用者]  對話方塊中，完成下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  方塊中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱] 方塊中，輸入 **brittasimon@\<yourcompanydomain>.\<extension>** 。 (例如，BrittaSimon@contoso.com)。

    c. 選取 [顯示密碼]  ，然後記下 [密碼]  方塊中的值。

    d. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Pega Systems 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Pega Systems]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Pega Systems]  。

    ![應用程式清單](common/all-applications.png)

3. 在左窗格中，選取 [使用者和群組]  ：

    ![選取 [使用者和群組]](common/users-groups-blade.png)

4. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![選取 [新增使用者]](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您在 SAML 判斷提示中需要角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-pega-systems-test-user"></a>建立 Pega Systems 測試使用者

接下來，您需要在 Pega Systems 中建立名為 Britta Simon 的使用者。 配合 [Pega Systems 支援小組](https://www.pega.com/contact-us)建立使用者。

### <a name="test-single-sign-on"></a>測試單一登入

您現在必須使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Pega Systems] 圖格時，應該會自動登入您已設定 SSO 的 Pega Systems 執行個體。 如需詳細資訊，請參閱[在「我的應用程式」入口網站上存取和使用應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)