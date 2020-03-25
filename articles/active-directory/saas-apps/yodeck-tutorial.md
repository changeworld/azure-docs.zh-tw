---
title: 教學課程：Azure Active Directory 與 Yodeck 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Yodeck 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: af0748f36e8d2299921f987c517f7a4923bd0d55
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086483"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>教學課程：Azure Active Directory 與 Yodeck 整合

在本教學課程中，您將了解如何整合 Yodeck 與 Azure Active Directory (Azure AD)。
Yodeck 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Yodeck 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Yodeck (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Yodeck 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Yodeck 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Yodeck 支援由 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-yodeck-from-the-gallery"></a>從資源庫新增 Yodeck

若要設定 Yodeck 與 Azure AD 整合，您需要從資源庫將 Yodeck 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Yodeck，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Yodeck**，從結果面板中選取 [Yodeck]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Yodeck](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，搭配 Yodeck 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Yodeck 中相關使用者之間的連結關聯性。

若要搭配 Yodeck 來設定及測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Yodeck 單一登入](#configure-yodeck-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Yodeck 測試使用者](#create-yodeck-test-user)** - 使 Yodeck 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 Yodeck 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Yodeck]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Yodeck 網域及 URL 單一登入資訊](common/idp-identifier.png)

    在 [識別碼]  文字方塊中，鍵入 URL：`https://app.yodeck.com/api/v1/account/metadata/`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![image](common/both-preintegrated-signon.png)

    在 [登入 URL]  文字方塊中，輸入 URL：`https://app.yodeck.com/login`

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>設定 Yodeck 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Yodeck 公司網站。

2. 按一下頁面右上角的 [使用者設定]  選項，然後選取 [帳戶設定]  。

    ![Yodeck 設定](./media/yodeck-tutorial/configure1.png)

3. 選取 [SAML]  並執行下列步驟：

    ![Yodeck 設定](./media/yodeck-tutorial/configure2.png)

    a. 選取 [從 URL 匯入]  。

    b. 在 [URL]  文字方塊中，貼上您從 Azure 入口網站複製的**應用程式同盟中繼資料 URL** 值，然後按一下 [匯入]  。
    
    c. 匯入**應用程式同盟中繼資料 URL** 之後，即會自動填入其餘欄位。

    d. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 brittasimon@yourcompanydomain.extension。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Yodeck 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Yodeck]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Yodeck]  。

    ![應用程式清單中的 Yodeck 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-yodeck-test-user"></a>建立 Yodeck 測試使用者

若要讓 Azure AD 使用者能夠登入 Yodeck，必須將他們佈建到 Yodeck。 Yodeck 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Yodeck 公司網站。

2. 按一下頁面右上角的 [使用者設定]  選項，然後選取 [使用者]  。

    ![新增員工](./media/yodeck-tutorial/user1.png)

3. 按一下 [+使用者]  以開啟 [使用者詳細資料]  索引標籤。

    ![新增員工](./media/yodeck-tutorial/user2.png)

4. 在 [使用者詳細資料]  對話頁面上，執行下列步驟：

    ![新增員工](./media/yodeck-tutorial/user3.png)

    a. 在 [名字]  文字方塊中，輸入使用者的名字，例如 **Britta**。

    b. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [電子郵件]  文字方塊中，輸入像是 brittasimon@contoso.com 的使用者電子郵件地址。

    d. 根據您的組織需求，選取適當的**帳戶權限**選項。
    
    e. 按一下 [檔案]  。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Yodeck] 圖格時，應該會自動登入您已設定 SSO 的 Yodeck。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

