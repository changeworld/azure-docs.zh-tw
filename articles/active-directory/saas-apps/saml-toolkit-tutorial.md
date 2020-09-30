---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Azure AD SAML Toolkit 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Azure AD SAML Toolkit 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7886691559a63e6d54ea748582f641f33cecf995
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90979742"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Azure AD SAML Toolkit 整合

在本教學課程中，您會了解如何整合 Azure AD SAML Toolkit 與 Azure Active Directory (Azure AD)。 當您整合 Azure AD SAML Toolkit 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Azure AD SAML Toolkit 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Azure AD SAML Toolkit。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Azure AD SAML Toolkit 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Azure AD SAML Toolkit 支援 **SP** 起始的 SSO

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>從資源庫新增 Azure AD SAML Toolkit

若要設定將 Azure AD SAML Toolkit 整合到 Azure AD 中，您需要從資源庫將 Azure AD SAML Toolkit 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Azure AD SAML Toolkit**。
1. 從結果面板選取 [Azure AD SAML Toolkit]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>設定並測試 Azure AD SAML Toolkit 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Azure AD SAML Toolkit 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Azure AD SAML Toolkit 中相關使用者之間的連結關聯性。

若要設定及測試與 Azure AD SAML Toolkit 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** 在應用程式端設定單一登入設定。
    * **[建立 Azure AD SAML Toolkit 測試使用者](#create-azure-ad-saml-toolkit-test-user)** 讓 Azure AD SAML Toolkit 中對應的 B.Simon 連結到使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Azure AD SAML Toolkit] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，輸入 URL：`https://samltoolkit.azurewebsites.net/`

    b. 在 [回覆 URL] 文字方塊中，輸入 URL：`https://samltoolkit.azurewebsites.net/SAML/Consume`

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (原始)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificateraw.png)

1. 在 [設定 Azure AD SAML Toolkit]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Azure AD SAML Toolkit 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Azure AD SAML Toolkit]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-azure-ad-saml-toolkit-sso"></a>設定 Azure AD SAML Toolkit SSO

1. 開啟新的網頁瀏覽器視窗，如果您尚未在 Azure AD SAML Toolkit 網站中註冊，請先按一下 [註冊]  進行註冊。 如果您已經註冊，請使用已註冊的登入認證來登入 Azure AD SAML Toolkit 公司網站。

    ![Azure AD SAML Toolkit 註冊](./media/saml-toolkit-tutorial/register.png)

1. 按一下 [SAML 組態]  。

    ![Azure AD SAML 工具組 SAML 組態](./media/saml-toolkit-tutorial/saml-configure.png)

1. 按一下頁面底部的 [新增]  。

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. 在 [SAML SSO 組態]  頁面上，執行下列步驟：

    ![Azure AD SAML Toolkit 建立 SSO 組態](./media/saml-toolkit-tutorial/fill-details.png)

    1. 在 [登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    1. 在 [Azure AD 識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    1. 在 [登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    1. 按一下 [選擇檔案]  及上傳您從 Azure 入口網站下載的**憑證 (原始)** 檔案。

    1. 按一下頁面底部的 [新增]  。

    1. 在 SAML 工具組的 SSO 設定頁面上複製 [登入 URL]、[識別碼] 和 [ACS URL] 值，並貼到 Azure 入口網站中 [基本 SAML 設定]  區段的個別文字方塊。

### <a name="create-azure-ad-saml-toolkit-test-user"></a>建立 Azure AD SAML Toolkit 測試使用者

本節會在 Azure AD SAML Toolkit 中建立名為 B.Simon 的使用者。 請註冊新的使用者並提供所有使用者詳細資料，以在工具中建立測試使用者。 

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 SAML Toolkit 登入 URL。 

2. 直接移至 SAML Toolkit 登入 URL，然後從該處起始登入流程。

3. 您可以使用 Microsoft 存取面板。 當您在存取面板中按一下 SAML Toolkit 圖格時，應該會自動登入您已設定 SSO 的 SAML Toolkit。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Azure AD SAML Toolkit 後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)
