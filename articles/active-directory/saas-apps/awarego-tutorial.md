---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 AwareGo 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 AwareGo 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 6599abd6282d0d1eb7cb81002c34ddd5158dab6b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>教學課程：Azure Active Directory 單一登入與 AwareGo 整合

在本教學課程中，您將了解如何整合 AwareGo 與 Azure Active Directory (Azure AD)。 在整合 AwareGo 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 AwareGo 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 AwareGo。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 AwareGo 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 AwareGo 支援由服務提供者 (SP) 起始的 SSO。


## <a name="adding-awarego-from-the-gallery"></a>從資源庫新增 AwareGo

若要進行將 AwareGo 整合到 Azure AD 中的設定，您必須從資源庫將 AwareGo 新增至受控軟體即服務 (SaaS) 應用程式清單。

1. 使用公司帳戶、學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 選取 [企業應用程式]   > [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊，中輸入 **AwareGo**。
1. 在結果面板中選取 [AwareGo]，然後新增應用程式。 應用程式需要幾秒鐘才會新增至您的租用戶。


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>設定和測試 AwareGo 的 Azure AD SSO

使用名為 **B.Simon** 的測試使用者，設定及測試與 AwareGo 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 AwareGo 中相關使用者之間的連結關聯性。

若要設定及測試與 AwareGo 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。  

    a. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用者 B.Simon 測試 Azure AD 單一登入。  
    b. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓使用者 B.Simon 能夠使用 Azure AD 單一登入。  

1. **[設定 AwareGo SSO](#configure-awarego-sso)** 以在應用程式端設定單一登入設定。

    a. **[建立 AwareGo 測試使用者](#create-an-awarego-test-user)** - 使 AwareGo 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。  
    b. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

若要在 Azure 入口網站中啟用 Azure AD SSO，請執行下列動作：

1. 在 Azure 入口網站的 [AwareGo] 應用程式整合頁面的 [管理] 下，選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 若要編輯設定，請在 [設定使用 SAML 的單一登入] 窗格中，選取 [編輯] 按鈕。

   ![基本 SAML 組態編輯按鈕的螢幕擷取畫面。](common/edit-urls.png)

1. 在 [基本 SAML 組態] 窗格下的編輯窗格中，執行下列步驟：

    a. 在 [登入 URL] 方塊中，以下列格式輸入 URL︰

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. 在 [識別碼 (實體識別碼)] 方塊中，以下列格式輸入 URL：`https://<SUBDOMAIN>.awarego.com`

    c. 在 [回覆 URL] 方塊中，以下列格式輸入 URL：`https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > 上述值並非真正的值。 請使用實際的識別碼和回覆 URL 來更新這些值。 若要取得這些值，請連絡 [AwareGo 用戶端支援小組](mailto:support@awarego.com)。 您也可以參考 Azure 入口網站中 [基本 SAML 組態] 區段中的範例。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段的 [憑證 (Base64)] 旁選取 [下載]，以下載憑證並儲存在電腦上。

    ![SAML 簽署憑證窗格中憑證「下載」連結的螢幕擷取畫面。](common/certificatebase64.png)

1. 在 **設定 AwareGo** 區段中，依需求複製一或多個 URL。

    ![」設定 AwareGo」窗格的螢幕擷取畫面，可在此複製組態 URL。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左側窗格中，依序選取 [Azure Active Directory]、[使用者] > [所有使用者]。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 **使用者** 窗格中執行下列動作：

   a. 在 [名稱] 方塊中，輸入 **B.Simon**。  
   b. 在 [使用者名稱] 方塊中，以下列格式輸入使用者名稱：`<username>@<companydomain>.<extension>` (例如，B.Simon@contoso.com)。  
   c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值供稍後使用。  
   d. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 AwareGo 的存取權授與使用者 B.Simon，讓其能夠使用 Azure SSO。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在 **應用程式** 清單中，選取 [AwareGo]。
1. 在應用程式概觀頁面上的 [管理] 區段中，選取 [使用者與群組]。
1. 選取 [新增使用者]，然後在 [新增指派] 窗格中選取 [使用者與群組]。
1. 在 [使用者和群組] 窗格的 [使用者] 清單中，選取 [B.Simon]，然後選取 [選取] 按鈕。
1. 如果您要將角色指派給使用者，可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，則系統會選取 *預設存取* 角色。
1. 在 [新增指派] 窗格中，選取 [指派] 按鈕。

## <a name="configure-awarego-sso"></a>設定 AwareGo SSO

若要在 **AwareGo** 端設定單一登入，請將您稍早下載的 **憑證 (Base64)** 憑證，以及稍早從 Azure 入口網站複製的 URL 傳送至 [AwareGo 支援小組](mailto:support@awarego.com)。 支援小組會建立此設定，在兩端建立正確的 SAML SSO 連線。

### <a name="create-an-awarego-test-user"></a>建立 AwareGo 測試使用者

在本節中，您會在 AwareGo 中建立名為 Britta Simon 的使用者。 請與 [AwareGo 支援小組](mailto:support@awarego.com)合作，在 AwareGo 平台中新增使用者。 您必須先建立和啟動使用者，才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列方式來測試您的 Azure AD 單一登入組態： 

* 在 Azure 入口網站中，選取 [測試此應用程式]。 這會將您重新導向至 AwareGo 登入頁面，您可以在其中起始登入流程。 

* 直接移至 AwareGo 登入頁面，然後從該處起始登入流程。

* 移至 Microsoft 我的應用程式。 選取我的應用程式中的 **AwareGo** 圖格時，系統會將您重新導向至 AwareGo 登入頁面。 如需詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 AwareGo 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取應用程式控制延伸。 如需詳細資訊，請參閱[了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。


