---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 SiteIntel 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SiteIntel 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c987f101-8746-467e-ad12-75cf2920af2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b3f6bbda9923b7f4f19dda505e799cb8b102ca
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424324"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SiteIntel 整合

在本教學課程中，您將學習如何整合 SiteIntel 與 Azure Active Directory (Azure AD)。 在整合 SiteIntel 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制誰可存取 SiteIntel。
* 允許使用者使用其 Azure AD 帳戶，自動登入 SiteIntel。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 可以使用 SiteIntel 單一登入 (SSO) 的訂閱。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SiteIntel 支援 SP 起始與 IdP 起始的 SSO。
* 當您設定了 SiteIntel 之後，就能施行工作階段控制項，即時保護您組織的敏感性資料免遭外洩及滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-siteintel-from-the-gallery"></a>從資源庫新增 SiteIntel

若要設定 SiteIntel 與 Azure AD 的整合，必須從資源庫將 SiteIntel 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格上，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 方塊中，輸入 **SiteIntel**。
1. 在結果清單中，選取 [SiteIntel]，然後新增該應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>設定及測試 SiteIntel 的 Azure AD 單一登入

以名為 *B.Simon* 的測試使用者身分，設定及測試 Azure AD SSO 與 SiteIntel 的搭配組合。 SSO 要能夠運作，必須建立 Azure AD 使用者與 SiteIntel 中相關使用者之間的連結關係。

若要設定及測試 Azure AD SSO 與 SiteIntel 的搭配組合，請完成下列基本組件：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。  

    a. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用者 B.Simon 測試 Azure AD 單一登入。  

    b. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓使用者 B.Simon 能夠使用 Azure AD 單一登入。

1. **[設定 SiteIntel SSO](#configure-siteintel-sso)** - 在應用程式端上設定單一登入設定。

    * **[建立 SiteIntel 測試使用者](#create-a-siteintel-test-user)** - 在 SiteIntel 中設定 B.Simon 的對應身分，並將其連結到 Azure AD 中該使用者的代表。

1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

若要在 Azure 入口網站中啟用 Azure AD SSO，請執行下列動作：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SiteIntel] 應用程式整合頁面上，移至 [管理] 區段，然後選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 選取 [使用 SAML 設定單一登入] 頁面上之 [基本 SAML 設定] 旁的 [編輯] (鉛筆圖示)。

   ![「設定 SAML 單一登入」窗格的螢幕擷取畫面](common/edit-urls.png)

1. 若要在 IdP 起始的模式下設定應用程式，請在 [基本 SAML 設定] 區段中執行下列動作：

    a. 在 [識別碼] 方塊中，以下列格式鍵入 URL︰`urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. 在 [回覆 URL] 方塊中，以下列格式鍵入 URL︰`https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. 在 [轉送狀態] 方塊中，以下列格式鍵入 URL︰`https://<CLIENT>.siteintel.com`

1. 若要在 SP 起始的模式下設定應用程式，請選取 [設定其他 URL]，然後執行下列動作：

   * 在 [登入 URL] 方塊中，以下列格式鍵入 URL︰`https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL、登入 URL 及轉送狀態更新這些值。 您可以連絡 [SiteIntel 用戶端支援小組](mailto:support@intalytics.com)取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 從 [設定 SAML 單一登入] 頁面上的 [SAML 簽署憑證] 區段中，選取 [複製] 按鈕，以複製 [應用程式同盟中繼資料 URL] 方塊中的 URL。

    ![「應用程式同盟中繼資料 URL」複製按鈕的螢幕擷取畫面](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]  >  [使用者]  >  [所有使用者]。
1. 在窗格頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列動作：

   a. 在 [名稱] 方塊中，輸入 **B.Simon**。  

   b. 在 [使用者名稱] 方塊中，以下列格式輸入使用者名稱：`username@companydomain.extension` (例如，`B.Simon@contoso.com`)。

   c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

   d. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您將透過授與 SiteIntel 的存取權，允許使用者 B.Simon 使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在 [應用程式] 清單中，選取 [SiteIntel]。
1. 在應用程式概觀頁面上的 [管理] 區段中，選取 [使用者與群組]。

   ![「使用者與群組」連結的螢幕擷取畫面](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 窗格中選取 [使用者與群組]。

    ![「新增使用者」按鈕的螢幕擷取畫面](common/add-assign-user.png)

1. 在 [使用者與群組] 窗格中選取 [B.Simon]，然後選取畫面底部的 [選取] 按鈕。
1. 若您希望在 SAML 判斷提示中出現任何角色值，請在 [選取角色] 窗格的清單中，選取適當的使用者角色，然後選取 [選取] 按鈕。
1. 在 [新增指派] 窗格中，選取 [指派] 按鈕。

## <a name="configure-siteintel-sso"></a>設定 SiteIntel SSO

若要在 SiteIntel 端設定單一登入，請將您從 [應用程式同盟中繼資料 URL] 方塊複製的 URL，傳送給 [SiteIntel 支援小組](mailto:support@intalytics.com)。 他們會設定此值，為兩端建立正確的 SAML SSO 連線。

### <a name="create-a-siteintel-test-user"></a>建立 SiteIntel 測試使用者

在本節中，您將在 SiteIntel 中建立名為 *Britta Simon* 的使用者。 請與  [SiteIntel 支援小組](mailto:support@intalytics.com)合作，在 SiteIntel 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您選取 [存取面板] 中選取 [SiteIntel] 磚時，應能自動登入已經設定 SSO 的 SiteIntel。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [什麼是應用程式存取權與 Azure Active Directory 單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [試用 SiteIntel 與 Azure AD 的搭配組合](https://aad.portal.azure.com/)
- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [如何使用進階的視覺功能與控制項保護 SiteIntel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
