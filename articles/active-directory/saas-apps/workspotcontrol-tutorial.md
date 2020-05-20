---
title: 教學課程：Azure Active Directory 與 Workspot Control 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workspot Control 的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086672"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>教學課程：Azure Active Directory 與 Workspot Control 整合

在本教學課程中，您將了解如何整合 Workspot Control 與 Azure Active Directory (Azure AD)。 在整合 Workspot Control 與 Azure AD 時，您可以︰

* 使用 Azure AD 來控制有權存取 Workspot Control 的人員。
* 可以讓使用者藉由使用其 Azure AD 帳戶自動登入 Workspot Control (單一登入 [SSO])。
* 在 Azure 入口網站中集中管理您的帳戶。

如需有關 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱 [Azure AD 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Workspot Control 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。

* 已啟用 Workspot Control 單一登入的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

> [!Note]
> Workspot Control 支援由 SP 起始和由 IDP 起始的 SSO。


## <a name="adding-workspot-control-from-the-gallery"></a>從資源庫新增 Workspot Control

若要進行將 Workspot Control 整合至 Azure AD 的設定，您需要從資源庫將 Workspot Control 新增至受控 SaaS 應用程式的清單中。

**若要從資源庫新增 Workspot Control，請遵循下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

3. 請選取視窗頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入 **Workspot Control**，從結果面板中選取 [Workspot Control]  ，然後選取 [新增]  。

     ![[從資源庫新增] 視窗](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會為測試使用者 Britta Simon 設定及測試 Workspot Control 的 Azure AD 單一登入。
若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Workspot Control 中相關使用者之間的連結。

若要設定並測試 Workspot Control 的 Azure AD 單一登入，您需要完成下列工作：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [設定 Workspot Control 單一登入](#configure-workspot-control-single-sign-on)，以在應用程式端設定單一登入設定。
3. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，測試 Britta Simon 的 Azure AD 單一登入。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [建立 Workspot Control 測試使用者](#create-a-workspot-control-test-user)，以在 Workspot Control 中建立 Britta Simon 的對應項目，且該項目須與 Azure AD 中代表使用者的項目連結。
6. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Workspot Control 搭配運作的 Azure AD 單一登入，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Workspot Control] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  視窗中，選取 [SAML]  模式以啟用單一登入。

    ![選取單一登入選取方法的視窗](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  圖示 (鉛筆)，以存取 [基本 SAML 組態]  。

    ![[基本 SAML 組態] 中醒目提示的編輯圖示](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段中，若您想要以 IDP 起始模式設定應用程式，請遵循下列步驟：

    ![Workspot Control 網域與 URL 單一登入資訊](common/idp-intiated.png)

    1. 在 [識別碼]  文字方塊中，以下列模式輸入 URL：<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. 在 [回覆 URL]  文字方塊中，以下列模式輸入 URL：<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL]  。

    ![Workspot Control 網域與 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL：<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來取代這些值。 請連絡 [Workspot Control 客戶支援小組](mailto:support@workspot.com)以取得這些值。 或是您也可以參考 Azure 入口網站中 [基本 SAML 組態]  區段中的模式。

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中選取 [下載]  ，以依據您的需求從可用選項下載 [憑證 (Base64)]  。 將其儲存在您的電腦上。

    ![憑證 (Base64) 下載連結](common/certificatebase64.png)

7. 在 [安裝 Workspot Control]  區段中，依據您的需求複製適當的 URL：

    ![複製組態 URL](common/copy-configuration-urls.png)

    - **登入 URL**

    - **Azure AD 識別碼**

    - **登出 URL**

### <a name="configure-workspot-control-single-sign-on"></a>設定 Workspot Control 單一登入

1. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Workspot Control。

2. 在頁面頂端的工具列上，依序選取 [設定]  和 [SAML]  。

    ![設定選項](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. 在 [安全性聲明標記語言組態]  視窗中，遵循下列步驟：
 
    ![安全性判斷提示標記語言組態視窗](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. 在 [實體識別碼]  方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  。

    1. 在 [登入服務 URL]  方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。

    1. 在 [登出服務 URL]  方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  。

    1. 選取 [更新檔案]  ，將您從 Azure 入口網站下載的 base-64 編碼憑證上傳至 X.509 憑證中。

    1. 選取 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立測試使用者。

1. 在 Azure 入口網站的左側窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在視窗頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在使用者的屬性中，遵循下列步驟：

    ![使用者屬性視窗](common/user-properties.png)

    1. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱]  欄位中，輸入 **brittasimon@* yourcompanydomain.extension***。 例如，輸入 **BrittaSimon@contoso.<i></i>com**。

    1. 選取 [顯示密碼]  核取方塊。 然後記下 [密碼]  方塊中顯示的值。

    1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Workspot Control 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  和 [Workspot Control]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Workspot Control]  。

    ![應用程式清單中的 [Workspot Control] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]  按鈕。 然後在 [新增指派] 視窗中，選取 [使用者和群組]。

    ![[新增指派] 視窗](common/add-assign-user.png)

5. 在 [使用者和群組]  視窗的 [使用者]  清單中，選取 [Britta Simon]  。 然後按一下 [選取]  。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  視窗的清單中選取適當的使用者角色。 然後按一下底部的 [選取]  。

7. 在 [新增指派]  視窗中，選取 [指派]  。

### <a name="create-a-workspot-control-test-user"></a>建立 Workspot Control 測試使用者

若要讓 Azure AD 使用者能夠登入 Workspot Control，必須將他們佈建到 Workspot Control 中。 佈建是手動工作。

**若要佈建使用者帳戶，請遵循下列步驟：**

1. 以安全性系統管理員身分登入 Workspot Control。

2. 在頁面頂端的工具列上，依序選取 [使用者]  和 [新增使用者]  。

    ![[使用者] 選項](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. 在 [新增使用者]  視窗中，遵循下列步驟：

    ![[新增使用者] 視窗](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. 在 [名字]  方塊中，輸入使用者的名字，例如 **Britta**。

    1. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    1. 在 [電子郵件]  方塊中，輸入使用者的電子郵件地址，例如 **Brittasimon@contoso.<i></i>com**。

    1. 從 [角色]  下拉式清單中選取適當的使用者角色。

    1. 從 [群組]  下拉式清單中選取適當的使用者群組。

    1. 選取 [新增使用者]  。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，我們會透過 [存取面板]  測試 Azure AD 單一登入組態。

當您在存取面板中按一下 [Workspot Control]  圖格時，應該會自動登入您已設定 SSO 的 Workspot Control。 如需詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
