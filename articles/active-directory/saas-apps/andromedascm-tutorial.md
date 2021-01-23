---
title: 教學課程：Azure Active Directory 與 Andromeda 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Andromeda 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736033"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>教學課程：Azure Active Directory 與 Andromeda

在本教學課程中，您將了解如何整合 Andromeda 與 Azure Active Directory (Azure AD)。
Andromeda 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Andromeda 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Andromeda (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定與 Andromeda 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Andromeda 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Andromeda 支援由 **SP 和 IDP** 起始的 SSO
* Andromeda 支援 **Just In Time** 使用者佈建

## <a name="adding-andromeda-from-the-gallery"></a>從資源庫新增 Andromeda

若要設定 Andromeda 與 Azure AD 整合，您需要從資源庫將 Andromeda 新增至受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Andromeda**。
1. 從結果面板中選取 [Andromeda]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>設定和測試 Andromeda 的 Azure AD SSO

以名為 **B. Simon** 的測試使用者，設定及測試與 Andromeda 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Andromeda 中相關使用者之間的連結關聯性。

若要設定及測試與 Andromeda 搭配運作的 Azure AD SSO，請執行下列步驟：


1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Andromeda SSO](#configure-andromeda-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Andromeda 測試使用者](#create-andromeda-test-user)** - 讓 Andromeda 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **Andromeda** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<tenantURL>.ngcxpress.com/`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![顯示您可以在其中輸入登入 URL 的設定額外 URL 螢幕擷取畫面。](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > 這些都不是真正的值。 您將會使用實際的「識別碼」、「回覆 URL」與「登入 URL」來更新值，稍後會在本教學課程中說明。

6. Andromeda 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![顯示使用者屬性的螢幕擷取畫面，例如名字 user.givenname 和電子郵件地址 user.mail。](common/edit-attribute.png)

    > [!Important]
    > 在設定這些項目時清除 NameSpace 定義。

7. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，使用 [編輯] 圖示來編輯宣告或使用 [新增宣告] 來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟： 

    | 名稱 | 來源屬性|
    | ------ | -----------|
    | 角色 (role)        | 應用程式專屬角色 |
    | type        | 應用程式類型 |
    | company       | CompanyName |

    > [!NOTE]
    > Andromeda 需要將使用者的角色指派給應用程式。 請在 Azure AD 中設定這些角色，以便為使用者指派適當的角色。 若要了解如何在 Azure AD 中設定角色，請參閱[此文章](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)。

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![顯示使用者宣告選項以新增新宣告並且儲存的螢幕擷取畫面。](common/new-save-attribute.png)

    ![顯示管理使用者宣告的螢幕擷取畫面，您可以在其中輸入此步驟中所述的值。](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

8. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 Andromeda]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Andromeda 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Andromeda]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您已如上述所述設定角色，可以從 **選取角色** 下拉式清單中選取。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="configure-andromeda-sso"></a>設定 Andromeda SSO

1. 以系統管理員身分登入您的 Andromeda 公司網站。

2. 在功能表列頂端按一下 [系統管理員]  ，並且瀏覽至 [系統管理]  。

    ![Andromeda 系統管理](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. 在 [介面]  區段底下的工具列左側，按一下 [SAML 設定]  。

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. 在 [SAML 設定]  區段頁面上，執行下列步驟：

    ![Andromeda 設定](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. 勾選 [以 SAML 進行 SSO]  。

    b. 在 [Andromeda 資訊] 區段底下，複製 [SP 身分識別] 值並將其貼至 [基本 SAML 設定] 區段的 [識別碼] 文字方塊。

    c. 複製 [取用者 URL] 值並將其貼至 [基本 SAML 設定] 區段的 [回覆 URL] 文字方塊。

    d. 複製 [登入 URL] 值並將其貼至 [基本 SAML 設定] 區段的 [登入 URL] 文字方塊。

    e. 在 [SAML 識別提供者]  區段底下，輸入您的 IDP 名稱。

    f. 在 [單一登入端點]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    g. 從記事本中的 Azure 入口網站開啟已下載的 [Base64 編碼憑證]  ，將它貼至 [X 509 憑證]  文字方塊。
    
    h. 以個別值對應下列屬性，以便加速從 Azure AD 的 SSO 登入。 **User ID** 屬性是登入的必要項目。 佈建需要 **電子郵件**、**公司**、**使用者類型** 和 **角色**。 我們會在本節中定義屬性對應 (名稱和值)，此對應會與在 Azure 入口網站中定義的項目相互關聯。

    ![Andromeda 屬性對應](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. 按一下 [檔案]  。


### <a name="create-andromeda-test-user"></a>建立 Andromeda 測試使用者

本節會在 Andromeda 中建立名為 Britta Simon 的使用者。 Andromeda 支援預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Andromeda 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。 如果您需要手動建立使用者，請連絡 [Andromeda 用戶端支援小組](https://www.ngcsoftware.com/support/)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Andromeda 登入 URL。  

* 直接移至 Andromeda 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入已設定 SSO 的 Andromeda 

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您按一下「我的應用程式」中的 [Andromeda] 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 Andromeda。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 Andromedaa 後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。
