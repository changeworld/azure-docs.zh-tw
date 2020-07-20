---
title: 教學課程：Azure Active Directory 與 Absorb LMS 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Absorb LMS 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107508"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>教學課程：Azure Active Directory 與 Absorb LMS

在本教學課程中，您將了解如何整合 Absorb LMS 與 Azure Active Directory (Azure AD)。
將 Absorb LMS 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Absorb LMS 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Absorb LMS (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定與 Absorb LMS 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Absorb LMS 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Absorb LMS 支援由 **IDP** 起始的 SSO

## <a name="adding-absorb-lms-from-the-gallery"></a>從資源庫新增 Absorb LMS

若要進行將 Absorb LMS 整合到 Azure AD 中的設定，您必須從資源庫將 Absorb LMS 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Absorb LMS，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Absorb LMS**，從結果面板中選取 [Absorb LMS]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Absorb LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Absorb LMS 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Absorb LMS 中相關使用者之間的連結關聯性。

若要設定及測試與 Absorb LMS 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Absorb LMS 單一登入](#configure-absorb-lms-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Absorb LMS 測試使用者](#create-absorb-lms-test-user)** - 使 Absorb LMS 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Absorb LMS 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Absorb LMS]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [基本 SAML 組態]  對話方塊。

    ![Absorb LMS 網域與 URL 單一登入資訊](common/idp-intiated.png)

    如果您要使用 **Absorb 5 - UI**，請使用下列組態︰

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://company.myabsorb.com/account/saml`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://company.myabsorb.com/account/saml`

    如果您要使用 **Absorb 5 - New Learner Experience**，請使用下列組態︰

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Absorb LMS 用戶端支援小組](https://support.absorblms.com/hc/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。

    ![image](common/edit-attribute.png)

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [設定 Absorb LMS]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-absorb-lms-single-sign-on"></a>設定 Absorb LMS 單一登入

1. 在新的網頁瀏覽器視窗中，以系統管理員身分登入您的 Absorb LMS 公司網站。

2. 選取右上方的 [帳戶]  按鈕。

    ![[帳戶] 按鈕](./media/absorblms-tutorial/1.png)

3. 在 [帳戶] 窗格中，選取 [入口網站設定]  。

    ![[入口網站設定] 連結](./media/absorblms-tutorial/2.png)

4. 選取 [管理 SSO 設定]  索引標籤。

    ![[使用者] 索引標籤](./media/absorblms-tutorial/managesso.png)

5. 在 [管理單一登入設定]  頁面上，執行下列操作：

    ![[單一登入設定] 頁面](./media/absorblms-tutorial/settings.png)

    a. 在 [名稱]  文字方塊中，輸入 Azure AD Marketplace SSO 之類的名稱。

    b. 選取 [SAML]  作為 [方法]  。

    c. 在 [記事本] 中，開啟您從 Azure 入口網站下載的憑證。 移除 **---BEGIN CERTIFICATE---** 和 **---END CERTIFICATE---** 標記。 接著，在 [金鑰]  方塊中，貼上其餘的內容。

    d. 在 [模式]  方塊中，選取 [識別提供者起始]  。

    e. 在 [ID 屬性]  方塊中，選取您在 Azure AD 中設定為使用者識別碼的屬性。 例如，如果已在 Azure AD 中選取 nameidentifier  ，請選取 [使用者名稱]  。

    f. 選取 [Sha256]  作為 [簽章類型]  。

    g. 在 [登入 URL] 方塊中，從應用程式的 Azure 入口網站 [屬性] 頁面貼上**使用者存取 URL**。

    h. 在 [登出 URL] 中，貼上您從 Azure 入口網站的 [設定登入] 視窗所複製的**登出 URL** 值。

    i. 將 [自動重新導向]  切換為 [開啟]  。

6. 選取 [儲存]  。

    ![[只允許 SSO 登入] 切換](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon\@yourcompanydomain.extension`  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Absorb LMS 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Absorb LMS]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入並選取 [Absorb LMS]  。

    ![應用程式清單中的 Absorb LMS 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-absorb-lms-test-user"></a>建立 Absorb LMS 測試使用者

若要將 Azure AD 使用者登入 Absorb LMS，必須在 Absorb LMS 中設定這些使用者。 在 Absorb LMS 的案例中，佈建必須手動執行。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 Absorb LMS 公司網站。

2. 在 [使用者]  窗格中，選取 [使用者]  。

    ![[使用者] 連結](./media/absorblms-tutorial/absorblms_userssub.png)

3. 選取 [使用者]  索引標籤。

    ![[新增] 下拉式清單](./media/absorblms-tutorial/absorblms_createuser.png)

4. 在 [新增使用者]  頁面上，執行下列操作：

    ![[新增使用者] 頁面](./media/absorblms-tutorial/user.png)

    a. 在 [名字]  方塊中，輸入名字，例如 **Britta**。

    b. 在 [姓氏]  方塊中，輸入姓氏，例如 **Simon**。

    c. 在 [使用者名稱]  方塊中，輸入全名，例如 **Britta Simon**。

    d. 在 [密碼]  方塊中，輸入使用者密碼。

    e. 在 [確認密碼]  方塊中，重新輸入密碼。

    f. 將 [為使用中]  切換設定為 [使用中]  。

5. 選取 [儲存]  。

    ![[只允許 SSO 登入] 切換](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 根據預設，在 SSO 中不會啟用 [使用者佈建]。 如果客戶想要啟用這項功能，他們必須依照[這份](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning)文件中的說明加以設定。 此外也請注意，「使用者佈建」僅適用於 **Absorb 5 - New Learner Experience**，ACS URL 為 `https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Absorb LMS] 圖格時，應該會自動登入您已設定 SSO 的 Absorb LMS。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
