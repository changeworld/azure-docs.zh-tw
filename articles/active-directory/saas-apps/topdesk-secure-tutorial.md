---
title: 教學課程：Azure Active Directory 與 TOPdesk - Secure 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 TOPdesk - Secure 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: db7f2afa7993c8f8d2fe9fe30e5ecd4f2388b44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524216"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>教學課程：Azure Active Directory 與 TOPdesk - Secure 整合

在本教學課程中，您將了解如何整合 TOPdesk - Secure 與 Azure Active Directory (Azure AD)。
將 TOPdesk - Secure 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 TOPdesk - Secure 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 TOPdesk - Secure (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 TOPdesk - Secure 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 TOPdesk - Secure 單一登入功能的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* TOPdesk - Secure 支援 **SP** 起始的 SSO

## <a name="adding-topdesk---secure-from-the-gallery"></a>從資源庫新增 TOPdesk - Secure

若要設定將 TOPdesk - Secure 整合到 Azure AD 中，您需要將 TOPdesk - Secure 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 TOPdesk - Secure，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **TOPdesk - Secure**，從結果面板中選取 [TOPdesk - Secure]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 TOPdesk - Secure](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 TOPdesk - Secure 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 TOPdesk - Secure 中相關使用者之間的連結關聯性。

若要設定及測試與 TOPdesk - Secure 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 TOPdesk - Secure 單一登入](#configure-topdesk---secure-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 TOPdesk - Secure 測試使用者](#create-topdesk---secure-test-user)** - 使 TOPdesk - Secure 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 TOPdesk - Secure 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [TOPdesk - Secure]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![TOPdesk - Secure 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<companyname>.topdesk.net`

    b. 在 [識別碼 URL]  方塊中，填入您可以從 TOPdesk 設定中擷取的 TOPdesk 中繼資料 URL。 它應該使用下列模式：`https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [TOPdesk - Secure 用戶端支援小組](https://www.topdesk.com/us/support/) \(英文\) 以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 TOPdesk - Secure]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-topdesk---secure-single-sign-on"></a>設定 TOPdesk - Secure 單一登入

1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。

2. 在 [TOPdesk]  功能表中按一下 [設定]  。

    ![設定](./media/topdesk-secure-tutorial/ic790598.png "設定")

3. 按一下 [登入設定]  。

    ![登入設定](./media/topdesk-secure-tutorial/ic790599.png "登入設定")

4. 展開 [登入設定]  功能表，然後按一下 [一般]  。

    ![一般](./media/topdesk-secure-tutorial/ic790600.png "一般")

5. 在 [SAML 登入] 組態區段的 **Secure** 區段中，執行下列步驟：

    ![技術設定](./media/topdesk-secure-tutorial/ic790855.png "技術設定")

    a. 按 [下載]  來下載公用中繼資料檔案，然後再將它儲存在本機電腦上。

    b. 開啟此中繼資料檔案，然後找到 **AssertionConsumerService** 節點。

    ![判斷提示取用者服務](./media/topdesk-secure-tutorial/ic790856.png "判斷提示取用者服務")

    c. 複製 **AssertionConsumerService** 值，在 [TOPdesk - Secure 網域及 URL]  區段的 [回覆 URL] 文字方塊中，貼上此值。

6. 若要建立憑證檔案，請執行下列步驟：

    ![[MSSQLSERVER 的通訊協定內容]](./media/topdesk-secure-tutorial/ic790606.png "憑證")

    a. 從 Azure 入口網站開啟下載的中繼資料檔案。

    b. 展開 **RoleDescriptor** 節點，其具有 **fed:ApplicationServiceType** 的 **xsi:type**。

    c. 複製 **X509Certificate** 節點的值。

    d. 儲存複製的 **X509Certificate** 值到本機電腦的檔案中。

7. 在 **Public** 區段中，按一下 [加入]  。

    ![加入](./media/topdesk-secure-tutorial/ic790607.png "加")

8. 在 [SAML 組態輔助程式]  對話頁面上，執行下列步驟：

    ![SAML 組態輔助程式](./media/topdesk-secure-tutorial/ic790608.png "SAML 組態輔助程式")

    a. 若要上傳您從 Azure 入口網站下載的中繼資料檔案，請在 [同盟中繼資料]  下按一下 [瀏覽]  。

    b. 若要上傳您的憑證檔案，請在 [憑證 (RSA)]  下按一下 [瀏覽]  。

    c. 針對 **私密金鑰 (RSA、PKCS8、DER)** ，您可以上傳您自己的私密金鑰，也可以連絡 [TOPdesk - Secure 用戶端支援小組](https://www.topdesk.com/us/support)以取得私密金鑰。

    d. 若要上傳您從 TOPdesk 支援小組取得的標誌檔案，請在 [標誌圖示]  下按一下 [瀏覽]  。

    e. 在 [使用者名稱屬性]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    f. 在 [顯示名稱]  文字方塊中，輸入您的組態名稱。

    g. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 TOPdesk - Secure 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [TOPdesk - Secure]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **TOPdesk - Secure** 並加以選取。

    ![應用程式清單中的 [TOPdesk - Secure] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-topdesk---secure-test-user"></a>建立 TOPdesk - Secure 測試使用者

若要讓 Azure AD 使用者可以登入 TOPdesk - Secure，則必須將他們佈建到 TOPdesk - Secure。  
TOPdesk - Secure 需以手動的方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：

1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。

2. 在上方功能表中按一下 [TOPdesk] \> [新增] \> [支援檔案] \> [操作員]。

    ![運算子](./media/topdesk-secure-tutorial/ic790610.png "運算子")

3. 在 [新增操作員]  對話方塊上，執行下列步驟：

    ![新增操作員](./media/topdesk-secure-tutorial/ic790611.png "新增操作員")

    a. 按一下 [General] \(一般\)  索引標籤。

    b. 在 [Surname] \(姓氏\)  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [位置] 區段中選取該帳戶的 [網站]。

    d. 在 [TOPdesk 登入] 區段的 [登入名稱] 文字方塊中，輸入使用者的登入名稱。

    e. 按一下 [檔案]  。

> [!NOTE]
> 您可以使用任何其他的 TOPdesk - Secure 使用者帳戶建立工具或 TOPdesk - Secure 提供的 API，來佈建 Azure AD 使用者帳戶。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 TOPdesk - Secure 圖格時，應該會自動登入您設定 SSO 的 TOPdesk - Secure。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

