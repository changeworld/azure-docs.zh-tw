---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 StatusPage 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 StatusPage 之間的單一登入。
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
ms.openlocfilehash: 40cb3c6e326cbacfbfb9879cfa92b5780d86fe99
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724199"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 StatusPage 整合

在本教學課程中，您將了解如何整合 StatusPage 與 Azure Active Directory (Azure AD)。
StatusPage 與 Azure AD 整合提供下列優點：

* 您可在 Azure AD 中控制可存取 StatusPage 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 StatusPage (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 StatusPage 的整合作業，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 StatusPage 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* StatusPage 支援由 **IDP** 起始的 SSO

## <a name="adding-statuspage-from-the-gallery"></a>從資源庫加入 StatusPage

若要設定 StatusPage 與 Azure AD 的整合作業，您必須從資源庫將 StatusPage 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **StatusPage**。
1. 從結果面板中選取 [StatusPage]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>設定和測試 StatusPage 的 Azure AD SSO

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 StatusPage 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 StatusPage 中相關使用者之間的連結關聯性。

若要設定及測試與 StatusPage 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[設定 StatusPage SSO](#configure-statuspage-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 StatusPage 測試使用者](#create-statuspage-test-user)** - 在 StatusPage 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [AskYourTeam] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [以 SAML 設定單一登入] 頁面上，執行下列步驟：

    a. 在 [識別碼] 文字方塊中，使用下列其中一種模式來輸入 URL：

    | 識別碼 |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：

     | 回覆 URL |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > 請透過 [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)連絡 StatusPage 支援小組，要求設定單一登入所需的中繼資料。 
    >
    > a. 從中繼資料複製簽發者值，然後貼至 [識別碼]  文字方塊中。
    >
    > b. 從中繼資料複製回覆 URL，然後貼至 [回覆 URL]  文字方塊中。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 StatusPage]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 StatusPage 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [StatusPage]  。

2. 在應用程式清單中，選取 [StatusPage]  。

3. 在左側功能表中，選取 [使用者和群組]  。

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-statuspage-sso"></a>設定 StatusPage SSO

1. 若要自動執行 StatusPage 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 StatusPage] 便會將您導向至 StatusPage 應用程式。 請從該處提供用以登入 StatusPage 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 StatusPage，請在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 StatusPage 公司網站。

1. 在主工具列中，按一下 [管理帳戶]  。

    ![此螢幕擷取畫面顯示從 StatusPage 公司網站選取的 [管理帳戶]。](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. 按一下 [單一登入]  索引標籤。

    ![此螢幕擷取畫面顯示 [單一登入] 索引標籤。](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. 在 [SSO 設定] 頁面上，執行下列步驟：

    ![此螢幕擷取畫面顯示 [SSO 設定] 頁面，您可以在其中輸入所述的值。](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![此螢幕擷取畫面顯示 [儲存組態] 按鈕。](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. 在 [SSO 目標 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    b. 在記事本中開啟下載的憑證，複製其內容，然後貼到 [憑證]  文字方塊中。

    c. 按一下 [儲存組態]  。

### <a name="create-statuspage-test-user"></a>建立 StatusPage 測試使用者

本節目標是在 StatusPage 中建立名為 Britta Simon 的使用者。

StatusPage 支援 Just-in-Time 佈建。 您已在[設定 Azure AD 單一登入](#configure-azure-ad-sso)中啟用該功能。

**若要在 StatusPage 中建立名為 Britta Simon 的使用者，請執行下列步驟：**

1. 以管理員身分登入您的 StatusPage 公司網站。

1. 在頂端功能表中，按一下 [ **管理帳戶**]。

    ![此螢幕擷取畫面顯示從 StatusPage 公司網站選取的 [管理帳戶]。](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. 按一下 [小組成員]  索引標籤。
  
    ![此螢幕擷取畫面顯示 [小組成員] 索引標籤。](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. 按一下 [新增小組成員]  。
  
    ![此螢幕擷取畫面顯示 [新增小組成員] 按鈕。](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. 在相關的文字方塊中，輸入您想要佈建之有效使用者的 [電子郵件地址]  、[名字]  和 [姓氏]  。 

    ![此螢幕擷取畫面顯示 [新增使用者] 對話方塊，您可以在其中輸入所述的值。](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. 針對 [角色]  ，選擇 [用戶端系統管理員]  。

1. 按一下 [建立帳戶]  。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入已設定 SSO 的 StatusPage

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [StatusPage] 圖格時，應該會自動登入已設定 SSO 的 StatusPage。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 StatusPage 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。
