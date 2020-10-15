---
title: 教學課程：Azure Active Directory 與 Tableau Online 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Tableau Online 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Tableau Online 整合

在本教學課程中，您將了解如何整合 Tableau Online 與 Azure Active Directory (Azure AD)。 在整合 Tableau Online 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Tableau Online 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Tableau Online。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Tableau Online 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Tableau Online 支援由 **SP** 起始的 SSO
* 設定 Tableau Online 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>從資源庫新增 Tableau Online

若要設定 Tableau Online 與 Azure AD 整合，您需要從資源庫將 Tableau Online 加入到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Tableau Online**。
1. 從結果面板中選取 [Tableau Online]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Tableau Online 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Tableau Online 中相關使用者之間的連結關聯性。

若要設定及測試與 Tableau Online 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Tableau Online SSO](#configure-tableau-online-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Tableau Online 測試使用者](#create-tableau-online-test-user)** - 使 Tableau Online 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Tableau Online 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Tableau Online]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Tableau Online 網域與 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，輸入 URL：`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入 URL：`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > 您會收到本教學課程的 [設定 Tableau Online]  區段中的 `<entityid>` 值。 實體 ID 值會是 [設定 Tableau Online]  區段中的 [Azure AD 識別碼]  值。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Tableau Online]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如，BrittaSimon\@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Tableau Online 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Tableau Online]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Tableau Online]  。

    ![應用程式清單中的 Tableau Online 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-tableau-online-sso"></a>設定 Tableau Online SSO

1. 在不同的瀏覽器視窗中，登入您的 Tableau Online 應用程式。 依序前往 [設定]  和 [驗證]  。

    ![設定單一登入](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. 若要在 [驗證類型]  區段下啟用 SAML： 請勾選 [啟用其他驗證方法]  ，然後勾選 [SAML]  核取方塊。

    ![設定單一登入](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. 向下捲動到 [將中繼資料檔匯入到 Tableau Online]  區段。  按一下 [瀏覽]，然後匯入您從 Azure AD 下載的中繼資料檔案。 然後，按一下 [套用]  。

   ![設定單一登入](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. 在 [比對判斷提示]  區段中，針對 [電子郵件地址]  、[名字]  和 [姓氏]  插入對應的識別提供者判斷提示名稱。 若要從 Azure AD 取得這項資訊︰ 
  
    a. 在 Azure 入口網站中，移至 [Tableau Online]  應用程式整合頁面。

    b. 在 [使用者屬性和宣告]  區段中，按一下編輯圖示。

   ![設定單一登入](./media/tableauonline-tutorial/attributesection.png)

    c. 使用下列步驟複製 givenname、email 和 surname 屬性的命名空間值：

   ![Azure AD 單一登入](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. 按一下 [user.givenname]  值

    e. 從 [命名空間]  文字方塊複製該值。

    ![設定單一登入](./media/tableauonline-tutorial/attributesection2.png)

    f. 若要複製 email 和 surname 的命名空間值，請重複上述步驟。

    g. 切換至 Tableau Online 應用程式，然後將 [使用者屬性和宣告]  區段設定如下：

    * 電子郵件︰**mail** 或 **userprincipalname**

    * 名字︰ **givenname**

    * 姓氏︰ **surname**

    ![設定單一登入](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>建立 Tableau Online 測試使用者

在本節中，您要在 Tableau Online 中建立名為 Britta Simon 的使用者。

1. 在 [Tableau Online]  中，依序按一下 [設定]  和 [驗證]  區段。 向下捲動至 [管理使用者]  區段。 依序按一下 [新增使用者]  和 [輸入電子郵件地址]  。
  
    ![建立 Azure AD 測試使用者](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. 選取 [新增 (SAML) 驗證的使用者]  。 在 [輸入電子郵件地址]  文字方塊中新增 britta.simon\@contoso.com
  
    ![建立 Azure AD 測試使用者](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. 按一下 [加入使用者]  。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Tableau Online 圖格時，應該會自動登入您設定 SSO 的 Tableau Online。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)