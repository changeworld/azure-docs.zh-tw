---
title: 教學課程：Azure Active Directory 與 Abstract 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Abstract 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 3f19733f01cf601c1145d5e2bb5deb58db828dc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88538326"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>教學課程：整合 Abstract 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Abstract 與 Azure Active Directory (Azure AD)。 在整合 Abstract 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Abstract 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Abstract。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Abstract 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Getabstract 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-abstract-from-the-gallery"></a>從資源庫新增 Abstract

若要設定將 Abstract 整合到 Azure AD 中，您需要從資源庫將 Abstract 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Abstract**。
1. 從結果面板選取 [Abstract]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B. Simon** 的測試使用者，設定及測試與 Abstract 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Abstract 中相關使用者之間的連結關聯性。

若要設定及測試與 Abstract 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Abstract SSO](#configure-abstract-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Abstract 測試使用者](#create-abstract-test-user)** - 在 Abstract 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Abstract]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，已預先以 **IDP** 起始的模式設定好應用程式，並已經為 Azure 預先填入必要的 URL。 使用者必須按一下 [儲存]  按鈕，才能儲存設定。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，輸入 URL：`https://app.abstract.com/signin`

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>設定 Abstract SSO

請務必從 Azure 入口網站取出 `App Federation Metadata Url` 和 `Azure AD Identifier`，因為您需要這些資訊來設定 Abstract 上的 SSO。

您會在 [以 SAML 設定單一登入]  頁面上找到這些資訊：

* `App Federation Metadata Url` 位在 [SAML 簽署憑證]  區段中。
* `Azure AD Identifier` 位於 [設定 Abstract]  區段中。


您現在已可以在 Abstract 上設定 SSO：

>[!Note]
>您必須使用組織系統管理員帳戶進行驗證，才能存取 Abstract 上的 SSO 設定。

1. 開啟 [Abstract Web 應用程式](https://app.abstract.com/)。
2. 移至左側工具列中的 [權限]  頁面。
3. 在 [設定 SSO]  區段中，輸入您的**中繼資料 URL** 和**實體識別碼**。
4. 輸入您可能會遇到的任何手動例外。 手動例外區段中所列的電子郵件將會略過 SSO，並且能夠使用電子郵件和密碼登入。 
5. 按一下 **[儲存變更]** 。

>[!Note] 
>您必須在手動例外清單中使用主要電子郵件地址。 如果您列出的電子郵件是使用者的次要電子郵件，SSO 將會無法啟用。 如果發生這種情況，您會看到失敗帳戶的主要電子郵件錯誤訊息。 請在確認您知道使用者是誰之後，將該主要電子郵件新增至手動例外清單。

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

在本節中，您會將 Abstract 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Abstract]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-abstract-test-user"></a>建立 Abstract 測試使用者

在 Abstract 上測試 SSO：

1. 開啟 [Abstract Web 應用程式](https://app.abstract.com/)。
2. 移至左側工具列中的 [權限]  頁面。
3. 按一下 [以我的帳戶進行測試]  。 如果測試失敗，請[連絡我們的支援小組](https://www.abstract.com/help/contact/)。

>[!Note]
>您必須使用組織系統管理員帳戶進行驗證，才能存取 Abstract 上的 SSO 設定。
此組織的系統管理員帳戶必須在 Azure 入口網站上指派給 Abstract。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Abstract 圖格時，應該會自動登入您已設定 SSO 的 Abstract。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

