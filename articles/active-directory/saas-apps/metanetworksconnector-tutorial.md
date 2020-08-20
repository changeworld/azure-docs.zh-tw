---
title: 教學課程：Azure Active Directory 與 Meta Networks Connector 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Meta Networks Connector 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 71aaaeec174611a86b037b693187cc4f49fe1a0c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528840"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>教學課程：Azure Active Directory 與 Meta Networks Connector 整合

在本教學課程中，您將了解如何整合 Meta Networks Connector 與 Azure Active Directory (Azure AD)。
Meta Networks Connector 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Meta Networks Connector 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Meta Networks Connector (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Meta Networks Connector 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Meta Networks Connector 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Meta Networks Connector 支援由 **SP** 和 **IDP** 起始的 SSO
 
* Meta Networks Connector 支援 **Just In Time** 使用者佈建

## <a name="adding-meta-networks-connector-from-the-gallery"></a>從資源庫新增 Meta Networks Connector

若要設定 Meta Networks Connector 與 Azure AD 整合，您需要從資源庫將 Meta Networks Connector 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Meta Networks Connector，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Meta Networks Connector**，從結果面板中選取 [Meta Networks Connector]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Meta Networks Connector](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Meta Networks Connector 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須在 Azure AD 使用者與 Meta Networks Connector 中的相關使用者之間建立連結關聯性。

若要使用 Meta Networks Connector 來設定並測試 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Meta Networks Connector 單一登入](#configure-meta-networks-connector-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Meta Networks Connector 測試使用者](#create-meta-networks-connector-test-user)** - 使 Meta Networks Connector 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Meta Networks Connector 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Meta Networks Connector]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Meta Networks Connector 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Meta Networks Connector 網域及 URL 單一登入資訊](common/both-advanced-urls.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. 在 [轉送狀態]  文字方塊中，使用下列模式輸入 URL：`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > 這些都不是真正的值。 本教學課程稍後會說明如何使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。

6. Meta Networks Connector 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 **編輯** 圖示以開啟 [使用者屬性] 對話方塊。 

    ![image](common/edit-attribute.png)
    
7. 除了以上屬性外，Meta Networks Connector 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：
    
    | 名稱 | 來源屬性 | 命名空間|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | NAME | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | 電話 | user.telephonenumber | |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

8. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 Meta Networks Connector]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>設定 Meta Networks Connector 單一登入

1. 在瀏覽器中開啟新索引標籤，登入您的 Meta Networks Connector 系統管理員帳戶。
    
    > [!NOTE]
    > Meta Networks Connector 是安全的系統。 因此，在存取其入口網站之前，您必須先將公用 IP 位址新增至其系統端的允許清單。 若要取得公用 IP 位址，請遵循[此處](https://whatismyipaddress.com/)指定的下列連結。 將 IP 位址傳送至 [Meta Networks Connector 用戶端支援小組](mailto:support@metanetworks.com)，以將 IP 位址新增至允許清單。
    
2. 移至 [系統管理員]  ，然後選取 [設定]  。
    
    ![設定單一登入](./media/metanetworksconnector-tutorial/configure3.png)
    
3. 確定 [記錄網際網路流量]  和 [強制執行 VPN MFA]  已設定為關閉。
    
    ![設定單一登入](./media/metanetworksconnector-tutorial/configure1.png)
    
4. 移至 [系統管理員]  ，然後選取 [SAML]  。
    
    ![設定單一登入](./media/metanetworksconnector-tutorial/configure4.png)
    
5. 在 [詳細資料]  頁面上，執行下列步驟：
    
    ![設定單一登入](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. 複製 [SSO URL] 值並將它貼至 [Meta Networks Connector 網域和 URL] 區段的 [登入 URL] 文字方塊。
    
    b. 複製 [收件者 URL] 值並將它貼至 [Meta Networks Connector 網域和 URL] 區段的 [回覆 URL] 文字方塊。
    
    c. 複製 [對象 URI (SP 實體識別碼)] 值並將它貼至 [Meta Networks Connector 網域和 URL] 區段的 [識別碼 (實體識別碼)] 文字方塊。
    
    d. 啟用 SAML
    
6. 在 [一般]  索引標籤上，執行下列步驟：

    ![設定單一登入](./media/metanetworksconnector-tutorial/configure5.png)

    a. 在 [識別提供者單一登入 URL]  中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    b. 在 [識別提供者簽發者]  中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    c. 從記事本中的 Azure 入口網站開啟已下載的憑證，將它貼至 [X.509 憑證]  文字方塊。

    d. 啟用 [Just-In-Time 佈建]  。

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

在本節中，您會將 Meta Networks Connector 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Meta Networks Connector]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Meta Networks Connector]  。

    ![應用程式清單中的 Meta Networks Connector 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-meta-networks-connector-test-user"></a>建立 Meta Networks Connector 測試使用者

本節會在 Meta Networks Connector 中建立名為 Britta Simon 的使用者。 Meta Networks Connector 支援預設啟用的 Just-in-Time 佈建。 在這一節沒有您需要進行的動作項目。 如果 Meta Networks Connector 中還沒有使用者存在，在您嘗試存取 Meta Networks Connector 時就會建立新的使用者。

>[!Note]
>如果您需要手動建立使用者，請連絡 [Meta Networks Connector 用戶端支援小組](mailto:support@metanetworks.com)。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Meta Networks Connector 圖格時，應該就會自動登入您已設定 SSO 的 Meta Networks Connector。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

