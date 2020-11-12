---
title: 教學課程：Azure Active Directory 與 Atlassian Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Atlassian Cloud 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330533"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>教學課程：整合 Atlassian Cloud 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Atlassian Cloud 與 Azure Active Directory (Azure AD)。 在整合 Atlassian Cloud 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Atlassian Cloud 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Atlassian Cloud。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Atlassian Cloud 單一登入 (SSO) 的訂用帳戶。
* 若要針對 Atlassian Cloud 產品啟用安全性聲明標記語言 (SAML) 單一登入，您必須設定 Atlassian Access。 深入了解 [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager) \(英文\)。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 

* Atlassian Cloud 支援由 **SP 和 IDP** 初始化的 SSO
* Atlassian Cloud 支援[自動化的使用者佈建和取消佈建](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>從資源庫新增 Atlassian Cloud

若要設定將 Atlassian Cloud 整合到 Azure AD 中，您需要從資源庫將 Atlassian Cloud 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Atlassian Cloud** 。
1. 從結果面板選取 [Atlassian Cloud]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Atlassian Cloud 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Atlassian Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 Atlassian Cloud 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[使用 Atlassian Cloud SSO 設定 Azure AD](#configure-azure-ad-sso)** - 讓您的使用者能夠搭配使用 Azure AD 型的 SAML SSO 與 Atlassian Cloud。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[建立 Atlassian Cloud 測試使用者](#create-atlassian-cloud-test-user)** ：使 Atlassian Cloud 中具有與 Azure AD 中的 B.Simon 連結的相對應使用者。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 若要自動執行 Atlassian Cloud 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 Atlassian Cloud] 便會將您導向到 Atlassian Cloud 應用程式。 請從該處提供用以登入 Atlassian Cloud 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Atlassian Cloud，請以管理員身分登入 Atlassian Cloud 公司網站，然後執行下列步驟。

1. 開始之前，請移至 Atlassian 產品執行個體，並複製/儲存執行個體 URL
   > [!NOTE]
   > URL 應符合 `https://<instancename>.atlassian.net` 模式

   ![執行個體名稱](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. 開啟 [Atlassian 管理員入口網站](https://admin.atlassian.com/)，然後按一下您的組織名稱

   ![組織](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. 您必須先驗證您的網域再繼續設定單一登入。 如需詳細資訊，請參閱 [Atlassian 網域驗證](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)文件。
1. 從 Atlassian 管理員入口網站畫面，選取左側隱藏式選單中的 [安全性]

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. 從 Atlassian 管理員入口網站的 [安全性] 畫面，選取左側隱藏式選單上的 [SAML 單一登入]

   ![saml sso](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. 按一下 [新增 SAML 設定] 並讓頁面保持開啟

   ![新增 SAML 設定](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![新增 SAML 設定 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. 在 Azure 入口網站的 [Atlassian Cloud] 應用程式整合頁面上，尋找 [管理] 區段並選取 [設定單一登入]。

   ![設定 SSO](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。

   ![azure 中的 saml](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. 在 [設定使用 SAML 的單一登入] 頁面上，向下捲動至 [設定 Atlassian Cloud]
   
   a. 按一下 [設定 URL]

   ![URL](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. 從 Azure 入口網站複製 [Azure AD 識別碼] 值，並將其貼到 Atlassian 中的 [識別提供者實體識別碼] 文字方塊中
   
   c. 從 Azure 入口網站複製 [登入 URL] 值，並將其貼到 Atlassian 中的 [識別提供者 SSO URL] 文字方塊中

   ![識別提供者 SSO URL](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![實體識別碼和 ss](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![簽署憑證](./media/atlassian-cloud-tutorial/certificate.png)

   ![憑證 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. 在 Atlassian 中 **新增/儲存** SAML 設定

1. 如果您想要以 **IDP** 起始的模式設定應用程式，請在 Azure 中編輯 [設定使用 SAML 的單一登入] 頁面的 [基本 SAML 設定] 區段，然後在 Atlassian 管理員入口網站上開啟 [SAML 單一登入頁面]

   a. 從 Atlassian 複製 [SP 實體識別碼] 值，並將其貼到 Azure 中的 [識別碼 (實體識別碼)] 方塊中，然後將其設定為預設值
   
   b. 從 Atlassian 複製 [SP 判斷提示取用者服務 URL] 值，並將其貼到 Azure 中的 [回復 URL (判斷提示取用者服務 URL)] 方塊中，然後將其設定為預設值
   
   c. 複製您在步驟 1 複製的 [執行個體 URL] 值，並將其貼到 Azure 中的 [轉送狀態] 方塊中

   ![複製 URL](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![編輯按鈕](./media/atlassian-cloud-tutorial/edit-button.png)

   ![URL 映像](./media/atlassian-cloud-tutorial/urls.png)
   
1. 如果您想要以 **SP** 起始的模式設定應用程式，請在 Azure 中編輯 [設定使用 SAML 的單一登入] 頁面的 [基本 SAML 設定] 區段。 複製您的 [執行個體 URL] (來自步驟 1)，並將其貼到 Azure 中的 [登入 URL] 方塊中

   ![URL 中的編輯按鈕](./media/atlassian-cloud-tutorial/edit-button.png)

   ![登入 URL](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. 您的 Atlassian Cloud 應用程式會預期要有特定格式的 SAML 判斷提示，這需要您將自訂屬性對應新增到您的「SAML 權杖屬性」設定。 您可以按一下 [編輯] 圖示來編輯屬性對應。 

   ![屬性](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. 具有 Microsoft 365 授權的 Azure AD 租用戶屬性對應
      
      a. 按一下 [唯一使用者識別碼 (名稱識別碼)] 宣告

      ![屬性與宣告](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud 預期 **nameidentifier** ( **唯一使用者識別碼** ) 會對應到使用者的電子郵件 ( **user.email** )。 編輯 [來源屬性]，並將其變更為 **user.mail** 。 儲存宣告的變更。

      ![唯一的使用者識別碼](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. 最後的屬性對應看起來應該如下所示。

      ![影像 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. 沒有 Microsoft 365 授權的 Azure AD 租用戶屬性對應 

      a. 按一下 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 宣告。

      ![影像 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. 雖然 Azure 不會針對在沒有 Microsoft 365 授權的 Azure AD 租用戶中建立的使用者填入 **user.mail** 屬性，並且會在 **userprincipalname** 屬性中儲存這類使用者的電子郵件。 Atlassian Cloud 預期 **nameidentifier** ( **唯一使用者識別碼** ) 會對應到使用者的電子郵件 ( **user.userprincipalname** )。  編輯 [來源屬性]，並將其變更為 **user.userprincipalname** 。 儲存宣告的變更。

      ![設定電子郵件](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. 最後的屬性對應看起來應該如下所示。

      ![影像 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Atlassian Cloud 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Atlassian Cloud]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-atlassian-cloud-test-user"></a>建立 Atlassian Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 Atlassian Cloud，請執行下列操作，在 Atlassian Cloud 中手動佈建使用者帳戶：

1. 在 [Administration] \(管理\)  窗格中，選取 [Users] \(使用者\)  。

    ![Atlassian Cloud [Users] \(使用者\) 連結](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. 若要在 Atlassian Cloud 中建立使用者，請選取 [Invite user] \(邀請使用者\)  。

    ![建立 Atlassian Cloud 使用者](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. 在 [Email address] \(電子郵件地址\)  方塊中，輸入使用者的電子郵件地址，然後指派應用程式存取權。

    ![Atlassian Cloud 使用者](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. 若要傳送電子郵件邀請給使用者，請選取 [Invite users] \(邀請使用者\)  。 電子郵件邀請會傳送給使用者，使用者在接受邀請之後，就會在系統中變成作用中使用者。

> [!NOTE]
> 您也可以在 [Users] \(使用者\)  區段中選取 [Bulk Create] \(大量建立\)  按鈕來大量建立使用者。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 Atlassian Cloud 登入 URL。  

* 直接移至 Atlassian Cloud 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Atlassian Cloud 

您也可以使用 Microsoft 存取面板，以任何模式測試應用程式。 當您按一下存取面板中的 Atlassian Cloud 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果在 IDP 模式中設定，您應該會自動登入已設定 SSO 的 Atlassian Cloud。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 Atlassian Cloud 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。