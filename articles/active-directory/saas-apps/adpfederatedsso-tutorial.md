---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 ADP 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ADP 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 535e8671905bd0f829471bdd9c85f10a3cbd5b32
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048465"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 ADP 整合

在本教學課程中，您會了解如何將 ADP 與 Azure Active Directory (Azure AD) 整合。 在整合 ADP 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 ADP 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 ADP。
* 在 Azure 入口網站集中管理您的帳戶。


## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 ADP 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* ADP 支援由 **IDP** 起始的 SSO

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-adp-from-the-gallery"></a>從資源庫新增 ADP

若要設定將 ADP 整合到 Azure AD 中，您需要從資源庫將 ADP 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **ADP**。
1. 從結果面板選取 [ADP]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>設定和測試 ADP 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 ADP 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 ADP 中相關使用者之間的連結關聯性。

若要設定及測試與 ADP 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 ADP SSO](#configure-adp-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 ADP 測試使用者](#create-adp-test-user)** - 使 ADP 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **ADP** 應用程式整合頁面上，按一下 [屬性] 索引標籤，然後執行下列步驟： 

    ![單一登入屬性](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. 將 [為使用者啟用登入] 欄位值設定為 [是]。

    b. 複製 [使用者存取 URL]，而且您必須將其貼到 [設定登入 URL] 區段中，本教學課程稍後會說明。

    c. 將 [需要使用者指派] 欄位值設定為 [是]。

    d. 將 [是否要向使用者顯示] 欄位值設定為 [否]。

1. 在 Azure 入口網站的 [ADP] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    在 [識別碼 (實體識別碼)] 文字方塊中，輸入 URL：`https://fed.adp.com`

4. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 ADP] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ADP 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [ADP]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-adp-sso"></a>設定 ADP SSO

若要在 **ADP** 端設定單一登入，您必須將已下載的 **中繼資料 XML** 上傳至 [ADP 網站](https://adpfedsso.adp.com/public/login/index.fcc)。

> [!NOTE]  
> 此流程可能需要幾天的時間。

### <a name="configure-your-adp-services-for-federated-access"></a>設定 ADP 服務以進行同盟存取

>[!Important]
> 您的員工如果需要對 ADP 服務進行同盟存取，您就必須將其指派給 ADP 服務應用程式，接著再將其重新指派給特定的 ADP 服務。
從您的 ADP 代表收到確認之後，請設定您的 ADP 服務並指派/管理使用者，以控制使用者對特定 ADP 服務的存取權。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **ADP**。
1. 從結果面板選取 [ADP]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。
1. 在 Azure 入口網站的 **ADP** 應用程式整合頁面上，按一下 [屬性] 索引標籤，然後執行下列步驟：  

    ![與單一登入連結的屬性](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. 將 [為使用者啟用登入] 欄位值設定為 [是]。

    1. 將 [需要使用者指派] 欄位值設定為 [是]。

    1. 將 [是否要向使用者顯示] 欄位值設定為 [是]。

1. 在 Azure 入口網站的 [ADP] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。

1. 在 [選取單一登入方法] 對話方塊上，選取 [連結的] 作為 [模式]， 以將您的應用程式連結至 **ADP**。

    ![已連結的單一登入](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. 瀏覽至 [設定登入 URL] 區段，執行下列步驟︰

    ![單一登入屬性](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. 貼上您從上面 [屬性] 索引標籤 (來自主要 ADP 應用程式) 複製的 [使用者存取 URL]。

    1. 以下是 5 個支援不同 **轉送狀態 URL** 的應用程式。 您必須手動將特定應用程式的適當 **轉送狀態 URL** 值附加至 [使用者存取 URL]。

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. [儲存] 變更。

1. 從您的 ADP 代表收到確認之後，使用一或兩個使用者開始進行測試。

    1. 將幾個使用者指派給 ADP 服務應用程式，以測試同盟存取。

    1. 當使用者存取資源庫上的 ADP 服務應用程式，且能夠存取其 ADP 服務時，即表示測試成功。

1. 確認測試成功時，將同盟 ADP 服務指派給個別的使用者或使用者群組 (本教學課程稍後會說明)，然後向您的員工首度發行。

### <a name="create-adp-test-user"></a>建立 ADP 測試使用者

本節的目標是要在 ADP 中建立一個名為 B.Simon 的使用者。 請與 [ADP 支援小組](https://www.adp.com/contact-us/overview.aspx)合作，在 ADP 帳戶中新增使用者。 

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 ADP

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 ADP 圖格時，應該會自動登入您已設定 SSO 的 ADP。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 ADP 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。