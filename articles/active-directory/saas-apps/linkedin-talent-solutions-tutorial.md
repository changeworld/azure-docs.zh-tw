---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 LinkedIn Talent Solutions 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 LinkedIn Talent Solutions 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: ccad668476ecf3311d51a476138a2b2190ac7966
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 LinkedIn Talent Solutions 整合

在本教學課程中，您將了解如何整合 LinkedIn Talent Solutions 與 Azure Active Directory (Azure AD)。 在整合 LinkedIn Talent Solutions 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 LinkedIn Talent Solutions 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 LinkedIn Talent Solutions。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 存取 LinkedIn Talent Solutions 儀表板中的帳戶中心

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* LinkedIn Talent Solutions 支援由 **SP 和 IDP** 起始的 SSO
* LinkedIn Talent Solutions 支援 **Just In Time** 使用者佈建


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>從資源庫新增 LinkedIn Talent Solutions

如要設定將 LinkedIn Talent Solutions 整合到 Azure AD 中，您需要從資源庫把 LinkedIn Talent Solutions 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **LinkedIn Talent Solutions**。
1. 從結果面板中選取 [LinkedIn Talent Solutions]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>設定和測試 LinkedIn Talent Solutions 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 LinkedIn Talent Solutions 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 LinkedIn Talent Solutions 中相關使用者之間的連結關聯性。

若要設定及測試與 LinkedIn Talent Solutions 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 LinkedIn Talent Solutions SSO](#configure-linkedin-talent-solutions-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 LinkedIn Talent Solutions 測試使用者](#create-linkedin-talent-solutions-test-user)** - 使 LinkedIn Talent Solutions 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **LinkedIn Talent Solutions** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 如果您有 **服務提供者中繼資料檔案**，請在 [基本 SAML 設定]  區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  。

    ![image1](common/upload-metadata.png)

    b. 按一下 **資料夾圖示** 以選取中繼資料檔案，然後按一下 [上傳]  。

    ![image2](common/browse-upload-metadata.png)

    c. 成功上傳中繼資料檔案後，就會在 [基本 SAML 設定] 區段中自動填入 [識別碼]  和 [回覆 URL]  值：

    ![image3](common/idp-intiated.png)

    > [!Note]
    > 如果 [識別碼]  和 [回覆 URL]  值未自動填入，請根據您的需求手動填入這些值。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，輸入 URL：`https://www.linkedin.com/talent/`

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 LinkedIn Talent Solutions] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 LinkedIn Talent Solutions 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [LinkedIn Talent Solutions]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-linkedin-talent-solutions-sso"></a>設定 LinkedIn Talent Solutions SSO

1. 以管理員身分登入 LinkedIn Talent Solutions 網站。
1. 瀏覽至 **帳戶中心**。 
1. 從導覽列中選取 [設定]。

    ![設定頁面](./media/linkedin-talent-solutions-tutorial/settings.png)

1. 展開 **單一登入 (SSO)** 區段。 

1. 按一下 [下載] 按鈕下載 **中繼資料檔案**，或按一下 [按一下這裡載入和複製表單的個別欄位] 連結，以顯示組態資料。

    ![ 組態資料](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. 請執行下列步驟，複製表單上的個別欄位。

    ![ 具有輸入資料的組態](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. 複製 **實體識別碼** 值，並將此值貼到 Azure 入口網站的 [基本 SAML 組態] 區段上的 [Azure AD 識別碼] 文字方塊中。

    b. 複製 **ACS URL** 值，在 Azure 入口網站的 [基本 SAML 組態] 區段中的 [回覆 URL] 文字方塊內貼上該值。

    c. 將 **SP X.509 憑證 (簽署)** 文字方塊的內容複寫到記事本，並儲存在您的電腦中。

1. 按一下 [上傳 XML 檔案] 上傳從 Azure 入口網站複製的 **同盟中繼資料 XML** 檔案。

    ![ 上傳 XML 檔案](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>建立 LinkedIn Talent Solutions 測試使用者

本節會在 LinkedIn Talent Solutions 中建立名為 Britta Simon 的使用者。 LinkedIn Talent Solutions 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 LinkedIn Talent Solutions 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 LinkedIn Talent Solutions 登入 URL。  

* 直接移至 LinkedIn Talent Solutions 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 LinkedIn Talent Solutions 

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您按一下「我的應用程式」中的 [LinkedIn Talent Solutions] 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 LinkedIn Talent Solutions。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 LinkedIn Talent Solutions 之後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
