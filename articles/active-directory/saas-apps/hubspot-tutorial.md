---
title: 教學課程：Azure Active Directory 與 HubSpot 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 HubSpot 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 3b60ab21bb1e8f5270512d3f1e77aeab2baedc31
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968631"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>教學課程：Azure Active Directory 與 HubSpot 整合

在本教學課程中，您將了解如何整合 HubSpot 與 Azure Active Directory (Azure AD)。 在整合 HubSpot 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 HubSpot 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 HubSpot。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定與 HubSpot 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入的 HubSpot 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入並整合 HubSpot 與 Azure AD。

HubSpot 支援下列功能︰

* **SP 起始的單一登入**
* **由 IDP 起始的單一登入**

## <a name="adding-hubspot-from-the-gallery"></a>從資源庫新增 HubSpot

若要設定 HubSpot 與 Azure AD 整合，您需要從資源庫將 HubSpot 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **HubSpot**。
1. 從結果面板中選取 [HubSpot]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>設定和測試 HubSpot 的 Azure AD SSO

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 HubSpot 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 HubSpot 中相關使用者之間的連結關聯性。

若要設定及測試與 HubSpot 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

| Task | 描述 |
| --- | --- |
| **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** | 讓使用者能夠使用此功能。 |
| **[設定 HubSpot 單一登入](#configure-hubspot-single-sign-on)** | 在應用程式中設定單一登入設定。 |
| **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** | 為名為 Britta Simon 的使用者測試 Azure AD 單一登入。 |
| **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** | 讓 Britta Simon 能夠使用 Azure AD 單一登入。 |
| **[建立 HubSpot 測試使用者](#create-a-hubspot-test-user)** | 在 HubSpot 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。 |
| **[測試單一登入](#test-single-sign-on)** | 驗證組態是否能運作。 |

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

1. 在 Azure 入口網站的 [HubSpot] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 若要在 [基本 SAML 組態]  窗格中設定「IDP 起始模式」  ，請完成下列步驟：

    1. 在 **識別碼** 方塊中輸入具有下列模式的 URL：https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>。

    1. 在 **回覆 URL** 方塊中輸入具有下列模式的 URL：https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>。

    ![HubSpot 網域與 URL 單一登入資訊](common/idp-intiated.png)

    > [!NOTE]
    > 若要格式化 URL，您也可以參考 Azure 入口網站中 [基本 SAML 組態]  窗格所示的模式。

1. 若要在「SP 起始的模式」  中設定應用程式：

    1. 選取 [設定其他 URL]  。

    1. 在 [登入 URL]  方塊中，輸入 **https:\//app.hubspot.com/login**。

    ![[設定其他 URL] 選項](common/metadata-upload-additional-signon.png)

1. 在 [以 SAML 設定單一登入]  窗格的 [SAML 簽署憑證]  區段中，選取 [憑證 (Base64)]  旁邊的 [下載]  。 根據您的需求選取下載選項。 將憑證儲存在您的電腦上。

    ![憑證 (Base64) 下載選項](common/certificatebase64.png)

1. 在 [設定 HubSpot]  區段中，根據您的需求複製下列 URL：

    * 登入 URL
    * Azure AD 識別碼
    * 登出 URL

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>設定 HubSpot 單一登入

1. 在瀏覽器中開啟新索引標籤，登入您的 HubSpot 系統管理員帳戶。

1. 選取頁面右上角的 [設定]  圖示。

    ![HubSpot 中的設定圖示](./media/hubspot-tutorial/config1.png)

1. 選取 [帳戶預設值]  。

    ![HubSpot 中的帳戶預設值選項](./media/hubspot-tutorial/config2.png)

1. 向下捲動至 [安全性]  區段，然後選取 [設定]  。

    ![HubSpot 中的設定選項](./media/hubspot-tutorial/config3.png)

1. 在 [設定單一登入]  區段中，完成下列步驟：

    1. 在 [對象 URl (服務提供者實體識別碼)]  方塊中，選取 [複製]  來複製值。 在 Azure 入口網站的 [基本 SAML 組態]  窗格中，將值貼入 [識別碼]  方塊。

    1. 在 [登入 URl、ACS、收件者或重新導向]  方塊中，選取 [複製]  來複製值。 在 Azure 入口網站的 [基本 SAML 組態]  窗格中，將值貼入 [回覆 URL]  方塊。

    1. 在 HubSpot 中的 [識別提供者識別碼或簽發者 URL]  方塊中，貼上您在 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    1. 在 HubSpot 中的 [識別提供者單一登入 URL]  方塊中，貼上您在 Azure 入口網站複製的 [登入 URL]  值。

    1. 在 Windows 的「記事本」中，開啟您下載的憑證 (Base64) 檔案。 選取並複製該檔案的內容。 然後，將其貼入 HubSpot 中的 [X.509 憑證]  方塊。

    1. 選取 [驗證]  。

        ![HubSpot 中的設定單一登入區段](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
    1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
    1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
    1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
    1. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 HubSpot 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [HubSpot]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-a-hubspot-test-user"></a>建立 HubSpot 測試使用者

若要讓 Azure AD 使用者可以登入 HubSpot，則必須將他們佈建至 HubSpot。 在 HubSpot 中，需以手動方式佈建。

若要在 HubSpot 中佈建使用者帳戶：

1. 以系統管理員身分登入您的 HubSpot 公司網站。

1. 選取頁面右上角的 [設定]  圖示。

    ![HubSpot 中的設定圖示](./media/hubspot-tutorial/config1.png)

1. 選取 [使用者及小組]  。

    ![HubSpot 中的使用者及小組選項](./media/hubspot-tutorial/user1.png)

1. 選取 [建立使用者]  。

    ![HubSpot 中的建立使用者選項](./media/hubspot-tutorial/user2.png)

1. 在 [新增電子郵件地址]  方塊中，輸入格式為 brittasimon\@contoso.com 的使用者電子郵件地址，然後選取 [下一步]  。

    ![HubSpot 建立使用者區段中的新增電子郵件地址方塊](./media/hubspot-tutorial/user3.png)

1. 在 [建立使用者]  區段中，選取每個索引標籤。在每個索引標籤上，設定使用者的相關選項和權限。 然後，選取 [下一步]  。

    ![HubSpot 建立使用者區段中的索引標籤](./media/hubspot-tutorial/user4.png)

1. 若要傳送邀請給使用者，請選取 [傳送]  。

    ![HubSpot 中的傳送選項](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > 使用者會在接受邀請之後啟動。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 HubSpot 登入 URL。  

* 直接移至 HubSpot 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 HubSpot 

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您按一下「我的應用程式」中的 [HubSpot] 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 HubSpot。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 HubSpot 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)。