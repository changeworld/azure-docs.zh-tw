---
title: 教學課程：Azure Active Directory 與 RunMyProcess 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 RunMyProcess 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: db47849575c097cf56824b623dddbb6e0835d1b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543596"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>教學課程：整合 RunMyProcess 與 Azure Active Directory

在本教學課程中，您會了解如何整合 RunMyProcess 與 Azure Active Directory (Azure AD)。 在整合 RunMyProcess 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 RunMyProcess 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 RunMyProcess。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 RunMyProcess 單一登入的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* RunMyProcess 支援 **SP** 起始的 SSO

## <a name="adding-runmyprocess-from-the-gallery"></a>從資源庫新增 RunMyProcess

若要設定將 RunMyProcess 整合到 Azure AD 中，您需要從資源庫將 RunMyProcess 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **RunMyProcess**。
1. 從結果面板選取 [RunMyProcess]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 RunMyProcess 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 RunMyProcess 中相關使用者之間的連結關聯性。

若要設定及測試與 RunMyProcess 搭配運作的 Azure AD SSO，請完成下列建構元素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 RunMyProcess SSO](#configure-runmyprocess-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
5. **[建立 RunMyProcess 測試使用者](#create-runmyprocess-test-user)** - 使 RunMyProcess 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [RunMyProcess]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [RunMyProcess 客戶支援小組](mailto:support@runmyprocess.com)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 RunMyProcess]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>設定 RunMyProcess SSO

1. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 RunMyProcess 租用戶。

1. 在左方瀏覽面板中，按一下 [帳戶]  ，然後選取 [組態]  。

    ![在應用程式端設定單一登入](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. 移至 [驗證方法]  區段並執行下列步驟：

    ![在應用程式端設定單一登入](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. 在 [方法]  ，選取 [使用 Samlv2 進行 SSO]  。

    b. 在 [SSO 重新導向]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 在 [登出重新導向]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    d. 在 [名稱識別碼格式]  文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** 作為**名稱識別碼格式**的值。

    e. 從 Azure 入口網站將所下載的憑證檔案以記事本開啟，複製憑證檔案的內容，然後將其貼至 [憑證]  文字方塊。

    f. 按一下 [儲存]  圖示。

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

在本節中，您會將 RunMyProcess 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [RunMyProcess]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-runmyprocess-test-user"></a>建立 RunMyProcess 測試使用者

若要讓 Azure AD 使用者能夠登入 RunMyProcess，必須將他們佈建到 RunMyProcess。 RunMyProcess 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 RunMyProcess 公司網站。

1. 按一下 [帳戶]  並選取 [使用者]  ，然後按一下 [新增使用者]  。

    ![新增使用者](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "新增使用者")

1. 在 [使用者設定]  區段中，執行下列步驟：

    ![設定檔](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "設定檔")
  
    a. 在相關的文字方塊中，輸入您想要佈建之有效 Azure AD 帳戶的 [名稱]  和 [電子郵件]  。

    b. 選取 [IDE 語言]  、[語言]  和 [設定檔]  。

    c. 選取 [傳送帳戶建立電子郵件給我]  。

    d. 按一下 [檔案]  。

    > [!NOTE]
    > 您可以使用任何其他的 RunMyProcess 使用者帳戶建立工具或 RunMyProcess 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 RunMyProcess 圖格時，應該會自動登入您已設定 SSO 的 RunMyProcess。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)