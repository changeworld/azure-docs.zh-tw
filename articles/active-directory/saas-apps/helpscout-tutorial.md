---
title: 教學課程：Azure Active Directory 與 Help Scout 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Help Scout 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 16783d344c6a0dedec4e24d49150e0b4b2edbe3e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548135"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>教學課程：Azure Active Directory 與 Help Scout 整合

在本教學課程中，您會了解如何將 Help Scout 與 Azure Active Directory (Azure AD) 整合。
Help Scout 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中管控可存取 Help Scout 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Help Scout (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定與 Help Scout 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Help Scout 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Help Scout 支援由 **SP 和 IDP** 起始的 SSO
* Help Scout 支援 **Just In Time** 使用者佈建

## <a name="adding-help-scout-from-the-gallery"></a>從資源庫新增 Help Scout

若要設定將 Help Scout 整合到 Azure AD 中，您需要從資源庫將 Help Scout 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Help Scout**。
1. 從結果面板選取 [Help Scout]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **B.Simon** 的測試使用者為基礎，設定及測試與 Help Scout 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Help Scout 中相關使用者之間的連結關聯性。

若要使用 Help Scout 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Help Scout SSO](#configure-help-scout-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Help Scout 測試使用者](#create-help-scout-test-user)** - 使 Help Scout 中具有與 Azure AD 中的 B.Simon 連結的相對應使用者。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Help Scout 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Help Scout]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

1. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Help Scout 網域與 URL 單一登入資訊](common/idp-intiated.png)

    a. **識別碼**是 Help Scout 的**對象 URI (服務提供者實體識別碼)** ，以 `urn:` 開頭

    b. **回覆 URL** 是 Help Scout 的**回傳 URL (判斷提示取用者服務 URL)** ，以 `https://` 開頭 

    > [!NOTE]
    > 這些 URL 中的值僅供示範。 您必須從實際的「回覆 URL」和「識別碼」來更新這些值。 您可以從 [驗證] 區段下的 [單一登入]  索引標籤取得這些值，本教學課程稍後會說明。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Help Scout 網域與 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，將 URL 輸入為：`https://secure.helpscout.net/members/login/`

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Help Scout]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **B.Simon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **B.Simon\@yourcompanydomain.extension**  
    例如， B.Simon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Help Scout 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Help Scout]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Help Scout]  。

    ![應用程式清單中的 Help Scout 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-help-scout-sso"></a>設定 Help Scout SSO

1. 若要自動執行 Help Scout 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 Help Scout]  便會將您導向到 Help Scout 應用程式。 請從該處提供用以登入 Help Scout 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Help Scout，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Help Scout 公司網站，然後執行下列步驟：

1. 在頂端功能表中按一下 [管理]  ，然後從下拉式功能表中選取 [公司]  。

    ![設定單一登入](./media/helpscout-tutorial/settings1.png)

1. 在左側導覽窗格中，選取 [驗證]  。

    ![設定單一登入](./media/helpscout-tutorial/settings2.png)

1. 這會帶您前往 [SAML 設定] 區段，請執行下列步驟：

    ![設定單一登入](./media/helpscout-tutorial/settings3.png)

    a. 複製**回傳 URL (判斷提示取用者服務 URL)** 值，並在 Azure 入口網站的 [基本 SAML 組態] 區段中的 [回覆 URL] 文字方塊內貼上該值。

    b. 複製 [對象 URl (服務提供者實體識別碼)] 值，並在 Azure 入口網站的 [基本 SAML 組態] 中的 [識別碼] 文字方塊內貼上該值。

1. 將 [啟用 SAML]  切換為開啟，並執行下列步驟：

    ![設定單一登入](./media/helpscout-tutorial/settings4.png)

    a. 在 [登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    b. 按一下 [上傳憑證]  以上傳從 Azure 入口網站下載的**憑證 (Base64)** 。

    c. 在 [電子郵件網域] 文字方塊中，輸入組織的電子郵件網域，如 `contoso.com`。 您可以使用逗號來分隔多個網域。 每當 Help Scout 使用者或系統管理員在 [Help Scout 登入頁面](https://secure.helpscout.net/members/login/)中輸入該特定網域時，便會路由傳送至「識別提供者」，以驗證其認證。

    d. 最後，如果您希望使用者只能透過此方法登入 Help Scout，則可以切換開啟 [強制 SAML 登入]  。 如果您仍想保留以 Help Scout 認證登入的選項，則可以將其切換為關閉。 即使啟用此選項，帳戶擁有者一律可以使用其帳戶密碼來登入 Help Scout。

    e. 按一下 [檔案]  。

### <a name="create-help-scout-test-user"></a>建立 Help Scout 測試使用者

本節會在 Help Scout 中建立名為 B.Simon 的使用者。 Help Scout 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Help Scout 中還沒有使用者存在，在驗證之後就會建立新的使用者。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Help Scout] 圖格時，應該會自動登入您已設定 SSO 的 Help Scout。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Help Scout](https://aad.portal.azure.com/)