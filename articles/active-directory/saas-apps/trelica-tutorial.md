---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Trelica 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Trelica 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 577bdae2-dbab-4445-a07e-de0119b65d76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733e7529af5de453462efb1a13c21203681e442c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Trelica 整合

在本教學課程中，您將了解如何整合 Trelica 與 Azure Active Directory (Azure AD)。

透過此整合，您可以：

* 在 Azure AD 中控制可存取 Trelica 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Trelica。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Trelica 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Trelica 支援由 IDP 起始的 SSO。
* Trelica 支援 Just-In-Time 使用者佈建。
* 設定 Trelica 後，您可以強制執行工作階段控制項。 此控制項可即時保護組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-trelica-from-the-gallery"></a>從資源庫新增 Trelica

若要設定將 Trelica 整合到 Azure AD 中，您需要從資源庫將 Trelica 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在最左側的瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Trelica**。
1. 從搜尋結果中選取 [Trelica]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>設定及測試 Trelica 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Trelica 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Trelica 中相關使用者之間的連結關聯性。

若要設定及測試與 Trelica 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Trelica SSO](#configure-trelica-sso)** 在應用程式端設定單一登入設定。
    1. **[建立 Trelica 測試使用者](#create-a-trelica-test-user)** 以在 Trelica 中建立一個與 Azure AD 中代表 B.Simon 使用者。 此對應項會與 Azure AD 中出現的使用者連結。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Trelica] 應用程式整合頁面上，移至 [管理] 區段。 選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![[以 SAML 設定單一登入] 頁面，其中已醒目提示 [基本 SAML 設定] 的鉛筆圖示](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入] 頁面上，輸入下列值：

    1. 在 [識別碼] 方塊中，輸入 URL **https://app.trelica.com** 。

    1. 在 [回覆 URL] 方塊中，輸入模式為 `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` 的 URL。

    > [!NOTE]
    > [回覆 URL] 不是真實的值。 使用實際的「回覆 URL」(亦稱為 ACS) 來更新此值。
    > 您可以登入 Trelica 並前往 [SAML 識別提供者設定頁面](https://app.trelica.com/Admin/Profile/SAML) \([系統管理員] > [帳戶] > [SAML]\) 來找到此資訊。 按一下 [判斷提示取用者服務 (ACS) URL] 旁的複製按鈕來將其放在剪貼簿上，以準備好貼入 Azure AD 中的 [回覆 URL] 文字方塊。
    > 如有任何問題，請閱讀 [Trelica 說明文件](https://docs.trelica.com/admin/saml/azure-ad) \(英文\) 或連絡 [Trelica 用戶端支援小組](mailto:support@trelica.com)。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將資料儲存在您的電腦上。

    ![[SAML 簽署憑證] 區段，[應用程式同盟中繼資料 Url] 旁邊醒目提示著 [複製] 按鈕](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的最左側窗格上，選取 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 **B.Simon**。
   1. 在 [使用者名稱] 欄位中，輸入 **B.Simon@** _companydomain_ **.** _extension_。 例如： B.Simon@contoso.com 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Trelica 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在應用程式清單中，選取 [Trelica]。
1. 在應用程式的概觀頁面中移至 [管理] 區段，然後選取 [使用者和群組]。

   ![已醒目提示使用者和群組的 [管理] 區段](common/users-groups-blade.png)

1. 選取 [新增使用者]。 在 [新增指派] 對話方塊中，選取 [使用者和群組]。

   ![已醒目提示 [新增使用者] 的 [使用者和群組] 視窗](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]。 然後選擇畫面底部的 [選取] 按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後選擇畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-trelica-sso"></a>設定 Trelica SSO

若要在 **Trelica** 端設定單一登入，請移至 [SAML 識別提供者設定頁面](https://app.trelica.com/Admin/Profile/SAML) \([系統管理員] > [帳戶] > [SAML]\)。 按一下 [新增] 按鈕。 輸入 **Azure AD** 作為名稱，然後選擇 [來自 URL 的中繼資料] 作為資中繼資料類型。 將您從 Azure AD 取得的 [應用程式同盟中繼資料 URL]，貼入 Trelica 的 [中繼資料 URL] 欄位中。

如有任何問題，請閱讀 [Trelica 說明文件](https://docs.trelica.com/admin/saml/azure-ad) \(英文\) 或連絡 [Trelica 用戶端支援小組](mailto:support@trelica.com)。

### <a name="create-a-trelica-test-user"></a>建立 Trelica 測試使用者

Trelica 支援依預設啟用的 Just-In-Time 使用者佈建。 這一節沒有您需要進行的動作。 如果 Trelica 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在「我的應用程式」入口網站中選取 [Trelica] 圖格時，應該會自動登入您已設定 SSO 的 Trelica。 如需「我的應用程式」入口網站的詳細資訊，請參閱[我的應用程式入口網站簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [用於整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Trelica](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Trelica](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
