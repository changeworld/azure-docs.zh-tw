---
title: 教學課程：Azure Active Directory 與 Jobscience 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Jobscience 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 8dc4087d1a10b4c4af7477a02f397c5a2bc547c2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459385"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>教學課程：Azure Active Directory 與 Jobscience 整合

在本教學課程中，您會了解如何整合 Jobscience 與 Azure Active Directory (Azure AD)。

Jobscience 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Jobscience 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Kantega SSO for FishEye/Crucible (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要瞭解有關 SaaS 應用程式與 Azure AD 整合的更多詳細資料，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Jobscience 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Jobscience 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在這裡取得一個月試用：[試用供應項目](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Jobscience
1. 設定並測試 Azure AD 單一登入

## <a name="adding-jobscience-from-the-gallery"></a>從資源庫新增 Jobscience
若要設定將 Jobscience 整合到 Azure AD 中，您需要從資源庫將 Jobscience 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Jobscience，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]****。 然後移至 [所有應用程式]****。

    ![螢幕擷取畫面顯示在 [管理] 下選取的 Azure 入口網站企業應用程式，並選取所有應用程式。][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![螢幕擷取畫面顯示已選取新的應用程式。][3]

1. 在搜尋方塊中，輸入 **Jobscience**。

    ![螢幕擷取畫面顯示 [從資源庫新增]，並輸入 jobscience。](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. 在結果面板中，選取 [Jobscience]****，然後按一下 [新增]**** 按鈕以新增應用程式。

    ![螢幕擷取畫面顯示包含 Jobscience 的結果。](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Jobscience 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Jobscience 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Jobscience 中的相關使用者之間，建立連結關聯性。

在 Jobscience 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 Jobscience 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. 設定**[Azure AD 單一登入](#configuring-azure-ad-single-sign-on)**-讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** -使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Jobscience 測試使用者](#creating-a-jobscience-test-user)** - 使 Jobscience 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#testing-single-sign-on)** -驗證設定是否正常運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Jobscience 應用程式中設定單一登入。

**若要設定與 Jobscience 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Jobscience]**** 應用程式整合頁面上，按一下 [單一登入]****。

    ![螢幕擷取畫面顯示在 Azure 入口網站的 [管理] 下選取了單一登入。][4]

1. 在 [單一登入]**** 對話方塊上，於 [模式]**** 選取 [SAML 登入]****，以啟用單一登入。
 
    ![螢幕擷取畫面顯示已選取 SAML 型登入模式。](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. 在 [Jobscience 網域及 URL]**** 區段中，執行下列步驟：

    ![螢幕擷取畫面顯示登入 U R L。](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    在 [登 **入 url** ] 文字方塊中，使用下列模式輸入 URL：  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > 這不是真實的值。 請使用實際的登入 URL 來更新此值。 從 [Jobscience 客戶支援小組](http://www.jobscience.com/support)或從您將建立的 SSO 設定檔 (本教學課程稍後會說明) 取得此值。 
 
1. 在 [ **SAML 簽署憑證** ] 區段上，按一下 [ **Certificate (Base64) ** 然後將憑證檔案儲存在您的電腦上。

    ![此螢幕擷取畫面顯示您可以在其中下載憑證的 [SAML 簽署憑證] 窗格。](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. 按一下 [儲存]  按鈕。

    ![螢幕擷取畫面：顯示 [儲存] 按鈕。](./media/jobscience-tutorial/tutorial_general_400.png)

1. 在 [Jobscience 組態]**** 區段上，按一下 [設定 Jobscience]**** 以開啟 [設定登入]**** 視窗。 從 [**快速參考] 區段**複製 [**登出 url]、[saml 實體識別碼] 和 [Saml 單一 Sign-On 服務 URL** ]。

    ![螢幕擷取畫面顯示 [Jobscience 設定] 視窗。](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. 以系統管理員身分登入您的 Jobscience 公司網站。

1. 移至 [設定]****。
   
   ![螢幕擷取畫面：顯示公司的安裝專案。](./media/jobscience-tutorial/IC784358.png "安裝程式")

1. 在 [系統管理員]**** 區段的左方導覽窗格中，按一下 [網域管理]**** 展開相關的區段，然後按一下 [我的網域]**** 來開啟 [我的網域]**** 頁面。 
   
   ![我的網域](./media/jobscience-tutorial/ic767825.png "我的網域")

1. 若要確認您的網域是否已正確設定，請確定它是在「**部署給使用者的步驟 4**」中，並檢查您的「**我的網域設定**」。

    ![部署到使用者的網域](./media/jobscience-tutorial/ic784377.png "部署到使用者的網域")

1. 在 Jobscience 公司網站上，依序按一下 [安全性控制]**** 及 [單一登入設定]****。
    
    ![螢幕擷取畫面會顯示從安全性控制項選取的單一 Sign-On 設定。](./media/jobscience-tutorial/ic784364.png "安全性控制")

1. 在 [單一登入設定]**** 區段中，執行下列步驟：
    
    ![單一登入設定](./media/jobscience-tutorial/ic781026.png "單一登入設定")
    
    a. 選取 [啟用 SAML]****。

    b. 按一下 **[新增]** 。

1. 在 [SAML 單一登入設定編輯]**** 對話方塊上，執行下列步驟：
    
    ![SAML 單一登入設定](./media/jobscience-tutorial/ic784365.png "SAML 單一登入設定")
    
    a. 在 [名稱]  文字方塊中，輸入您的組態名稱。

    b. 在 [簽發者]**** 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼]**** 值。

    c. 在 [實體識別碼]**** 文字方塊中，輸入 `https://salesforce-jobscience.com`

    d. 按一下 [瀏覽]**** 以上傳您的 Azure AD 憑證。

    e. 針對 [SAML 識別身分類型]****，選取 [判斷提示包含使用者物件的同盟識別碼]****。

    f. 在 [SAML 識別位置]**** 中，請選取 [身分識別在 Subject 陳述式的 NameIdentfier 元素中]****。

    g. 在 [識別提供者登入 URL]**** 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL]**** 值。

    h. 在 [識別提供者登出 URL]**** 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]**** 值。

    i. 按一下 [檔案]  。

1. 在 [系統管理員]**** 區段的左方導覽窗格中，按一下 [網域管理]**** 展開相關的區段，然後按一下 [我的網域]**** 來開啟 [我的網域]**** 頁面。 
    
    ![我的網域](./media/jobscience-tutorial/ic767825.png "我的網域")

1. 在 [我的網域]**** 頁面的 [登入頁面商標]**** 區段中，按一下 [編輯]****。
    
    ![螢幕擷取畫面顯示 [登入頁面商標] 區段和 [編輯] 按鈕。](./media/jobscience-tutorial/ic767826.png "登入頁面商標")

1. [登入頁面商標]**** 頁面的 [驗證服務]**** 區段中，會顯示您的 [SAML SSO 設定]**** 的名稱。 請選取該名稱，然後按一下 [儲存] ****。
    
    ![螢幕擷取畫面顯示 [登入頁面商標] 區段，其中包含 PPE 並選取 [儲存]。](./media/jobscience-tutorial/ic784366.png "登入頁面商標")

1. 若要取得 SP 初始化單一登入 URL，請按一下 [安全性控制項]**** 功能表區段中的 [單一登入設定]****。

    ![螢幕擷取畫面顯示已選取單一 Sign-On 設定的管理安全性控制項。](./media/jobscience-tutorial/ic784368.png "安全性控制")
    
    按一下您已經在上述步驟中建立的 SSO 設定檔。 此頁面會顯示您公司的單一登入 URL (例如 `https://companyname.my.salesforce.com?so=companyid`)。    

> [!TIP]
> 當您設定應用程式時，您現在可以在 [Azure 入口網站](https://portal.azure.com)中閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式]**** 區段新增此應用程式之後，只要按一下 [單一登入]**** 索引標籤，即可透過底部的 [組態]**** 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory]**** 圖示。

    ![螢幕擷取畫面顯示 Azure 入口網站中的 Azure A D 圖示。](./media/jobscience-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [ **使用者和群組** ]，然後按一下 [ **所有使用者**]。
    
    ![螢幕擷取畫面顯示從 [管理] 功能表選取的使用者和群組，並選取所有使用者。](./media/jobscience-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者]**** 對話方塊，按一下對話方塊頂端的 [新增]****。
 
    ![顯示 [新增] 按鈕以開啟 [使用者] 對話方塊的螢幕擷取畫面。](./media/jobscience-tutorial/create_aaduser_03.png) 

1. 在 [使用者] **** 對話頁面上，執行下列步驟：
 
    ![螢幕擷取畫面顯示 [使用者] 對話方塊，您可以在此輸入此步驟中的值。](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. 在 [名稱]**** 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱]**** 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼]**** 並記下 [密碼]**** 的值。

    d. 按一下 [建立]。
 
### <a name="creating-a-jobscience-test-user"></a>建立 Jobscience 測試使用者

若要讓 Azure AD 使用者能夠登入 Jobscience，必須將他們佈建到 Jobscience。 Jobscience 需以手動的方式佈建。

>[!NOTE]
>您可以使用任何其他的 Jobscience 使用者帳戶建立工具，或 Jobscience 提供的 API，以佈建 Azure Active Directory 使用者帳戶。
>  
        
**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Jobscience** 公司網站。

1. 移到 [設定]。
   
   ![螢幕擷取畫面：顯示安裝專案。](./media/jobscience-tutorial/ic784358.png "安裝程式")
1. 移至 [管理使用者 \> 使用者]****。
   
   ![使用者](./media/jobscience-tutorial/ic784369.png "使用者")
1. 按一下 **[新增使用者]**。
   
   ![所有使用者](./media/jobscience-tutorial/ic784370.png "所有使用者")
1. 在 [編輯使用者]**** 對話方塊中，執行下列步驟：
   
   ![使用者編輯](./media/jobscience-tutorial/ic784371.png "使用者編輯")
   
   a. 在 [名字]**** 文字方塊中，輸入使用者的名字，例如 Britta。
   
   b. 在 [姓氏]**** 文字方塊中，輸入使用者的姓氏，例如 Simon。
   
   c. 在 [別名]**** 文字方塊中，輸入使用者的別名，例如 brittas。

   d. 在 [電子郵件]  文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件地址。

   e. 在 [使用者名稱]**** 文字方塊中，輸入使用者的使用者名稱，例如 Brittasimon@contoso.com。

   f. 在 [暱稱]**** 文字方塊中，輸入使用者的暱稱，例如 Simon。

   g. 按一下 [檔案]  。

    
> [!NOTE]
> Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Jobscience 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![螢幕擷取畫面：顯示帳戶顯示名稱。][200] 

**若要將 Britta Simon 指派給 Jobscience，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟 [應用程式] 視圖，然後流覽至 [目錄] 視圖並移至 [ **企業應用程式** ]，然後按一下 [ **所有應用程式**]。

    ![螢幕擷取畫面顯示 [Azure 入口網站] 功能表中已選取所有應用程式的企業應用程式。][201] 

1. 在應用程式清單中，選取 [Jobscience]****。

    ![螢幕擷取畫面顯示已選取 Jobscience。](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]****。

    ![螢幕擷取畫面：顯示從 Azure 入口網站] 功能表選取的使用者和群組。][202] 

1. 按一下 [新增]  按鈕。 然後選取 [新增指派]**** 對話方塊上的 [使用者和群組]****。

    ![螢幕擷取畫面顯示用來新增指派的 [新增] 按鈕。][203]

1. 在 [使用者和群組]**** 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]****。

1. 按一下 [使用者和群組]**** 對話方塊上的 [選取]**** 按鈕。

1. 按一下 [新增指派]**** 對話方塊上的 [指派]**** 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Jobscience 圖格時，應該會自動登入您的 Jobscience 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png