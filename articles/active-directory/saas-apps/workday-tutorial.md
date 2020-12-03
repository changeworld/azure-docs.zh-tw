---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Workday 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workday 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Workday 整合

在本教學課程中，您將了解如何整合 Workday 與 Azure Active Directory (Azure AD)。 在整合 Workday 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Workday 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Workday。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Workday 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Workday 支援 **SP** 起始的 SSO。

* Workday 行動應用程式現在可以搭配 Azure AD 設定來啟用 SSO。 如需如何設定的詳細資訊，請遵循[此](workday-mobile-tutorial.md)連結。

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-workday-from-the-gallery"></a>從資源庫新增 Workday

若要設定將 Workday 整合到 Azure AD 中，您需要從資源庫將 Workday 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Workday**。
1. 從結果面板選取 [Workday]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>設定及測試 Workday 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Workday 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Workday 中相關使用者之間的連結關聯性。

若要設定及測試與 Workday 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Workday](#configure-workday)** 以在應用程式端設定 SSO 設定。
    1. **[建立 Workday 測試使用者](#create-workday-test-user)**，使 Workday 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Workday] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`

    c. 在 [登出 URL] 文字方塊中，以下列模式輸入 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的 [登入 URL]、[回覆 URL] 和 [登出 URL] 來更新這些值。 您的回覆 URL 必須有子網域 (例如：www、wd2、wd3、wd3-impl、wd5、wd5-impl)。
    > 使用 `http://www.myworkday.com` 之類的形式可以運作，但 `http://myworkday.com` 則不行。 請連絡 [Workday 客戶支援小組](https://www.workday.com/en-us/partners-services/services/support.html)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. Workday 應用程式會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 Workday 應用程式會預期 **nameidentifier** 與 **user.mail**、**UPN** 等對應，因此您必須按一下 [編輯] 圖示並變更屬性對應，來編輯屬性對應。

    ![顯示使用者屬性的螢幕擷取畫面，其中已選取 [編輯] 圖示。](common/edit-attribute.png)

    > [!NOTE]
    > 依預設，這裡已使名稱識別碼和 UPN (user.userprincipalname) 相對應。 您必須將名稱識別碼對應至 Workday 帳戶中的實際使用者識別碼 (您的電子郵件或 UPN 等)，才能讓 SSO 順利運作。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificatebase64.png)

1. 若要根據您的需求修改 [簽署] 選項，請按一下 [編輯] 按鈕以開啟 [SAML 簽署憑證] 對話方塊。

    ![憑證](common/edit-certificate.png) 

    ![SAML 簽署憑證](./media/workday-tutorial/signing-option.png)

    a. 針對 [簽署選項] 選取 [簽署 SAML 回應及判斷提示]。

    b. 按一下 [儲存] 

1. 在 [設定 Workday] 區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Workday 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Workday]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-workday"></a>設定 Workday

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Workday 公司網站。

1. 在 **搜尋方塊** 中，使用首頁左上方的名稱 [Edit Tenant Setup – Security] \(編輯租用戶設定 – 安全性\) 來進行搜尋。

    ![編輯租用戶安全性](./media/workday-tutorial/IC782925.png "編輯租用戶安全性")


1. 在 [SAML 設定] 區段中，執行下列步驟：

    ![SAML 設定](./media/workday-tutorial/IC782926.png "SAML 設定")

    a.  選取 [啟用 SAML 驗證]。

    b.  按一下 [加入資料列]  。

1. 在 [SAML 識別提供者] 區段中，請對新建立的資料列執行下列動作。

    a. 針對欄位執行下列動作，如下所示。

    ![SAML 識別提供者 1](./media/workday-tutorial/IC7829271.png "SAML 身分識別提供者")

    * 在 [識別提供者名稱] 文字方塊中，輸入提供者名稱 (例如：*SPInitiatedSSO*)。

    * 在 Azure 入口網站的 [安裝 Workday] 區段上，複製 [Azure AD 識別碼] 值，然後將它貼到 [Issuer] \(簽發者\) 文字方塊中。

    * 以記事本開啟從 Azure 入口網站下載的 [憑證]，然後將內容貼到 [x.509 憑證] 文字方塊中。

    b. 針對欄位執行下列動作，如下所示。

    ![SAML 識別提供者 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML 身分識別提供者")

    * 按一下 [啟用 IDP 起始的登出] 核取方塊。

    * 在 [登出回應 URL] 文字方塊中，輸入 **http://www.workday.com** 。

    * 在 [登出要求 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

    * 按一下 [SP 起始] 核取方塊。

    * 在 [服務提供者識別碼] 文字方塊中，輸入 **http://www.workday.com**。


    * 選取 [不要對 SP 初始化驗證要求使用 Deflate 編碼]。

    c. 針對欄位執行下列動作，如下所示。

    ![SAML 識別提供者 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML 身分識別提供者")

    * 在 Azure 入口網站的 [安裝 Workday] 區段上，複製 [登入 URL] 值，然後將它貼到 [IdP SSO Service URL] \(IdP SSO 服務 URL\) 文字方塊中。

    * 在 [用於環境] 文字方塊中，從下拉式清單中選取適當的環境名稱。

1. 執行下圖中的下列步驟。

    ![Workday](./media/workday-tutorial/service-provider.png "SAML 身分識別提供者")

    a. 在 [服務提供者識別碼 (即將淘汰)] 文字方塊中，輸入 **http://www.workday.com** 。

    b. 在 [IDP SSO 服務 URL (即將淘汰)] 文字方塊中，輸入 [登入 URL] 值。

    c. 選取 [不要壓縮 SP 起始的驗證要求 (即將淘汰)]。

    d. 在 [驗證要求簽章方法] 中，選取 [SHA256]。

    e。 按一下 [確定]。

    > [!NOTE]
    > 請確定您已正確設定單一登入。 如果您以不正確的設定啟用單一登入，您可能無法使用認證進入應用程式，並且會遭到鎖定。在此情況下，Workday 提供備份的登入 URL，使用者可以透過此格式：[Your Workday URL]/login.flex?redirect=n，使用他們的一般使用者名稱和密碼來登入

### <a name="create-workday-test-user"></a>建立 Workday 測試使用者

1. 以系統管理員身分登入您的 Workday 公司網站。

1. 按一下右上角的 [設定檔]，選取 [首頁]，然後在 [應用程式] 索引標籤中按一下 [目錄]。 

1. 在 [目錄] 頁面中，選取 [檢視] 索引標籤中的 [尋找背景工作角色]。

    ![尋找背景工作角色](./media/workday-tutorial/user-directory.png)

1.  在 [尋找背景工作角色] 頁面上，從結果中選取使用者。

1. 在下列頁面中，選取 [作業] > [背景工作角色安全性]，而且 [Workday 帳戶] 必須和 [名稱識別碼] 值一樣符合 Azure Active Directory。

    ![背景工作角色安全性](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> 如需如何建立 Workday 測試使用者的詳細資訊，請連絡 [Workday 用戶端支援小組](https://www.workday.com/en-us/partners-services/services/support.html)。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Workday 登入 URL。 

2. 直接移至 Workday 登入 URL，然後從該處起始登入流程。

3. 您可以使用 Microsoft 存取面板。 當您在存取面板中按一下 [Workday] 圖格時，應該會自動登入您已設定 SSO 的 Workday。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 Workday 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)