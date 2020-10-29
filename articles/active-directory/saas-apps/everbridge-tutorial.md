---
title: 教學課程：Azure Active Directory 與 Everbridge 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Everbridge 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 732362ef7099e93697320d8e47180c1207e8cb32
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453853"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>教學課程：Azure Active Directory 與 Everbridge 整合

在本教學課程中，您將了解如何整合 Everbridge 與 Azure Active Directory (Azure AD)。
在整合 Everbridge 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Everbridge 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Everbridge。 此存取控制稱為單一登入 (SSO)。
* 使用 Azure 入口網站集中管理您的帳戶。
如需有關軟體即服務 (SaaS) 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Everbridge 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* 使用單一登入的 Everbridge 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Everbridge 支援由 IDP 起始的 SSO。

## <a name="add-everbridge-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Everbridge

若要設定 Everbridge 與 Azure AD 的整合作業，需從 Azure Marketplace 將 Everbridge 新增至受控 SaaS 應用程式清單。

若要從 Azure Marketplace 新增 Everbridge，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格上，選取 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Everbridge** 。 從結果面板中選取 [Everbridge]  ，然後選取 [新增]  。

     ![結果清單中的 Everbridge](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以測試使用者 Britta Simon 的身分，使用 Everbridge 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，需建立 Azure AD 使用者與 Everbridge 中相關使用者之間的連結關聯性。

若要設定及測試與 Everbridge 搭配運作的 Azure AD 單一登入，請完成下列構成要素：

- [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
- [將 Everbridge設定為 Everbridge 管理員入口網站單一登入](#configure-everbridge-as-everbridge-manager-portal-single-sign-on)，以在應用程式端設定單一登入設定。
- [將 Everbridge設定為 Everbridge 成員入口網站單一登入](#configure-everbridge-as-everbridge-member-portal-single-sign-on)，以在應用程式端設定單一登入設定。
- [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
- [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
- [建立 Everbridge 測試使用者](#create-an-everbridge-test-user)，在 Everbridge 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
- [測試單一登入](#test-single-sign-on)，驗證設定是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Everbridge 搭配運作的 Azure AD 單一登入，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 **Everbridge** 應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  ，以開啟 [基本 SAML 組態]  對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

    >[!NOTE]
    >在 Azure 入口網站和 Everbridge 入口網站上，將應用程式設定為管理員入口網站「或」  成員入口網站。

4. 若要將 **Everbridge** 應用程式設為 **EverBridge 管理員入口網站** ，請在 [基本 SAML 設定]  區段執行下列步驟：

    ![Everbridge 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  方塊中，輸入遵循 `https://sso.everbridge.net/<API_Name>` 模式的 URL

    b. 在 [回覆 URL]  方塊中，輸入遵循 `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias` 模式的 URL

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 值來更新這些值。 若要取得這些值，請連絡 [Everbridge 支援小組](mailto:support@everbridge.com)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 若要將  應用程式設為 **EverBridge 成員入口網站** ，請在 [基本 SAML 設定]  區段執行下列步驟：

  * 如果您想要以 IDP 起始模式設定應用程式，請遵循下列步驟：

     ![IDP 起始模式的 Everbridge 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  方塊中，輸入遵循 `https://sso.everbridge.net/<API_Name>/<Organization_ID>` 模式的 URL

    b. 在 [回覆 URL]  方塊中，輸入遵循 `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias` 模式的 URL

   * 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL]  ，然後遵循此步驟：

     ![SP 起始模式的 Everbridge 網域及 URL 單一登入資訊](common/both-signonurl.png)

     a. 在 [登入 URL]  方塊中，輸入遵循 `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true` 模式的 URL

     > [!NOTE]
     > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」和「登入 URL」值更新這些值。 若要取得這些值，請連絡 [Everbridge 支援小組](mailto:support@everbridge.com)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

6. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，選取 [下載]  以下載 **同盟中繼資料 XML** 。 將其儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [設定 Everbridge]  區段上，依據需求複製所需的 URL：

    ![複製組態 URL](common/copy-configuration-urls.png)

    - 登入 URL
    - Azure AD 識別碼
    - 登出 URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>將 Everbridge 設定為 Everbridge 管理員入口網站單一登入

若要在作為 **Everbridge 管理員入口網站** 應用程式的 **Everbridge** 上設定 SSO，請遵循下列步驟。
 
1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Everbridge。

1. 在頂端的功能表中，選取 [設定]  索引標籤。在 [安全性]  底下選取 [單一登入]  。
   
     ![設定單一登入](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. 在 [名稱]  方塊中，輸入識別碼提供者的名稱。 例如您的公司名稱。
   
     b. 在 [API 名稱]  方塊中，輸入 API 的名稱。
   
     c. 選取 [選擇檔案]  ，以上傳您從 Azure 入口網站下載的中繼資料檔案。
   
     d. 對於 [SAML 身分識別位置]  ，請選取 [身分識別位於 Subject 陳述式的 NameIdentifier 元素中]  。
   
     e. 在 [識別提供者登入 URL]  方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。
   
     f. 對於 [服務提供者起始的要求繫結]  ，請選取 [HTTP 重新導向]  。

     g. 選取 [儲存]  。

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>將 Everbridge 設定為 Everbridge 成員入口網站單一登入

若要在作為 **Everbridge 成員入口網站** 的 **Everbridge** 上設定單一登入，您需要將所下載的 **同盟中繼資料 XML** 傳送至 [ 支援小組](mailto:support@everbridge.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

若要在 Azure 入口網站中建立測試使用者 Britta Simon，請遵循以下步驟。

1. 在 Azure 入口網站中，於左窗格選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

    ![[使用者] 和 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者]  對話方塊中，遵循下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  方塊中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  方塊中，輸入 `brittasimon@yourcompanydomain.extension`。 例如 BrittaSimon@contoso.com。

    c. 選取 [顯示密碼]  核取方塊。 記下 [密碼]  方塊中顯示的值。

    d. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

藉由將 Everbridge 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]   >[Everbridge]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Everbridge]  。

    ![應用程式清單中的 Everbridge 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 對話方塊](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊中，選取 [使用者] 清單中的 [Britta Simon]  。 選擇畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 選擇畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-an-everbridge-test-user"></a>建立 Everbridge 測試使用者

在本節中，您會在 Everbridge 中建立測試使用者 Britta Simon。 若要在 Everbridge 平台中新增使用者，請與 [Everbridge 支援小組](mailto:support@everbridge.com)合作。 您必須先在 Everbridge 中建立和啟動使用者，然後才能使用單一登入。 

### <a name="test-single-sign-on"></a>測試單一登入 

使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 Everbridge 圖格時，應該會自動登入您已設定 SSO 的 Everbridge 帳戶。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](./tutorial-list.md)
- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)