---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Nitro Productivity Suite 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Nitro Productivity Suite 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: e645f4075aa1c4c027e8ea884108fdeb708467af
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279943"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Nitro Productivity Suite 整合

在此教學課程中，您將了解如何整合 Nitro Productivity Suite 與 Azure Active Directory (Azure AD)。 在整合 Nitro Productivity Suite 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Nitro Productivity Suite 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Nitro Productivity Suite。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* Nitro Productivity Suite [Enterprise 訂用帳戶](https://www.gonitro.com/pricing)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Nitro Productivity Suite 支援由 **SP** 和 **IDP** 起始的 SSO。
* Nitro Productivity Suite 支援 **Just In Time** 使用者佈建。

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>從資源庫新增 Nitro Productivity Suite

若要設定以將 Nitro Productivity Suite 整合到 Azure AD 中，您需要從資源庫將 Nitro Productivity Suite 新增到受控的 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格上，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Nitro Productivity Suite**。
1. 從結果中選取 [Nitro Productivity Suite]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>設定及測試 Nitro Productivity Suite 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Nitro Productivity Suite 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Nitro Productivity Suite 中相關使用者之間的連結關聯性。

若要設定及測試與 Nitro Productivity Suite 搭配運作的 Azure AD SSO，請完成下列建構元素：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。

    a. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    
    b. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
    
2. [建立 Nitro Productivity Suite 測試使用者](#create-a-nitro-productivity-suite-test-user) - 使 Nitro Productivity Suite 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Nitro Productivity Suite] 應用程式整合頁面上，尋找 [管理] 區段。 選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  。 選取 [下載]  以下載憑證，並將其儲存在您的電腦上。

    ![已醒目提示 [下載] 連結的 [SAML 簽署憑證] 區段螢幕擷取畫面](common/certificatebase64.png)
    
1. 在 [設定 Nitro Productivity Suite]  區段中，選取 [登入 URL]  旁邊的複製圖示。
    
    ![[設定 Nitro Productivity Suite] 區段的螢幕擷取畫面，其中的 URL 和複製圖示已醒目提供](common/copy-configuration-urls.png)
    
1. 在 [Nitro 管理員入口網站](https://admin.gonitro.com/)的 [企業設定]  頁面上，尋找 [單一登入]  區段。 選取 [設定 SAML SSO]  。

    a. 將上述步驟的 [登入 URL]  貼到 [登入 URL]  欄位中。
    
    b. 從上述步驟中上傳 [X509 簽署憑證]  欄位中的 [憑證 (Base64)]  。
    
    c. 選取 [提交]  。
    
    d. 選取 [啟用單一登入]  。


1. 返回 [Azure 入口網站](https://portal.azure.com/)。 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的鉛筆圖示，以編輯設定。

   ![以 SAML 設定單一登入頁面的螢幕擷取畫面，其中的鉛筆圖示已醒目提示](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段中，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，從 [Nitro 管理員入口網站](https://admin.gonitro.com/)複製並貼上 [SAML 實體識別碼]  欄位。 此欄位應該具有下列模式：`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. 在 [回覆 URL]  文字方塊中，從 [Nitro 管理員入口網站](https://admin.gonitro.com/)複製並貼上 [ACS URL]  欄位。 此欄位應該具有下列模式：`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. 如果您想要以 **SP** 起始的模式設定應用程式，請選取 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，輸入 URL：`https://sso.gonitro.com/login`

1. 選取 [儲存]  。

1. Nitro Productivity Suite 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![預設屬性的螢幕擷取畫面](common/default-attributes.png)

1. 除了上述屬性，Nitro Productivity Suite 應用程式還需要於 SAML 回應中再傳回一些屬性。 這些屬性會預先填入，但您可以根據您的需求來檢閱這些屬性。
    
    | 名稱  |  來源屬性|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下密碼。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Nitro Productivity Suite 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [Nitro Productivity Suite]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 然後，選擇畫面底部的 [選取]  按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-nitro-productivity-suite-test-user"></a>建立 Nitro Productivity Suite 測試使用者

Nitro Productivity Suite 支援依預設啟用的 Just-In-Time 使用者佈建。 沒有您需要進行的額外動作。 如果 Nitro Productivity Suite 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 Nitro Productivity Suite 登入 URL。  

2. 直接移至 Nitro Productivity Suite 登入 URL，並從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Nitro Productivity Suite 

您也可以使用 Microsoft 存取面板，以任何模式測試應用程式。 當您按一下存取面板中的 Nitro Productivity Suite 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果是在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 Nitro Productivity Suite。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 Nitro Productivity Suite 後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
