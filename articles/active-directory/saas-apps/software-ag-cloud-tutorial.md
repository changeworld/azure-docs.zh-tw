---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Software AG Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Software AG Cloud 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 852fabfd1fe496064e6d13c21ba052ee6f96b79e
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Software AG Cloud 整合 | Microsoft Docs

在本教學課程中，您會了解如何整合 Software AG Cloud 與 Azure Active Directory (Azure AD)。 在整合 Software AG Cloud 與 Azure AD 後，您可以︰

* 在 Azure AD 中控制可存取 Software AG Cloud 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Software AG Cloud。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Software AG Cloud 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Software AG Cloud 支援 **SP** 起始的 SSO
* Software AG Cloud 支援 **Just In Time** 使用者佈建

## <a name="adding-software-ag-cloud-from-the-gallery"></a>從資源庫新增 Software AG Cloud

若要設定將 Software AG Cloud 整合到 Azure AD 中，您需要從資源庫將 Software AG Cloud 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Software AG Cloud**。
1. 從結果面板選取 [Software AG Cloud]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>設定和測試 Software AG Cloud 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Software AG Cloud 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Software AG Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 Software AG Cloud 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Software AG Cloud 單一登入](#configure-software-ag-cloud-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Software AG Cloud 測試使用者](#create-software-ag-cloud-test-user)** - 使 Software AG Cloud 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **Software AG Cloud** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Software AG Cloud 用戶端支援小組](mailto:support@softwareag.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Software AG Cloud] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Software AG Cloud 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Software AG Cloud]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-software-ag-cloud-sso"></a>設定 Software AG Cloud SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Software AG Cloud 網站。

1.  按一下 [管理]

    ![設定 Software AG Cloud1](./media/software-ag-cloud-tutorial/admin.png)

1. 前往 **單一登入 > 新增身分識別提供者**

    ![設定 Software AG Cloud2](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. 在下列頁面上，執行下列步驟。

    ![設定 Software AG Cloud3](./media/software-ag-cloud-tutorial/saml-1.png)

    a. 在 **身分識別提供者名稱** 文字方塊中名稱，例如 `azure ad`。

    b. 在 **Software AG Cloud 重新導向 URL中使用的識別提供者唯一識別碼** 文字方塊中，貼上您從 Azure 入口網站複製的 [實體識別碼] 值。

    c. 在 **識別提供者組態** 中匯入 **同盟中繼資料 XML** 檔案，然後按一下 [下一步]。

    d. 移至 **組態** 頁面，並視需要完成欄位。

### <a name="create-software-ag-cloud-test-user"></a>建立 Software AG Cloud 測試使用者

本節會在 Software AG Cloud 中建立名為 Britta Simon 的使用者。 Software AG Cloud 支援預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Software AG Cloud 中還沒有使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 Software AG Cloud 登入 URL。 

2. 直接移至 Software AG Cloud 登入 URL，然後從該處起始登入流程。

3. 您可以使用 Microsoft 存取面板。 當您按一下存取面板中的 Software AG Cloud 圖格時，將會重新導向至 Software AG Cloud登入 URL。 如需存取面板的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>後續步驟

設定 Software AG Cloud 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。


