---
title: 教學課程：Azure Active Directory 與 Citrix ShareFile 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Citrix ShareFile 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: d7cafdcbbf44f3f501d54c13d1b4549c446ed8c8
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723961"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>教學課程：Azure Active Directory 與 Citrix ShareFile 整合

在本教學課程中，您會了解如何整合 Citrix ShareFile 與 Azure Active Directory (Azure AD)。
Citrix ShareFile 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Citrix ShareFile 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Citrix ShareFile (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Citrix ShareFile 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Citrix ShareFile 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Citrix ShareFile 支援 **SP** 起始的 SSO

## <a name="adding-citrix-sharefile-from-the-gallery"></a>從資源庫新增 Citrix ShareFile

若要設定 Citrix ShareFile 與 Azure AD 整合，您需要從資源庫將 Citrix ShareFile 新增到受控 SaaS App 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Citrix ShareFile**。
1. 從結果面板選取 [Citrix ShareFile]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>設定和測試 Citrix ShareFile 的 Azure AD SSO

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Citrix ShareFile 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Citrix ShareFile 中相關使用者之間的連結關聯性。

若要設定及測試與 Citrix ShareFile 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Citrix ShareFile SSO](#configure-citrix-sharefile-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Citrix ShareFile 測試使用者](#create-citrix-sharefile-test-user)** - 讓 Citrix ShareFile 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Citrix ShareFile] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<tenant-name>.sharefile.com/saml/login`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Citrix ShareFile 用戶端支援小組](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Citrix ShareFile] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Citrix ShareFile 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Citrix ShareFile]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-citrix-sharefile-sso"></a>設定 Citrix ShareFile SSO

1. 若要自動執行 **Citrix ShareFile** 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Citrix ShareFile] 便會將您導向至 Citrix ShareFile 應用程式。 請從該處提供用以登入 Citrix ShareFile 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Citrix ShareFile，請在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Citrix ShareFile 公司網站。

1. 在 [儀表板] 中按一下 [設定]，然後選取 [管理員設定]

    ![系統管理](./media/sharefile-tutorial/settings.png)

1. 在 [管理員設定] 中，移至 [安全性] -> [登入和安全性原則]。
   
    ![帳戶管理](./media/sharefile-tutorial/settings-security.png "帳戶管理")

1. 在 **[基本設定]** 下的 **[單一登入 / SAML 2.0 設定]** 對話方塊頁面上，執行下列步驟：
   
    ![單一登入](./media/sharefile-tutorial/saml-configuration.png "單一登入")
   
    a. 在 [啟用 SAML] 中，選取 [是]。

    b. 複製 [ShareFile 簽發者/實體識別碼] 值，並將其貼至 Azure 入口網站中 [基本 SAML 設定] 對話方塊中的 [識別碼 URL] 方塊內。
    
    c. 在 [您的 IDP 簽發者/實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    d. 按一下 [X.509 憑證] 欄位旁的 [變更]，然後上傳您從 Azure 入口網站下載的憑證。
    
    e. 在 [登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。
    
    f. 在 [登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

5. 按一下 Citrix ShareFile 管理入口網站上的 **[儲存]**。

## <a name="create-citrix-sharefile-test-user"></a>建立 Citrix ShareFile 測試使用者

1. 登入您的 **Citrix ShareFile** 租用戶。

2. 按一下 [人員] -> [管理使用者首頁] -> [建立新使用者] -> [建立員工]。
   
    ![建立員工](./media/sharefile-tutorial/create-user.png "建立員工")

3. 在 [基本資訊] 區段中，執行下列步驟：
   
    ![基本資訊](./media/sharefile-tutorial/user-form.png "基本資訊")
   
    a. 在 [名字] 文字方塊中，輸入 **Britta** 作為使用者的 **名字**。
   
    b.  在 [姓氏] 文字方塊中，輸入 **Simon** 作為使用者的 **姓氏**。
   
    c. 在 [Email Address] \(電子郵件地址\) 文字方塊中，以 **brittasimon\@contoso.com** 形式輸入 Britta Simon 帳戶的電子郵件地址。

4. 按一下 [新增使用者]  。
  
    >[!NOTE]
    >Azure AD 帳戶持有者將收到一封電子郵件，依循連結來確認其帳戶，帳戶就會生效。您可以使用任何其他 Citrix ShareFile 使用者帳戶建立工具或 Citrix ShareFile 所提供的 API 來佈建 Azure AD 使用者帳戶。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Citrix ShareFile 登入 URL。

* 直接移至 Citrix ShareFile 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [Citrix ShareFile] 圖格時，將會重新導向至 Citrix ShareFile 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 Citrix ShareFile 之後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。