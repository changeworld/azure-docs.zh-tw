---
title: 教學課程：Azure Active Directory 與 Tableau Online 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Tableau Online 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: d6b1fad722fa0a2f44c7e415f208f7e29a272a70
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813950"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Tableau Online 整合

在本教學課程中，您將了解如何整合 Tableau Online 與 Azure Active Directory (Azure AD)。 在整合 Tableau Online 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Tableau Online 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Tableau Online。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Tableau Online 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Tableau Online 支援由 **SP** 起始的 SSO

## <a name="adding-tableau-online-from-the-gallery"></a>從資源庫新增 Tableau Online

若要設定 Tableau Online 與 Azure AD 整合，您需要從資源庫將 Tableau Online 加入到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Tableau Online**。
1. 從結果面板中選取 [Tableau Online]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>設定和測試 Tableau Online 的 Azure AD SSO

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Tableau Online 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Tableau Online 中相關使用者之間的連結關聯性。

若要設定及測試與 Tableau Online 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Tableau Online SSO](#configure-tableau-online-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Tableau Online 測試使用者](#create-tableau-online-test-user)** - 使 Tableau Online 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Tableau Online] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，輸入 URL：`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入 URL：`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > 您會收到本教學課程的 [設定 Tableau Online]  區段中的 `<entityid>` 值。 實體 ID 值會是 [設定 Tableau Online]  區段中的 [Azure AD 識別碼]  值。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Tableau Online]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

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

在本節中，您會將 Tableau Online 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Tableau Online]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-tableau-online-sso"></a>設定 Tableau Online SSO

1. 若要自動執行 Tableau Online 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器延伸模組」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Tableau Online] 便會將您導向至 Tableau Online 應用程式。 請從該處提供用以登入 Tableau Online 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Tableau Online，請在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Tableau Online 公司網站。

1. 依序前往 [設定]  和 [驗證]  。

    ![螢幕擷取畫面：顯示已從 [設定] 功能表中選取 [驗證]。](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. 若要在 [驗證類型]  區段下啟用 SAML： 請勾選 [啟用其他驗證方法]  ，然後勾選 [SAML]  核取方塊。

    ![螢幕擷取畫面：顯示 [驗證類型] 區段，您可以在其中選取值。](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. 向下捲動到 [將中繼資料檔匯入到 Tableau Online]  區段。  按一下 [瀏覽]，然後匯入您從 Azure AD 下載的中繼資料檔案。 然後，按一下 [套用]  。

   ![螢幕擷取畫面：顯示可於其中匯入中繼資料檔案的區段。](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. 在 [比對判斷提示]  區段中，針對 [電子郵件地址]  、[名字]  和 [姓氏]  插入對應的識別提供者判斷提示名稱。 若要從 Azure AD 取得這項資訊︰ 
  
    a. 在 Azure 入口網站中，移至 [Tableau Online]  應用程式整合頁面。

    b. 在 [使用者屬性和宣告]  區段中，按一下編輯圖示。

   ![螢幕擷取畫面：顯示 [使用者屬性和宣告] 區段，您可以在其中選取 [編輯] 圖示。](./media/tableauonline-tutorial/attributesection.png)

    c. 使用下列步驟複製 givenname、email 和 surname 屬性的命名空間值：

   ![螢幕擷取畫面：顯示 Givenname、Surname 和 Emailaddress 屬性。](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. 按一下 [user.givenname]  值

    e. 從 [命名空間]  文字方塊複製該值。

    ![螢幕擷取畫面：顯示 [管理使用者宣告] 區段，您可以在其中輸入命名空間。](./media/tableauonline-tutorial/attributesection2.png)

    f. 若要複製 email 和 surname 的命名空間值，請重複上述步驟。

    g. 切換至 Tableau Online 應用程式，然後將 [使用者屬性和宣告]  區段設定如下：

    * 電子郵件︰**mail** 或 **userprincipalname**

    * 名字︰ **givenname**

    * 姓氏︰ **surname**

    ![螢幕擷取畫面：顯示 [比對屬性] 區段，您可以在其中輸入值。](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>建立 Tableau Online 測試使用者

在本節中，您要在 Tableau Online 中建立名為 Britta Simon 的使用者。

1. 在 [Tableau Online]  中，依序按一下 [設定]  和 [驗證]  區段。 向下捲動至 [管理使用者]  區段。 依序按一下 [新增使用者]  和 [輸入電子郵件地址]  。
  
    ![螢幕擷取畫面：顯示 [管理使用者] 區段，您可以在其中選取 [新增使用者]。](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. 選取 [新增 (SAML) 驗證的使用者]  。 在 [輸入電子郵件地址]  文字方塊中新增 britta.simon\@contoso.com
  
    ![螢幕擷取畫面：顯示 [新增使用者] 頁面，您可以在其中輸入電子郵件地址。](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. 按一下 [加入使用者]  。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 Tableau Online 登入 URL。

* 直接移至 Tableau Online 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [Tableau Online] 圖格時，將會重新導向至Tableau Online 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Tableau Online 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)