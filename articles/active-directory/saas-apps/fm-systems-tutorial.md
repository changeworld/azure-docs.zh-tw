---
title: 教學課程：Azure Active Directory 與 FM:Systems 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 FM:Systems 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: 9f869d595aeb3ff2ab7c3fd0bab7b52c72ab904b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048601"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>教學課程：Azure Active Directory 與 FM:Systems 整合

在本教學課程中，您將了解如何整合 FM:Systems 與 Azure Active Directory (Azure AD)。
FM:Systems 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 FM:Systems 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 FM:Systems (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 FM:Systems 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 FM:Systems 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* FM:Systems 支援由 **IDP** 起始的 SSO

## <a name="adding-fmsystems-from-the-gallery"></a>從資源庫新增 FM:Systems

若要設定將 FM:Systems 整合到 Azure AD 中，您需要從資源庫將 FM:Systems 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 FM:Systems，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **FM:Systems**，從結果面板中選取 [FM:Systems]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 FM:Systems](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會透過名為 **Britta Simon** 的測試使用者，搭配 FM:Systems 來設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 FM:Systems 中相關使用者之間的連結關聯性。

若要設定及測試與 FM:Systems 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 FM:Systems 單一登入](#configure-fmsystems-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 FM:Systems 測試使用者](#create-fmsystems-test-user)** - 在 FM:Systems 中建立 Britta Simon 的對應項目，使該項目與 Azure AD 中代表該使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 FM:Systems 設定 Azure AD 單一登入，請執行以下步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [FM:Systems]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![FM:Systems 網域及 URL 單一登入資訊](common/both-replyurl.png)

    在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`
    
    > [!NOTE]
    > 這不是真實的值。 請使用實際的「回覆 URL」來更新此值。 請連絡 [FM:Systems 客戶支援小組](https://fmsystems.com/support-services/) \(英文\) 以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 FM:Systems]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-fmsystems-single-sign-on"></a>設定 FM:Systems 單一登入

若要在 **FM:Systems** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和所複製的適當 URL 傳送給 [FM:Systems 支援小組](https://fmsystems.com/support-services/) \(英文\)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

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

在本節中，您會將 FM:Systems 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [FM:Systems]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [FM:Systems]  。

    ![應用程式清單中的 FM:Systems 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-fmsystems-test-user"></a>建立 FM:Systems 測試使用者

1. 在網頁瀏覽器視窗中，以系統管理員身分登入您的 FM:Systems 公司網站。

2. 移至 [系統管理] **[管理安全性]\>[使用者]\>[使用者清單]\>** 。
   
    ![系統管理](./media/fm-systems-tutorial/ic795905.png "系統管理")

3. 按一下 [建立新的使用者]  。
   
    ![建立新的使用者](./media/fm-systems-tutorial/ic795906.png "建立新的使用者")

4. 在 [建立使用者]  區段中，執行下列步驟：
   
    ![建立使用者](./media/fm-systems-tutorial/ic795907.png "建立使用者")
   
    a. 在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的 [使用者名稱]  、[密碼]  、[確認密碼]  、[電子郵件]  、[員工識別碼]  、[電子郵件地址]。
   
    b. 按 [下一步]  。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [FM:Systems] 圖格時，應該會自動登入您已設定 SSO 的 FM:Systems。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

