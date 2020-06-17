---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Anyone Home CRM 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Anyone Home CRM 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1de27845-c06e-44b0-892b-010b6854b711
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4146e6de2ef23f7e12ee6b59c40045f8a678dc13
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-anyone-home-crm"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Anyone Home CRM 整合

在本教學課程中，您會了解如何整合 Anyone Home CRM 與 Azure Active Directory (Azure AD)。 將 Anyone Home CRM 與 Azure AD 整合時，您可以︰

* 在 Azure AD 中控制哪些使用者可以存取 Anyone Home CRM。
* 讓使用者透過其 Azure AD 帳戶自動登入 Anyone Home CRM。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Anyone Home CRM 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Anyone Home CRM 支援由 **IDP** 起始的 SSO
* 設定 Anyone Home CRM 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-anyone-home-crm-from-the-gallery"></a>從資源庫新增 Anyone Home CRM

若要設定 Anyone Home CRM 與 Azure AD 整合，您需要從資源庫將 Anyone Home CRM 加入受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Anyone Home CRM**。
1. 從結果面板選取 [Anyone Home CRM]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-anyone-home-crm"></a>設定及測試 Anyone Home CRM 的 Azure AD 單一登入

利用名稱為 **B.Simon** 的測試使用者，設定及測試 Anyone Home CRM 的 Azure AD SSO。 為了讓 SSO 能夠運作，您必須為 Azure AD 使用者與 Anyone Home CRM 中的相關使用者建立連結關聯性。

若要設定及測試 Anyone Home CRM 的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Anyone Home CRM SSO](#configure-anyone-home-crm-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Anyone Home CRM 測試使用者](#create-anyone-home-crm-test-user)** - 將 Anyone Home CRM 中 B.Simon 的對應使用者與 Azure AD 中該使用者的代表身分連結。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Anyone Home CRM] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入] 頁面上，輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://app.anyonehome.com/webroot/files/simplesamlphp/www/module.php/saml/sp/metadata.php/<Anyone_Home_Provided_Unique_Value>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://app.anyonehome.com/webroot/files/simplesamlphp/www/module.php/saml/sp/saml2-acs.php/<Anyone_Home_Provided_Unique_Value>`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Anyone Home CRM 客戶支援小組](mailto:support@anyonehome.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Anyone Home CRM 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Anyone Home CRM]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-anyone-home-crm-sso"></a>設定 Anyone Home CRM SSO

若要在 **Anyone Home CRM** 端設定單一登入，您必須將**應用程式同盟中繼資料 URL** 傳送給 [Anyone Home CRM 支援小組](mailto:support@anyonehome.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-anyone-home-crm-test-user"></a>建立 Anyone Home CRM 測試使用者

在本節中，您會在 Anyone Home CRM 中建立一個名稱為 Britta Simon 的使用者。 請與  [Anyone Home CRM 支援小組](mailto:support@anyonehome.com)合作，在 Anyone Home CRM 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Anyone Home CRM] 圖格時，應該會自動登入您已設定 SSO 的 Anyone Home CRM。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [試用 Anyone Home CRM 與 Azure AD](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Anyone Home CRM](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
