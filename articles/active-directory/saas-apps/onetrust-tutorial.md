---
title: 教學課程：Azure Active Directory 與 OneTrust Privacy Management Software 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 OneTrust Privacy Management Software 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 77604cdeca76bea847fa6b81c5f0f1865025e96f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623175"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>教學課程：Azure Active Directory 與 OneTrust Privacy Management Software 整合

在本教學課程中，您將瞭解如何整合 OneTrust 隱私權管理軟體與 Azure Active Directory (Azure AD) 。 當您整合 OneTrust 隱私權管理軟體與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 OneTrust 隱私權管理軟體的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 OneTrust 隱私權管理軟體。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 OneTrust Privacy Management Software 整合，您需要以下項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用版。
* 已啟用 OneTrust 隱私權管理軟體單一登入的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* OneTrust Privacy Management Software 支援由 **SP** 和 **IDP** 起始的 SSO

* OneTrust Privacy Management Software 支援 **Just In Time** 使用者佈建

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>從資源庫新增 OneTrust 隱私權管理軟體

若要設定 OneTrust Privacy Management Software 與 Azure AD 整合，您需要從資源庫將 OneTrust Privacy Management Software 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **OneTrust 隱私權管理軟體** 。
1. 從結果面板選取 [ **OneTrust 隱私權管理軟體** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>設定及測試 Azure AD OneTrust 隱私權管理軟體的 SSO

使用名為 **b. Simon** 的測試使用者，設定及測試與 OneTrust 隱私權管理軟體搭配的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 OneTrust 隱私權管理軟體中相關使用者之間的連結關聯性。

若要設定及測試 Azure AD SSO 與 OneTrust 隱私權管理軟體，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 OneTrust 隱私權管理軟體 SSO](#configure-onetrust-privacy-management-software-sso)** -在應用程式端設定單一登入設定。
    1. **[建立 OneTrust 隱私權管理軟體測試使用者](#create-onetrust-privacy-management-software-test-user)** -使 Simon InOneTrust 隱私權管理軟體的對應使用者連結到該使用者在 Azure AD 的代表身分。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD SSO。
 
1. 在 Azure 入口網站的 [ **OneTrust 隱私權管理軟體** 應用程式整合] 頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的鉛筆圖示，以編輯設定。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    a. 在 [識別碼]  文字方塊中，輸入 URL：`https://www.onetrust.com/saml2`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.onetrust.com/auth/consumerservice`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

     在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的回覆 URL 與登入 URL 更新這些值。 請連絡 [OneTrust Privacy Management Software 用戶端支援小組](mailto:support@onetrust.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [安裝 OneTrust Privacy Management Software]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

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

在本節中，您會將 OneTrust 隱私權管理軟體的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [OneTrust Privacy Management Software]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 然後，選擇畫面底部的 [選取]  按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="configure-onetrust-privacy-management-software-sso"></a>設定 OneTrust 隱私權管理軟體 SSO

若要在 **OneTrust Privacy Management Software** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和複製的適當 URL 傳送給 [OneTrust Privacy Management Software 支援小組](mailto:support@onetrust.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-onetrust-privacy-management-software-test-user"></a>建立 OneTrust Privacy Management Software 測試使用者

本節目標是要在 OneTrust Privacy Management Software 中建立一個名為 Britta Simon 的使用者。 OneTrust Privacy Management Software 支援預設會啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 OneTrust Privacy Management Software 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!Note]
>如果您需要手動建立使用者，請連絡 [OneTrust Privacy Management Software 支援小組](mailto:support@onetrust.com)。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 OneTrust 隱私權管理軟體登入 URL，您可以在其中起始登入流程。  
 
* 直接前往 OneTrust 隱私權管理軟體登入 URL，並從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 按一下 [在 Azure 入口網站 **測試此應用程式** ]，您應該會自動登入您已設定 SSO 的 OneTrust 隱私權管理軟體。 

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您在我的應用程式中按一下 [OneTrust 隱私權管理軟體] 圖格時，如果在 SP 模式中設定，系統會將您重新導向至應用程式登入頁面以起始登入流程，如果是在 IDP 模式中設定，您應該會自動登入您已設定 SSO 的 OneTrust 隱私權管理軟體。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 OneTrust 隱私權管理軟體後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。