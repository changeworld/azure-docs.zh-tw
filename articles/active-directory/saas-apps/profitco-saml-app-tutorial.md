---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Profit.co 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Profit.co 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 669ab403d738a12a7d4b3093fe50b9cf55a75510
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553490"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Profit.co 整合

在本教學課程中，您將了解如何整合 Profit.co 與 Azure Active Directory (Azure AD)。 在整合 Profit.co 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Profit.co 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Profit.co。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Profit.co 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Profit.co 支援由 IDP 起始的 SSO。

* 設定 Profit.co 後，您可以強制執行工作階段控制項。 這可即時保護貴組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-profitco-from-the-gallery"></a>從資源庫新增 Profit.co

若要進行將 Profit.co 整合到 Azure AD 中的設定，您必須將 Profit.co 從資源庫新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Profit.co**。
1. 從結果面板中選取 [Profit.co]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco"></a>設定及測試 Profit.co 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Profit.co 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，請建立 Azure AD 使用者與 Profit.co 中相關使用者之間的連結關聯性。

以下提供設定及測試與 Profit.co 搭配運作之 Azure AD SSO 的一般步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Profit.co SSO](#configure-profitco-sso)** ，以在應用程式端設定單一登入設定。
    1. **[建立 Profit.co 測試使用者](#create-a-profitco-test-user)** ，以在 Profit.co 中擁有對應的 B.Simon。此對應項會與 Azure AD 中出現的使用者連結。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Profit.co] 應用程式整合頁面上，尋找 [管理] 區段。 選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 組態] 的鉛筆圖示，以編輯設定。

   ![以 SAML 設定單一登入頁面的螢幕擷取畫面，醒目提示鉛筆圖示](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段中，已預先設定好應用程式，並已在 Azure 中預先填入必要的 URL。 使用者必須選取 [儲存] 按鈕，才能儲存設定。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，選取 [複製] 按鈕。 這會複製**應用程式同盟中繼資料 URL**，並將它儲存在您的電腦上。

    ![SAML 簽署憑證區段的螢幕擷取畫面，醒目提示下載](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 欄位中顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Profit.co 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在應用程式清單中，選取 [Profit.co]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![管理區段的螢幕擷取畫面，醒目提示使用者和群組](common/users-groups-blade.png)

1. 選取 [新增使用者]。 在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![使用者和群組使用者及群組頁面的螢幕擷取畫面，其中已醒目提示 [新增使用者]](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]。 然後選擇畫面底部的 [選取] 按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後選擇畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-profitco-sso"></a>設定 Profit.co SSO

若要在 Profit.co 端設定單一登入，您必須將應用程式同盟中繼資料 URL 傳送給 [Profit.co 支援小組](mailto:support@profit.co)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-a-profitco-test-user"></a>建立 Profit.co 測試使用者

在本節中，您會在 Profit.co 中建立名為 B.Simon 的使用者。請與 [Profit.co 支援小組](mailto:support@profit.co)合作，在 Profit.co 平台中新增使用者。 您必須先建立和啟動使用者，才能使用單一登入。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Profit.co] 圖格時，應該會自動登入您已設定 SSO 的 Profit.co。 如需詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Profit.co](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Profit.co](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)