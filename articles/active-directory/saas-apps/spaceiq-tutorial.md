---
title: 教學課程：Azure Active Directory 與 SpaceIQ 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SpaceIQ 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 74c3c4d1a9192256c36d41db7a4f9406b6c966f3
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514719"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>教學課程：Azure Active Directory 與 SpaceIQ 整合

在本教學課程中，您會了解如何整合 SpaceIQ 與 Azure Active Directory (Azure AD)。
SpaceIQ 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 SpaceIQ 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 SpaceIQ (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SpaceIQ 的整合作業，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 SpaceIQ 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SpaceIQ 支援由 **IDP** 起始的 SSO

## <a name="adding-spaceiq-from-the-gallery"></a>從資源庫新增 SpaceIQ

若要設定 SpaceIQ 與 Azure AD 整合，您需要從資源庫將 SpaceIQ 加入受控 SaaS 應用程式清單中。

**若要從資源庫新增 SpaceIQ，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **SpaceIQ** ，從結果面板中選取 [SpaceIQ]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 SpaceIQ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 SpaceIQ 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SpaceIQ 中相關使用者之間的連結關聯性。

若要設定及測試與 SpaceIQ 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SpaceIQ 單一登入](#configure-spaceiq-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SpaceIQ 測試使用者](#create-spaceiq-test-user)** - 使 SpaceIQ 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 SpaceIQ 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SpaceIQ]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [以 SAML 設定單一登入]  頁面上，執行下列步驟：

    ![SpaceIQ 網域與 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，輸入 URL：`https://api.spaceiq.com`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > 請使用實際的「回覆 URL」及識別碼來更新這些值 (本教學課程稍後會說明)。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 SpaceIQ]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-spaceiq-single-sign-on"></a>設定 SpaceIQ 單一登入

1. 開啟新的瀏覽器視窗，然後以系統管理員身分登入您的 SpaceIQ 環境。

1. 登入後，請按一下右上方的拼圖記號，然後按一下 [整合] 

    ![帳戶設定](./media/spaceiq-tutorial/setting1.png) 

1. 在 [所有佈建和 SSO]  下，按一下 [Azure]  圖格，新增 Azure 的執行個體成為 IDP。

    ![SAML 圖示](./media/spaceiq-tutorial/setting2.png)

1. 在 [SSO]  對話方塊中，執行下列步驟：

    ![SAML 驗證設定](./media/spaceiq-tutorial/setting3.png)

    a. 在 [SAML 簽發者 URL]  方塊中，貼上從 Azure AD 應用程式設定視窗複製的 [Azure AD 識別碼]  值。

    b. 複製 [SAML 回呼端點 URL (唯讀)] 值，並在 Azure 入口網站 [基本 SAML 設定] 區段中的 [回覆 URL] 方塊中貼上該值。

    c. 複製 [SAML 對象 URI (唯讀)] 值，並在 Azure 入口網站 [基本 SAML 設定] 區段中的 [識別碼] 方塊中貼上該值。

    d. 在記事本中開啟下載的憑證，複製其內容，然後貼到 [X.509 憑證]  方塊中。

    e. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會授與 SpaceIQ 的存取權，讓 Britta Simon 能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [SpaceIQ]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [SpaceIQ]  。

    ![應用程式清單中的 SpaceIQ 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-spaceiq-test-user"></a>建立 SpaceIQ 測試使用者

在本節中，您要在 SpaceIQ 中建立名為 Britta Simon 的使用者。 請與 [SpaceIQ 支援小組](mailto:eng@spaceiq.com)合作，在 SpaceIQ 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SpaceIQ] 圖格時，應該會自動登入您已設定 SSO 的 SpaceIQ。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)