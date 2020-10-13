---
title: 教學課程：Azure Active Directory 與 AnswerHub 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 AnswerHub 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 69ebc238d36223a4287ae1de668f60f9c2db014a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713582"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>教學課程：Azure Active Directory 與 AnswerHub 整合

在本教學課程中，您會了解如何將 AnswerHub 與 Azure Active Directory (Azure AD) 整合。
將 AnswerHub 與 Azure AD 整合可提供下列優點：

* 您可以使用 Azure AD 來控制可存取 AnswerHub 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 AnswerHub (單一登入)。
* 您可以從一個位置集中管理您的帳戶：Azure 入口網站。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 AnswerHub 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以開始[一個月的試用](https://azure.microsoft.com/pricing/free-trial/)。
* 已啟用單一登入的 AnswerHub 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* AnswerHub 支援 SP 起始的 SSO。

## <a name="add-answerhub-from-the-gallery"></a>從資源庫新增 AnswerHub

若要設定將 AnswerHub 整合到 Azure AD 中，您需要將 AnswerHub 從資源庫新增到受控 SaaS 應用程式。

**若要從資源庫新增 AnswerHub：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取視窗頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **AnswerHub**。 在結果清單中選取 [AnswerHub]  ，然後選取 [新增]  。

     ![結果清單中的 AnswerHub](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會使用名為 Britta Simon 的測試使用者，設定及測試與 AnswerHub 搭配運作的 Azure AD 單一登入。
針對單一登入，您必須在 Azure AD 使用者與 AnswerHub 中的對應使用者之間建立連結。

若要設定及測試與 AnswerHub 搭配運作的 Azure AD 單一登入，您需要完成下列工作：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [設定 AnswerHub 單一登入](#configure-answerhub-single-sign-on) - 在應用程式端設定單一登入設定。
3. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user) (名為 Britta Simon)。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. 建立對應於並連結至 Azure AD 測試使用者的 AnswerHub 測試使用者。
6. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中設定 Azure AD 單一登入。

**若要設定與 AnswerHub 搭配運作的 Azure AD 單一登入：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [AnswerHub]  應用程式整合頁面上，選取 [單一登入]  。

    ![單一登入按鈕](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取方法對話方塊](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取編輯圖示，以開啟 [基本 SAML 設定]  對話方塊。

    ![以 SAML 設定單一登入頁面](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段中，完成下列步驟：

    ![基本 SAML 設定區段](common/sp-identifier.png)

    a. 在 [登入 URL]  方塊中，輸入具有以下模式的 URL：`https://<company>.answerhub.com`

    b. 在 [識別碼 (實體識別碼)]  方塊中，輸入具有以下模式的 URL：`https://<company>.answerhub.com`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [AnswerHub 客戶支援小組](mailto:success@answerhub.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，依據您的需求選取 [憑證 (Base64)] 旁邊的 [下載] 連結，並將憑證儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 AnswerHub]  區段中，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

   您可以複製這些 URL：
    - 登入 URL

    - Azure AD 識別碼

    - 登出 URL

### <a name="configure-answerhub-single-sign-on"></a>設定 AnswerHub 單一登入

在本節中，您會設定 AnswerHub 的單一登入。  

**若要設定 AnswerHub 單一登入：**

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 AnswerHub 公司網站。

    > [!NOTE]
    > 如果您需要設定 AnswerHub 的協助，請連絡 [AnswerHub 支援小組](mailto:success@answerhub.com.)。

2. 移至 [管理]  。

3. 在 [使用者和群組]  索引標籤上，於左側窗格的 [社交設定]  區段中，選取 [SAML 設定]  。

4. 在 [IDP 設定]  索引標籤上，完成下列步驟：

    ![顯示已選取 [使用者和群組] 索引標籤的 AnswerHub 頁面螢幕擷取畫面。](./media/answerhub-tutorial/ic785172.png "SAML 設定")  
  
    a. 在 [IDP 登入 URL]  方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。
  
    b. 在 [IDP 登出 URL]  方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  。

    c. 在 [IDP 名稱識別碼格式] 方塊中，輸入在 Azure 入口網站的 [使用者屬性] 區段中選取的 [識別碼] 值。
  
    d. 選取 [金鑰和憑證]  。

5. 在 [金鑰和憑證]  區段中，完成下列步驟：

    [金鑰和憑證] 區段![](./media/answerhub-tutorial/ic785173.png "金鑰和憑證")  

    a. 在「記事本」中開啟從 Azure 入口網站下載的 Base-64 編碼憑證，複製其內容，然後將內容貼到 [IDP 公開金鑰 (x509 格式)]  方塊中。
  
    b. 選取 [儲存]  。

6. 在 [IDP 組態]  索引標籤上，再次選取 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

**若要建立 Azure AD 測試使用者：**

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![選取 Azure Active Directory、使用者、所有使用者](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在使用者屬性中，完成下列步驟。

    ![使用者屬性](common/user-properties.png)

    a. 在 [名稱]  方塊中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  方塊中，輸入 **brittasimon\@<yourcompanydomain.extension>** 。  
    例如： BrittaSimon@contoso.com 。

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。

    d. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 AnswerHub 的存取權授與 Britta Simon，以設定讓她使用 Azure AD 單一登入。

**若要指派 Azure AD 測試使用者：**

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [AnswerHub]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [AnswerHub]  。

    ![應用程式清單](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![選取 [使用者和群組]](common/users-groups-blade.png)

4. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後選取畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 

7. 選取畫面底部的 [選取]  按鈕。

8. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-an-answerhub-test-user"></a>建立 AnswerHub 測試使用者

若要讓 Azure AD 使用者能夠登入 AnswerHub，必須在 AnswerHub 中新增他們。 在 AnswerHub 中，此工作會手動完成。

**若要設定使用者帳戶：**

1. 以系統管理員身分登入您的 **AnswerHub** 公司網站。

2. 移至 [管理]  。

3. 選取 [使用者和群組]  索引標籤。

4. 在左側窗格的 [管理使用者]  區段中，選取 [建立或匯入使用者]  ，然後選取 [使用者和群組]  。

   ![顯示 AnswerHub 頁面的螢幕擷取畫面，其中已選取 [使用者和群組] 索引標籤以及呼叫建立或匯入使用者連結。](./media/answerhub-tutorial/ic785175.png "使用者與群組")

5. 在適當的方塊中，輸入您要新增之有效 Azure AD 帳戶的 [電子郵件地址]  、[使用者名稱]  和 [密碼]  ，然後選取 [儲存]  。

> [!NOTE]
> 您可以使用由 AnswerHub 提供的任何其他使用者帳戶建立工具或 API 來設定 Azure AD 使用者帳戶。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 AnswerHub 圖格時，應該會自動登入您已設定 SSO 的 AnswerHub。 如需存取面板的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [用於整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

