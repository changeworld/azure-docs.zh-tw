---
title: 教學課程：Azure Active Directory 與 Silverback 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Silverback 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a70b6bb50b397429af1af41869bbe9ecf7e8bad9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92510761"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>教學課程：Azure Active Directory 與 Silverback 整合

在本教學課程中，您會了解如何整合 Silverback 與 Azure Active Directory (Azure AD)。
Silverback 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Silverback 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Silverback (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Silverback 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Silverback 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Silverback 支援 **SP** 起始的 SSO

## <a name="adding-silverback-from-the-gallery"></a>從資源庫新增 Silverback

若要設定 Silverback 與 Azure AD 整合，您需要從資源庫將 Silverback 加入受控 SaaS 應用程式清單中。

**若要從資源庫新增 Silverback，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Silverback** ，從結果面板中選取 [Silverback]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Silverback](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Silverback 設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Silverback 中相關使用者之間的連結關聯性。

若要使用 Silverback 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Silverback 單一登入](#configure-silverback-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Silverback 測試使用者](#create-silverback-test-user)** - 使 Silverback 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Silverback 設定 Azure AD 單一登入功能，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Silverback]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Silverback 網域與 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<YOURSILVERBACKURL>.com/ssp`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`<YOURSILVERBACKURL>.com`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Silverback 用戶端支援小組](mailto:helpdesk@matrix42.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>設定 Silverback 單一登入

1. 在不同的網頁瀏覽器中，以系統管理員身分登入 Silverback Server。

2. 瀏覽至 [管理員]   >  [驗證提供者]  。

3. 在 [驗證提供者設定]  頁面上，執行下列步驟：

    ![系統管理員](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  按一下 [從 URL 匯入]  。

    b.  貼上複製的中繼資料 URL，然後按一下 [確定]  。

    c.  按一下 [確定]  進行確定，系統便會自動填入值。

    d.  啟用 [在登入頁面上顯示]  。

    e.  如果您想要自動新增 Azure AD 授權的使用者，請啟用 [動態使用者建立]  (選擇性)。

    f.  在自助入口網站上為該按鈕建立 [標題]  。

    g.  按一下 [選擇檔案]  ，上傳 [圖示]  。

    h.  為按鈕選取 **彩色** 背景。

    i.  按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Silverback 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Silverback]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Silverback]  。

    ![應用程式清單中的 Silverback 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-silverback-test-user"></a>建立 Silverback 測試使用者

若要讓 Azure AD 使用者能夠登入 Silverback，必須將他們佈建到 Silverback。 在 Silverback 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入 Silverback Server。

2. 瀏覽至 [使用者]  ，並 **新增新的裝置使用者** 。

3. 在 [基本]  頁面上，執行下列步驟：

    ![使用者](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. 在 [使用者名稱]  文字方塊中，輸入使用者的名稱，例如 **Britta** 。

    b. 在 [名字]  文字方塊中，輸入使用者的名字，例如 **Britta** 。

    c. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon** 。

    d. 在 [電子郵件地址] 文字方塊中，輸入使用者的電子郵件地址，例如 **Brittasimon\@contoso.com** 。

    e. 在 [密碼]  文字方塊中，輸入您的密碼。

    f. 在 [確認密碼]  文字方塊中，再次輸入您的密碼並確認。

    g. 按一下 [檔案]  。

> [!NOTE]
> 如果您不想要手動建立每個使用者，請到 [管理員] >  [驗證提供者] 下方，啟用 [動態使用者建立] 核取方塊。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Silverback] 圖格時，應該會自動登入您已設定 SSO 的 Silverback。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)