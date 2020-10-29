---
title: 教學課程：Azure Active Directory 與 Dropbox Business 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Dropbox Business 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: bf86656ff670df19162867d597a869d762a012e8
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454554"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>教學課程：整合 Dropbox Business 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Dropbox Business 與 Azure Active Directory (Azure AD)。 在整合 Dropbox Business 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Dropbox Business 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Dropbox Business。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Dropbox Business 單一登入 (SSO) 的訂用帳戶。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

* 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Dropbox Business 支援 **SP** 起始的 SSO

* Dropbox Business 支援[自動化的使用者佈建和取消佈建](dropboxforbusiness-tutorial.md)
* 設定 Dropbox 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>從資源庫新增 Dropbox Business

若要設定 Dropbox Business 與 Azure AD 整合，您需要從資源庫將 SAP Dropbox Business 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Dropbox Business** 。
1. 從結果面板選取 [Dropbox Business]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 Dropbox Business 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Dropbox Business 中相關使用者之間的連結關聯性。

若要設定及測試與 Dropbox Business 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。    
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Dropbox Business SSO](#configure-dropbox-business-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Dropbox Business 測試使用者](#create-dropbox-business-test-user)** - 使 Dropbox Business 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Dropbox Business] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 頁面上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：`https://www.dropbox.com/sso/<id>`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入值：`Dropbox`

    > [!NOTE]
    > 上述登入 URL 值並非真正的值。 您將會使用實際的登入 URL 來更新值，稍後會在本教學課程中說明。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Dropbox Business] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL


### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Dropbox Business 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Dropbox Business]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊中，從使用者清單選取 **Britta Simon** ，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-dropbox-business-sso"></a>設定 Dropbox Business SSO

1. 若要自動執行 Dropbox Business 內的設定，您必須按一下 [安裝延伸模組] 來安裝「我的應用程式安全登入瀏覽器延伸模組」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Dropbox Business ] 便會將您導向到 Dropbox Business 應用程式。 請從該處提供用以登入 Dropbox Business 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 8。

    ![設定組態](common/setup-sso.png)

3. 如果您要手動設定 Dropbox Business，請開啟新的網頁瀏覽器視窗並移至 Dropbox Business 租用戶，然後登入您的 Dropbox Business 租用戶。 並執行下列步驟：

    ![顯示 [商務用 Dropbox 登入] 頁面的螢幕擷取畫面。](./media/dropboxforbusiness-tutorial/ic769509.png "設定單一登入")

4. 按一下 [使用者圖示]，然後選取 [設定] 索引標籤。

    ![顯示已選取 [使用者圖示] 動作和 [設定] 的螢幕擷取畫面。](./media/dropboxforbusiness-tutorial/configure1.png "設定單一登入")

5. 在左側的導覽窗格中，按一下 [管理主控台]。

    ![顯示已選取 [管理主控台] 的螢幕擷取畫面。](./media/dropboxforbusiness-tutorial/configure2.png "設定單一登入")

6. 在 [管理主控台] 上，按一下左側導覽窗格中的 [設定]。

    ![顯示已選取 [設定] 的螢幕擷取畫面。](./media/dropboxforbusiness-tutorial/configure3.png "設定單一登入")

7. 選取 [驗證]區段下的 [單一登入] 選項。

    ![顯示 [驗證] 區段的螢幕擷取畫面，其中已選取 [單一登入]。](./media/dropboxforbusiness-tutorial/configure4.png "設定單一登入")

8. 在 [單一登入]  區段中，執行下列步驟：  

    ![顯示 [單一登入] 組態設定的螢幕擷取畫面。](./media/dropboxforbusiness-tutorial/configure5.png "設定單一登入")

    a. 在 [單一登入] 的下拉式清單中選取 [必要] 選項。

    b. 按一下 [新增登入 URL]，並在 [識別提供者登入 URL] 文字方塊中貼上您從 Azure 入口網站複製的 [登入 URL] 值，然後選取 [完成]。

    ![設定單一登入](./media/dropboxforbusiness-tutorial/configure6.png "設定單一登入")

    c. 按一下 [上傳憑證]，然後瀏覽至您從 Azure 入口網站下載的 **Base64 編碼憑證檔案** 。

    d. 按一下 [複製連結]，並在 Azure 入口網站中，將複製的值貼到 [Dropbox Business 網域和 URL] 區段的 [登入 URL] 文字方塊。

    e. 按一下 [檔案] 。

### <a name="create-dropbox-business-test-user"></a>建立 Dropbox Business 測試使用者

本節會在 Dropbox Business 中建立名為 B.Simon 的使用者。 Dropbox Business 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Dropbox Business 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!Note]
>如果您需要手動建立使用者，請連絡 [Dropbox Business 用戶端支援小組](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Dropbox Business] 圖格時，應該會自動登入您已設定 SSO 的 Dropbox Business。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](/cloud-app-security/proxy-intro-aad)

- [嘗試搭配 Azure AD 使用 Dropbox Business](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)