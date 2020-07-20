---
title: 教學課程：Azure Active Directory 與 Work.com 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Work.com 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087085"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>教學課程：Azure Active Directory 與 Work.com 整合

在本教學課程中，您將了解如何整合 Work.com 與 Azure Active Directory (Azure AD)。
Work.com 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Work.com 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Work.com (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Work.com 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Work.com 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Work.com 支援 **SP** 起始的 SSO

## <a name="adding-workcom-from-the-gallery"></a>從資源庫新增 Work.com

若要設定將 Work.com 整合到 Azure AD 中，您需要從資源庫將 Work.com 新增到受控 SaaS app 清單。

**若要從資源庫新增 Work.com，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Work.com**，從結果面板中選取 [Work.com]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Work.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Work.com 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Work.com 中相關使用者之間的連結關聯性。

若要設定及測試與 Work.com 搭配運作的 Azure AD 單一登入，您需要完成下列建構元素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Work.com 單一登入](#configure-workcom-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Work.com 測試使用者](#create-workcom-test-user)** - 使Work.com 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

>[!NOTE]
>若要設定單一登入，您還需要設定自訂的 Work.com 網域名稱。 您至少需要定義一個網域名稱、測試網域名稱，並將它部署到整個組織。

若要使用 Work.com 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Work.com]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Work.com 網域及 URL 單一登入資訊](common/sp-signonurl.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [Work.com 用戶端支援小組](https://help.salesforce.com/articleView?id=000159855&type=3)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Work.com]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-workcom-single-sign-on"></a>設定 Work.com 單一登入

1. 以系統管理員身分登入 Work.com 租用戶。

2. 移到 [設定]  。
   
    ![安裝程式](./media/work-com-tutorial/ic794108.png "安裝程式")

3. 在 [系統管理員]  區段的左方導覽窗格中，按一下 [網域管理]  展開相關的區段，然後按一下 [我的網域]  來開啟 [我的網域]  頁面。 
   
    ![我的網域](./media/work-com-tutorial/ic767825.png "我的網域")

4. 若要確認已正確設定您的網域，請確定目前在 [步驟 4 已部署到使用者]  中，然後檢閱 [我的網域設定]  。
   
    ![已部署到使用者的網域](./media/work-com-tutorial/ic784377.png "已部署到使用者的網域")

5. 登入 Work.com 租用戶。

6. 移到 [設定]  。
    
    ![安裝程式](./media/work-com-tutorial/ic794108.png "安裝程式")

7. 展開 [安全性控制]  功能表，然後再按一下 [單一登入設定]  。
    
    ![單一登入設定](./media/work-com-tutorial/ic794113.png "單一登入設定")

8. 在 [單一登入設定]  對話方塊頁面執行下列步驟：
    
    ![啟用 SAML](./media/work-com-tutorial/ic781026.png "啟用 SAML")
    
    a. 選取 [已啟用 SAML]  。
    
    b. 按一下 **[新增]** 。

9. 在 [SAML 單一登入設定]  區段中，執行下列步驟：
    
    ![SAML 單一登入設定](./media/work-com-tutorial/ic794114.png "SAML 單一登入設定")
    
    a. 在 [名稱]  文字方塊中，輸入您的組態名稱。  
       
    > [!NOTE]
    > 提供 [名稱]  的值，[API 名稱]  文字方塊就會自動填入內容。
    
    b. 在 [簽發者]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。
    
    c. 若要上傳從 Azure 入口網站下載的憑證，請按一下 [瀏覽]  。
    
    d. 在 [實體識別碼]  文字方塊中，輸入 `https://salesforce-work.com`。
    
    e. 對於 [SAML 身分識別類型]  ，選取 [判斷提示包含來自使用者物件的同盟識別碼]  。
    
    f. 在 [SAML 識別位置]  ，請選取 [識別位於 Subject 陳述式的 NameIdentifier 元素中]  。
    
    g. 在 [識別提供者登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    h. 在 [識別提供者登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。
    
    i. 在 [服務提供者起始的要求繫結]  ，選取 [HTTP Post]  。
    
    j. 按一下 [檔案]  。

10. 在 Work.com 傳統入口網站的左側導覽窗格中，按一下 [網域管理]  以展開相關區段，然後按一下 [我的網域]  來開啟 [我的網域]  頁面。 
    
    ![我的網域](./media/work-com-tutorial/ic794115.png "我的網域")

11. 在 [我的網域]  頁面的 [登入頁面商標]  區段中，按一下 [編輯]  。
    
    ![登入頁面商標](./media/work-com-tutorial/ic767826.png "登入頁面商標")

12. [登入頁面商標]  頁面的 [驗證服務]  區段中，會顯示您的 [SAML SSO 設定]  的名稱。 請選取該名稱，然後按一下 [儲存]  。
    
    ![登入頁面商標](./media/work-com-tutorial/ic784366.png "登入頁面商標")

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Work.com 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Work.com]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Work.com]  。

    ![應用程式清單中的 Work.com 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-workcom-test-user"></a>建立 Work.com 測試使用者

Azure Active Directory 使用者必須先佈建到 Work.com，才可以登入。 Work.com 需以手動方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：

1. 以系統管理員身分登入您的 Work.com 公司網站。

2. 移到 [設定]  。
   
    ![安裝程式](./media/work-com-tutorial/IC794108.png "安裝程式")

3. 移至 [管理使用者 \> 使用者]  。
   
    ![管理使用者](./media/work-com-tutorial/IC784369.png "管理使用者")

4. 按一下 [新使用者]  。
   
    ![所有使用者](./media/work-com-tutorial/IC794117.png "所有使用者")

5. 在 [使用者編輯] 區段中，在您想要佈建至相關文字方塊之有效 Azure AD 帳戶的屬性中，執行下列步驟：
   
    ![使用者編輯](./media/work-com-tutorial/ic794118.png "使用者編輯")
   
    a. 在 [名字]  文字方塊中，輸入使用者的**名字**：**Britta**。
    
    b. 在 [姓氏]  文字方塊中，輸入使用者的**姓氏**：**Simon**。
    
    c. 在 [別名]  文字方塊中，輸入使用者的**名字**：**BrittaS**。
    
    d. 在 [電子郵件]  文字方塊中，輸入使用者 Brittasimon@contoso.com 的**電子郵件地址**。
    
    e. 在 [使用者名稱]  文字方塊中，輸入使用者的使用者名稱，例如 Brittasimon@contoso.com。
    
    f. 在 [暱稱]  文字方塊中，輸入使用者的**暱稱**：**Simon**。
    
    g. 選取 [角色]  、[使用者授權]  和 [設定檔]  。
    
    h. 按一下 [檔案]  。  
      
    > [!NOTE]
    > Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。
    > 

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Work.com] 圖格時，應該會自動登入您已設定 SSO 的 Work.com。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

