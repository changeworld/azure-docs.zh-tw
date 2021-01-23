---
title: 教學課程：Azure Active Directory 與 Sage Intacct 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Sage Intacct 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5a216e39ca32b16de405c7924d08da52c6eae4c1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736948"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>教學課程：整合 Sage Intacct 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Sage Intacct 與 Azure Active Directory (Azure AD)。 在整合 Sage Intacct 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Sage Intacct 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Sage Intacct。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Sage Intacct 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Sage Intacct 支援由 **IDP** 起始的 SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>從資源庫新增 Sage Intacct

若要設定將 Sage Intacct 整合到 Azure AD 中，您需要從資源庫將 Sage Intacct 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Sage Intacct**。
1. 從結果面板選取 [Sage Intacct]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>設定和測試 Sage Intacct 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Sage Intacct 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Sage Intacct 中相關使用者之間的連結關聯性。

若要設定及測試與 Sage Intacct Azure AD SSO，請完成下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
2. **[設定 Sage Intacct SSO](#configure-sage-intacct-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Sage Intacct 測試使用者](#create-sage-intacct-test-user)** - 在 Sage Intacct 中建立 B.Simon 的對應項目，且該項目必須與 Azure AD 中代表 B.Simon 的項目連結。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [ **Sage Intacct** 應用程式整合] 頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    在 [回覆 URL] 文字方塊中，輸入 URL：`https://www.intacct.com/ia/acct/sso_response.phtml`

1. Sage Intacct 應用程式需要特定格式的 SAML 判斷提示，需要您將自訂屬性對應新增到您的 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] 圖示，以開啟 [使用者屬性] 對話方塊。

    ![image](common/edit-attribute.png)

1. 除了以上屬性外，Sage Intacct 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性與宣告] 對話方塊中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 屬性名稱  |  來源屬性|
    | ---------------| --------------- |
    | 公司名稱 | **Sage Intacct 公司識別碼** |
    | NAME | 此值應等同 Sage Intacct 的 **使用者識別碼** (您在 [建立 Sage Intacct 測試使用者] 區段中輸入的值)，本教學課程稍後會說明 |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性] 清單中，輸入或選取該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案] 。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，尋找 [憑證 (Base64)] 並選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Sage Intacct] 區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Sage Intacct 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Sage Intacct]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-sage-intacct-sso"></a>設定 Sage Intacct SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Sage Intacct 公司網站。

1. 按一下 [公司] 索引標籤，然後按一下 [公司資訊]。

    ![公司](./media/intacct-tutorial/ic790037.png "公司")

1. 按一下 [安全性] 索引標籤，然後按一下 [編輯]。

    ![安全性](./media/intacct-tutorial/ic790038.png "安全性")

1. 在 [單一登入 (SSO)]  區段中，執行下列步驟：

    ![單一登入](./media/intacct-tutorial/ic790039.png "單一登入")

    a. 選取 [啟用單一登入] 。

    b. 在 [身分識別提供者類型]，選取 **SAML 2.0**。

    c. 在 [簽發者 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    d. 在 [登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    e. 在記事本中開啟您的 **base-64** 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [憑證] 方塊中。

    f. 按一下 [檔案] 。

### <a name="create-sage-intacct-test-user"></a>建立 Sage Intacct 測試使用者

若要設定 Azure AD 使用者以便讓他們能夠登入 Sage Intacct，則必須將他們佈建到 Sage Intacct。 在 Sage Intacct 中，佈建是手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Sage Intacct** 租用戶。

1. 按一下 [公司] 索引標籤，然後按一下 [使用者]。

    ![使用者](./media/intacct-tutorial/ic790041.png "使用者")

1. 按一下 [新增] 索引標籤。

    ![加入](./media/intacct-tutorial/ic790042.png "加")

1. 在 [使用者資訊]  區段中，執行下列步驟：

    ![顯示 [使用者資訊] 區段的螢幕擷取畫面，您可以在其中輸入此步驟中的資訊。](./media/intacct-tutorial/ic790043.png "使用者資訊")

    a. 在 [使用者資訊] 區段中，為您要佈建的 Azure AD 帳戶輸入 [使用者識別碼]、[姓氏]、[名字]、[電子郵件地址]、[職稱] 和 [電話]。

    > [!NOTE]
    > 請確定上面螢幕擷取畫面中的 **使用者識別碼** 等同 **來源屬性** 值，也就是在 Azure 入口網站的 [使用者屬性] 區段中，與 **名稱** 屬性相對應的值。

    b. 選取您要佈建之 Azure AD 帳戶的 [系統管理員權限]。

    c. 按一下 [檔案] 。 
    
    d. Azure AD 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

1. 按一下 [單一登入] 索引標籤，並確定下方螢幕擷取畫面中的 **同盟 SSO 使用者識別碼** 等同 Azure 入口網站中 [使用者屬性] 區段中與 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 對應的 **來源屬性** 值。

    ![此螢幕擷取畫面顯示用來輸入同盟 SSO 使用者識別碼的 [使用者資訊] 區段。](./media/intacct-tutorial/ic790044.png "使用者資訊")

> [!NOTE]
> 若要佈建 Azure AD 使用者帳戶，您可以使用 Sage Intacct 所提供的其他 Sage Intacct 使用者帳戶建立工具或 API。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 按一下 [在 Azure 入口網站測試此應用程式]，您應該會自動登入您已設定 SSO 的 Sage Intacct

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [Sage Intacct] 磚時，應該會自動登入您已設定 SSO 的 Sage Intacct。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 Sage Intacct 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。