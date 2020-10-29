---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 BlueJeans for Azure AD 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 BlueJeans for Azure AD 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: 4026bd9c5b4d73953509617fe4a8623987d22341
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 BlueJeans for Azure AD 整合

在本教學課程中，您會了解如何將 BlueJeans for Azure AD 與 Azure Active Directory (Azure AD) 整合。 整合 BlueJeans for Azure AD 與 Azure AD 後，您可以：

* 在 Azure AD 中控制可存取 BlueJeans for Azure AD 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 BlueJeans for Azure AD。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 BlueJeans for Azure AD 單一登入 (SSO) 的訂用帳戶。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* BlueJeans for Azure AD 支援 **SP** 起始的 SSO

* BlueJeans for Azure AD 支援 [**自動** 使用者佈建](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>從資源庫新增 BlueJeans for Azure AD

若要設定將 BlueJeans for Azure AD 整合到 Azure AD 中，您需要從資源庫將 BlueJeans for Azure AD 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **BlueJeans for Azure AD** 。
1. 從結果面板選取 [BlueJeans for Azure AD]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>設定及測試 BlueJeans for Azure AD 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 BlueJeans for Azure AD 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 BlueJeans for Azure AD 中相關使用者之間的連結關聯性。

若要設定及測試與 BlueJeans for Azure AD 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 BlueJeans for Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 BlueJeans for Azure AD 測試使用者](#create-bluejeans-for-azure-ad-test-user)** - 使 BlueJeans for Azure AD 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [BlueJeans for Azure AD] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<companyname>.bluejeans.com`

    a. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入 URL：`http://samlsp.bluejeans.com`

    a. 在 [回覆 URL] 文字方塊中，鍵入 URL：`https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [BlueJeans for Azure AD 用戶端支援小組](https://support.bluejeans.com/contact)以取得此值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. BlueJeans 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，BlueJeans 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 |  來源屬性|
    | ---------| --------- |
    | 電話 | user.telephonenumber |
    | title | user.jobtitle |

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，尋找 [憑證 (Base64)] 並選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 BlueJeans for Azure AD] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 BlueJeans for Azure AD 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [BlueJeans for Azure AD]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-bluejeans-for-azure-ad-sso"></a>設定 BlueJeans for Azure AD SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **BlueJeans for Azure AD** 公司網站。

2. 移至 [管理] \> [群組設定] \> [安全性]。

    ![顯示部分瀏覽器視窗的螢幕擷取畫面，其中已選取 [管理] 索引標籤以及 [群組設定] 和 [安全性]。](./media/bluejeans-tutorial/ic785868.png "管理")

3. 在 [安全性] 區段中，執行下列步驟：

    ![SAML 單一登入](./media/bluejeans-tutorial/ic785869.png "SAML 單一登入")

    a. 選取 [SAML 單一登入] 。

    b. 選取 [啟用自動佈建] 。

4. 繼續執行下列步驟：

    ![憑證路徑](./media/bluejeans-tutorial/ic785870.png "憑證路徑")

    a. 按一下 [選擇檔案]，以上傳您從 Azure 入口網站下載的 base-64 編碼憑證。

    b. 在 [登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    c. 在 [密碼變更 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [變更密碼 URL] 值。

    d. 在 [登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

5. 繼續執行下列步驟：

    ![儲存變更](./media/bluejeans-tutorial/ic785874.png "儲存變更")

    a. 在 [使用者識別碼] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    b. 在 [電子郵件] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    c. 按一下 [儲存變更]。

### <a name="create-bluejeans-for-azure-ad-test-user"></a>建立 BlueJeans for Azure AD 測試使用者

本節的目標是在 BlueJeans for Azure AD 中建立名為 B.Simon 的使用者。 BlueJeans for Azure AD 支援自動使用者佈建，該功能預設為啟用。 您可以在[這裡](bluejeans-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

**如果您需要手動建立使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 **BlueJeans for Azure AD** 公司網站。

2. 移至 [管理] \> [管理使用者] \> [新增使用者]。

    ![顯示部分瀏覽器視窗的螢幕擷取畫面，其中已選取 [管理] 索引標籤以及 [管理使用者] 和 [新增使用者]。](./media/bluejeans-tutorial/ic785877.png "管理")

    > [!IMPORTANT]
    > 只有在未核取 [安全性] 索引標籤中的 [啟用自動佈建] 時，才能使用 [新增使用者] 索引標籤。

3. 在 [新增使用者] 區段中，執行下列步驟：

    ![顯示 [新增使用者] 區段的螢幕擷取畫面，您會在其中輸入此步驟中所述的資訊。](./media/bluejeans-tutorial/ic785886.png "新增使用者")

    a. 在 [名字] 文字方塊中，輸入使用者的名字，例如 **B** 。

    b. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 **Simon** 。

    c. 在 [挑選 BlueJeans for Azure AD 使用者名稱] 文字方塊中，輸入使用者的名稱，例如 **Brittasimon**

    d. 在 [建立密碼] 文字方塊中，輸入您的密碼。

    e. 在 [公司] 文字中，輸入您的公司。

    f. 在 [電子郵件地址] 文字方塊中，輸入使用者的電子郵件，例如 `b.simon\@contoso.com`。

    g. 在 [建立 BlueJeans for Azure AD 會議識別碼] 文字方塊中，輸入您的會議識別碼。

    h. 在 [挑選審查工具密碼] 文字方塊中，輸入您的密碼。

    i. 按一下 [繼續]。

    ![顯示 [新增使用者] 區段的螢幕擷取畫面，您可以在其中檢視設定和功能，其中已選取 [新增使用者] 按鈕。](./media/bluejeans-tutorial/ic785887.png "新增使用者")

    J. 按一下 [新增使用者]。

> [!NOTE]
> 您可以使用任何其他的 BlueJeans for Azure AD 使用者帳戶建立工具或 BlueJeans for Azure AD 提供的 API 來佈建 Azure AD 使用者帳戶。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [BlueJeans for Azure AD] 圖格時，應該會自動登入您已設定 SSO 的 BlueJeans for Azure AD。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 BlueJeans for Azure AD](https://aad.portal.azure.com/)