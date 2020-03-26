---
title: 教學課程：Azure Active Directory 與 Appraisd 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Appraisd 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67561207"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>教學課程：整合 Appraisd 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Appraisd 與 Azure Active Directory (Azure AD)。 在整合 Appraisd 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Appraisd 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Appraisd。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Appraisd 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Appraisd 支援由 **SP 和 IDP** 初始化的 SSO。

## <a name="adding-appraisd-from-the-gallery"></a>從資源庫新增 Appraisd

若要設定 Appraisd 與 Azure AD 整合，您需要從資源庫將 Appraisd 新增至受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Appraisd**。
1. 從結果面板選取 [Appraisd]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B. Simon** 的測試使用者，設定及測試與 Appraisd 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Appraisd 中相關使用者之間的連結關聯性。

若要設定及測試與 Appraisd 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
2. **[設定 Appraisd](#configure-appraisd)** 以在應用程式端設定 SSO 設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B. Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B. Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Appraisd 測試使用者](#create-appraisd-test-user)** ，在 Appraisd 中建立一個與 Azure AD 使用者代表 B. Simon 連結的對應使用者。
6. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Appraisd]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  設定區段上，已預先設定好應用程式，並已經為 Azure 預先填入必要的 URL。 使用者必須按一下 [儲存] 按鈕並執行下列步驟，才能儲存設定：

    a. 按一下 [設定額外的 URL]  。

    b. 在 [轉送狀態]  文字方塊中，鍵入 URL：`<TENANTCODE>`

    c. 如果您想要以 **SP** 起始模式設定應用程式，請在 [登入 URL]  文字方塊中使用下列模式輸入 URL：`https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > 您可在 [Appraisd SSO 組態] 頁面上取得實際的 [登入 URL] 與 [轉送狀態] 值，稍後會在本教學課程中說明該頁面。

1. Appraisd 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML 權杖屬性設定中。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 Appraisd 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯]  圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Appraisd]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>設定 Appraisd

1. 若要自動執行 Appraisd 內的設定，您必須按一下 [安裝延伸模組]  來安裝「我的應用程式安全登入瀏覽器延伸模組」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Appraisd]  便會將您導向到 Appraisd 應用程式。 請從該處提供用以登入 Appraisd 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Appraisd，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Appraisd 公司網站，然後執行下列步驟：

4. 按一下頁面右上方的 [設定]  圖示，然後導覽至 [組態]  。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. 從功能表左側，按一下 [SAML 單一登入]  。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. 在 [SAML 2.0 單一登入組態]  頁面上，執行下列步驟：

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. 複製 [預設轉送狀態]  值，並將它貼到 Azure 入口網站上 [基本 SAML 組態]  的 [轉送狀態]  文字方塊中。

    b. 複製 [服務起始的登入 URL]  值，並將它貼到 Azure 入口網站上 [基本 SAML 組態]  的 [登入 URL]  文字方塊中。

7. 在相同頁面向下捲動到 [識別使用者]  之下，執行下列步驟：

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. 在 [識別提供者單一登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值，然後按一下 [儲存]  。

    b. 在 [識別提供者簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值，然後按一下 [儲存]  。

    c. 在「記事本」中開啟從 Azure 入口網站下載的 Base-64 編碼憑證，複製其內容，然後貼到 [X.509 憑證]  方塊並按一下 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 B. Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B. Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B. Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Appraisd 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Appraisd]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B. Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-appraisd-test-user"></a>建立 Appraisd 測試使用者

若要讓 Azure AD 使用者能夠登入 Appraisd，必須將這些使用者佈建到 Appraisd。 在 Appraisd 中，需手動進行佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以安全性系統管理員身分登入 Appraisd。

2. 按一下頁面右上方的 [設定]  圖示，然後導覽至 [管理中心]  。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. 在頁面頂端的工具列中，按一下 [人員]  ，然後巡覽至 [新增使用者]  。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. 在 [新增使用者]  頁面上，執行下列步驟：

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. 在 [名字]  文字方塊中，輸入使用者的名字，例如 **Britta**。

    b. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [電子郵件]  文字方塊中，輸入使用者的電子郵件，例如 `B. Simon@contoso.com`。

    d. 按一下 [新增使用者]  。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Appraisd] 圖格時，應該會自動登入您已設定 SSO 的 Appraisd。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
