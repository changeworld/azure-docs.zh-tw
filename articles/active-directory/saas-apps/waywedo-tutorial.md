---
title: 教學課程：Azure Active Directory 與 Way We Do 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Way We Do 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67310396"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>教學課程：整合 Way We Do 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Way We Do 與 Azure Active Directory (Azure AD)。 在整合 Way We Do 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Way We Do 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Way We Do。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Way We Do 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Way We Do 支援 **SP** 起始的 SSO
* Way We Do 支援 **Just In Time** 使用者佈建

## <a name="adding-way-we-do-from-the-gallery"></a>從資源庫新增 Way We Do

若要設定 Way We Do 與 Azure AD 整合，您需要從資源庫將 Way We Do 新增到受控的 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Way We Do**。
1. 從結果面板選取 [Way We Do]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Way We Do 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Way We Do 中相關使用者之間的連結關聯性。

若要設定及測試與 Way We Do 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Way We Do SSO](#configure-way-we-do-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Way We Do 測試使用者](#create-way-we-do-test-user)** - 使 Way We Do 中 Britta Simon 的對應使用者連結到使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Way We Do]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Way We Do 用戶端支援小組](mailto:support@waywedo.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (原始)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificateraw.png)

1. 在 [設定 Way We Do]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>設定 Way We Do SSO

1. 若要自動執行 Way We Do 內的設定，您必須按一下 [安裝延伸模組]  來安裝「我的應用程式安全登入瀏覽器延伸模組」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將延伸模組新增至瀏覽器之後，按一下 [安裝 Way We Do]  便會將您導向到 Way We Do 應用程式。 請從該處提供用以登入 Way We Do 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Way We Do，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Way We Do 公司網站，然後執行下列步驟：

1. 在 Way We Do 中的任何頁面上，按一下右上角的**人員圖示**，然後在下拉式清單中按一下 [Account]  \(帳戶\)。

    ![Way We Do [Account] \(帳戶\)](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. 按一下**功能表圖示**以開啟推送導覽功能表，然後按一下 [Single Sign On]  \(單一登入\)。

    ![Way We Do 單一登入](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. 在 [Single sign-on setup]  \(單一登入設定\) 頁面上，執行下列步驟：

    ![Way We Do [Save] \(儲存\)](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. 按一下 [Turn on single sign-on]  \(開啟單一登入\)，切換為 [Yes]  \(是\) 以啟用單一登入。

    b. 在 [Single sign-on name]  \(單一登入名稱\) 文字方塊中，輸入您的名稱。

    c. 在 [實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    d. 在 [SAML SSO URL]  文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    e. 按一下 [Certificate]  \(憑證\) 旁的 [select]  \(選取\) 按鈕來上傳憑證。

    f. **選擇性設定** -
    
    * 啟用密碼：停用此選項時，會針對 Way We Do 使用一般密碼，讓使用者只能使用單一登入。

    * 啟用自動佈建：啟用此選項時，用於登入的電子郵件地址將自動與 Way We Do 中的使用者清單進行比較。 如果電子郵件地址與 Way We Do 中的作用中使用者不符，就會為登入的人員自動新增新的使用者帳戶，並要求該人員提供所有遺失的資訊。

      > [!NOTE]
      > 透過單一登入新增的使用者會被新增為一般使用者，且不會被指派系統中的角色。 系統管理員後續將能夠將這些人員的安全性角色修改為編輯者或系統管理員，同時也可以指派一或多個組織圖角色。

    g. 按一下 [Save]  \(儲存\) 以保存您的設定。

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

在本節中，您會將 Way We Do 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Way We Do]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-way-we-do-test-user"></a>建立 Way We Do 測試使用者

本節會在 Way We Do 中建立名為 Britta Simon 的使用者。 Way We Do 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Way We Do 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!Note]
> 如果您需要手動建立使用者，請連絡 [Way We Do 用戶端支援小組](mailto:support@waywedo.com)。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Way We Do] 圖格時，應該會自動登入您已設定 SSO 的 Way We Do。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)