---
title: 教學課程：Azure Active Directory 與 Mimecast Admin Console 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Mimecast Admin Console 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160661"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>教學課程：Azure Active Directory 與 Mimecast Admin Console 整合

在本教學課程中，您會了解如何整合 Mimecast Admin Console 與 Azure Active Directory (Azure AD)。
Mimecast Admin Console 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Mimecast Admin Console 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Mimecast Admin Console (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Mimecast Admin Console 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Mimecast Admin Console 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Mimecast Admin Console 支援 **SP** 起始的 SSO

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>從資源庫新增 Mimecast Admin Console

若要設定將 Mimecast Admin Console 整合到 Azure AD 中，您需要從資源庫將 Mimecast Admin Console 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Mimecast Admin Console，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Mimecast Admin Console**，從結果面板中選取 **Mimecast Admin Console**，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Mimecast Admin Console](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Mimecast Admin Console 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Mimecast Admin Console 中相關使用者之間的連結關聯性。

若要設定及測試與 Mimecast Admin Console 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Mimecast Admin Console 單一登入](#configure-mimecast-admin-console-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Mimecast Admin Console 測試使用者](#create-mimecast-admin-console-test-user)** - 使 Mimecast Admin Console 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Mimecast Admin Console 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Mimecast Admin Console]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Mimecast Admin Console 網域與 URL 單一登入資訊](common/sp-signonurl.png)

    在 [登入 URL]  文字方塊中，輸入 URL：
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > 登入 URL 是地區特定的 URL。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Mimecast Admin Console]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-mimecast-admin-console-single-sign-on"></a>設定 Mimecast Admin Console 單一登入

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Mimecast Admin Console 公司網站。

2. 移至 [服務 \> 應用程式]。

    ![服務](./media/mimecast-admin-console-tutorial/ic794998.png "服務")

3. 按一下 [驗證設定檔]  。

    ![驗證設定檔](./media/mimecast-admin-console-tutorial/ic794999.png "驗證設定檔")
    
4. 按一下 [新驗證設定檔]  。

    ![新驗證設定檔](./media/mimecast-admin-console-tutorial/ic795000.png "新驗證設定檔")

5. 在 [驗證設定檔]  區段中，執行下列步驟：

    ![驗證設定檔](./media/mimecast-admin-console-tutorial/ic795015.png "驗證設定檔")
    
    a. 在 [描述]  文字方塊中，輸入您的組態名稱。
    
    b. 選取 [強制執行 Mimecast Admin Console 的 SAML 驗證]  。
    
    c. 在 [提供者]  中選取 [Azure Active Directory]  。
    
    d. 在 [簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  。
    
    e. 在 [登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。

    f. 在 [登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。
    
    >[!NOTE]
    >Mimecast Admin Console 的登入 URL 值與登出 URL 值是相同的。
    
    g. 在記事本中開啟您從 Azure 入口網站下載的 base-64 憑證，移除第一行 (“ *--* “) 與最後一行 (“ *--* “)，將它的其餘內容複製到您的剪貼簿，然後貼到 [識別提供者憑證 (中繼資料)]  文字方塊中。
    
    h. 選取 [允許單一登入]  。
    
    i. 按一下 [檔案]  。

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

在本節中，您會將 Mimecast Admin Console 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  和 [Mimecast Admin Console]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 [Mimecast Admin Console]  並加以選取。

    ![應用程式清單中的 Mimecast Admin Console 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-mimecast-admin-console-test-user"></a>建立 Mimecast Admin Console 測試使用者

若要讓 Azure AD 使用者能夠登入 Mimecast Admin Console，必須將它們佈建到 Mimecast Admin Console。 Mimecast Admin Consol 需以手動的方式佈建。

* 您需要先註冊網域才能建立使用者。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身份登入您的 **Mimecast Admin Console** 。

2. 移至 [目錄 \> 內部]。
   
    ![Directories](./media/mimecast-admin-console-tutorial/ic795003.png "目錄")

3. 按一下 [註冊新網域]  。
   
    ![註冊新網域](./media/mimecast-admin-console-tutorial/ic795004.png "註冊新網域")

4. 在您建立好新網域之後，，按一下 [新位址]  。
   
    ![新位址](./media/mimecast-admin-console-tutorial/ic795005.png "新位址")

5. 在 [新位址] 對話方塊中，執行下列步驟：
   
    ![儲存](./media/mimecast-admin-console-tutorial/ic795006.png "儲存")
   
    a. 在相關的文字方塊中，輸入您要佈建之有效 Azure AD 帳戶的 [電子郵件地址]  、[全域名稱]  、[密碼]  、[確認密碼]  屬性。

    b. 按一下 [檔案]  。

>[!NOTE]
>您可以使用任何其他的 Mimecast Admin Console 使用者帳戶建立工具或 Mimecast Admin Console 提供的 API，佈建 Azure AD 使用者帳戶。 

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Mimecast Admin Console 圖格時，應該會自動登入您設定 SSO 的 Mimecast Admin Console。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

