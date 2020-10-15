---
title: 教學課程︰Azure Active Directory 與 SensoScientific Wireless Temperature Monitoring System 整合 | Microsoft Docs
description: 了解如何在 Azure Active Directory 與 SensoScientific Wireless Temperature Monitoring System 之間設定單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 61807f90caad9e6be2a25e54dcaa837595afc729
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543148"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>教學課程︰Azure Active Directory 與 SensoScientific Wireless Temperature Monitoring System 整合

在本教學課程中，您會了解如何將 SensoScientific Wireless Temperature Monitoring System 與 Azure Active Directory (Azure AD) 進行整合。
將 SensoScientific Wireless Temperature Monitoring System 與 Azure AD 整合可以提供下列優點︰

* 您可以在 Azure AD 中控制可存取 SensoScientific Wireless Temperature Monitoring System 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 SensoScientific Wireless Temperature Monitoring System (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SensoScientific Wireless Temperature Monitoring System 整合，您需要下列項目︰

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 SensoScientific Wireless Temperature Monitoring System 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SensoScientific Wireless Temperature Monitoring System 支援由 **IDP** 起始的 SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>從資源庫新增 SensoScientific Wireless Temperature Monitoring System

若要設定將 SensoScientific Wireless Temperature Monitoring System 整合到 Azure AD 中，您需要從資源庫將 SensoScientific Wireless Temperature Monitoring System 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 SensoScientific Wireless Temperature Monitoring System，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **SensoScientific Wireless Temperature Monitoring System**，並從結果面板中選取 [SensoScientific Wireless Temperature Monitoring System]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 SensoScientific Wireless Temperature Monitoring System](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 SensoScientific Wireless Temperature Monitoring System 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者和 SensoScientific Wireless Temperature Monitoring System 中相關使用者之間的連結關聯性。

若要設定及測試對 SensoScientific Wireless Temperature Monitoring System 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SensoScientific Wireless Temperature Monitoring System 單一登入](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SensoScientific Wireless Temperature Monitoring System 測試使用者](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - 使 SensoScientific Wireless Temperature Monitoring System 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 SensoScientific Wireless Temperature Monitoring System 搭配運作的 Azure AD 單一登入，請執行下列步驟︰

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SensoScientific Wireless Temperature Monitoring System]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段中，使用者不需要執行任何步驟，因為應用程式已預先與 Azure 整合。

    ![SensoScientific Wireless Temperature Monitoring System 網域和 URL 單一登入資訊](common/preintegrated.png)

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 SensoScientific Wireless Temperature Monitoring System]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>設定 SensoScientific Wireless Temperature Monitoring System 單一登入

1. 以系統管理員身分登入您的 SensoScientific Wireless Temperature Monitoring System 應用程式。

1. 在頂端的導覽功能表上，按一下 [設定]，然後前往 [單一登入] 下的 [設定] 開啟 [單一登入設定]，並執行下列步驟：

    ![設定單一登入](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. 選取 [簽發者名稱]  作為 Azure AD。

    b. 在 [簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  。

    c. 在 [單一登入服務 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。

    d. 在 [單一登出服務 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  。

    e. 瀏覽您從 Azure 入口網站下載的憑證並在這裡上傳。

    f. 按一下 [檔案]  。

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

在本節中，您會將 SensoScientific Wireless Temperature Monitoring System 支援小組的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [SensoScientific Wireless Temperature Monitoring System]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **SensoScientific Wireless Temperature Monitoring System**。

    ![應用程式清單中的 SensoScientific Wireless Temperature Monitoring System 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>建立 SensoScientific Wireless Temperature Monitoring System 測試使用者

若要讓 Azure AD 使用者能夠登入 SensoScientific Wireless Temperature Monitoring System，必須將他們佈建到 SensoScientific Wireless Temperature Monitoring System。 請與 [SensoScientific Wireless Temperature Monitoring System 支援小組](https://www.sensoscientific.com/contact-us/)合作，在 SensoScientific Wireless Temperature Monitoring System 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SensoScientific Wireless Temperature Monitoring System] 圖格時，應該會自動登入您已設定 SSO 的 SensoScientific Wireless Temperature Monitoring System。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

