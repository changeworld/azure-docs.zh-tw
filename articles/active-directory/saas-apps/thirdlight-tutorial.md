---
title: 教學課程：Azure Active Directory 與 ThirdLight 整合 | Microsoft Docs
description: 在本教學課程中，您會了解如何設定 Azure Active Directory 與 ThirdLight 之間的單一登入。
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
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514651"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>教學課程：Azure Active Directory 與 ThirdLight 整合

在本教學課程中，您將了解如何整合 ThirdLight 與 Azure Active Directory (Azure AD)。 此整合有下列優點：

* 您可以使用 Azure AD 來控制可存取 ThirdLight 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 ThirdLight (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

如果您想要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](../manage-apps/what-is-single-sign-on.md)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 ThirdLight 的整合，您需要具備：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入的 ThirdLight 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* ThirdLight 支援 SP 起始的 SSO。

## <a name="add-thirdlight-from-the-gallery"></a>從資源庫新增 ThirdLight

若要設定 ThirdLight 與 Azure AD 整合，您需要從資源庫將 ThirdLight 新增至受控 SaaS 應用程式清單。

1. 在 [Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [Azure Active Directory]  ：

    ![選取 Azure Active Directory](common/select-azuread.png)

2. 移至 [企業應用程式]   > [所有應用程式]  ：

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取視窗頂端的 [新增應用程式]  ：

    ![選取 [新增應用程式]](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **ThirdLight** 。 在搜尋結果中，選取 [ThirdLight]  ，然後選取 [新增]  。

     ![搜尋結果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會使用名為 Britta Simon 的測試使用者，設定及測試與 ThirdLight 搭配運作的 Azure AD 單一登入。
若要啟用單一登入，您必須建立 Azure AD 使用者與 ThirdLight 中相關使用者之間的關聯性。

若要設定及測試與 ThirdLight 搭配運作的 Azure AD 單一登入，您需要完成下列步驟：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** ，讓您的使用者能夠使用此功能。
2. 在應用程式端 **[設定 ThirdLight 單一登入](#configure-thirdlight-single-sign-on)** 。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，為使用者啟用 Azure AD 單一登入。
5. **[建立 ThirdLight 測試使用者](#create-a-thirdlight-test-user)** ，其會連結至 Azure AD 中代表該使用者的項目。
6. **[測試單一登入](#test-single-sign-on)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 ThirdLight 搭配運作的 Azure AD 單一登入，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [ThirdLight] 應用程式整合頁面上，選取 [單一登入]  ：

    ![選取 [單一登入]](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入：

    ![選取單一登入方法](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  圖示，以開啟 [基本 SAML 組態]  對話方塊：

    ![編輯圖示](common/edit-urls.png)

4. 在 [基本 SAML 組態]  對話方塊中，完成下列步驟。

    ![[基本 SAML 組態] 對話方塊](common/sp-identifier.png)

    1. 在 [登入 URL]  方塊中，輸入以下模式的 URL：
    
          `https://<subdomain>.thirdlight.com/`

    1. 在 [識別碼 (實體識別碼)]  方塊中，輸入以下模式的 URL：

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > 這些值都是預留位置。 您需要使用實際的登入 URL 和識別碼。 請連絡 [ThirdLight 支援小組](https://www.thirdlight.com/support)以取得這些值。 您也可以參考 Azure 入口網站中的 [基本 SAML 組態]  對話方塊所顯示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，依據您的需求選取 [同盟中繼資料 XML]  旁邊的 [下載]  連結，並將檔案儲存在您的電腦上：

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 ThirdLight]  區段中，依據您的需求複製適當的 URL：

    ![複製組態 URL](common/copy-configuration-urls.png)

    1. **登入 URL** 。

    1. **Azure AD 識別碼** 。

    1. **登出 URL** 。

### <a name="configure-thirdlight-single-sign-on"></a>設定 ThirdLight 單一登入

1. 在新的網頁瀏覽器視窗中，以系統管理員身分登入您的 ThirdLight 公司網站。

1. 移至 [設定]   > [系統管理]   > [SAML2]  ：

    ![系統管理](./media/thirdlight-tutorial/ic805843.png "系統管理")

1. 在 [SAML2 設定] 區段中，採取下列步驟。
  
    ![[SAML2 設定] 區段](./media/thirdlight-tutorial/ic805844.png "[SAML2 設定] 區段")

    1. 選取 [啟用 SAML2 單一登入]  。

    1. 在 [IdP 中繼資料的來源]  底下，選取 [從 XML 載入 IdP 中繼資料]  。

    1. 開啟您在上一節從 Azure 入口網站下載的中繼資料檔案。 複製檔案的內容，並將其貼至 [IdP 中繼資料 XML]  方塊中。

    1. 選取 [儲存 SAML2 設定]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  ：

    ![選取 [所有使用者]](common/users.png)

2. 在視窗頂端選取 [新增使用者]  ：

    ![選取 [新增使用者]](common/new-user.png)

3. 在 [使用者]  對話方塊中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    1. 在 [名稱]  方塊中，輸入 **BrittaSimon** 。
  
    1. 在 [使用者名稱] 方塊中，輸入 **BrittaSimon@\<yourcompanydomain>.\<extension>** 。 (例如，BrittaSimon@contoso.com)。

    1. 選取 [顯示密碼]  ，然後記下 [密碼]  方塊中的值。

    1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ThirdLight 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [ThirdLight]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ThirdLight]  。

    ![應用程式清單](common/all-applications.png)

3. 在左窗格中，選取 [使用者和群組]  ：

    ![選取 [使用者和群組]](common/users-groups-blade.png)

4. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![選取 [新增使用者]](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下視窗底部的 [選取]  按鈕。

6. 如果您在 SAML 判斷提示中需要角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 按一下視窗底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-thirdlight-test-user"></a>建立 ThirdLight 測試使用者

若要讓 Azure AD 使用者能夠登入 ThirdLight，必須在 ThirdLight 中新增他們。 您需要手動新增他們。

若要建立使用者帳戶，請執行下列步驟：

1. 以系統管理員身分登入您的 ThirdLight 公司網站。

1. 移至 [使用者]  索引標籤。

1. 選取 [使用者和群組]  。

1. 選取 [新增使用者]  。

1. 針對所要佈建的有效 Azure AD 帳戶，輸入其使用者名稱、名稱或描述，以及電子郵件地址。 選擇新成員的 [預設] 或 [群組]。

1. 選取 [建立]  。

> [!NOTE]
> 您可以使用任何使用者帳戶建立工具或 ThirdLight 提供的 API 來佈建 Azure AD 使用者帳戶。

### <a name="test-single-sign-on"></a>測試單一登入

您現在必須使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [ThirdLight] 圖格時，應該會自動登入您設定 SSO 的 ThirdLight 執行個體。 如需存取面板的詳細資訊，請參閱[在 My Apps 入口網站上存取和使用應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)