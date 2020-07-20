---
title: 教學課程：Azure Active Directory 與 Mind Tools Toolkit 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Mind Tools Toolkit 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0c1cab94a6b83ca429fd640759bed8af0ae881
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124851"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>教學課程：Azure Active Directory 與 Mind Tools Toolkit 整合

在本教學課程中，您將了解如何整合 Mind Tools Toolkit 與 Azure Active Directory (Azure AD)。

透過此整合，您可以：

* 在 Azure AD 中控制可存取 Mind Tools Toolkit 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Mind Tools Toolkit (單一登入)。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Mind Tools Toolkit 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Mind Tools Toolkit 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Mind Tools Toolkit 支援由 SP 起始的 SSO。
* Mind Tools Toolkit 支援 Just-In-Time 使用者佈建。
* 設定 Mind Tools Toolkit 後，您可以強制執行工作階段控制項。 此控制項可即時保護組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>從資源庫新增 Mind Tools Toolkit

若要進行將 Mind Tools Toolkit 整合到 Azure AD 中的設定，您必須從資源庫將 Mind Tools Toolkit 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在最左側的瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Mind Tools Toolkit**。
1. 從搜尋結果中選取 [Mind Tools Toolkit]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會使用名為 **B.Simon** 的測試使用者，設定及測試與 Mind Tools Toolkit 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Mind Tools Toolkit 中相關使用者之間的連結關聯性。

若要設定及測試與 Mind Tools Toolkit 搭配運作的 Azure AD 單一登入，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Mind Tools Toolkit SSO](#configure-mind-tools-toolkit-sso)** ，以在應用程式端設定單一登入設定。
    1. **[建立 Mind Tools Toolkit 測試使用者](#create-a-mind-tools-toolkit-test-user)** ，以在 Mind Tools Toolkit 中擁有對應的 B.Simon。 此對應項會與 Azure AD 中出現的使用者連結。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會依照下列步驟設定與 Mind Tools Toolkit 搭配運作的 Azure AD 單一登入：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Mind Tools Toolkit] 應用程式整合頁面上，選取 [單一登入]。

    ![已醒目提示單一登入的 [管理] 區段](common/select-sso.png)

1. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 以啟用單一登入。

    ![已醒目提示 SAML 的 [選取單一登入方法] 對話方塊](common/select-saml-option.png)

1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

    ![[以 SAML 設定單一登入] 頁面，其中已醒目提示 [基本 SAML 設定] 的鉛筆圖示](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段的 [登入 URL] 方塊中，輸入具有 `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` 模式的 URL。

    > [!NOTE]
    > [登入 URL] 值不是真正的值。 請使用實際的登入 URL 來更新此值。 請連絡 [Mind Tools Toolkit 用戶端支援小組](mailto:support@goodpractice.com)以取得此值。

1. 在 [以 SAML 設定單一登入] 頁面上，移至 [SAML 簽署憑證] 區段。 在 [同盟中繼資料 XML] 右側選取 [下載]，以下載 XML 文字，並將其儲存在您的電腦上。 XML 內容取決於您選取的選項。

    ![[SAML 簽署憑證] 區段，其中已醒目提示 [同盟中繼資料 XML] 旁的 [下載]](common/metadataxml.png)

1. 在 [設定 Mind Tools Toolkit] 區段中，複製您所需的下列其中一個 URL。

    * **登入 URL**

    * **Azure AD 識別碼**

    * **登出 URL**

    ![已醒目提示設定 URL 的 [設定 Mind Tools Toolkit] 區段](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者：

1. 在 Azure 入口網站最左側，選取 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 **B.Simon**。  
   1. 在 [使用者名稱] 欄位中，輸入 **B.Simon@** _companydomain_ **.** _extension_。 例如： B.Simon@contoso.com 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Mind Tools Toolkit 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在應用程式清單中，選取 [Mind Tools Toolkit]。
1. 在應用程式的概觀頁面中移至 [管理] 區段，然後選取 [使用者和群組]。

   ![已醒目提示使用者和群組的 [管理] 區段](common/users-groups-blade.png)

1. 選取 [新增使用者]。 在 [新增指派] 對話方塊中，選取 [使用者和群組]。

   ![已醒目提示 [新增使用者] 的 [使用者和群組] 視窗](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]。 然後，選擇畫面底部的 [選取] 按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後選擇畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-mind-tools-toolkit-sso"></a>設定 Mind Tools Toolkit SSO

若要在 **Mind Tools Toolkit** 端設定單一登入，請將已下載的 [同盟中繼資料 XML] 文字和先前複製的 URL 傳送給 [Mind Tools Toolkit 支援小組](mailto:support@goodpractice.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-a-mind-tools-toolkit-test-user"></a>建立 Mind Tools Toolkit 測試使用者

在本節中，您會在 Mind Tools Toolkit 中建立名為 B.Simon 的使用者。

Mind Tools Toolkit 支援依預設啟用的 Just-In-Time 佈建。 這一節沒有您需要進行的動作。 如果 Mind Tools Toolkit 中還沒有使用者，當您嘗試存取 Mind Tools Toolkit 時，就會建立新的使用者。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在「我的應用程式」入口網站中選取 [Mind Tools Toolkit] 圖格時，應該會自動登入您已設定 SSO 的 Mind Tools Toolkit。 如需「我的應用程式」入口網站的詳細資訊，請參閱[我的應用程式入口網站簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [用於整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Mind Tools Toolkit](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Mind Tools Toolkit](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
