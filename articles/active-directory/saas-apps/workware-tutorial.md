---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Workware 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workware 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f3f48695f0ef26b6e54073dbfc4bb0c61802fe58
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92904779"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Workware 整合

在本教學課程中，您將了解如何整合 Workware 與 Azure Active Directory (Azure AD)。 在整合 Workware 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Workware 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Workware。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Workware 單一登入 (SSO) 功能的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Workware 支援由 **IDP** 起始的 SSO

## <a name="adding-workware-from-the-gallery"></a>從資源庫新增 Workware

若要設定將 Workware 整合到 Azure AD 中，您需要將 Workware 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Workware** 。
1. 從結果面板中選取 [Workware]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>設定及測試 Workware 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Workware 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Workware 中相關使用者之間的連結關聯性。

若要設定及測試與 Workware 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Workware SSO](#configure-workware-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Workware 測試使用者](#create-workware-test-user)** - 使 Workware 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Workware] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入] 頁面上，輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`<WORKWARE_URL>/WW/AuthServices`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Workware 用戶端支援小組](mailto:support@activeops.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Workware] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Workware 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Workware]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-workware-sso"></a>設定 Workware SSO

若要在 Workware 中使用 SSO 功能，必須完成下列設定：

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>啟用 Workware 系統管理員的 SSO 權限

* 若要讓 Workware 系統管理員能夠設定 SSO 驗證，您必須為 Workware 系統管理員啟用 [SSO 驗證] 權限 (在 [系統設定] > [角色的權限] 畫面中的 [系統管理] > [系統設定權限類別] 內)。

    ![SSO 驗證權限](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>在 Workware 中設定 SSO 驗證

1. 移至 [系統設定] 頁面，然後按一下 [SSO 驗證]

1. 在 [SSO 驗證] 區段中，按一下 [新增 SSO 驗證] 按鈕，然後執行下列步驟： 

    ![SSO 驗證](./media/workware-tutorial/authentication.png)

    1. 在 [外部識別提供者] 中，提供 IDP 的名稱。
    1. 選取 [SAML2.0] 作為 [驗證類型]
    1. 在 [識別提供者登入 URL] 文字方塊中，輸入您從 Azure 入口網站複製的 [登入 URL] 值。
    1. 在 [識別提供者簽發者 URL] 文字方塊中，輸入您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。
    1. 在 [識別提供者登出 URL] 文字方塊中，輸入您從 Azure 入口網站複製的 [登出 URL] 值。
    1. 按一下 [啟用]。
    1. 將從 Azure 入口網站下載的 [憑證] 上傳至 [識別提供者憑證]。
    1. 按一下 [儲存]


### <a name="create-workware-test-user"></a>建立 Workware 測試使用者

1. 以系統管理員身分登入 Workware 網站。

1. 選取 [管理員] > [建立/檢視] > [使用者帳戶] > [新增]

1. 在下列頁面上，執行下列步驟。

    ![測試使用者](./media/workware-tutorial/create-user.png)

    a. 在 [名稱] 欄位中提供有效的名稱。

    b. 將 [驗證類型] 選取為 [SSO]。

    c. 輸入必要的欄位，然後按一下 [儲存]。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Workware

* 您可以使用 Microsoft 存取面板。 當您在存取面板中按一下 [Workware] 圖格時，應該會自動登入您已設定 SSO 的 Workware。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 Workware 後，您可以強制執行工作階段控制，以即時防止組織的敏感性資料遭到外洩及滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。


