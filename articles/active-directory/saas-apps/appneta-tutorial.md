---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 AppNeta Performance Monitor 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 AppNeta Performance Monitor 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: 43447d98cf4af6887e66dd0811464e34fc1ac717
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457886"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 AppNeta Performance Monitor 整合

在本教學課程中，您將了解如何整合 AppNeta Performance Monitor 與 Azure Active Directory (Azure AD)。 當您整合 AppNeta Performance Monitor 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 AppNeta Performance Monitor 的人員。
* 讓使用者使用他們的 Azure AD 帳戶自動登入 AppNeta Performance Monitor。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 AppNeta Performance Monitor 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* AppNeta Performance Monitor 支援 **SP** 初始化的 SSO


* AppNeta Performance Monitor 支援 **Just In Time** 使用者佈建

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>從資源庫新增 AppNeta Performance Monitor

若要設定將 AppNeta Performance Monitor 整合到 Azure AD 中，您需要從資源庫將 AppNeta Performance Monitor 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **AppNeta Performance Monitor** 。
1. 從結果面板中選取 **AppNeta Performance Monitor** ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>設定及測試適用於 AppNeta Performance Monitor 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 AppNeta Performance Monitor 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 AppNeta Performance Monitor 中相關使用者之間的連結關聯性。

若要設定及測試與 AppNeta Performance Monitor 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 AppNeta Performance Monitor SSO](#configure-appneta-performance-monitor-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 AppNeta Performance Monitor 測試使用者](#create-appneta-performance-monitor-test-user)** - 使 AppNeta Performance Monitor 中對應的 B.Simon 連結到使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [AppNeta Performance Monitor]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.pm.appneta.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入值：`PingConnect`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的登入 URL 來更新此值。 請連絡 [AppNeta Performance Monitor 用戶端支援小組](mailto:support@appneta.com)以取得此值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. AppNeta Performance Monitor 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/edit-attribute.png)

1. 除了上述屬性外，AppNeta Performance Monitor 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | 電子郵件| user.userprincipalname|
    | NAME| user.userprincipalname|
    | groups  | user.assignedroles |
    | 電話| user.telephonenumber |
    | title| user.jobtitle|
    | | |

    > [!NOTE]
    > **groups** 代表 Appneta 中的安全性群組，其對應至 Azure AD 中的 **角色** 。 請參閱[這份](../develop/active-directory-enterprise-app-role-management.md)文件，該文件說明如何在 Azure AD 中建立自訂角色。

    1. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    1. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    1. 讓 [命名空間]  保持空白。

    1. 選取 [來源] 作為 [屬性]  。

    1. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    1. 按一下 [確定]  。

    1. 按一下 [檔案]  。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 AppNeta Performance Monitor]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 AppNeta Performance Monitor 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [AppNeta Performance Monitor]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-appneta-performance-monitor-sso"></a>設定 AppNeta Performance Monitor SSO

若要在 **AppNeta Performance Monitor** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和複製的適當 URL 傳送給 [AppNeta Performance Monitor 支援小組](mailto:support@appneta.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-appneta-performance-monitor-test-user"></a>建立 AppNeta Performance Monitor 測試使用者

本節會在 AppNeta Performance Monitor 中建立名為 Britta Simon 的使用者。 AppNeta Performance Monitor 支援預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 AppNeta Performance Monitor 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!Note]
> 如果您需要手動建立使用者，請連絡 [AppNeta Performance Monitor 支援小組](mailto:support@appneta.com)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [AppNeta Performance Monitor] 圖格時，應該會自動登入您已設定 SSO 的 AppNeta Performance Monitor。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 AppNeta Performance Monitor](https://aad.portal.azure.com/)