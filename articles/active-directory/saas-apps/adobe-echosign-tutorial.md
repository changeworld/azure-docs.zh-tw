---
title: 教學課程：Azure Active Directory 與 Adobe Sign 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Adobe Sign 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 18a034c6af9659e113a821e072098aee873c2ee2
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760796"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>教學課程：Azure Active Directory 與 Adobe Sign 整合

在本教學課程中，您會了解如何將 Adobe Sign 與 Azure Active Directory (Azure AD) 整合。
將 Adobe Sign 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Adobe Sign 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Adobe Sign (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Adobe Sign 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Adobe Sign 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Adobe Sign 支援由 **SP** 起始的 SSO

## <a name="adding-adobe-sign-from-the-gallery"></a>從資源庫新增 Adobe Sign

若要設定將 Adobe Sign 整合到 Azure AD 中，您需要從資源庫將 Adobe Sign 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Adobe Sign，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Adobe Sign**，並從結果面板中選取 [Adobe Sign]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Adobe Sign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Adobe Sign 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Adobe Sign 中相關使用者之間的連結關聯性。

若要設定及測試與 Adobe Sign 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Adobe Sign 單一登入](#configure-adobe-sign-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Adobe Sign 測試使用者](#create-adobe-sign-test-user)** - 使 Adobe Sign 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Adobe Sign 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Adobe Sign]  應用程式整合頁面中，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Adobe Sign 網域和 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.echosign.com/`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.echosign.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Adobe Sign 用戶端支援小組](https://helpx.adobe.com/in/contact/support.html)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Adobe Sign]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-adobe-sign-single-sign-on"></a>設定 Adobe Sign 單一登入

1. 設定之前，請先連絡 [Adobe Sign 用戶端支援小組](https://helpx.adobe.com/in/contact/support.html)，以將網域新增至 Adobe Sign 允許清單。 以下是新增網域的方式：

    a. [Adobe Sign Client 支援小組](https://helpx.adobe.com/in/contact/support.html)會傳送隨機產生的權杖給您。 您網域的權杖會如下所示：**adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. 在 DNS 文字記錄中發佈驗證權杖，然後通知 [Adobe Sign Client 支援小組](https://helpx.adobe.com/in/contact/support.html)。
    
    > [!NOTE]
    > 這可能需要幾天或更長的時間。 請注意，DNS 傳播延遲意謂著在 DNS 中發佈的值可能長達一或多個小時無法顯示。 您的 IT 系統管理員應該知道如何在 DNS 文字記錄中發佈此權杖。
    
    c. 當您於發佈權杖後透過支援票證通知 [Adobe Sign Client 支援小組](https://helpx.adobe.com/in/contact/support.html)時，他們會驗證該網域並將它新增至您的帳戶。
    
    d. 通常在 DNS 記錄上發佈權杖的步驟如下：

    * 登入您的網域帳戶
    * 尋找用於更新 DNS 記錄的頁面。 此頁面可能名為 [DNS 管理]、[名稱伺服器管理] 或 [進階設定]。
    * 尋找您網域的 TXT 記錄。
    * 以 Adobe 提供的完整權杖值新增 TXT 記錄。
    * 儲存您的變更。

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Adobe Sign 公司網站。

1. 在 [SAML] 功能表中，選取 [Account Settings] \(帳戶設定\)   > [SAML Settings] \(SAML 設定\)  。
   
    ![Adobe Sign [SAML Settings] \(SAML 設定\) 頁面的螢幕擷取畫面](./media/adobe-echosign-tutorial/ic789520.png "帳戶")

1. 在 [SAML 設定]  區段中，執行下列步驟：
  
   ![醒目提示 SAML 設定 (包括 [SAML 強制]) 的螢幕擷取畫面。](./media/adobe-echosign-tutorial/ic789521.png "SAML 設定")
   
   ![[SAML Settings] \(SAML 設定\) 的螢幕擷取畫面](./media/adobe-echosign-tutorial/ic789522.png "SAML 設定")

   a. 在 [SAML Mode] \(SAML 模式\)  底下，選取 [SAML Mandatory] \(SAML 強制\)  。
   
   b. 選取 [Allow Echosign Account Administrators to log in using their Echosign Credentials] \(允許 EchoSign 帳戶管理員使用其 EchoSign 認證登入\)  。
   
   c. 在 [User Creation] \(使用者建立\)  底下，選取 [Automatically add users authenticated through SAML] \(透過 SAML 自動新增已驗證的使用者\)  。

   d. 將從 Azure 入口網站複製的 [Azure AD 識別碼]  貼到 [Idp 實體識別碼]  文字方塊中。
    
   e. 在 [Idp 登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]。
   
   f. 在 [Idp 登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]。

   g. 在「記事本」中開啟所下載的**憑證 (Base64)** 檔案。 將其內容複製到剪貼簿，然後貼到 [IdP Certificate] \(IdP 憑證\)  文字方塊中。

   h. 選取 [Save Changes] \(儲存變更\)  。

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

在本節中，您會將 Adobe Sign 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Adobe Sign]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **Adobe Sign** 並加以選取。

    ![應用程式清單中的 Adobe Sign 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-adobe-sign-test-user"></a>建立 Adobe Sign 測試使用者

若要讓 Azure AD 使用者能夠登入 Adobe Sign，必須將這些使用者佈建到 Adobe Sign。 這是手動工作。

>[!NOTE]
>您可以使用任何其他 Adobe Sign 使用者帳戶建立工具或 Adobe Sign 提供的 API，來佈建 Azure AD 使用者帳戶。 

1. 以系統管理員身分登入您的 **Adobe Sign** 公司網站。

2. 在頂端的功能表中，選取 [Account] \(帳戶\)  。 接著，在左側窗格中，選取 [Users & Groups] \(使用者和群組\)   > [Create a new user] \(建立新使用者\)  。
   
    ![Adobe Sign 公司網站的螢幕擷取畫面，其中已醒目提示 [Account] \(帳戶\)、[Users &Groups] \(使用者和群組\) 及 [Create a new user] \(建立新使用者\)](./media/adobe-echosign-tutorial/ic789524.png "帳戶")
   
3. 在 [建立新的使用者]  區段中，執行下列步驟：
   
    ![[Create a new user] \(建立新使用者\) 區段的螢幕擷取畫面](./media/adobe-echosign-tutorial/ic789525.png "建立使用者")
   
    a. 針對您想要佈建的有效 Azure AD 帳戶，將帳戶的 [Email Address] \(電子郵件地址\)  、[First Name] \(名字\)  及 [Last Name] \(姓氏\)  輸入至相關文字方塊中。
   
    b. 選取 [Create User] \(建立使用者\)  。

>[!NOTE]
>Azure Active Directory 帳戶持有者會收到一封電子郵件，其中包含一個用來確認帳戶以使其生效的連結。 

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Adobe Sign] 圖格時，應該會自動登入您已設定 SSO 的 Adobe Sign。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

