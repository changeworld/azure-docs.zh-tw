---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Serraview Space Utilization Software Solutions 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Serraview Space Utilization Software Solutions 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.openlocfilehash: 6c31a20b527dd6f4741d00b8306c0f88b27f2f6b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672852"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-serraview-space-utilization-software-solutions"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Serraview Space Utilization Software Solutions 整合

在本教學課程中，您將了解如何整合 Serraview Space Utilization Software Solutions 與 Azure Active Directory (Azure AD)。 嘗試搭配 Azure AD 整合 Serraview Space Utilization Software Solutions 時，您可以：

* 在 Azure AD 中控制可存取 Serraview Space Utilization Software Solutions 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Serraview Space Utilization Software Solutions。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Serraview Space Utilization Software Solutions 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Serraview Space Utilization Software Solutions 支援 **SP 和 IDP** 起始的 SSO

* 設定 Serraview Space Utilization Software Solutions 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-serraview-space-utilization-software-solutions-from-the-gallery"></a>從資源庫新增 Serraview Space Utilization Software Solutions

若要設定將 Serraview Space Utilization Software Solutions 整合到 Azure AD 中，您需要從資源庫將 Serraview Space Utilization Software Solutions 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Serraview Space Utilization Software Solutions** 。
1. 從結果面板中選取 **Serraview Space Utilization Software Solutions** ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-serraview-space-utilization-software-solutions"></a>設定和測試適用於 Serraview Space Utilization Software Solutions 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Serraview Space Utilization Software Solutions 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Serraview Space Utilization Software Solutions 中相關使用者之間的連結關聯性。

設定及測試與 Serraview Space Utilization Software Solutions 搭配運作的 Azure AD SSO，完成下列組建區塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Serraview Space Utilization Software Solutions SSO](#configure-serraview-space-utilization-software-solutions-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Serraview Space Utilization Software Solutions 測試使用者](#create-serraview-space-utilization-software-solutions-test-user)** - 讓 Serraview Space Utilization Software Solutions 中的 B.Simon 相對項連結該使用者在 Azure AD 中的代表身分。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Serraview Space Utilization Software Solutions] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`urn:Serraview:<SERRAVIEW_IDENTIFIER>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<SUBDOMAIN>.serraview.com/SAML/AssertionConsumerService.aspx`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<SUBDOMAIN>.serraview.com/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請洽詢 [Serraview Space Utilization Software Solutions 用戶端支援小組](mailto:svprodops@serraview.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Serraview Space Utilization Software Solutions] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Serraview Space Utilization Software Solutions 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Serraview Space Utilization Software Solutions]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-serraview-space-utilization-software-solutions-sso"></a>設定 Serraview 空間使用量軟體解決方案 SSO

若要在 **Serraview Space Utilization Software Solutions** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML] 和複製的適當 URL 傳送給 [Serraview Space Utilization Software Solutions 支援小組](mailto:svprodops@serraview.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-serraview-space-utilization-software-solutions-test-user"></a>建立 Serraview Space Utilization Software Solutions 測試使用者

在本節中，您會在 Serraview Space Utilization Software Solutions 中建立名為 B.Simon 的使用者。 請與 [Serraview Space Utilization Software Solutions 支援小組](mailto:svprodops@serraview.com)合作，在 Serraview Space Utilization Software Solutions 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Serraview Space Utilization Software Solutions] 圖格時，應該會自動登入您已設定 SSO 的 Serraview Space Utilization Software Solutions。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 Serraview Space Utilization Software Solutions](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)