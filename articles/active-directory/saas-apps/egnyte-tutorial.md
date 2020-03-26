---
title: 教學課程：Azure Active Directory 與 Egnyte 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Egnyte 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8eb0f0d566d656436da11141ea7f6c521b7b82
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983641"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Egnyte 整合

在本教學課程中，您將了解如何整合 Egnyte 與 Azure Active Directory (Azure AD)。 在整合 Egnyte 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Egnyte 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Egnyte。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Egnyte 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Egnyte 支援 **SP** 起始的 SSO
* 設定 Egnyte 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>從資源庫新增 Egnyte

若要設定將 Egnyte 整合到 Azure AD 中，您需要從資源庫將 Egnyte 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Egnyte**。
1. 從結果面板中選取 [Egnyte]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Egnyte 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Egnyte 中相關使用者之間的連結關聯性。

若要設定及測試與 Egnyte 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

若要設定及測試與 Egnyte 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Egnyte SSO](#configure-egnyte-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Egnyte 測試使用者](#create-egnyte-test-user)** - 使 Egnyte 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Egnyte 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Egnyte]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Egnyte 網域與 URL 單一登入資訊](common/sp-signonurl.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<companyname>.egnyte.com`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [Egnyte 用戶端支援小組](https://www.egnyte.com/corp/contact_egnyte.html)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

5. 在 [設定 Egnyte]  區段上，依據您的需求複製適當的 URL。

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
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Egnyte 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Egnyte]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Egnyte]  。

    ![應用程式清單中的 Egnyte 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-egnyte-sso"></a>設定 Egnyte SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Egnyte 公司網站。

2. 按一下 [設定]  。
   
    ![設定](./media/egnyte-tutorial/ic787819.png "設定")

3. 在功能表中按一下 [設定]  。

    ![設定](./media/egnyte-tutorial/ic787820.png "設定")

4. 按一下 [組態]  索引標籤，然後按一下 [安全性]  。

    ![安全性](./media/egnyte-tutorial/ic787821.png "安全性")

5. 在 [單一登入驗證]  區段中，執行下列步驟：

    ![單一登入驗證](./media/egnyte-tutorial/ic787822.png "單一登入驗證")   
    
    a. 針對 [單一登入驗證]  ，選取 **SAML 2.0**。
   
    b. 針對 [識別提供者]  ，選取 [AzureAD]  。
   
    c. 將從 Azure 入口網站複製的 [登入 URL]  貼到 [識別提供者登入 URL]  文字方塊中。
   
    d. 將從 Azure 入口網站複製的 [Azure AD 識別碼]  貼到 [識別提供者實體識別碼]  文字方塊中。
      
    e. 在從 Azure 入口網站下載的記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後在 [識別提供者憑證]  文字方塊貼上。
   
    f. 針對 [預設使用者對應]  ，選取 [電子郵件地址]  。
   
    g. 針對 [使用網域指定的簽發者值]  ，選取 [停用]  。
   
    h. 按一下 [檔案]  。

### <a name="create-egnyte-test-user"></a>建立 Egnyte 測試使用者

若要讓 Azure AD 使用者可以登入 Egnyte，必須將他們佈建到 Egnyte 中。 Egnyte 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Egnyte** 公司網站。

2. 移至 [設定] \> [使用者和群組]  。

3. 按一下 [新增使用者]  ，然後選取您想要加入的使用者類型。
   
    ![使用者](./media/egnyte-tutorial/ic787824.png "使用者")

4. 在 [新增強大使用者]  區段中，執行下列步驟：
    
    ![新的標準使用者](./media/egnyte-tutorial/ic787825.png "新的標準使用者")   

    a. 在 [電子郵件]  文字方塊中，輸入使用者的電子郵件地址，例如 **Brittasimon\@contoso.com**。

    b. 在 [使用者名稱]  文字方塊中，輸入使用者的使用者名稱，例如 **Brittasimon**。

    c. 選取 [單一登入]  作為 [驗證類型]  。
   
    d. 按一下 [檔案]  。
    
    >[!NOTE]
    >Azure Active Directory 帳戶持有者將會收到電子郵件通知。
    >

>[!NOTE]
>您可以使用任何其他的 Egnyte 使用者帳戶建立工具或 Egnyte 提供的 API，來佈建 Azure AD 使用者帳戶。
>

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Egnyte] 圖格時，應該會自動登入您設定 SSO 的 Egnyte。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
