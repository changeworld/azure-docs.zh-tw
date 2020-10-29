---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Workplace by Facebook 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workplace by Facebook 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: f20849e4085adcca909d990a2ae9fb7b9751220b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520093"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Workplace by Facebook 整合

在本教學課程中，您會了解如何將 Workplace by Facebook 與 Azure Active Directory (Azure AD) 整合。 當您將 Workplace by Facebook 與 Azure AD 整合時，您可以：

* 在 Azure AD 中控制可存取 Workplace by Facebook 的人員。
* 讓使用者使用他們的 Azure AD 帳戶自動登入 Workplace by Facebook。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Workplace by Facebook (SSO) 單一登入的訂用帳戶。

> [!NOTE]
> Facebook 有兩項產品 Workplace Standard (免費) 和 Workplace Premium (付費)。 任何 Workplace Premium 租用戶都可以設定 SCIM 和 SSO 整合，而無須任何其他費用或授權。 Workplace Standard 執行個體中並未提供 SSO 和 SCIM。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Workplace by Facebook 支援 **SP** 起始的 SSO
* Workplace by Facebook 支援 **Just-In-Time 佈建**
* Workplace by Facebook 支援 **[自動使用者佈建](workplacebyfacebook-provisioning-tutorial.md)**
* Workplace by Facebook 行動應用程式現在可以搭配 Azure AD 設定來啟用 SSO。 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。
* 設定 Workplace by Facebook 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>從資源庫新增 Workplace by Facebook

若要設定將 Workplace by Facebook 整合到 Azure AD 中，您需要從資源庫將 Workplace by Facebook 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Workplace by Facebook** 。
1. 從結果面板選取 [Workplace by Facebook]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>針對 Workplace by Facebook 設定並測試 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Workplace by Facebook 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Workplace by Facebook 中相關使用者之間的連結關聯性。

若要設定及測試與 Workplace by Facebook 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Workplace by Facebook SSO](#configure-workplace-by-facebook-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Workplace by Facebook 測試使用者](#create-workplace-by-facebook-test-user)** - 使 Workplace by Facebook 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Workplace by Facebook]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<instancename>.facebook.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://www.facebook.com/company/<instanceID>`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請參閱 [Workplace 公司儀表板] 的 [驗證] 頁面，以查看您 Workplace 社群的正確值，稍後本教學課程會加以說明。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Workplace by Facebook]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Workplace by Facebook 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Workplace by Facebook]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-workplace-by-facebook-sso"></a>設定 Workplace by Facebook SSO

1. 若要自動執行 Workplace by Facebook 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 Workplace by Facebook]  便會將您導向到 Workplace by Facebook 應用程式。 請從該處提供用以登入 Workplace by Facebook 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 5。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Workplace by Facebook，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Workplace by Facebook 公司網站，然後執行下列步驟：

    > [!NOTE]
    > 為將參數傳遞給 Azure AD，Workplace 可利用大小最多 2.5 KB 的查詢字串，作為 SAML 驗證流程的一部分。

1. 在左側導覽面板中，瀏覽至 [安全性]   > [驗證]  索引標籤。

    ![管理面板](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. 核取 [單一登入 (SSO)]  選項。
    
    b. 按一下 [+新增 SSO 提供者]  。
    > [!NOTE]
    > 請務必一併勾選 [密碼登入] 核取方塊。 在執行憑證變換時，管理員可能需要此選項來進行登入，以防止自己遭到鎖定。

1. 在 [驗證]  索引標籤底下，選取 [單一登入 (SSO)]  ，然後執行下列步驟：

    ![[驗證] 索引標籤](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. 在 [SSO 提供者的名稱]  中，輸入 SSO 執行個體名稱，例如 Azureadsso。

    b. 在 [SAML URL]  文字方塊中，貼上您從 Azure 入口網站複製的 **登入 URL** 值。

    c. 在 [SAML 簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼** 值。

    d. 在從 Azure 入口網站下載的記事本檔案中開啟您的 **base-64 編碼憑證** ，將憑證的內容複製到剪貼簿，再貼到 [SAML 憑證]  文字方塊。

    e. 複製執行個體的 [Audience URL] \(對象 URL\)  ，並將其貼到 Azure 入口網站上 [基本 SAML 設定]  區段中的 [識別碼 (實體識別碼)]  文字方塊內。

    f. 複製執行個體的 [Recipient URL] \(收件者 URL\)  ，並將其貼到 Azure 入口網站上 [基本 SAML 設定]  區段中的 [登入 URL]  文字方塊內。

    g. 複製執行個體的 [ACS (判斷提示取用者服務) URL]  ，並將其貼到 Azure 入口網站上的 [基本 SAML 組態]  區段的 [回覆 URL]  文字方塊中。

    h. 捲動到區段的底部，然後按一下 [測試 SSO]  按鈕。 快顯視窗中的這個結果隨即顯示，並會出現 Azure AD 登入頁面。 如往常輸入您的認證以進行驗證。

    **疑難排解：** 確保從 Azure AD 傳回的電子郵件地址與您用來登入的 Workplace 帳戶相同。

    i. 一旦測試已順利完成後，捲動到頁面底部，然後按一下 [儲存]  按鈕。

    j. 現在，使用 Workplace 的所有使用者都將會看到進行驗證的 Azure AD 登入頁面。

1. **SAML 登出重新導向 (選擇性)**  -

    您可以選擇性地選擇設定 SAML 登出 URL，可用來指向 Azure AD 的登出頁面。 當啟用並設定此設定時，就不會再將使用者導向至 Workplace 登出頁面。 相反地，系統會將使用者重新導向至 [SAML 登出重新導向] 設定中所新增的 URL。

### <a name="configuring-reauthentication-frequency"></a>設定重新驗證頻率

您可以將 Workplace 設定為每一天、每三天、每一週、每兩週、每一個月或一律不會提示進行 SAML 檢查。

> [!NOTE]
> 行動應用程式的 SAML 檢查最小值是設定為一週。

您也可以使用下列按鈕，強制所有使用者重設 SAML：要求所有使用者立即進行 SAML 驗證。

### <a name="create-workplace-by-facebook-test-user"></a>建立 Workplace by Facebook 測試使用者

本節會在 Workplace by Facebook 中建立名為 B.Simon 的使用者。 Workplace by Facebook 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作。 如果使用者不存在於 Workplace by Facebook 中，當您嘗試存取 Workplace by Facebook 時，就會建立一個新的。

>[!Note]
>如果您需要手動建立使用者，請連絡 [Workplace by Facebook 用戶端支援小組](https://www.workplace.com/help/work/)

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Workplace by Facebook] 圖格時，應該會自動登入您設定 SSO 的 Workplace by Facebook。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>測試 Workplace by Facebook 的 SSO (行動裝置)

1. 開啟 Workplace by Facebook 行動應用程式。 在登入頁面上，按一下 [登入]  。

    ![登入](./media/workplacebyfacebook-tutorial/test05.png)

2. 輸入您的公司電子郵件，然後按一下 [繼續]  。

    ![電子郵件](./media/workplacebyfacebook-tutorial/test02.png)

3. 按一下 [僅一次]  。

    ![一次](./media/workplacebyfacebook-tutorial/test04.png)

4. 按一下 [允許]  。

    ![允許](./media/workplacebyfacebook-tutorial/test03.png)

5. 最後，成功登入之後，應用程式首頁會隨即顯示。    

    ![首頁](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [設定使用者佈建](workplacebyfacebook-provisioning-tutorial.md)

- [嘗試搭配 Azure AD 使用 Workplace by Facebook](https://aad.portal.azure.com)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)