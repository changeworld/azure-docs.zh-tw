---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Elium 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Elium 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7f0e9d0c97b9325a30de3cb8c6ce10a3ba8489f4
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454108"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Elium 整合

在本教學課程中，您會了解如何整合 Elium 與 Azure Active Directory (Azure AD)。 在整合 Elium 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Elium 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Elium。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Elium 單一登入 (SSO) 的訂用帳戶。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Elium 支援由 **SP 和 IDP** 起始的 SSO
* Elium 支援 **Just In Time** 使用者佈建

## <a name="adding-elium-from-the-gallery"></a>從資源庫新增 Elium

若要設定將 Elium 整合到 Azure AD 中，您需要從資源庫將 Elium 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Elium** 。
1. 從結果面板中選取 [Elium]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>設定和測試 Elium 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Elium 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Elium 中相關使用者之間的連結關聯性。

若要設定及測試與 Elium 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Elium SSO](#configure-elium-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Elium 測試使用者](#create-elium-test-user)** - 使 Elium 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Elium] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<platform-domain>.elium.com/login/saml2/acs`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > 這些都不是真正的值。 您可以從 **SP 中繼資料檔案** (可於 `https://<platform-domain>.elium.com/login/saml2/metadata` 下載) 取得這些值，該檔案會在本教學課程稍後加以說明。

1. Elium 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，Elium 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---------------| ----------------|
    | 電子郵件   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > 這些是預設宣告。 **只需要電子郵件宣告** 。 若為 JIT 佈建，也只需要電子郵件宣告。 其他自訂宣告會因客戶平台而有所不同。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Elium] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Elium 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Elium]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-elium-sso"></a>設定 Elium SSO

1. 若要自動執行 Elium 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 Elium]  便會將您導向到 Elium 應用程式。 請從該處提供用以登入 Elium 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Elium，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Elium 公司網站，然後執行下列步驟：

1. 按一下右上角的 [使用者設定檔]，然後選取 [設定]。

    ![設定單一登入 Elium 01](./media/elium-tutorial/elium-01.png)

1. 選取 [進階] 底下的 [安全性]。

    ![設定單一登入 Elium 02](./media/elium-tutorial/elium-02.png)

1. 向下捲動至 [單一登入 (SSO)]  區段，然後執行下列步驟：

    ![設定單一登入 Elium 03](./media/elium-tutorial/elium-03.png)

    a. 複製 [確認 SAML2 驗證適用於您的帳戶]  的值，並將該值貼到 Azure 入口網站中的 [基本 SAML 組態]  區段上的 [登入 URL]  文字方塊中。

    > [!NOTE]
    > 設定 SSO 之後，您永遠可以存取下列 URL 的預設遠端登入頁面：`https://<platform_domain>/login/regular/login` 

    b. 選取 [啟用 SAML2 同盟]  核取方塊。

    c. 選取 [JIT 佈建]  核取方塊。

    d. 按一下 [下載]  按鈕以開啟 [SP 中繼資料]  。

    e. 在 **SP 中繼資料** 檔案中搜尋 **entityID** ，複製 **entityID** 值，並將其貼到 Azure 入口網站中的 [基本 SAML 組態]  區段上的 [識別碼]  文字方塊中。 

    ![設定單一登入 Elium 04](./media/elium-tutorial/elium-04.png)

    f. 在 **SP 中繼資料** 檔案中搜尋 **AssertionConsumerService** ，複製 [位置]  值，並將其貼到 Azure 入口網站中的 [基本 SAML 組態]  區段上的 [回覆 URL]  文字方塊中。

    ![設定單一登入 Elium 05](./media/elium-tutorial/elium-05.png)

    g. 在記事本中開啟從 Azure 入口網站下載的中繼資料檔案，複製內容並將其貼到 [IdP 中繼資料]  文字方塊中。

    h. 按一下 [檔案]  。

### <a name="create-elium-test-user"></a>建立 Elium 測試使用者

本節會在 Elium 中建立名為 B.Simon 的使用者。 Elium 支援依預設啟用的 **Just-In-Time 佈建** 。 在這一節沒有您需要進行的動作項目。 如果 Elium 中還沒有使用者存在，在您嘗試存取 Elium 時就會建立新的使用者。

> [!Note]
> 如果您需要手動建立使用者，請連絡 [Elium 支援小組](mailto:support@elium.com)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 
 
#### <a name="sp-initiated"></a>SP 起始：
 
* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 Elium 登入 URL。  
 
* 直接移至 Elium 登入 URL，然後從該處起始登入流程。
 
#### <a name="idp-initiated"></a>IDP 起始：
 
* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Elium 
 
您也可以使用 Microsoft 存取面板，以任何模式測試應用程式。 當您按一下存取面板中的 Elium 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果在 IDP 模式中設定，您應該會自動登入已設定 SSO 的 Elium。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 Elium 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。