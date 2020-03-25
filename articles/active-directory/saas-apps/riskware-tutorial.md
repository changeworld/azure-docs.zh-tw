---
title: 教學課程：Azure Active Directory 與 Riskware 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Riskware 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027109"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>教學課程：Azure Active Directory 與 Riskware 整合

在本教學課程中，您將了解如何整合 Riskware 與 Azure Active Directory (Azure AD)。
將 Riskware 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Riskware 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Riskware (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Riskware 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Riskware 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Riskware 支援由 **SP** 起始的 SSO

## <a name="adding-riskware-from-the-gallery"></a>從資源庫新增 Riskware

若要設定將 Riskware 整合到 Azure AD 中，您需要從資源庫將 Riskware 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Riskware，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Riskware**，從結果面板中選取 [Riskware]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Riskware](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Riskware 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Riskware 中相關使用者之間的連結關聯性。

若要設定及測試與 Riskware 搭配運作的 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Riskware 單一登入](#configure-riskware-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Riskware 測試使用者](#create-riskware-test-user)** - 使 Riskware 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Riskware 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Riskware]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![[Riskware 網域及 URL] 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：
    
    | 環境| URL 模式|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | 示範| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入 URL：
    
    | 環境| URL 模式|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | 示範| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [Riskware 用戶端支援小組](mailto:support@pansoftware.com.au)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Riskware]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-riskware-single-sign-on"></a>設定 Riskware 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Riskware 公司網站。

1. 按一下右上方的 [Maintenance] \(維護\)  以開啟維護頁面。

    ![Riskware 設定：維護](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. 在維護頁面中，按一下 [Authentication] \(驗證\)  。

    ![Riskware 設定：驗證](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. 在 [Authentication Configuration] \(驗證設定\)  頁面中，執行下列步驟：

    ![Riskware 設定：驗證設定](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. 選取 [SAML]  作為驗證的 [Type] \(類型\)  。

    b. 在 [Code]\(代碼\)  文字方塊中，輸入您的代碼，例如 AZURE_UAT。

    c. 在 [Description]\(描述\)  文字方塊中，輸入您的描述，例如 AZURE Configuration for SSO (AZURE 的 SSO 設定)。

    d. 在 [單一登入頁面]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    e. 在 [登出頁面]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    f. 在 [POST 表單欄位]  文字方塊中，輸入 POST 回應中包含 SAML 的欄位名稱，例如 SAMLResponse

    g. 在 [XML Identity Tag Name] \(XML 身分識別標記名稱\)  文字方塊中，輸入 SAML 回應中包含唯一識別碼的屬性，例如 NameID。

    h. 在記事本中開啟從 Azure 入口網站下載的 **Metadata Xml**，複製中繼資料檔中的憑證，然後將其貼到 [Certificate]\(憑證\)  文字方塊中

    i. 在 [Consumer URL]\(取用者 URL\)  文字方塊中，貼上您向支援小組取得的**回覆 URL** 值。

    j. 在 [簽發者]  文字方塊中，貼上您向支援小組取得的**識別碼**值。

    > [!Note]
    > 請連絡 [Riskware 用戶端支援小組](mailto:support@pansoftware.com.au)以取得這些值

    k. 勾選 [使用 POST]  核取方塊。

    l. 勾選 [使用 SAML 要求]  核取方塊。

    m. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Riskware 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Riskware]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Riskware]  。

    ![應用程式清單中的 Riskware 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-riskware-test-user"></a>建立 Riskware 測試使用者

若要讓 Azure AD 使用者能夠登入 Riskware，必須將這些使用者佈建到 Riskware。 在 Riskware 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以安全性系統管理員身分登入 Riskware。

1. 按一下右上方的 [Maintenance] \(維護\)  以開啟維護頁面。 

    ![Riskware 設定：維護](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. 在維護頁面中，按一下 [People]\(人員\)  。

    ![Riskware 設定：人員](./media/riskware-tutorial/tutorial_riskware_people.png)

1. 選取 [詳細資料]  索引標籤，然後執行下列步驟：

    ![Riskware 設定：詳細資料](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. 選取 [Person Type]\(人員類型\)  ，例如 [Employee] \(員工\)。

    b. 在 [First Name]\(名字\)  文字方塊中，輸入使用者的名字，例如 **Britta**。

    c. 在 [Surname]\(姓氏\)  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

1. 在 [安全性]  索引標籤上執行下列步驟：。

    ![Riskware 設定：安全性](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. 在 [Authentication]\(驗證\)  區段下，選取您已設定的 [Authentication]\(驗證\)  模式，例如 [AZURE Configuration for SSO] \(AZURE 的 SSO 設定\)。

    b. 在 [登入詳細資料]  區段下的 [使用者識別碼]  文字方塊中，輸入使用者的電子郵件，例如 `brittasimon@contoso.com`。

    c. 在 [Password] \(密碼\)  文字方塊中，輸入使用者的密碼。

1. 在 [組織]  索引標籤上，執行下列步驟：

    ![Riskware 設定：組織](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. 選取選項作為 **Level1** 組織。

    b. 在 [Person's Primary Workplace]\(人員的主要工作場所\)  區段下的 [Location]\(位置\)  文字方塊中，輸入您的位置。

    c. 在 [Employee]\(員工\)  區段下，選取 [Employee Status]\(員工狀態\)  ，例如 [Casual]\(臨時\)。

    d. 按一下 [檔案]  。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Riskware] 圖格時，應該會自動登入您已設定 SSO 的 Riskware。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
