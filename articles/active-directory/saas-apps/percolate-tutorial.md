---
title: 教學課程：Azure Active Directory 與 Percolate 整合 | Microsoft Docs
description: 在本教學課程中，您會了解如何設定 Azure Active Directory 與 Percolate 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: be9f2163813d1dc52a593c110fa4cef9b561614e
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995416"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>教學課程：Azure Active Directory 與 Percolate 整合

在本教學課程中，您將了解如何整合 Percolate 與 Azure Active Directory (Azure AD)。

此整合有下列優點：

* 您可以使用 Azure AD 來控制可存取 Percolate 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Percolate (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Percolate 的整合，您需要具備：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入的 Percolate 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Percolate 支援 SP 和 IDP 起始的 SSO。

## <a name="add-percolate-from-the-gallery"></a>從資源庫新增 Percolate

若要進行將 Percolate 整合到 Azure AD 中的設定，您必須從資源庫將 Percolate 新增至受控 SaaS 應用程式清單。

1. 在 [Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [Azure Active Directory]  ：

    ![選取 Azure Active Directory](common/select-azuread.png)

2. 移至 [企業應用程式]   > [所有應用程式]  ：

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取視窗頂端的 [新增應用程式]  ：

    ![選取 [新增應用程式]](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Percolate**。 在搜尋結果中，選取 [Percolate]  ，然後選取 [新增]  。

     ![搜尋結果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會使用名為 Britta Simon 的測試使用者，設定及測試與 Percolate 搭配運作的 Azure AD 單一登入。
若要啟用單一登入，您必須建立 Azure AD 使用者與 PPercolate 中相關使用者之間的關聯性。

若要設定及測試與 Percolate 搭配運作的 Azure AD 單一登入，您需要完成下列步驟：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** ，讓您的使用者能夠使用此功能。
2. 在應用程式端 **[設定 Percolate 單一登入](#configure-percolate-single-sign-on)** 。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，為使用者啟用 Azure AD 單一登入。
5. **[建立 Percolate 測試使用者](#create-a-percolate-test-user)** ，其會連結至 Azure AD 中代表該使用者的項目。
6. **[測試單一登入](#test-single-sign-on)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Percolate 搭配運作的 Azure AD 單一登入，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Percolate]  應用程式整合頁面上，選取 [單一登入]  ：

    ![選取 [單一登入]](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入：

    ![選取單一登入方法](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  圖示，以開啟 [基本 SAML 組態]  對話方塊：

    ![編輯圖示](common/edit-urls.png)

4. 在 [基本 SAML 組態]  對話方塊中，您不需要執行任何動作來設定 IDP 起始模式中的應用程式。 應用程式已與 Azure 整合。

    ![Percolate 網域和 URL 單一登入資訊](common/preintegrated.png)

5. 若要在 SP 起始的模式中設定應用程式，請選取 [設定額外的 URL]  ，然後在 [登入 URL]  方塊中輸入 **https://percolate.com/app/login** ：

   ![顯示選取了 [設定其他 U R L] 的螢幕擷取畫面，其中已反白顯示 [登入 U R L] 文字方塊。](common/metadata-upload-additional-signon.png)
6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，選取 [複製]  圖示以複製**應用程式同盟中繼資料 URL**。 要儲存此 URL。

    ![複製應用程式同盟中繼資料 URL](common/copy-metadataurl.png)

7. 在 [設定 Percolate]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    1. **登入 URL**。

    1. **Azure AD 識別碼**。

    1. **登出 URL**。

### <a name="configure-percolate-single-sign-on"></a>設定 Percolate 單一登入

1. 在新的網頁瀏覽器視窗中，以系統管理員身分登入 Percolate。

2. 在 [首頁] 的左側選取 [設定]  ：
    
    ![選取設定](./media/percolate-tutorial/configure01.png)

3. 在左側窗格中，選取 [組織]  下方的 [SSO]  ：

    ![選取組織下方的 SSO](./media/percolate-tutorial/configure02.png)

    1. 在 [登入 URL]  方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    1. 在 [實體識別碼]  方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    1. 在記事本中開啟您從 Azure 入口網站下載的 base-64 編碼憑證。 複製其內容，並將其貼至 [x509 憑證]  方塊中。

    1. 在 [電子郵件屬性]  方塊中，輸入**電子郵件地址**。

    1. [識別提供者中繼資料 URL]  方塊是選擇性欄位。 如果您已從 Azure 入口網站複製**應用程式同盟中繼資料 URL**，您可以將其貼到此方塊中。

    1. 在 [是否應簽署 AuthNRequests？]  清單中，選取 [否]  。

    1. 在 [啟用 SSO 自動佈建]  清單中，選取 [否]  。

    1. 選取 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  ：

    ![選取 [所有使用者]](common/users.png)

2. 在畫面頂端選取 [新增使用者]  ：

    ![選取 [新增使用者]](common/new-user.png)

3. 在 [使用者]  對話方塊中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    1. 在 [名稱]  方塊中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱] 方塊中，輸入 **BrittaSimon@\<yourcompanydomain>.\<extension>** 。 (例如，BrittaSimon@contoso.com)。

    1. 選取 [顯示密碼]  ，然後記下 [密碼]  方塊中的值。

    1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Percolate 的存取權授與 Britta Simon，讓她能夠使用 Azure AD 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Percolate]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Percolate]  。

    ![應用程式清單](common/all-applications.png)

3. 在左窗格中，選取 [使用者和群組]  ：

    ![顯示已從左窗格中選取 [使用者和群組] 的螢幕擷取畫面。](common/users-groups-blade.png)

4. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![選取 [使用者和群組]](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您在 SAML 判斷提示中需要角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-percolate-test-user"></a>建立 Percolate 測試使用者

若要讓 Azure AD 使用者能夠登入 Percolate，必須在 Percolate 中新增他們。 您需要手動新增他們。

若要建立使用者帳戶，請執行下列步驟：

1. 以系統管理員身分登入 Percolate。

2. 在左側窗格中，選取 [組織]  下方的 [使用者]  。 選取 [新增使用者]  ：

    ![選取 [新增使用者]](./media/percolate-tutorial/configure03.png)

3. 在 [建立使用者]  頁面上，執行下列步驟。

    ![建立使用者頁面](./media/percolate-tutorial/configure04.png)

    1. 在 [電子郵件]  方塊中，輸入使用者的電子郵件地址。 例如： brittasimon@contoso.com 。

    1. 在 [全名]  方塊中，輸入使用者的名稱。 例如，**Brittasimon**。

    1. 選取 [建立使用者]  。

### <a name="test-single-sign-on"></a>測試單一登入

您現在必須使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Percolate] 圖格時，應該會自動登入您已設定 SSO 的 Percolate 執行個體。 如需詳細資訊，請參閱[在「我的應用程式」入口網站上存取和使用應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)