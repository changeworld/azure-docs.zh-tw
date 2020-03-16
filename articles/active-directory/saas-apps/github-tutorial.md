---
title: 教學課程：Azure Active Directory 與 GitHub 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 GitHub 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4084532ac370880df69d825927240c297dd371
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129898"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 GitHub 整合

在本教學課程中，您將了解如何整合 GitHub 與 Azure Active Directory (Azure AD)。 在整合 GitHub 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 GitHub 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 GitHub。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 GitHub 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 在 [GitHub Enterprise 雲端](https://help.github.com/articles/github-s-products/#github-enterprise)中建立的 GitHub 組織，需使用 [GitHub Enterprise 的計費方案](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* GitHub 支援 **SP** 起始的 SSO

* GitHub 支援[**自動使用者佈建**](github-provisioning-tutorial.md)
* 設定 GitHub 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>從資源庫新增 GitHub

若要設定將 GitHub 整合到 Azure AD 中，您需要從資源庫將 GitHub 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **GitHub**。
1. 從結果面板中選取 [GitHub]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>設定及測試 GitHub 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 GitHub 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 GitHub 中相關使用者之間的連結關聯性。

若要設定及測試與 GitHub 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 GitHub SSO](#configure-github-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 GitHub 測試使用者](#create-github-test-user)** - 使 GitHub 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [GitHub]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

   a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://github.com/orgs/<entity-id>/sso`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > 請注意這些不是真正的值。 您必須使用實際的登入 URL 及識別碼來更新這些值。 在此建議您在 [識別碼] 中使用唯一的字串值。 移至 [GitHub 管理] 區段來擷取這些值。

5. GitHub 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML Token 屬性組態。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 GitHub 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯]  圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 在 [設定 GitHub]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

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

在本節中，您會將 GitHub 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [GitHub]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-github-sso"></a>設定 GitHub SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 GitHub 組織網站。

2. 瀏覽至 [設定]  ，然後按一下 [安全性] 

    ![設定](./media/github-tutorial/tutorial_github_config_github_03.png)

3. 勾選 [啟用 SAML 驗證]  方塊，以顯示單一登入設定欄位。 然後，使用單一登入 URL 值來更新 Azure AD 組態上的單一登入 URL。

    ![設定](./media/github-tutorial/tutorial_github_config_github_13.png)

4. 設定下列欄位：

    ![設定](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. 在 [登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    b. 在 [簽發者]  文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。

    c. 在記事本中開啟從 Azure 入口網站下載的憑證，將內容貼至 [公開憑證]  文字方塊。

    d. 按一下 [編輯]  圖示以編輯 [簽章方法]  和 [摘要方法]  ，將 **RSA-SHA1** 和 **SHA1** 改為 **RSA-SHA256** 和 **SHA256**，如下所示。
    
    e. 更新預設 URL 中的**判斷提示取用者服務 URL (回覆 URL)** ，讓 Github 中的 URL 符合 Azure 應用程式註冊中的 URL。

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. 按一下 [測試 SAML 組態]  ，確認 SSO 期間沒有驗證失敗或錯誤。

    ![設定](./media/github-tutorial/tutorial_github_config_github_06.png)

6. 按一下 [儲存] 

> [!NOTE]
> GitHub 中的單一登入會向 GitHub 中的特定組織驗證，而不會取代 GitHub 本身的驗證。 因此，如果使用者的 github.com 工作階段已過期，系統可能會在單一登入程序進行期間，要求您使用 GitHub 的識別碼/密碼進行驗證。

### <a name="create-github-test-user"></a>建立 GitHub 測試使用者

本節的目標是在 GitHub 中建立名為 Britta Simon 的使用者。 GitHub 支援自動使用者佈建，該功能預設為啟用。 您可以在[這裡](github-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

**如果您需要手動建立使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 GitHub 公司網站。

2. 按一下 [人員]  。

    ![人員](./media/github-tutorial/tutorial_github_config_github_08.png "人員")

3. 按一下 [邀請成員]  。

    ![邀請使用者](./media/github-tutorial/tutorial_github_config_github_09.png "邀請使用者")

4. 在 [邀請成員]  對話方塊頁面上，執行下列步驟：

    a. 在 [電子郵件]  文字方塊中，輸入 Britta Simon 帳戶的電子郵件地址。

    ![邀請人員](./media/github-tutorial/tutorial_github_config_github_10.png "邀請人員")

    b. 按一下 [傳送邀請]  。

    ![邀請人員](./media/github-tutorial/tutorial_github_config_github_11.png "邀請人員")

    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [GitHub] 圖格時，應該會自動登入您設定 SSO 的 GitHub。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 GitHub](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
