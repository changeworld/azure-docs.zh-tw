---
title: 教學課程：Azure Active Directory 與 Mimecast Personal Portal 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Mimecast Personal Portal 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: ad7b5b97149d38b64d75f5a02cd0aa776893e832
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522558"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Mimecast Personal Portal 整合

在本教學課程中，您會了解如何整合 Mimecast Personal Portal 與 Azure Active Directory (Azure AD)。 在整合 Mimecast Personal Portal 與 Azure AD 後，您可以︰

* 在 Azure AD 中控制可存取 Mimecast Personal Portal 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Mimecast Personal Portal。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Mimecast Personal Portal 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Mimecast Personal Portal 支援由 **SP 和 IDP** 起始的 SSO
* 設定 Mimecast Personal Portal 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>從資源庫新增 Mimecast Personal Portal

若要設定將 Mimecast Personal Portal 整合到 Azure AD 中，您需要從資源庫將 Mimecast Personal Portal 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Mimecast Personal Portal** 。
1. 從結果面板中選取 [Mimecast Personal Portal]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>設定及測試 Mimecast Personal Portal 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Mimecast Personal Portal 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Mimecast Personal Portal 中相關使用者之間的連結關聯性。

若要設定及測試與 Mimecast Personal Portal 搭配運作的 Azure AD SSO，請完成下列建構元素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Mimecast Personal Portal SSO](#configure-mimecast-personal-portal-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Mimecast Personal Portal 測試使用者](#create-mimecast-personal-portal-test-user)** - 使 Mimecast Personal Portal 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Mimecast Personal Portal] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，如果您想要以 IDP 起始模式設定應用程式，請執行下列步驟：

    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：

    | 區域  |  值 | 
    | --------------- | --------------- |
    | 歐洲          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | 美國   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | 南非    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 澳大利亞       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 海外        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > 您會在 Mimecast Personal Portal 的 [帳戶] > [設定] > [帳戶代碼] 中找到 `accountcode` 值。 將 `accountcode` 附加至識別碼。

    b. 在 [回覆 URL] 文字方塊中輸入 URL：

    | 區域  |  值 |
    | --------------- | --------------- |
    | 歐洲          | `https://eu-api.mimecast.com/login/saml`|
    | 美國   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亞       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

1. 如果您想要以 **SP** 起始模式設定應用程式：

    在 [登入 URL] 文字方塊中，輸入 URL：

    | 區域  |  值 |
    | --------------- | --------------- |
    | 歐洲          | `https://eu-api.mimecast.com/login/saml`|
    | 美國   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亞       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

1. 按一下 [檔案] 。

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
   1. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Mimecast Personal Portal 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Mimecast Personal Portal]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-mimecast-personal-portal-sso"></a>設定 Mimecast Personal Portal SSO

1. 在不同的網頁瀏覽器視窗中，登入 Mimecast Administration Console。

1. 瀏覽至 [系統管理] > [服務] > [應用程式]。

    ![此螢幕擷取畫面顯示已選取 [應用程式] 的 Mimecast 視窗。](./media/mimecast-personal-portal-tutorial/services.png)

1. 按一下 [驗證設定檔] 索引標籤。
    
    ![此螢幕擷取畫面顯示已選取 [驗證設定檔] 的 [應用程式] 索引標籤。](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. 按一下 [新驗證設定檔] 索引標籤。

    ![顯示已選取 [新驗證設定檔] 的螢幕擷取畫面。](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. 在 [描述] 文字方塊中提供有效的描述，然後選取 [強制執行 Mimecast Personal Portal 的 SAML 驗證] 核取方塊。

    ![顯示已選取 [新驗證設定檔] 的螢幕擷取畫面。](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. 在 [Mimecast Personal Portal 的 SAML 組態] 頁面上，執行下列步驟：

    ![此螢幕擷取畫面顯示要為 Administration Console 選取強制執行 SAML 驗證的位置。](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. 針對 [提供者]，從下拉式清單中選取 [Azure Active Directory]。

    b. 在 [中繼資料 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL] 值。

    c. 按一下 [匯入] 。 匯入中繼資料 URL 之後，就會自動填入欄位，而不需對這些欄位執行任何動作。

    d. 務必取消核取 [使用受密碼保護的內容] 和 [使用整合式驗證內容] 核取方塊。

    e. 按一下 [檔案] 。

### <a name="create-mimecast-personal-portal-test-user"></a>建立 Mimecast Personal Portal 測試使用者

1. 在不同的網頁瀏覽器視窗中，登入 Mimecast Administration Console。

1. 瀏覽至 [系統管理] > [目錄] > [內部目錄]。

    ![此螢幕擷取畫面顯示 Mimecast Personal Portal 的 SAML 設定，您可以在其中輸入所述的值。](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. 選取您的網域 (如果網域如下所述)，否則請按一下 [新增網域] 來建立新網域。

    ![此螢幕擷取畫面顯示已選取內部目錄的 Mimecast 視窗。](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. 按一下 [新增位址] 索引標籤。

    ![顯示已選取網域的螢幕擷取畫面。](./media/mimecast-personal-portal-tutorial/new-address.png)

1. 在下列頁面上提供必要的使用者資訊：

    ![此螢幕擷取畫面顯示可輸入所述值的頁面。](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. 在 [電子郵件地址] 文字方塊中，輸入 `B.Simon@yourdomainname.com` 之類的使用者電子郵件地址。

    b. 在 [全域名字] 文字方塊中，輸入使用者的 **全名** 。

    c. 在 [密碼] 和 [確認密碼] 文字方塊中，輸入使用者的密碼。

    d. 選取 [在登入時強制變更] 核取方塊。

    e. 按一下 [檔案] 。

    f. 若要將角色指派給使用者，請按一下 [角色編輯]，然後依據您的組織需求，將所需的角色指派給使用者。

    ![此螢幕擷取畫面顯示您可以在其中選取 [角色編輯] 的位址設定。](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Mimecast Personal Portal] 圖格時，應該會自動登入您已設定 SSO 的 Mimecast Personal Portal。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試使用 Mimecast Personal Portal 搭配 Azure AD](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Mimecast Personal Portal](/cloud-app-security/proxy-intro-aad)