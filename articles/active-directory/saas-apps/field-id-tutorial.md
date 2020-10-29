---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與欄位識別碼整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與欄位識別碼之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 55a66f3f287ffb50d932263e407772efffa839ba
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453530"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與欄位識別碼整合

在本教學課程中，您將了解如何整合欄位識別碼與 Azure Active Directory (Azure AD)。 在整合欄位識別碼與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取欄位識別碼的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Field iD。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Field iD 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Field iD 支援由 IDP 起始的 SSO。
* 設定 Field iD 後，您可以強制執行工作階段控制項。 這可即時保護貴組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-field-id-from-the-gallery"></a>從資源庫新增 Field iD

若要設定將欄位識別碼整合到 Azure AD 中，您需要從資源庫將欄位識別碼新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左瀏覽窗格中，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **欄位識別碼** 。
1. 從結果面板中選取 [Field iD]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>設定及測試欄位識別碼的 Azure AD 單一登入

使用名為 **B.Simon** 的測試使用者，設定及測試與 Field iD 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Field iD 中相關使用者之間的連結關聯性。

若要設定及測試與 Field iD 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。
    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. [設定 Field iD SSO](#configure-field-id-sso) 以在應用程式端設定單一登入設定。
    1. [建立 Field iD 測試使用者](#create-a-field-id-test-user) 以使 Field iD 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Field iD] 應用程式整合頁面上，尋找 [管理] 區段。 然後選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 組態] 的鉛筆圖示，以編輯設定。

   ![以 SAML 設定單一登入頁面的螢幕擷取畫面，其中的鉛筆圖示已醒目提示](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，執行下列步驟：

   a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://<tenantname>.fieldid.com/fieldid`

   b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL︰`https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Field iD 支援小組](mailto:support@ecompliance.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取 [複製] 圖示以複製 [應用程式同盟中繼資料 URL]。 將其儲存在您的電腦上。

    ![SAML 簽署憑證的螢幕擷取畫面，並醒目提示複製圖示](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 針對 [名稱] 輸入 `B.Simon`。  
   1. 針對 [使用者名稱]，輸入 username@companydomain.extension (例如 `B.Simon@contoso.com`)。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將欄位識別碼的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在應用程式清單中，選取 [欄位識別碼]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![管理區段的螢幕擷取畫面，醒目提示使用者和群組](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增使用者] 的螢幕擷取畫面](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊中，從 [使用者] 清單中選取 [B.Simon]，然後選擇畫面底部的 [選取]。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後，選擇畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-field-id-sso"></a>設定欄位識別碼 SSO

若要在 Field iD 端設定單一登入，請將 **應用程式同盟中繼資料 URL** 傳送給 [Field iD 支援小組](mailto:support@ecompliance.com)。 他們會確保兩端的 SAML SSO 連線已正確設定。

### <a name="create-a-field-id-test-user"></a>建立 Field iD 測試使用者

在本節中，您會在欄位識別碼中建立名為 Britta Simon 的使用者。 請與 [Field iD 支援小組](mailto:support@ecompliance.com)合作，在 Field iD 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Field iD] 圖格時，應該會自動登入您已設定 SSO 的 Field iD。 如需詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試使用 Azure AD 的欄位識別碼](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項來保護欄位識別碼](/cloud-app-security/proxy-intro-aad)