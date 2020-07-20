---
title: 教學課程：Azure Active Directory 與 Optimizely 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Optimizely 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943946"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>教學課程：Azure Active Directory 與 Optimizely 整合

在本教學課程中，您會了解如何整合 Optimizely 與 Azure Active Directory (Azure AD)。
Optimizely 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Optimizely 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Optimizely (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Optimizely 與 Azure AD 的整合作業，需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Optimizely 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Optimizely 支援由 **SP** 起始的 SSO

## <a name="adding-optimizely-from-the-gallery"></a>從資源庫中新增 Optimizely

若要設定將 Optimizely 整合到 Azure AD 中，您需要從資源庫將 Optimizely 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Optimizely，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Optimizely**，從結果面板中選取 [Optimizely]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Optimizely](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Optimizely 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Optimizely 中相關使用者之間的連結關聯性。

若要設定及測試與 Optimizely 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Optimizely 單一登入](#configure-optimizely-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Optimizely 測試使用者](#create-optimizely-test-user)** - 在 Optimizely 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Optimizely 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Optimizely]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Optimizely 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://app.optimizely.net/<instance name>`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`urn:auth0:optimizely:contoso`

    > [!NOTE]
    > 這些都不是真正的值。 您將會使用實際的「登入 URL」與「識別碼」來更新值，稍後會在本教學課程中說明。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. Optimizely 應用程式會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 **編輯** 圖示以開啟 [使用者屬性] 對話方塊。 

    ![image](common/edit-attribute.png)

6. 除了以上屬性之外，Optimizely 應用程式還預期 SAML 回應中會再多傳回幾個屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 名稱 | 來源屬性 |
    | ---------------| --------------- |
    | 電子郵件 | user.mail |
    
    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [安裝 Optimizely]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-optimizely-single-sign-on"></a>設定 Optimizely 單一登入

1. 若要在 **Optimizely** 端設定單一登入，請連絡您的 Optimizely 帳戶管理員，並提供已下載的**憑證 (Base64)** 和複製的適當 URL。

2. Optimizely 會回應您的電子郵件，提供單一登入 URL (SP 啟始的 SSO) 和識別碼 (服務提供者的實體識別碼) 的值。

    a. 複製 Optimizely 提供的**SP 啟始的 SSO URL**，並貼到 Azure 入口網站上 [基本 SAML 設定] 區段的 [登入 URL] 文字方塊中。

    b. 複製 Optimizely 提供的**服務提供者實體識別碼**，並貼到 Azure 入口網站上 [基本 SAML 設定] 區段的 [識別碼] 文字方塊中。

3. 在不同的瀏覽器視窗中，登入您的 Optimizely 應用程式。

4. 按一下您在右上角的帳戶名稱，然後按 [帳戶設定]  。

    ![Azure AD 單一登入](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. 在 [帳戶] 索引標籤的 [概觀] 區段中，選取 [單一登入] 下的 [啟用 SSO] 方塊。
  
    ![Azure AD 單一登入](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. 按一下 [儲存] 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Optimizely 的存取權授與 Britta Simon，讓 Britta Simon 能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Optimizely]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Optimizely]  。

    ![應用程式清單中的 [Optimizely] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-optimizely-test-user"></a>建立 Optimizely 測試使用者

在本節中，您會在 Optimizely 中建立名為 Britta Simon 的使用者。

1. 在首頁上，選取 [Collaborators]  \(共同作業者) 索引標籤。

2. 若要將新的共同作業者加入專案，請按一下 [新增共同作業者]  。
   
    ![建立 Azure AD 測試使用者](./media/optimizely-tutorial/create_aaduser_10.png)

3. 填寫電子郵件地址，並為其指派角色。 按一下 [邀請]  。

    ![建立 Azure AD 測試使用者](./media/optimizely-tutorial/create_aaduser_11.png)

4. 這些人員會收到一封電子郵件邀請。 他們必須使用該電子郵件地址來登入 Optimizely。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Optimizely] 圖格時，應該會自動登入您已設定 SSO 的 Optimizely。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

