---
title: 教學課程：Azure Active Directory 與 Civic Platform 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Civic Platform 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 47ffaef940b3fe2ffe033405a712195c5c74d774
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520357"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>教學課程：整合 Civic Platform 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Civic Platform 與 Azure Active Directory (Azure AD)。 在整合 Civic Platform 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Civic Platform 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Civic Platform。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Civic Platform 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Civic Platform 支援 **SP** 起始的 SSO





## <a name="adding-civic-platform-from-the-gallery"></a>從資源庫新增 Civic Platform

若要設定將 Civic Platform 整合到 Azure AD 中，您需要從資源庫將 Civic Platform 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Civic Platform**。
1. 從結果面板選取 [Civic Platform]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Civic Platform 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Civic Platform 中相關使用者之間的連結關聯性。

若要設定及測試與 Civic Platform 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Civic Platform SSO](#configure-civic-platform-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Civic Platform 測試使用者](#create-civic-platform-test-user)** - 使 Civic Platform 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Civic Platform]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.accela.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入 URL：`civicplatform.accela.com`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 使用實際的登入 URL 來更新此值。 請連絡 [Civic Platform 用戶端支援小組](mailto:skale@accela.com)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

1. 在 Azure AD 中瀏覽至 [Azure Active Directory]   > [應用程式註冊]  ，選取您的應用程式。

1. 複製 [目錄 (租用戶) 識別碼]  並將它儲存到 [記事本] 中。

    ![複製目錄 (租用戶) 識別碼並將它儲存在您的應用程式程式碼中。](media/civic-platform-tutorial/directoryid.png)

1. 複製 [應用程式識別碼]  並將它儲存到 [記事本] 中。

   ![複製應用程式 (用戶端) 識別碼](media/civic-platform-tutorial/applicationid.png)

1. 在 Azure AD 中瀏覽至 [Azure Active Directory]   > [應用程式註冊]  ，選取您的應用程式。 選取 [憑證和秘密]  。

1. 選取 [用戶端秘密] -> [新增用戶端密碼]  。

1. 提供秘密的描述及持續時間。 完成時，選取 [新增]  。

   > [!NOTE]
   > 儲存用戶端秘密之後，就會顯示用戶端秘密的值。 請複製此值，因為您之後就無法擷取金鑰。

   ![複製秘密值，因為您之後無法取得此值](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>設定 Civic Platform SSO

1. 開啟新的 Web 瀏覽器視窗，以系統管理員身分登入您的 Atlassian Cloud 公司網站。

1. 按一下 [標準選擇]  。

    ![憑證下載連結](media/civic-platform-tutorial/standard-choices.png)

1. 建立標準選擇 **ssoconfig**。

1. 搜尋 **ssoconfig** 並提交。

    ![憑證下載連結](media/civic-platform-tutorial/sso-config.png)

1. 按一下紅點以展開 SSOCONFIG。

    ![憑證下載連結](media/civic-platform-tutorial/sso-config01.png)

1. 在下列步驟中提供 SSO 相關的組態資訊：

    ![憑證下載連結](media/civic-platform-tutorial/sso-config02.png)

    1. 在 [applicationid]  欄位中，輸入您從 Azure 入口網站複製的 [應用程式識別碼]  值。

    1. 在 [clientSecret]  欄位中，輸入您從 Azure 入口網站複製的 [秘密]  值。

    1. 在 [directoryId]  欄位中，輸入您從 Azure 入口網站複製的 [目錄 (租用戶) 識別碼]  值。

    1. 輸入 idpName。 例如：`Azure`。

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

在本節中，您會將 Civic Platform 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Civic Platform]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-civic-platform-test-user"></a>建立 Civic Platform 測試使用者

在本節中，您要在 Civic Platform 中建立名為 B.Simon 的使用者。 請與 Civic Platform 支援小組合作，在 [Civic Platform 用戶端支援小組](mailto:skale@accela.com)中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Civic Platform] 圖格時，應該會自動登入您設定 SSO 的 Civic Platform。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

