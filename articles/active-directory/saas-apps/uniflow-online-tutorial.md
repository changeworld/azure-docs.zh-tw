---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 uniFLOW Online 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 uniFLOW Online 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.openlocfilehash: fc1317ffce7c958f092ad01feff473e123581439
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521759"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 uniFLOW Online 整合

在本教學課程中，您將了解如何整合 uniFLOW Online 與 Azure Active Directory (Azure AD)。 在整合 uniFLOW Online 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 uniFLOW Online 的人員。
* 讓使用者能夠使用其 Azure AD 帳戶登入 uniFLOW Online。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* uniFLOW Online 租用戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* uniFLOW Online 支援由 **SP** 起始的 SSO

## <a name="adding-uniflow-online-from-the-gallery"></a>從資源庫新增 uniFLOW Online

若要進行將 uniFLOW Online 整合到 Azure AD 中的設定，您必須從資源庫將 uniFLOW Online 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **uniFLOW Online** 。
1. 從結果面板中選取 [uniFLOW Online]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>設定及測試 uniFLOW Online 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 uniFLOW Online 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 uniFLOW Online 中相關使用者之間的連結關聯性。

若要設定及測試與 uniFLOW Online 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
   1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
   1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 uniFLOW Online SSO](#configure-uniflow-online-sso)** - 在應用程式端設定單一登入設定。
    * **[使用已建立的測試使用者登入 uniFLOW Online](#sign-in-to-uniflow-online-using-the-created-test-user)** ，以在應用程式端上測試使用者登入。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [uniFLOW Online]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [uniFLOW Online 用戶端支援小組](mailto:support@nt-ware.com)以取得這些值。 您也可以參考 Azure 入口網站中 [基本 SAML 組態]  區段所示的模式，或參考您的 uniFLOW Online 租用戶中顯示的回覆 URL。

1. uniFLOW Online 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，uniFLOW Online 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 |  來源屬性|
    | -----------| --------------- |
    | displayname | user.displayname |
    | 暱稱 | user.onpremisessamaccountname |

   > [!NOTE]
   > 只有您的 Azure AD 使用者從本機 Windows Active Directory 同步時，`user.onpremisessamaccountname` 屬性才會包含值。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

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

在本節中，您會將 uniFLOW Online 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [uniFLOW Online]  。
1. 在應用程式的概觀頁面中移至 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

   ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

> [!NOTE]
> 若要允許所有使用者存取應用程式，而不需要手動指派，請移至 [管理]  區段，然後選取 [屬性]  。 然後，將 [需要使用者指派]  參數變更為 [否]  。

## <a name="configure-uniflow-online-sso"></a>設定 uniFLOW Online SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 uniFLOW Online 網站。

1. 在左側導覽面板中，選取 [使用者]  索引標籤。

    ![螢幕擷取畫面：顯示已從 uniflow Online 網站選取 [使用者]。](./media/uniflow-online-tutorial/configure1.png)

1. 按一下 [識別提供者]  。

    ![螢幕擷取畫面：顯示已選取 [識別提供者]。](./media/uniflow-online-tutorial/configure2.png)

1. 按一下 [新增識別提供者]  。

    ![螢幕擷取畫面：顯示已選取 [新增識別提供者]。](./media/uniflow-online-tutorial/configure3.png)

1. 在 [新增識別提供者]  區段上，執行下列步驟：

    ![螢幕擷取畫面：顯示 [新增識別提供者] 區段，您可以在其中輸入所述的值。](./media/uniflow-online-tutorial/configure4.png)

    a. 輸入顯示名稱，例如： *AzureAD SSO* 。

    b. 針對 [提供者類型]  ，從下拉式清單中選取 [WS-Fed]  選項。

    c. 針對 [WS-Fed 類型]  ，從下拉式清單中選取 [Azure Active Directory]  選項。

    d. 按一下 [檔案]  。

1. 在 [一般]  索引標籤上，執行下列步驟：

    ![此螢幕擷取畫面顯示可輸入所述值的 [一般] 索引標籤。](./media/uniflow-online-tutorial/configure5.png)

    a. 輸入顯示名稱，例如： *AzureAD SSO* 。

    b. 針對 [ADFS 同盟中繼資料]  ，選取 [從 URL]  選項。

    c. 在 [同盟中繼資料 URI]  文字方塊中，貼上您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  值。

    d. 針對 [識別提供者]  ，選取 [已啟用]  。

    e. 針對 [自動使用者註冊]  ，選取 [已啟用]  。

    f. 按一下 [檔案]  。

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>使用已建立的測試使用者登入 uniFLOW Online

1. 在不同的網頁瀏覽器視窗中，移至您租用戶的 uniFLOW Online URL。

1. 選取先前建立的身分識別提供者，以透過您的 Azure AD 執行個體登入。

1. 使用測試使用者登入。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 uniFLOW Online](https://aad.portal.azure.com/)