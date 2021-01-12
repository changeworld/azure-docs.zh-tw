---
title: 教學課程：Azure Active Directory 與 Greenhouse 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Greenhouse 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 0eb64ebe5e55bc054b6a280ac249cf451bb027db
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897352"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>教學課程：Azure Active Directory 與 Greenhouse 整合

在本教學課程中，您將了解如何整合 Greenhouse 與 Azure Active Directory (Azure AD)。 在整合 Greenhouse 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Greenhouse 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Greenhouse。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Greenhouse 單一登入 (SSO)的訂用帳戶。

> [!NOTE] 
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。 

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Greenhouse 支援 **SP** 起始的 SSO

## <a name="adding-greenhouse-from-the-gallery"></a>從資源庫新增 Greenhouse

若要設定 Greenhouse 與 Azure AD 整合，您需要從資源庫將 Greenhouse 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Greenhouse**。
1. 從結果面板選取 [Greenhouse]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>設定和測試適用於 Greenhouse 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Greenhouse 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Greenhouse 中相關使用者之間的連結關聯性。

若要設定及測試與 Greenhouse 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Greenhouse SSO](#configure-greenhouse-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Greenhouse 測試使用者](#create-greenhouse-test-user)** - 使 Greenhouse 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **Greenhouse** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.greenhouse.io`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.greenhouse.io`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Greenhouse 用戶端支援小組](https://www.greenhouse.io/contact)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Greenhouse] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Greenhouse 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Greenhouse]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-greenhouse-sso"></a>設定 Greenhouse SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Greenhouse 網站。

1. 移至 **設定 > 開發人員中心 > 單一登入**。

    ![SSO 頁面的螢幕擷取畫面](./media/greenhouse-tutorial/configure.png)

1. 在單一登入頁面上，執行下列步驟。

    ![SSO 組態頁面的螢幕擷取畫面](./media/greenhouse-tutorial/sso-page.png)

    a. 複製 **SSO 判斷提示取用者 URL** 值，在 Azure 入口網站的 [基本 SAML 組態] 區段中的 [回覆 URL] 文字方塊內貼上該值。

    b. 在 [實體識別碼/簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    c. 在 [單一登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    d. 開啟從 Azure 入口網站下載到記事本的 **同盟中繼資料 XML** 並將內容複製到 **IdP 憑證指紋** 文字方塊中。

    e. 從下拉式清單中選取選取 [名稱識別碼格式] 值。

    f. 按一下 [開始測試]。

    >[!NOTE]
    >您也可以按一下 [選擇檔案]  選項來上傳 **同盟中繼資料 XML**。

### <a name="create-greenhouse-test-user"></a>建立 Greenhouse 測試使用者

若要讓 Azure AD 使用者可以登入 Greenhouse，則必須將他們佈建至 Greenhouse。 在 Greenhouse 的情況下，需以手動的方式佈建。

>[!NOTE]
>您可以使用任何其他的 Greenhouse 使用者帳戶建立工具或 Greenhouse 提供的 API，來佈建 Azure AD 使用者帳戶。 

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Greenhouse** 公司網站。

2. 移至 **設定 > 使用者 > 新增使用者**
   
    ![使用者](./media/greenhouse-tutorial/create-user-1.png "使用者")

4. 在 [新增使用者] 區段中，執行下列步驟：
   
    ![新增使用者](./media/greenhouse-tutorial/create-user-2.png "新增使用者")

    a. 在 [輸入使用者電子郵件] 文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的電子郵件地址。

    b. 按一下 [檔案]  。    
   
      >[!NOTE]
      >Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 Greenhouse 登入 URL。 

* 直接移至 Greenhouse 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [Greenhouse] 圖格時，將會重新導向至 Greenhouse 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 Greenhouse 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。