---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 GitHub Enterprise Cloud - 企業帳戶整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 GitHub Enterprise Cloud - 企業帳戶之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: d88cbb79b42637721412dd0a35c231782a896721
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029824"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 GitHub Enterprise Cloud - 企業帳戶整合

在本教學課程中，您將了解如何整合 GitHub Enterprise Cloud - 企業帳戶與 Azure Active Directory (Azure AD)。 當您將 GitHub Enterprise Cloud - 企業帳戶與 Azure AD 整合後，您可以：

* 在 Azure AD 中控制可存取 GitHub Enterprise 帳戶及帳戶內任何組織的人員。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* [GitHub Enterprise 帳戶](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts)
* 屬於 Enterprise 帳戶擁有者的 GitHub 使用者帳戶。 

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* GitHub Enterprise Cloud - 企業帳戶支援 **SP** 和 **IDP** 起始的 SSO
* GitHub Enterprise Cloud - 企業帳戶支援 **Just In Time** 使用者佈建
* 設定 GitHub Enterprise Cloud - 企業帳戶後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>從資源庫新增 GitHub Enterprise Cloud - 企業帳戶

若要設定 GitHub Enterprise Cloud - 企業帳戶與 Azure AD 整合，您需要從資源庫將 GitHub Enterprise Cloud - 企業帳戶新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **GitHub Enterprise Cloud - 企業帳戶**。
1. 從 [結果] 面板中選取 [GitHub Enterprise Cloud - 企業帳戶]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>設定和測試適用於 GitHub Enterprise Cloud - 企業帳戶的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 GitHub Enterprise Cloud - 企業帳戶搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 GitHub Enterprise Cloud - 企業帳戶中相關使用者之間的連結關聯性。

若要設定及測試與 GitHub Enterprise Cloud - 企業帳戶搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[將您的 Azure AD 使用者和測試使用者帳戶指派給 GitHub 應用程式](#assign-the-azure-ad-test-user)** - 讓您的使用者帳戶和測試使用者 `B.Simon` 能夠使用 Azure AD 單一登入。
1. **[為 Enterprise 帳戶及其組織啟用和測試 SAML](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** - 在應用程式端設定單一登入設定。
    1. **[使用其他 Enterprise 帳戶擁有者或組織成員帳戶來測試 SSO](#test-sso)** - 確認設定是否可運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [GitHub Enterprise Cloud - 企業帳戶] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

     在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：`https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > 請將 `<ENTERPRISE-SLUG>` 取代為您 GitHub Enterprise 帳戶的實際名稱。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificateBase64.png)

1. 在 [設定 GitHub Enterprise Cloud - 企業帳戶] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 `B.Simon` 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>將您的 Azure AD 使用者和測試使用者帳戶指派給 GitHub 應用程式

在本節中，您會將 GitHub Enterprise Cloud - Enterprise 帳戶的存取權授與 `B.Simon`，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [GitHub Enterprise Cloud - 企業帳戶]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon] 和您的使用者帳戶，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>為 Enterprise 帳戶及其組織啟用和測試 SAML

若要在 **GitHub Enterprise Cloud - Enterprise 帳戶** 端設定單一登入，請執行[此 GitHub 文件](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account)中列出的步驟。 
1. 使用屬於 [Enterprise 帳戶擁有者](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner)的使用者帳戶登入 GitHub.com。 
1. 從 Azure 入口網站複製應用程式的 `Login URL` 欄位中的值，並將其貼到 [GitHub Enterprise 帳戶 SAML 設定] 的 `Sign on URL` 欄位中。 
1. 從 Azure 入口網站複製應用程式的 `Azure AD Identifier` 欄位中的值，並將其貼到 [GitHub Enterprise 帳戶 SAML 設定] 的 `Issuer` 欄位中。 
1. 複製您在上述步驟中從 Azure 入口網站下載的 [憑證 (Base64)] 檔案的內容，並將其貼到 [GitHub Enterprise 帳戶 SAML 設定] 的適當欄位中。 
1. 按一下 `Test SAML configuration`，並確認您能夠從 GitHub Enterprise 帳戶成功地向 Azure AD 進行驗證。
1. 測試成功後，請儲存設定。 
1. 第一次從 GitHub Enterprise 帳戶透過 SAML 進行驗證後，會在 GitHub Enterprise 帳戶中建立 _連結的外部身分識別_，將已登入的 GitHub 使用者帳戶與 Azure AD 使用者帳戶產生關聯。  
 
為您的 GitHub Enterprise 帳戶啟用 SAML SSO 後，您的 Enterprise 帳戶所擁有的所有組織依預設都會啟用 SAML SSO。 所有成員都必須使用 SAML SSO 進行驗證，以取得其所屬組織的存取權，而企業擁有者則需要在存取 Enterprise 帳戶時使用 SAML SSO 進行驗證。

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>使用其他 Enterprise 帳戶擁有者或組織成員帳戶來測試 SSO

為 GitHub Enterprise 帳戶設定 SAML 整合後 (也適用於 Enterprise 帳戶中的 GitHub 組織)，對 Azure AD 中的應用程式指派的其他 Enterprise 帳戶擁有者應該能夠瀏覽至 GitHub Enterprise 帳戶 URL (`https://github.com/enterprises/<enterprise account>`)、透過 SAML 進行驗證，以及存取 GitHub Enterprise 帳戶下的原則和設定。 

Enterprise 帳戶中所含組織的組織擁有者，應該能夠[邀請使用者加入其 GitHub 組織](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization)。 請使用組織擁有者帳戶登入 GitHub.com，並依照文章中的步驟邀請 `B.Simon` 加入組織。 如果 `B.Simon` 還沒有 GitHub 使用者帳戶，則必須為其建立此帳戶。 

若要使用 `B.Simon` 測試使用者帳戶測試 Enterprise 帳戶下的 GitHub 組織存取：
1. 以組織擁有者的身分，邀請 `B.Simon` 加入 Enterprise 帳戶下的組織。 
1. 使用您要連結至 `B.Simon` Azure AD 使用者帳戶的使用者帳戶登入 GitHub.com。
1. 使用 `B.Simon` 使用者帳戶登入 Azure AD。
1. 移至 GitHub 組織。 系統應該會提示使用者透過 SAML 進行驗證。 SAML 驗證成功後，`B.Simon` 應該就可以存取組織資源。 

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 GitHub Enterprise Cloud - 企業帳戶](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 GitHub Enterprise Cloud - 企業帳戶](/cloud-app-security/proxy-intro-aad)
