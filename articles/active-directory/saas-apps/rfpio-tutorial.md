---
title: 教學課程：Azure Active Directory 與 RFPIO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 RFPIO 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092853"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>教學課程：Azure Active Directory 與 RFPIO 整合

在本教學課程中，您會了解如何整合 RFPIO 與 Azure Active Directory (Azure AD)。
RFPIO 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 RFPIO 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 RFPIO (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 RFPIO 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 RFPIO 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* RFPIO 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-rfpio-from-the-gallery"></a>從資源庫新增 RFPIO

若要設定將 RFPIO 整合到 Azure AD 中，您需要從資源庫將 RFPIO 新增到受控 SaaS 應用程式清單。

**若要從資源庫加入 RFPIO，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **RFPIO**，從結果面板中選取 [RFPIO]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 RFPIO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 RFPIO 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 RFPIO 中相關使用者之間的連結關聯性。

若要設定及測試與 RFPIO 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 RFPIO 單一登入](#configure-rfpio-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 RFPIO 測試使用者](#create-rfpio-test-user)** - 在 RFPIO 中建立一個與 Azure AD 中代表使用者的項目連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 RFPIO 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [RFPIO]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![RFPIO 網域與 URL 單一登入資訊](common/idp-identifier.png)

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://www.rfpio.com`

    b. 按一下 [設定額外的 URL]  。

    c. 在 [回覆狀態]  文字方塊中輸入一個字串值。 請連絡 [RFPIO 支援小組](https://www.rfpio.com/contact/)以取得此值。

    ![RFPIO 網域與 URL 單一登入資訊](common/idp-preintegrated-relay.png)

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![image](common/both-preintegrated-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://www.app.rfpio.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和登入 URL 將這些值更新。 請連絡 [RFPIO 用戶端支援小組](https://www.rfpio.com/contact/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [設定 RFPIO]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-rfpio-single-sign-on"></a>設定 RFPIO 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **RFPIO** 網站。

1. 按一下左上角的下拉式清單。

    ![設定單一登入](./media/rfpio-tutorial/app1.png)

1. 按一下 [組織設定]  。 

    ![設定單一登入](./media/rfpio-tutorial/app2.png)

1. 按一下 [功能和整合]  。

    ![設定單一登入](./media/rfpio-tutorial/app4.png)

1. 在 [SAML SSO 組態]  中按一下 [編輯]  。

    ![設定單一登入](./media/rfpio-tutorial/app3.png)

1. 在本節中執行下列動作：

    ![設定單一登入](./media/rfpio-tutorial/app5.png)
    
    a. 複製 [下載的中繼資料 XML]  內容，然後貼到 [身分識別組態]  欄位中。

    > [!NOTE]
    >若要複製下載的 [同盟中繼資料 XML]  內容，請使用 [Notepad++]  或適當的 [XML 編輯器]  。

    b. 按一下 **[驗證]** 。

    c. 按一下 [驗證]  後，將 [SAML (已啟用)]  翻轉為開啟狀態。

    d. 按一下 [提交]  。

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

在本節中，您會將 RFPIO 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [RFPIO]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [RFPIO]  。

    ![應用程式清單中的 RFPIO 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-rfpio-test-user"></a>建立 RFPIO 測試使用者

1. 以系統管理員身分登入您的 RFPIO 公司網站。

1. 按一下左上角的下拉式清單。

    ![設定單一登入](./media/rfpio-tutorial/app1.png)

1. 按一下 [組織設定]  。 

    ![設定單一登入](./media/rfpio-tutorial/app2.png)

1. 按一下 [小組成員]  。

    ![設定單一登入](./media/rfpio-tutorial/app6.png)

1. 按一下 [新增成員]  。

    ![設定單一登入](./media/rfpio-tutorial/app7.png)

1. 在 [新增成員]  區段中。 執行下列動作：

    ![設定單一登入](./media/rfpio-tutorial/app8.png)

    a. 在 [每行輸入一個電子郵件] 欄位中輸入[電子郵件地址]。

    b. 請根據您的需求選取 [角色]  。

    c. 按一下[新增成員]  。

    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [RFPIO] 圖格時，應該會自動登入您已設定 SSO 的 Zoho。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

