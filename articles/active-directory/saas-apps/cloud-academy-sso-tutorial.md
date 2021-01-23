---
title: 教學課程：Azure Active Directory SSO 與 Cloud Academy - SSO 的整合
description: 在本教學課程中，您會了解如何設定 Azure Active Directory 與 Cloud Academy - SSO 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98729823"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>教學課程：Azure Active Directory 單一登入與 Cloud Academy - SSO 的整合

在本教學課程中，您會了解如何整合 Cloud Academy - SSO 與 Azure Active Directory (Azure AD)。 在整合 Cloud Academy - SSO 與 Azure AD 時，您可以︰

* 使用 Azure AD 來控制可以存取 Cloud Academy - SSO 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Cloud Academy - SSO。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Cloud Academy - SSO 訂用帳戶。

## <a name="tutorial-description"></a>教學課程說明

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Cloud Academy - SSO 支援由 **SP** 起始的 SSO
* Cloud Academy - SSO 支援 **Just In Time** 使用者佈建

## <a name="add-cloud-academy---sso-from-the-gallery"></a>從資源庫新增 Cloud Academy - SSO

若要設定將 Cloud Academy - SSO 整合到 Azure AD 中，您需要從資源庫將 Cloud Academy - SSO 新增到受控 SaaS 應用程式清單：

1. 使用公司或學校帳戶或使用個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Cloud Academy - SSO**。
1. 在結果面板中選取 [Cloud Academy - SSO]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>設定和測試 Cloud Academy - SSO 的 Azure AD SSO

您將使用名為 **B.Simon** 的測試使用者，設定及測試與 Cloud Academy - SSO 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Cloud Academy - SSO 中對應使用者之間的連結關聯性。

若要設定及測試與 Cloud Academy - SSO 搭配運作的 Azure AD SSO，您將完成下列高階步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以測試 Azure AD 單一登入。
    1. **[將存取權授予測試使用者](#grant-access-to-the-test-user)** ，讓該使用者能夠使用 Azure AD 單一登入。
1. 在應用程式端 **[設定 Cloud Academy - SSO 的單一登入](#configure-single-sign-on-for-cloud-academy)** 。
    1. **[建立 Cloud Academy - SSO 測試使用者](#create-a-cloud-academy-test-user)** 作為 Azure AD 中出現的使用者對應項。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 Azure 入口網站的 **Cloud Academy - SSO** 應用程式整合頁面上，於 **管理** 區段中選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 組態] 的鉛筆按鈕，以編輯設定：

   ![顯示用於編輯基本 SAML 設定之鉛筆按鈕的螢幕擷取畫面。](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段中，執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，輸入下列其中一個 URL：
    
    | 登入 URL |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. 在 [回覆 URL]  文字方塊中，輸入下列其中一個 URL：
    
    | 回覆 URL |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取複製按鈕以複製 [應用程式同盟中繼資料 URL]。 儲存 URL。

    ![顯示應用程式同盟中繼資料 URL 之複製按鈕的螢幕擷取畫面。](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]。 選取 [使用者 ]，然後選取 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者] 屬性中，完成下列步驟：
   1. 在 [名稱] 方塊中，輸入 **B.Simon**。  
   1. 在 [使用者名稱] 方塊中，輸入 \<username>@\<companydomain>.\<extension>。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

### <a name="grant-access-to-the-test-user"></a>將存取權授予測試使用者

在本節中，您會將 Cloud Academy - SSO 的存取權授與 B. Simon，讓該使用者能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Cloud Academy - SSO]。
1. 在應用程式的概觀頁面上，請在 [管理] 區段中，選取 [使用者和群組]：
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]：
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-single-sign-on-for-cloud-academy"></a>設定 Cloud Academy 的單一登入

1. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Cloud Academy - SSO 公司網站。

1. 選取您公司的名稱，然後在出現的功能表中選取 [設定和整合]：

    ![顯示 [設定和整合] 選項的螢幕擷取畫面。](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. 在 [設定和整合] 頁面的 [整合] 索引標籤上，選取 [SSO] 卡：

    ![在 [整合] 索引標籤上顯示 SSO 卡的螢幕擷取畫面。](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. 完成此頁面中的下列步驟：

    ![顯示 [整合] > [SSO] 頁面的螢幕擷取畫面。](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. 在 [實體識別碼 URL] 方塊中，輸入您從 Azure 入口網站複製的實體識別碼值。

    b. 在 [SSO URL] 方塊中，貼上您從 Azure 入口網站複製的登入 URL 值。

    c. 在記事本中開啟從 Azure 入口網站下載的 Base64 憑證。 將其內容貼入 [憑證] 方塊中。

    d. 在 [名稱識別碼格式] 方塊中，保留預設值：`urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`。

1. 選取 [儲存]。

    > [!NOTE]
    > 如需如何設定 Cloud Academy - SSO 的詳細資訊，請參閱[設定單一登入](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On)。

### <a name="create-a-cloud-academy-test-user"></a>建立 Cloud Academy 測試使用者

本節會在 Cloud Academy - SSO 中建立名為 Britta Simon 的使用者。 Cloud Academy - SSO 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Cloud Academy - SSO 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Cloud Academy - SSO 登入 URL。 

* 直接移至 Cloud Academy - SSO 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [Cloud Academy - SSO] 圖格時，將會重新導向至 Cloud Academy - SSO 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 Cloud Academy - SSO 之後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。