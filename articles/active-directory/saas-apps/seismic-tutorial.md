---
title: 教學課程：Azure Active Directory 與 Seismic 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Seismic 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: c42180990f81e179a8fa8cdfe9e5d762323213b0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661066"
---
# <a name="tutorial-azure-active-directory-integration-with-seismic"></a>教學課程：Azure Active Directory 與 Seismic 整合

在本教學課程中，您會了解如何整合 Seismic 與 Azure Active Directory (Azure AD)。
Seismic 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Seismic 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Seismic (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Seismic 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Seismic 單一登入的訂用帳戶

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Seismic 支援 **SP** 起始的 SSO
* 設定 Seismic 後，您可以強制執行工作階段控制，以即時防止組織的敏感性資料遭到外洩及滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-seismic-from-the-gallery"></a>從資源庫新增 Seismic

若要設定將 Seismic 整合到 Azure AD 中，您必須從資源庫將 Seismic 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Seismic**。
1. 從結果面板選取 [Seismic]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Seismic 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Seismic 中相關使用者之間的連結關聯性。

若要設定及測試與 Seismic 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Seismic SSO](#configure-seismic-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Seismic 測試使用者](#create-seismic-test-user)** - 使 Seismic 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 **Seismic** 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![Seismic 網域與 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.seismic.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.seismic.com`
    
    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<SUBDOMAIN>.seismic.com/SSO/<ROUTEURL>`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的登入 URL、識別碼及回覆 URL 來更新這些值。 請連絡 [Seismic 用戶端支援小組](mailto:support@seismic.com)以取得這些值。 您也可以上傳**服務提供者中繼資料**以自動填入識別碼值，如需**服務提供者中繼資料**的詳細資訊，請連絡 [Seismic 用戶端支援小組](mailto:support@seismic.com)。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Seismic] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Seismic 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Seismic]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-seismic-sso"></a>設定 Seismic SSO

若要設定 **Seismic** 端的單一登入，您必須將從 Azure 入口網站下載的 [憑證 (Base64)] 和複製的適當 URL 傳送給 [Seismic 支援小組](mailto:support@seismic.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-seismic-test-user"></a>建立 Seismic 測試使用者

在本節中，您要在 Seismic 中建立名為 Britta Simon 的使用者。 請與  [Seismic 支援小組](mailto:support@seismic.com)合作，以在 Seismic 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Seismic] 圖格時，應該會自動登入您已設定 SSO 的 Seismic。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
