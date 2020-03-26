---
title: 教學課程：Azure Active Directory 與 O.C. Tanner - AppreciateHub 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 O.C. 之間的單一登入。 Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 768756e013a4301ea11f5f9478178bdad46f9d67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983991"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>教學課程：整合 Azure Active Directory 單一登入 (SSO) 與 O.C. Tanner - AppreciateHub 的人員

在本教學課程中，您將了解如何將 O.C. Tanner - AppreciateHub 與 Azure Active Directory (Azure AD) 整合。 在整合 O.C. Tanner - AppreciateHub 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 O.C. Tanner - AppreciateHub 的人員。 Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者。
* 讓使用者使用其 Azure AD 帳戶自動登入 O.C. Tanner - AppreciateHub。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 O.C. Tanner - AppreciateHub 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* 已啟用 O.C. Tanner - AppreciateHub 支援由 **IDP** 起始的 SSO

* 設定 O.C. Tanner - AppreciateHub 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>從組件庫新增 O.C. Tanner - AppreciateHub

若要設定將 O.C. Tanner - AppreciateHub 整合到 Azure AD，您需要從組件庫將 O.C. Tanner - AppreciateHub 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **O.C.Tanner - AppreciateHub**。
1. 從結果面板中選取 [O.C.  Tanner - AppreciateHub]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>設定及測試 O.C. Tanner - AppreciateHub 的 Azure AD 單一登入 Tanner - AppreciateHub 的人員

設定及測試與 O.C. Tanner - AppreciateHub 搭配運作的 Azure AD SSO (使用名為 **B.Simon** 的測試使用者)。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 O.C. Tanner - AppreciateHub 中相關使用者之間的連結關聯性。 Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者。

若要設定及測試與 O.C. Tanner - AppreciateHub 搭配運作的 Azure AD SSO，您必須完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 O.C.Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 O.C.Tanner - AppreciateHub 測試使用者](#create-oc-tanner---appreciatehub-test-user)** - 使 O.C. Tanner - AppreciateHub 中有對應 Britta Simon 的使用者，以連結到 Azure AD 中代表的使用者。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 **[O.C.Tanner - AppreciateHub]** 應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段中，使用者不需要執行任何步驟，因為應用程式已預先與 Azure 整合。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 **[設定 O.C.Tanner - AppreciateHub]** 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 O.C. Tanner - AppreciateHub 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。 Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 **O.C.Tanner - AppreciateHub**。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-oc-tanner---appreciatehub-sso"></a>設定 O.C. Tanner - AppreciateHub SSO

若要在 **O.C.Tanner - AppreciateHub** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和所複製的適當 URL 傳送給 [O.C.Tanner - AppreciateHub 支援小組](mailto:sso@octanner.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-oc-tanner---appreciatehub-test-user"></a>建立 O.C. Tanner - AppreciateHub 測試使用者

本節目標是在 O.C. Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者。

**若要在 O.C.Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者，請執行下列步驟：**

要求您的 [O.C.Tanner - AppreciateHub 支援小組](mailto:sso@octanner.com)建立使用者，此使用者的 nameID 屬性與 Azure AD 中 Britta Simon 使用者名稱的值相同。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在 存取面板 按一下 O.C. Tanner - AppreciateHub 圖格時，應該會自動登入您已設定 SSO 的 O.C. Tanner - AppreciateHub。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 O.C.Tanner - AppreciateHub](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 O.C.Tanner - AppreciateHub](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)