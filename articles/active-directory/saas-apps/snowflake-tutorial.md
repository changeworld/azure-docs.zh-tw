---
title: 教學課程：Azure Active Directory 與 Snowflake 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Snowflake 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: f516f51c2e059526b4e678f2779ef0ad059c74e7
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968546"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>教學課程：Azure Active Directory 與 Snowflake 整合

在本教學課程中，您將了解如何整合 Snowflake 與 Azure Active Directory (Azure AD)。 在整合 Snowflake 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Snowflake 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Snowflake。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Snowflake 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用單一登入的 Snowflake 訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

- Snowflake 支援 **SP 和 IDP** 起始的 SSO
- Snowflake 支援[自動化的使用者佈建和取消佈建](snowflake-provisioning-tutorial.md) (建議選項)

## <a name="adding-snowflake-from-the-gallery"></a>從資源庫新增 Snowflake

若要設定將 Snowflake 整合到 Azure AD 中，您需要從資源庫將 Snowflake 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Snowflake**。
1. 從結果面板選取 [Snowflake]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>設定和測試 Snowflake 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Snowflake 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Snowflake 中相關使用者之間的連結關聯性。

若要設定及測試與 Snowflake 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Snowflake SSO](#configure-snowflake-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Snowflake 測試使用者](#create-snowflake-test-user)** - 讓 Snowflake 中對應的 B.Simon 連結到使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **Snowflake** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. 如果您想要以 SP 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. 在 [登出 URL] 文字方塊中，以下列模式輸入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [Snowflake 用戶端支援小組](https://support.snowflake.net/s/)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Snowflake]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Snowflake 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Snowflake]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-snowflake-sso"></a>設定 Snowflake SSO

1. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Snowflake。

1. 按一下頁面右上方的 [設定檔]，**切換角色** 為 **ACCOUNTADMIN**。

    > [!NOTE]
    > 這與您在右上角您使用者名稱底下選取的內容不同。
    
    ![Snowflake admin](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. 在記事本中開啟 **所下載的 Base 64 憑證**。 複製介於 “-----BEGIN CERTIFICATE-----” 與 “-----END CERTIFICATE-----" 之間的值，並將此值貼到下面 [憑證]  旁邊的引號中。 在 [ssoUrl]  中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。 選取 [所有查詢]  ，然後按一下 [執行]  。

   ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>建立 Snowflake 測試使用者

若要讓 Azure AD 使用者能夠登入 Snowflake，必須將他們佈建到 Snowflake。 在 Snowflake 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以安全性系統管理員身分登入 Snowflake。

2. 按一下頁面右上方的 [設定檔]，**切換角色** 為 **ACCOUNTADMIN**。  

    ![Snowflake admin](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. 執行下列 SQL 查詢來建立使用者，確定「登入名稱」是設定為工作表上的 Azure AD 使用者名稱，如下所示。

    ![Snowflake adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Snowflake 登入 URL。  

* 直接移至 Snowflake 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入已設定 SSO 的 Snowflake 

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您按一下「我的應用程式」中的 [Snowflake] 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 Snowflake。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 Snowflake 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)