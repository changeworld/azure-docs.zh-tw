---
title: 教學課程：Azure AD SSO 與 Smart Global Governance 整合
description: 了解如何設定 Azure Active Directory 與 Smart Global Governance 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: d93cd2efe49f819c8adeb53674b6d4f465312e06
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527412"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Smart Global Governance 整合

在本教學課程中，您會了解如何整合 Smart Global Governance 與 Azure Active Directory (Azure AD)。 在整合 Smart Global Governance 與 Azure AD 時，您可以︰

* 使用 Azure AD 來控制哪些使用者可以存取 Smart Global Governance。
* 讓使用者透過其 Azure AD 帳戶自動登入 Smart Global Governance。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Smart Global Governance 訂用帳戶。

## <a name="tutorial-description"></a>教學課程說明

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

Smart Global Governance 支援由 SP 和 IDP 起始的 SSO。

設定 Smart Global Governance 後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外流和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-smart-global-governance-from-the-gallery"></a>從資源庫新增 Smart Global Governance

若要設定將 Smart Global Governance 整合到 Azure AD 中，您需要從資源庫將 Smart Global Governance 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或使用個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格中，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Smart Global Governance**。
1. 在結果面板中選取 [Smart Global Governance]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>設定及測試 Smart Global Governance 的 Azure AD SSO

您將使用名稱為 B.Simon 的測試使用者，設定及測試 Smart Global Governance 與 Azure AD SSO 搭配運作。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Smart Global Governance 對應使用者之間的連結關聯性。

若要設定及測試 Smart Global Governance 與 Azure AD SSO 搭配運作，請採取下列高階步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以測試 Azure AD 單一登入。
    1. **[將存取權授予測試使用者](#grant-access-to-the-test-user)** ，讓該使用者能夠使用 Azure AD 單一登入。
1. 在應用程式端 **[設定 Smart Global Governance SSO](#configure-smart-global-governance-sso)** 。
    1. **[建立 Smart Global Governance 測試使用者](#create-a-smart-global-governance-test-user)** 以作為 Azure AD 中使用者代表的對應身分。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Smart Global Governance] 應用程式整合頁面上，選取 [管理] 區段中的 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 組態] 的鉛筆按鈕，以編輯設定：

   ![基本 SAML 組態的鉛筆按鈕](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段中，若您想要以 IDP 起始模式設定應用程式，請執行下列步驟。

    a. 在 [識別碼] 方塊中，輸入其中一個 URL：

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. 在 [回覆 URL] 方塊中，輸入其中一個 URL：

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL]，然後完成下列步驟。

   - 在 [登入 URL] 方塊中，輸入其中一個 URL：

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，針對 [憑證 (原始)] 選取 [下載]，以下載憑證並將其儲存在電腦上：

    ![憑證下載連結](common/certificateraw.png)

1. 在 [設定 Smart Global Governance] 區段中，根據您的需求複製適當的 URL：

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]。 選取 [使用者 ]，然後選取 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，完成下列步驟：
   1. 在 [名稱] 方塊中，輸入 **B.Simon**。  
   1. 在 [使用者名稱] 方塊中，輸入 \<username>@\<companydomain>.\<extension>。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

### <a name="grant-access-to-the-test-user"></a>將存取權授予測試使用者

在本節中，您會將 Smart Global Governance 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Smart Global Governance]。
1. 在應用程式的概觀頁面中，請在 [管理] 區段中，選取 [使用者和群組]：

   ![選取 [使用者和群組]](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]：

    ![選取 [新增使用者]](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-smart-global-governance-sso"></a>設定 Smart Global Governance SSO

若要設定 Smart Global Governance 端的單一登入，請將您從 Azure 入口網站下載的原始憑證和複製的適當 URL 傳送給 [Smart Global Governance 支援小組](mailto:support.tech@smartglobal.com)。 他們會將 SAML SSO 連線設定為在兩端都正確。

### <a name="create-a-smart-global-governance-test-user"></a>建立 Smart Global Governance 測試使用者

請與  [Smart Global Governance 支援小組](mailto:support.tech@smartglobal.com)合作，在 Smart Global Governance 中新增一個名稱為 B.Simon 的使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您將使用存取面板來測試您的 Azure AD SSO 組態。

當您在存取面板中選取 [Smart Global Governance] 圖格時，應該會自動登入您已設定 SSO 的 Smart Global Governance。 如需存取面板的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Smart Global Governance](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Smart Global Governance](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)