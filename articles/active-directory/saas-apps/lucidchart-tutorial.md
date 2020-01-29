---
title: 教學課程：Azure Active Directory 與 Lucidchart 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Lucidchart 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eace60445dc9d52f9690da74360282efbb4cbe5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291206"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Lucidchart 整合

在本教學課程中，您將了解如何整合 Lucidchart 與 Azure Active Directory (Azure AD)。 在整合 Lucidchart 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Lucidchart 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Lucidchart。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Lucidchart 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Lucidchart 支援 **SP** 起始的 SSO
* Lucidchart 支援 **Just In Time** 使用者佈建
* 設定 Lucidchart 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>從資源庫新增 Lucidchart

若要進行 Lucidchart 與 Azure AD 整合的設定，您需要從資源庫將 Lucidchart 新增至受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Lucidchart**。
1. 從結果面板中選取 [Lucidchart]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>設定及測試 Lucidchart 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Lucidchart 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Lucidchart 中相關使用者之間的連結關聯性。

若要設定及測試與 Lucidchart 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Lucidchart SSO](#configure-lucidchart-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Lucidchart 測試使用者](#create-lucidchart-test-user)** - 使 Lucidchart 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Lucidchart]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

   在 [登入 URL]  文字方塊中，將 URL 輸入為：`https://chart2.office.lucidchart.com/saml/sso/azure`

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Lucidchart]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Lucidchart 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Lucidchart]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-lucidchart-sso"></a>設定 Lucidchart SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Lucidchart 公司網站。

2. 在頂端的功能表中，按一下 [小組]  。

    ![小組](./media/lucidchart-tutorial/ic791190.png "小組")

3. 按一下 [應用程式] \> [管理 SAML]  。

    ![管理 SAML](./media/lucidchart-tutorial/ic791191.png "管理 SAML")

4. 在 [SAML 驗證設定]  對話方塊頁面上執行下列步驟：

    a. 選取 [啟用 SAML 驗證]  ，然後按一下 [選用]  。

    ![基本驗證設定](./media/lucidchart-tutorial/ic791192.png "SAML 驗證設定")

    b. 在 [網域]  文字方塊中，輸入您的網域，然後按一下 [變更憑證]  。

    ![變更憑證](./media/lucidchart-tutorial/ic791193.png "變更憑證")

    c. 開啟您下載的中繼資料檔案，然後複製內容並貼到並 [上傳中繼資料]  文字方塊中。

    ![上傳中繼資料](./media/lucidchart-tutorial/ic791194.png "上傳中繼資料")

    d. 選取 [Automatically Add new users to the team]\(自動新增使用者至小組\)  ，然後按一下 [儲存變更]  。

    ![儲存變更](./media/lucidchart-tutorial/ic791195.png "儲存變更")

### <a name="create-lucidchart-test-user"></a>建立 Lucidchart 測試使用者

沒有動作項目可讓您設定 Lucidchart 使用者佈建。  當指派的使用者嘗試使用存取面板登入 Lucidchart 時，Lucidchart 會檢查使用者是否存在。  

如果尚無可用的使用者帳戶，Lucidchart 會自動予以建立。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Lucidchart 圖格時，應該會自動登入您已設定 SSO 的 Lucidchart。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Lucidchart](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Lucidchart](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
