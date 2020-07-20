---
title: 教學課程：Azure Active Directory 與 Atlassian Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Atlassian Cloud 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c48d6f7d751e6d767921fad21e6213c6b1203b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384019"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>教學課程：整合 Atlassian Cloud 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Atlassian Cloud 與 Azure Active Directory (Azure AD)。 在整合 Atlassian Cloud 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Atlassian Cloud 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Atlassian Cloud。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Atlassian Cloud 單一登入 (SSO) 的訂用帳戶。
* 若要針對 Atlassian Cloud 產品啟用安全性聲明標記語言 (SAML) 單一登入，您必須設定 Atlassian Access。 深入了解 [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager) \(英文\)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 

* Atlassian Cloud 支援由 **SP 和 IDP** 初始化的 SSO
* Atlassian Cloud 支援[自動化的使用者佈建和取消佈建](atlassian-cloud-provisioning-tutorial.md)
* 設定 Atlassian Cloud 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
## <a name="adding-atlassian-cloud-from-the-gallery"></a>從資源庫新增 Atlassian Cloud

若要設定將 Atlassian Cloud 整合到 Azure AD 中，您需要從資源庫將 Atlassian Cloud 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Atlassian Cloud**。
1. 從結果面板選取 [Atlassian Cloud]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Atlassian Cloud 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Atlassian Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 Atlassian Cloud 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Atlassian Cloud 單一登入](#configure-atlassian-cloud-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Atlassian Cloud 測試使用者](#create-atlassian-cloud-test-user)** ：使 Atlassian Cloud 中具有與 Azure AD 中的 B.Simon 連結的相對應使用者。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Atlassian Cloud]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://auth.atlassian.com/saml/<unique ID>`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. 按一下 [設定額外的 URL]  。

    d. 在 [轉送狀態]  文字方塊中，使用下列模式輸入 URL：`https://<instancename>.atlassian.net`

    > [!NOTE]
    > 上述值並非真正的值。 請使用實際的識別碼和回覆 URL 來更新這些值。 您會從 [Atlassian Cloud SAML 組態]  畫面取得這些真正的值，本教學課程稍後會在＜**設定 Atlassian Cloud 單一登入**＞的步驟 7 中說明。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<instancename>.atlassian.net`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 貼上來自您用來登入 Atlassian Cloud 管理入口網站的執行個體值。

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. 您的 Atlassian Cloud 應用程式會預期要有特定格式的 SAML 判斷提示，這需要您將自訂屬性對應新增到您的「SAML 權杖屬性」設定。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 Atlassian Cloud 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯]  圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [安裝 Atlassian Cloud]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

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

在本節中，您會將 Atlassian Cloud 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Atlassian Cloud]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-atlassian-cloud-sso"></a>設定 Atlassian Cloud 單一登入

1. 若要自動執行 Atlassian Cloud 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 Atlassian Cloud]  便會將您導向到 Atlassian Cloud 應用程式。 請從該處提供用以登入 Atlassian Cloud 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Atlassian Cloud，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Atlassian Cloud 公司網站，然後執行下列步驟：

1. 您必須先驗證您的網域再繼續設定單一登入。 如需詳細資訊，請參閱 [Atlassian 網域驗證](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)文件。

1. 在左側窗格中，選取 [安全性]   >  [SAML 單一登入]  。 如果您尚未這樣做，請訂閱 Atlassian Identity Manager。

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. 在 [Add SAML configuration] \(新增 SAML 設定\)  視窗中，執行下列操作：

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. 在 [識別提供者實體識別碼]  方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  。

    b. 在 [識別提供者 SSO URL]  方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。

    c. 開啟從 Azure 入口網站下載的憑證 (.txt 檔案)，複製值 (不含 *Begin Certificate* 和 *End Certificate* 行)，然後將它貼到 [Public X509 certificate] \(公用 X509 憑證\)  方塊中。

    d. 按一下 [儲存組態]  。

1. 若要確定您已設定正確的 URL，請執行下列操作來更新 Azure AD 設定：

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. 在 SAML 視窗中，複製 [SP 身分識別識別碼]  ，然後在 Azure 入口網站中，將它貼到 Atlassian Cloud [基本 SAML 組態]  底下的 [識別碼]  方塊中。

    b. 在 SAML 視窗中，複製 [SP 判斷提示取用者服務 URL]  ，然後在 Azure 入口網站中，將它貼到 Atlassian Cloud [基本 SAML 組態]  底下的 [回覆 URL]  方塊中。 登入 URL 是您 Atlassian Cloud 的租用戶 URL。

    > [!NOTE]
    > 如果您是現有的客戶，在更新 Azure 入口網站中的 [SP Identity ID] \(SP 身分識別識別碼\)  和 [SP Assertion Consumer Service URL] \(SP 判斷提示取用者服務 URL\)  值之後，請選取 [Yes, update configuration] \(是，更新設定\)  。 如果您是新客戶，則可以略過這個步驟。

### <a name="create-atlassian-cloud-test-user"></a>建立 Atlassian Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 Atlassian Cloud，請執行下列操作，在 Atlassian Cloud 中手動佈建使用者帳戶：

1. 在 [Administration] \(管理\)  窗格中，選取 [Users] \(使用者\)  。

    ![Atlassian Cloud [Users] \(使用者\) 連結](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. 若要在 Atlassian Cloud 中建立使用者，請選取 [Invite user] \(邀請使用者\)  。

    ![建立 Atlassian Cloud 使用者](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. 在 [Email address] \(電子郵件地址\)  方塊中，輸入使用者的電子郵件地址，然後指派應用程式存取權。

    ![建立 Atlassian Cloud 使用者](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. 若要傳送電子郵件邀請給使用者，請選取 [Invite users] \(邀請使用者\)  。 電子郵件邀請會傳送給使用者，使用者在接受邀請之後，就會在系統中變成作用中使用者。

> [!NOTE]
> 您也可以在 [Users] \(使用者\)  區段中選取 [Bulk Create] \(大量建立\)  按鈕來大量建立使用者。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Atlassian Cloud] 圖格時，應該會自動登入您設定 SSO 的 Atlassian Cloud。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Atlassian Cloud](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Atlassian Cloud](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)