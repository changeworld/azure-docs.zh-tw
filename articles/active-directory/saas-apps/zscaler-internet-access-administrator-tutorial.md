---
title: 教學課程：Azure Active Directory 與 Zscaler Internet Access Administrator 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Internet Access Administrator 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c3a2f2b04827fac06a0729e45b46765928aedd34
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539798"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>教學課程：Azure Active Directory 與 Zscaler Internet Access Administrator 整合

在本教學課程中，您將瞭解如何整合 Zscaler Internet Access Administrator 與 Azure Active Directory (Azure AD) 。 當您整合 Zscaler Internet Access Administrator 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Zscaler Internet Access Administrator 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Zscaler Internet Access Administrator。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Zscaler Internet Access Administrator 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* Zscaler Internet Access Administrator 訂用帳戶

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Zscaler Internet Access Administrator 支援 **IDP** 初始的 SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>從資源庫新增 Zscaler Internet Access Administrator

若要設定將 Zscaler Internet Access Administrator 整合到 Azure AD 中，您需要將 Zscaler Internet Access Administrator 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **Zscaler Internet Access Administrator** 。
1. 從結果面板選取 [ **Zscaler Internet Access Administrator** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>設定及測試 Azure AD SSO for Zscaler Internet Access Administrator

使用名為 **b. Simon** 的測試使用者，設定及測試與 Zscaler Internet Access Administrator 搭配的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Zscaler Internet Access Administrator 中相關使用者之間的連結關聯性。

若要設定及測試與 Zscaler Internet Access Administrator 搭配 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Zscaler Internet Access ADMINISTRATOR SSO](#configure-zscaler-internet-access-administrator-sso)** -在應用程式端設定單一 Sign-On 設定。
    1. **[建立 Zscaler Internet Access Administrator 測試使用者](#create-zscaler-internet-access-administrator-test-user)** - 在 Zscaler Internet Access Administrator 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [ **Zscaler 網際網路存取系統管理員** 應用程式整合] 頁面的 [Azure 入口網站] 中，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，依據您的需求鍵入 URL：

    | 識別碼 |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. 在 [回覆 URL] 文字方塊中，依據您的需求鍵入 URL：

    | 回覆 URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet Access Administrator 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性與宣告] 區段中管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性與宣告] 對話方塊。

    ![屬性連結](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱  | 來源屬性  |
    | ---------| ------------ |
    | 角色 | user.assignedroles |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    b. 從 [來源屬性] 清單中，選取屬性值。

    c. 按一下 [確定] 。

    d. 按一下 [檔案]  。

    > [!NOTE]
    > 請按一下[這裡](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui)，以了解如何在 Azure AD 中設定角色。

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

8. 在 [設定 Zscaler Internet Access Administrator] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Zscaler Internet Access Administrator 的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [ **Zscaler 網際網路存取系統管理員**]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您已如上述所述設定角色，可以從 **選取角色** 下拉式清單中選取。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。


## <a name="configure-zscaler-internet-access-administrator-sso"></a>設定 Zscaler Internet Access Administrator SSO

1. 在不同的 Web 瀏覽器視窗中，登入您的 Zscaler Internet Access Admin UI。

2. 移至 管理員 > 管理員管理，執行下列步驟並按一下 儲存：

    ![螢幕擷取畫面：顯示 [系統管理員管理]，其中有選項可供啟用 SAML 驗證、上傳 SSL 憑證並指定簽發者。](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "系統管理")

    a. 核取 [啟用 SAML 驗證] 。

    b. 按一下 [上傳]，以上傳您從 Azure 入口網站的 [公開 SSL 憑證] 下載的 Azure SAML 簽署憑證。

    c. (選擇性) 為了增加安全性，新增 [簽發者] 詳細資料以驗證 SAML 回應的簽發者。

3. 在 Admin UI 上，執行下列步驟：

    ![螢幕擷取畫面：顯示 [管理員 UI]，您可以在其中執行步驟。](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. 將滑鼠停留在靠近左下方的 [啟用]  功能表上。

    b. 按一下 [啟用]  。

### <a name="create-zscaler-internet-access-administrator-test-user"></a>建立 Zscaler Internet Access Administrator 測試使用者

本節目標是在 Zscaler Internet Access Administrator 中建立名為 Britta Simon 的使用者。 Zscaler Internet Access 不支援 Administrator SSO 的 Just-In-Time 佈建。 您必須手動建立系統管理員帳戶。
如需有關如何建立系統管理員帳戶的步驟，請參閱 Zscaler 文件：

https://help.zscaler.com/zia/adding-admins

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 按一下 [在 Azure 入口網站測試此應用程式]，您應該會自動登入您已設定 SSO 的 Zscaler Internet Access Administrator

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [Zscaler Internet Access Administrator] 圖格時，應該會自動登入您已設定 SSO 的 Zscaler Internet Access Administrator。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Zscaler Internet Access Administrator 之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。
