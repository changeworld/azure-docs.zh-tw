---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Shopify Plus 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Shopify Plus 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: 61d2eb05f7953cda16664f6b4e3317feb46aa8ad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Shopify Plus 整合

在本教學課程中，您將了解如何整合 Shopify Plus 與 Azure Active Directory (Azure AD)。 在整合 Shopify Plus 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Shopify Plus 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Shopify Plus。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Shopify Plus 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Shopify Plus 支援由 **SP 和 IDP** 起始的 SSO

* 設定 Shopify Plus 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-shopify-plus-from-the-gallery"></a>從資源庫新增 Shopify Plus

若要進行將 Shopify Plus 整合到 Azure AD 中的設定，您需要從資源庫將 Shopify Plus 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Shopify Plus**。
1. 從結果面板中選取 [Shopify Plus]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>設定及測試 Shopify Plus 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Shopify Plus 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Shopify Plus 中相關使用者之間的連結關聯性。

若要設定及測試與 Shopify Plus 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Shopify Plus SSO](#configure-shopify-plus-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Shopify Plus 測試使用者](#create-shopify-plus-test-user)** - 使 Shopify Plus 中 B.Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Shopify Plus] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，輸入 URL：`https://shopify.plus/login`

    > [!NOTE]
    > [回覆 URL] 不是真實的值。 請使用實際的「回覆 URL」來更新此值。 請連絡 [Shopify Plus 用戶端支援小組](mailto:plus-user-management@shopify.com)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. Shopify Plus 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，Shopify Plus 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---- | --------------- |
    | 電子郵件 | user.mail |

1. 將 [名稱識別碼] 格式變更為 [持續性]。 選取 [唯一使用者識別碼 (名稱識別碼)] 選項，然後選取 [名稱識別碼] 格式。 為這個選項選取 [持續性]。 儲存您的變更。
1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取 [複製] 按鈕以複製**應用程式同盟中繼資料 URL**，並儲存在您的電腦上。

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

在本節中，您會將 Shopify Plus 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Shopify Plus]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-shopify-plus-sso"></a>設定 Shopify Plus SSO

若要檢視完整步驟，請參閱[有關設定 SAML 整合的 Shopify 文件](https://help.shopify.com/en/manual/shopify-plus/saml)。

若要在 **Shopify Plus** 端上設定單一登入，請從 Azure Active Directory 複製**應用程式同盟中繼資料 URL**。 然後，登入[組織管理](https://shopify.plus)，並移至 [使用者] > [安全性]。 選取 [設定組態]，然後在 [識別提供者中繼資料 URL] 區段中貼上您的應用程式同盟中繼資料 URL。 選取 [新增] 以完成這個步驟。

### <a name="create-shopify-plus-test-user"></a>建立 Shopify Plus 測試使用者

在本節中，您要在 Shopify Plus 中建立名為 B.Simon 的使用者。 返回 [使用者] 區段，並輸入他們的電子郵件和權限來新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="enforce-saml-authentication"></a>強制執行 SAML 驗證

> [!NOTE]
> 我們建議您先使用個別使用者測試整合，然後再廣泛套用。

個別使用者：
1. 使用由 Azure AD 管理並在 Shopify Plus 中驗證的電子郵件網域，移至 Shopify Plus 中的個別使用者頁面。
1. 在 [SAML 驗證] 區段中，選取 [編輯]、選取 [必要]，然後選取 [儲存]。
1. 測試此使用者是否可以透過 idP 起始和 SP 起始的流程成功登入。

針對電子郵件網域下的所有使用者：
1. 返回 [安全性] 頁面。
1. 針對您的 SAML 驗證設定，選取 [必要]。 這會針對在 Shopify Plus 中具有該電子郵件網域的所有使用者強制執行 SAML。
1. 選取 [儲存]。

> [!IMPORTANT]
> 為電子郵件網域下的所有使用者啟用 SAML，會影響所有使用此應用程式的使用者。 使用者將無法使用其一般登入頁面登入。 他們只能夠透過 Azure Active Directory 存取應用程式。 Shopify 不會提供備份登入 URL，讓使用者可以使用其一般使用者名稱和密碼登入。 如有必要，您可以連絡 Shopify 支援以關閉 SAML。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Shopify Plus] 圖格時，應該會自動登入您已設定 SSO 的 Shopify Plus。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Shopify Plus](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Shopify Plus](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
