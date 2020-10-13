---
title: 教學課程：Azure Active Directory 與 Palo Alto Networks - Aperture 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Palo Alto Networks - Aperture 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: d7f0ca619c990d2a42c31df82ee9f90bd7ea230b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801816"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>教學課程：Azure Active Directory 與 Palo Alto Networks - Aperture 整合

在本教學課程中，您將了解如何整合 Palo Alto Networks - Aperture 與 Azure Active Directory (Azure AD)。
將 Palo Alto Networks - Aperture 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Palo Alto Networks - Aperture 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Palo Alto Networks - Aperture (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Palo Alto Networks - Aperture 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Palo Alto Networks - Aperture 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Palo Alto Networks - Aperture 支援由 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>從資源庫新增 Palo Alto Networks - Aperture

若要設定將 Palo Alto Networks - Aperture 整合到 Azure AD 中，您需要將 Palo Alto Networks - Aperture 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Palo Alto Networks - Aperture**。
1. 從結果面板中選取 [Palo Alto Networks - Aperture]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

在本節中，您會以名為 **B.Simon** 的測試使用者為基礎，設定及測試與 Palo Alto Networks - Aperture 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Palo Alto Networks - Aperture 中相關使用者之間的連結關聯性。

若要設定並測試與 Palo Alto Networks - Aperture 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Palo Alto Networks - Aperture SSO](#configure-palo-alto-networks---aperture-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Palo Alto Networks - Aperture 測試使用者](#create-palo-alto-networks---aperture-test-user)** - 使 Palo Alto Networks - Aperture 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **Palo Alto Networks - Aperture** 應用程式整合頁面上，找到 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Palo Alto Networks - Aperture 網域及 URL 單一登入資訊 IDP](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Palo Alto Networks - Aperture 網域及 URL 單一登入資訊 SP](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [Palo Alto Networks - Aperture 用戶端支援小組](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) \(英文\) 以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 在 [設定 Palo Alto Networks - Aperture]**** 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Palo Alto Networks - Aperture 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Palo Alto Networks - Aperture]****。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-palo-alto-networks---aperture-sso"></a>設定 Palo Alto Networks - Aperture SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Palo Alto Networks - Aperture。

2. 按一下頂端功能表列上的 [SETTINGS] \(設定\)****。

    ![[SETTINGS] \(設定\) 索引標籤](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. 瀏覽至 [APPLICATION] \(應用程式\)**** 區段，然後從功能表左側按一下 [Authentication] \(驗證\)****。

    ![[Authentication] \(驗證\) 索引標籤](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. 在 [Authentication] \(驗證\) 頁面上，執行下列步驟：
    
    ![[Authentication] \(驗證\) 索引標籤](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. 從 [單一登入]**** 欄位中，選取 [啟用單一登入 (支援的 SSP 提供者為 Okta、One login)]****。

    b. 在 [識別提供者識別碼]**** 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]**** 值。

    c. 按一下 [Identity Provider Certificate] \(身分識別提供者憑證\)**** 欄位中的 [Choose File] \(選擇檔案\)****，以上傳從 Azure AD 下載的憑證。

    d. 在 [識別提供者 SSO URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    e. 檢閱來自 [Aperture Info] \(Aperture 資訊\)**** 區段的 IdP 資訊，然後從 [Aperture Key] \(Aperture 金鑰\)**** 欄位下載憑證。

    f. 按一下 [檔案] 。


### <a name="create-palo-alto-networks---aperture-test-user"></a>建立 Palo Alto Networks - Aperture 測試使用者

在本節中，您會在 Palo Alto Networks - Aperture 中建立名為 Britta Simon 的使用者。 請與 [Palo Alto Networks - Aperture 用戶端支援小組](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) \(英文\) 合作，以在 Palo Alto Networks - Aperture 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Palo Alto Networks - Aperture 登入 URL。  

* 直接移至 Palo Alto Networks - Aperture 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Palo Alto Networks - Aperture 

您也可以使用 Microsoft 存取面板，以任何模式測試應用程式。 當您按一下存取面板中的 Palo Alto Networks - Aperture 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果是在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 Palo Alto Networks - Aperture。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 Palo Alto Networks - Aperture 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
