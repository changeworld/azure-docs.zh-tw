---
title: 教學課程：Azure Active Directory 與 Zscaler Private Access Administrator 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Private Access Administrator 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085648"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>教學課程：Azure Active Directory 與 Zscaler Private Access Administrator 整合

在本教學課程中，您將了解如何整合 Zscaler Private Access Administrator 與 Azure Active Directory (Azure AD)。
將 Zscaler Private Access Administrator 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Zscaler Private Access Administrator 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Zscaler Private Access Administrator (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Zscaler Private Access Administrator 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用單一登入的 Zscaler Private Access Administrator 訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Zscaler Private Access Administrator 支援 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>從資源庫新增 Zscaler Private Access Administrator

若要設定將 Zscaler Private Access Administrator 整合到 Azure AD 中，您需要將 Zscaler Private Access Administrator 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Zscaler Private Access Administrator，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Zscaler Private Access Administrator**，從結果面板中選取 [Zscaler Private Access Administrator]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Zscaler Private Access Administrator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在此節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Zscaler Private Access Administrator 搭配運作的 Azure AD 單一登入。
為了讓單一登入運作，必須在 Azure AD 使用者與 Zscaler Private Access Administrator 中的相關使用者之間建立連結關聯性。

若要設定及測試與 Zscaler Private Access Administrator 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Zscaler Private Access Administrator 單一登入](#configure-zscaler-private-access-administrator-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Zscaler Private Access Administrator 測試使用者](#create-zscaler-private-access-administrator-test-user)** - 在 Zscaler Private Access Administrator 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 Zscaler Private Access Administrator 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Zscaler Private Access Administrator]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Zscaler Private Access Administrator 網域及 URL 單一登入資訊](common/idp-relay.png)

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.private.zscaler.com/auth/sso`

    c. 按一下 [設定額外的 URL]  。

    d. 在 [轉送狀態]  文字方塊中，鍵入 URL：`idpadminsso`

5.  如果您想要在 **SP** 起始模式中設定應用程式，請執行下列步驟：

    ![Zscaler Private Access Administrator 網域及 URL 單一登入資訊](common/both-signonurl.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [Zscaler Private Access Administrator 用戶端支援小組](https://help.zscaler.com/zpa-submit-ticket) \(英文\) 以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [設定 Zscaler Private Access Administrator]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>設定 Zscaler Private Access Administrator 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Zscaler Private Access Administrator。

2. 按一下頂端的 [Administration] \(管理\)  ，然後瀏覽至 [AUTHENTICATION] \(驗證\)  區段並按一下 [IdP Configuration] \(IdP 設定\)  。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. 按一下右上角的 [Add IdP Configuration] \(新增 IdP 設定\)  。 

    ![Zscaler Private Access Administrator 新增 IdP](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. 在 [Add IdP Configuration] \(新增 IdP 設定\)  頁面上，執行下列步驟：
 
    ![Zscaler Private Access Administrator IdP 選取](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. 按一下 [IdP Metadata File Upload] \(IdP 中繼資料檔案上傳\)  欄位中的 [Select File] \(選取檔案)  ，以上傳從 Azure AD 下載的中繼資料檔案。

    b. 它會從 Azure AD 讀取 **IdP 中繼資料**，並填入所有欄位資訊，如下所示。

    ![Zscaler Private Access Administrator IdP 設定](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. 在 [Single Sign On] \(單一登入\)  選取 [Administrator] \(系統管理員\)  。

    d. 從 [Domains] \(網域\)  欄位中選取您的網域。
    
    e. 按一下 [檔案]  。

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

在本節中，您會將 Zscaler Private Access Administrator 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Zscaler Private Access Administrator]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler Private Access Administrator]  。

    ![應用程式清單中的 Zscaler Private Access Administrator 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-zscaler-private-access-administrator-test-user"></a>建立 Zscaler Private Access Administrator 測試使用者

若要讓 Azure AD 使用者能夠登入 Zscaler Private Access Administrator，必須將他們佈建到 Zscaler Private Access Administrator 中。 就 Zscaler Private Access Administrator 而言，必須以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Zscaler Private Access Administrator 公司網站。

2. 按一下頂端的 [Administration] \(管理\)  ，然後瀏覽至 [AUTHENTICATION] \(驗證\)  區段並按一下 [IdP Configuration] \(IdP 設定\)  。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. 從功能表左側按一下 [Administrators] \(系統管理員\)  。

    ![Zscaler Private Access Administrator 系統管理員](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. 按一下右上角的 [Add Administrator] \(新增系統管理員\)  ：

    ![Zscaler Private Access Administrator 新增管理員](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. 在 [Add Administrator] \(新增系統管理員\)  頁面上，執行下列步驟：

    ![Zscaler Private Access Administrator 管理員使用者](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. 在 [Username]  \(使用者名稱\) 文字方塊中，輸入使用者的電子郵件，如 BrittaSimon@contoso.com。

    b. 在 [Password] \(密碼\)  文字方塊中，輸入密碼。

    c. 在 [Confirm Password] \(確認密碼\)  文字方塊中，輸入密碼。

    d. 在 [Role] \(角色\)  選取 [Zscaler Private Access Administrator]  。

    e. 在 [Email]  \(電子郵件\) 文字方塊中，輸入使用者的電子郵件，如 BrittaSimon@contoso.com。

    f. 在 [Phone] \(電話\)  文字方塊中，輸入電話號碼。

    g. 在 [Timezone] \(時區\)  文字方塊中，選取時區。

    h. 按一下 [檔案]  。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Zscaler Private Access Administrator] 圖格時，應該會自動登入您已設定 SSO 的 Zscaler Private Access Administrator。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

