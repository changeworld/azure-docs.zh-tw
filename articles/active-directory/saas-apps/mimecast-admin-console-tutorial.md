---
title: 教學課程：Azure Active Directory 與 Mimecast Admin Console 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Mimecast Admin Console 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 7198d341de9de42f28aeafe0fb94273b916482f1
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621274"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Mimecast Admin Console 整合

在本教學課程中，您會了解如何整合 Mimecast Admin Console 與 Azure Active Directory (Azure AD)。 在整合 Mimecast Admin Console 與 Azure AD 後，您可以︰

* 在 Azure AD 中控制可存取 Mimecast Admin Console 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Mimecast Admin Console。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Mimecast Admin Console 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Mimecast Admin Console 支援 **SP 和 IDP** 起始的 SSO

## <a name="add-mimecast-admin-console-from-the-gallery"></a>從資源庫新增 Mimecast personal portal 管理主控台

若要設定將 Mimecast Admin Console 整合到 Azure AD 中，您需要從資源庫將 Mimecast Admin Console 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Mimecast Admin Console**。
1. 從結果面板中選取 [Mimecast Admin Console]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>設定及測試 Azure AD SSO for Mimecast personal portal 管理主控台

以名為 **B.Simon** 的測試使用者，設定及測試與 Mimecast Admin Console 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Mimecast Admin Console 中相關使用者之間的連結關聯性。

若要使用 Mimecast personal portal 管理主控台來設定及測試 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Mimecast Admin Console SSO](#configure-mimecast-admin-console-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Mimecast Admin Console 測試使用者](#create-mimecast-admin-console-test-user)** - 使 Mimecast Admin Console 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [ **Mimecast personal portal 管理主控台** 應用程式整合] 頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，如果您想要以 IDP 起始模式設定應用程式，請執行下列步驟：

    a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：

    | 區域  |  值 | 
    | --------------- | --------------- |
    | 歐洲          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | 美國   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | 南非    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 澳大利亞       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 海外        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > 您會在 Mimecast Admin Console 的 [帳戶] > [設定] > [帳戶代碼] 中找到 `accountcode` 值。 將 `accountcode` 附加至識別碼。

    b. 在 [回覆 URL] 文字方塊中，輸入 URL： 

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
    | 歐洲          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | 美國   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | 南非    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | 澳大利亞       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | 海外        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. 按一下 [檔案] 。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將資料儲存在您的電腦上。

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

在本節中，您會將 Mimecast Admin Console 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Mimecast Admin Console]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-mimecast-admin-console-sso"></a>設定 Mimecast Admin Console SSO

1. 在不同的網頁瀏覽器視窗中，登入 Mimecast Administration Console。

1. 瀏覽至 [系統管理] > [服務] > [應用程式]。

    ![此螢幕擷取畫面顯示已選取 [應用程式] 的 Mimecast 視窗。](./media/mimecast-admin-console-tutorial/services.png)

1. 按一下 [驗證設定檔] 索引標籤。
    
    ![此螢幕擷取畫面顯示已選取 [驗證設定檔] 的 [應用程式] 索引標籤。](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. 按一下 [新驗證設定檔] 索引標籤。

    ![顯示已選取 [新驗證設定檔] 的螢幕擷取畫面。](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. 在 [描述] 文字方塊中提供有效的描述，然後選取 [強制執行 Admin Console 的 SAML 驗證] 核取方塊。

    ![此螢幕擷取畫面顯示要為 Administration Console 選取強制執行 SAML 驗證的位置。](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. 在 [Admin Console 的 SAML 組態] 頁面上，執行下列步驟：

    ![此螢幕擷取畫面顯示 Administration Console 頁面的 SAML 設定，您可以在其中輸入所述的值。](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. 針對 [提供者]，從下拉式清單中選取 [Azure Active Directory]。

    b. 在 [中繼資料 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL] 值。

    c. 按一下 [匯入] 。 匯入中繼資料 URL 之後，就會自動填入欄位，而不需對這些欄位執行任何動作。

    d. 務必取消核取 [使用受密碼保護的內容] 和 [使用整合式驗證內容] 核取方塊。

    e. 按一下 [檔案] 。

### <a name="create-mimecast-admin-console-test-user"></a>建立 Mimecast Admin Console 測試使用者

1. 在不同的網頁瀏覽器視窗中，登入 Mimecast Administration Console。

1. 瀏覽至 [系統管理] > [目錄] > [內部目錄]。

    ![此螢幕擷取畫面顯示已選取內部目錄的 Mimecast 視窗。](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. 選取您的網域 (如果網域如下所述)，否則請按一下 [新增網域] 來建立新網域。

    ![顯示已選取網域的螢幕擷取畫面。](./media/mimecast-admin-console-tutorial/domain-name.png)

1. 按一下 [新增位址] 索引標籤。

    ![顯示已選取 [新增位址] 的螢幕擷取畫面。](./media/mimecast-admin-console-tutorial/new-address.png)

1. 在下列頁面上提供必要的使用者資訊：

    ![此螢幕擷取畫面顯示可輸入所述值的頁面。](./media/mimecast-admin-console-tutorial/user-information.png)

    a. 在 [電子郵件地址] 文字方塊中，輸入 `B.Simon@yourdomainname.com` 之類的使用者電子郵件地址。

    b. 在 [ **全域名稱** ] 文字方塊中，輸入使用者的 **完整名稱** 。

    c. 在 [密碼] 和 [確認密碼] 文字方塊中，輸入使用者的密碼。

    d. 選取 [在登入時強制變更] 核取方塊。

    e. 按一下 [檔案] 。

    f. 若要將角色指派給使用者，請按一下 [角色編輯]，然後依據您的組織需求，將所需的角色指派給使用者。

    ![此螢幕擷取畫面顯示您可以在其中選取 [角色編輯] 的位址設定。](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 Mimecast personal portal 管理主控台登入 URL，您可以在其中起始登入流程。  

* 直接移至 Mimecast personal portal 系統管理員主控台登入 URL，並從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 按一下 [在 Azure 入口網站 **測試此應用程式** ]，您應該會自動登入您已設定 SSO 的 Mimecast personal portal 管理主控台 

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您在我的應用程式中按一下 [Mimecast personal portal 管理主控台] 圖格時，如果在 SP 模式中設定，系統會將您重新導向至應用程式登入頁面以起始登入流程，如果是在 IDP 模式中設定，您應該會自動登入您已設定 SSO 的 Mimecast personal portal 管理主控台。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Mimecast personal portal 管理主控台之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。