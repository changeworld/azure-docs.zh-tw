---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Cloud Academy - SSO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cloud Academy - SSO 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 88e626f9b3069b3b43d525914c017caf763a9047
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551756"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Cloud Academy - SSO 整合

在本教學課程中，您會了解如何整合 Cloud Academy - SSO 與 Azure Active Directory (Azure AD)。 在整合 Cloud Academy - SSO 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Cloud Academy - SSO 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Cloud Academy - SSO。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Cloud Academy - SSO 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Cloud Academy - SSO 支援由 **SP** 起始的 SSO

* 設定 Cloud Academy - SSO 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>從資源庫新增 Cloud Academy - SSO

若要設定將 Cloud Academy - SSO 整合到 Azure AD 中，您需要從資源庫將 Cloud Academy - SSO 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Cloud Academy - SSO**。
1. 從結果面板選取 [Cloud Academy - SSO]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>設定和測試 Cloud Academy - SSO 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Cloud Academy - SSO 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Cloud Academy - SSO 中相關使用者之間的連結關聯性。

若要設定及測試與 Cloud Academy - SSO 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Cloud Academy - SSO 單一登入](#configure-cloud-academy-sso-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Cloud Academy - SSO 測試使用者](#create-cloud-academy-sso-test-user)** - 使 Cloud Academy - SSO 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Cloud Academy - SSO] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    在 [登入 URL] 文字方塊中，輸入 URL：`https://cloudacademy.com/login/enterprise/`

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Cloud Academy - SSO 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Cloud Academy - SSO]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-cloud-academy-sso-sso"></a>設定 Cloud Academy-SSO 單一登入

1. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Cloud Academy - SSO 公司網站。

1. 按一下公司的名稱，然後從功能表中選取 [設定和整合]。

    ![組態 ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. 在 [設定和整合] 頁面上，移至 [整合] 索引標籤，然後按一下 [SSO] 卡。

    ![組態 ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. 在下列頁面上，執行下列步驟：

    ![組態 ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. 在 [實體識別碼 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [實體識別碼] 值。

    b. 在 [SSO URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    c. 從 Azure 入口網站將所下載的**憑證(Base64)** 以記事本開啟，然後將內容貼至 [憑證] 文字方塊。

    d. 在 [名稱識別碼格式] 文字方塊中，預設值為 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. 按一下 [儲存] 按鈕。

    > [!NOTE]
    > 如需如何設定 Cloud Academy - SSO 的詳細資訊，請參閱[支援文章](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On)。

### <a name="create-cloud-academy-sso-test-user"></a>建立 Cloud Academy - SSO 測試使用者

1. 登入 **Cloud Academy - SSO**。

1. 按一下公司的名稱，然後從功能表中選取 [成員]。

    ![ 建立測試使用者 ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. 按一下 [邀請成員]，然後選取 [邀請單一成員]。

    ![ 建立測試使用者 ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. 輸入必要的欄位，然後按一下 [邀請]。

    ![ 建立測試使用者 ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Cloud Academy - SSO] 圖格時，應該會自動登入您設定 SSO 的 Cloud Academy - SSO。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Cloud Academy - SSO](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Cloud Academy - SSO ](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)