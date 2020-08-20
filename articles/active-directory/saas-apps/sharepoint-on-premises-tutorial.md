---
title: 教學課程：Azure Active Directory 與 SharePoint 內部部署整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SharePoint 內部部署之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 996668751ee93d14b18e399035fd345f32c58fbe
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552168"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>教學課程：Azure Active Directory 單一登入與 SharePoint 內部部署整合

在本教學課程中，您將了解如何整合 SharePoint 內部部署與 Azure Active Directory (Azure AD)。 在整合 SharePoint 內部部署與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 SharePoint 內部部署的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SharePoint 內部部署。
* 在 Azure 入口網站中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SharePoint 內部部署整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* SharePoint 2013 伺服器陣列或更新版本。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入 (SSO)。 Azure AD 的使用者能夠存取您的 Sharepoint 內部部署。

## <a name="create-enterprise-applications-in-the-azure-portal"></a>在 Azure 入口網站中建立企業應用程式

若要設定將 SharePoint 內部部署整合到 Azure AD 中，您需要從資源庫將 SharePoint 內部部署新增到受控 SaaS 應用程式清單。

若要從資源庫新增 SharePoint 內部部署：

1. 在 [Azure 入口網站](https://portal.azure.com)最左邊的窗格中，選取 [Azure Active Directory]。

   > [!NOTE]
   > 如果無法使用此元素，您也可以透過最左邊窗格頂端的 [所有服務] 連結來開啟該元速。 在下列概觀中，[Azure Active Directory] 連結位於 [身分識別] 區段中。 您也可以使用篩選方塊加以搜尋。

1. 移至 [企業應用程式]，然後選取 [所有應用程式]。

1. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

1. 在搜尋方塊中，輸入 **SharePoint 內部部署**。 從結果窗格中選取 [SharePoint 內部部署]。

    <kbd>![結果清單中的 SharePoint 內部部署](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. 指定 SharePoint 內部部署執行個體的名稱，然後選取 [新增] 以新增應用程式。

1. 在新的企業應用程式中，選取 [屬性]，然後檢查 [需要使用者指派?] 的值。

   <kbd>![需要使用者指派? 切換](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   在本案例中，此值設定為「否」。

## <a name="configure-and-test-azure-ad"></a>設定並測試 Azure AD

在本節中，您會設定與 SharePoint 內部部署搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您會建立 Azure AD 使用者與 SharePoint 內部部署中相關使用者之間的連結關聯性。

若要使用 SharePoint 內部部署來設定並測試 Azure AD SSO，請完成下列建構元素：

- [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
- [設定 SharePoint 內部部署](#configure-sharepoint-on-premises)，以在應用程式端設定 SSO 設定。
- [在 Azure 入口網站中建立 Azure AD 測試使用者](#create-an-azure-ad-test-user-in-the-azure-portal)，以在 Azure AD 中建立新使用者進行 SSO。
- [在 Azure 入口網站中建立 Azure AD 安全性群組](#create-an-azure-ad-security-group-in-the-azure-portal)，以在 Azure AD 中建立新的安全性群組進行 SSO。
- [在 SharePoint 內部部署中將權限授予 Azure AD 帳戶](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises)，將權限提供給 Azure AD 使用者。
- [在 SharePoint 內部部署中將權限授予 Azure AD 群組](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises)，將權限提供給 Azure AD 群組。
- [在 Azure 入口網站中對來賓帳戶授與 SharePoint 內部部署的存取權](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)，以將 SharePoint 內部部署的權限提供給 Azure AD 中的來賓帳戶。
- [為多個 Web 應用程式設定信任的識別提供者](#configure-the-trusted-identity-provider-for-multiple-web-applications)，以對多個 Web 應用程式使用相同的受信任識別提供者。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD SSO。

若要設定與 SharePoint 內部部署搭配運作的 Azure AD SSO：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [Azure Active Directory] > [企業應用程式]。 選取先前建立的企業應用程式名稱，然後選取 [單一登入]。

1. 在 [選取單一登入方法] 對話方塊中，選取 [SAML] 模式以啟用 SSO。
 
1. 在 [以 SAML 設定單一登入] 頁面上選取 [編輯] 圖示，以開啟 [基本 SAML 組態] 對話方塊。

1. 在 [基本 SAML 組態] 區段中，遵循下列步驟：

    ![SharePoint 內部部署網域及 URL SSO 資訊](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. 在 [識別碼] 方塊中，輸入以下模式的 URL：`urn:<sharepointFarmName>:<federationName>`。

    1. 在 [回覆 URL] 方塊中，輸入以下模式的 URL：`https://<YourSharePointSiteURL>/_trust/`。

    1. 在 [登入 URL] 方塊中，輸入以下模式的 URL：`https://<YourSharePointSiteURL>/`。
    1. 選取 [儲存]。

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的登入 URL、識別碼及回覆 URL 來更新這些值。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取 [下載]以依據您的需求從指定選項下載 [憑證 (Base64)]，並將其儲存在電腦上。

    ![憑證下載連結](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. 在 [設定 SharePoint 內部部署] 區段中，根據您的需求複製適當的 URL：
    
    - **登入 URL**
    
        複製登入 URL，並將結尾的 **/saml2** 取代為 **/wsfed**，使其看起來像 https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed 。 (此 URL 不正確。)

    - **Azure AD 識別碼**
    - **登出 URL**

    > [!NOTE]
    > 此 URL 無法在 SharePoint 中按現狀使用。 您必須以 **/wsfed** 取代 **/saml2**。 SharePoint 內部部署應用程式使用 SAML 1.1 權杖，因此 Azure AD 會預期有來自 SharePoint 伺服器的 WS Fed 要求。 驗證之後，其會發出 SAML 1.1 權杖。

### <a name="configure-sharepoint-on-premises"></a>設定 SharePoint 內部部署

1. 在 SharePoint Server 2016 中建立新的受信任識別提供者。

    登入 SharePoint 伺服器，然後開啟 SharePoint 管理命令介面。 填寫值：
    - **$realm** 是 Azure 入口網站中 SharePoint 內部部署網域和 URL 區段中的識別碼值。
    - **$wsfedurl** 是 SSO 服務 URL。
    - **$filepath** 是將憑證檔案從 Azure 入口網站下載到其中的檔案路徑。

    執行下列命令來設定新的受信任識別提供者。

    > [!TIP]
    > 如果您不熟悉 PowerShell 的使用方式，或想要深入了解 PowerShell 的運作方式，請參閱 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)。


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. 為您的應用程式啟用信任的識別提供者。

    1. 在 [管理中心]，移至 [管理 Web 應用程式]，然後選取您想要以 Azure AD 保護的 Web 應用程式。

    1. 在功能區上，選取 [驗證提供者]，然後選擇您想要使用的區域。

    1. 選取 [信任的識別提供者]，然後選取您剛才註冊名為 *AzureAD* 的識別提供者。

    1. 選取 [確定]。

    ![設定您的驗證提供者](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure AD 測試使用者

本節的目標是在 Azure 入口網站中建立測試使用者。

1. 在 Azure 入口網站最左邊的窗格中，選取 [Azure Active Directory]。 在 [管理] 窗格中，選取 [使用者]。

1. 在畫面頂端選取 [所有使用者] > [新增使用者]。

1. 選取 [建立使用者]，然後在使用者屬性中，執行下列步驟。 您可能可以使用您的租用戶尾碼或任何已驗證的網域，在 Azure AD 中建立使用者。 

    1. 在 [名稱] 方塊中，輸入使用者名稱。 我們使用了 **TestUser**。
  
    1. 在 [使用者名稱] 方塊中，輸入 `TestUser@yourcompanydomain.extension`。 此範例顯示 `TestUser@contoso.com`。

       ![[使用者] 對話方塊](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    1. 選取 [建立]。

    1. 您現在可以與 TestUser@contoso.com 共用網站，並允許此使用者存取。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure AD 安全性群組

1. 選取 [Azure Active Directory] > [群組]。

1. 選取 [新增群組]。

1. 填寫 [群組類型]、[群組名稱]、[群組描述]和 [成員資格類型] 方塊。 選取箭號來選取成員，然後搜尋或選取您要新增至群組的成員。 選擇 [選取] 來新增所選的成員，然選取 [建立]。

![建立 Azure AD 安全性群組](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>在 SharePoint 內部部署中將權限授予 Azure AD 帳戶

若要將存取權授予 SharePoint 內部部署中的 Azure AD 使用者，請共用網站集合，或將 Azure AD 使用者新增至網站集合的其中一個群組。 使用者現在可以使用來自 Azure AD 的身分識別登入 SharePoint 201x，但此使用者體驗還有改善的空間。 例如，搜尋使用者會在人員選擇器中呈現多個搜尋結果。 在宣告對應中建立的每個宣告類型都有一個搜尋結果。 若要使用人員選擇器來選擇使用者，您必須精確地輸入使用者名稱，然後選擇 [name] 宣告結果。

![宣告搜尋結果](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

由於不會驗證您搜尋的值，因此可能會導致拼字錯誤或使用者不小心選擇錯誤的宣告類型。 這種情況可能會導致使用者無法成功存取資源。

為了修正人員選擇器的這種情況，有一個稱為 [AzureCP](https://yvand.github.io/AzureCP/) 的開放原始碼解決方案，可為 SharePoint 2013、2016 和 2019 提供自訂宣告提供者。 其會使用 Microsoft Graph API 來解析使用者的輸入內容並執行驗證。 如需詳細資訊，請參閱 [AzureCP](https://yvand.github.io/AzureCP/)。

  > [!NOTE]
  > 若沒有 AzureCP，您可藉由新增 Azure AD 群組的識別碼來新增群組，但這不是使用者容易記得且可靠的名稱。 其外觀如下所示：
  > 
  >![將 Azure AD 群組新增至 SharePoint 群組](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>在 SharePoint 內部部署中將權限授與 Azure AD 群組

若要將 Azure Active Directory 安全性群組指派給 SharePoint 內部部署，則必須使用適用於 SharePoint Server 的自訂宣告提供者。 此範例使用 AzureCP。

 > [!NOTE]
 > AzureCP 不是 Microsoft 產品，所以 Microsoft 支援服務不提供支援。 若要在內部部署 SharePoint 伺服器陣列上下載、安裝及設定 AzureCP，請參閱 [AzureCP](https://yvand.github.io/AzureCP/) 網站。 

1. 請在 SharePoint 內部部署伺服器陣列上設定 AzureCP，或設定替代的自訂宣告提供者解決方案。 若要設定 AzureCP，請參閱此 [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) 網站。

1. 在 Azure 入口網站中，選取 [Azure Active Directory] > [企業應用程式]。 選取先前建立的企業應用程式名稱，然後選取 [單一登入]。

1. 在 [以 SAM 設定單一登入]  頁面上，編輯 [使用者屬性與宣告] 區段。

1. 選取 [新增群組宣告]。

1. 選取宣告中應傳回哪些予使用者相關聯的群組。 在此案例中，選取 [所有群組]。 在 [來源屬性] 區段中，選取 [群組識別碼]，然後選取 [儲存]。

若要將存取權授予 SharePoint 內部部署中的 Azure AD 安全性群組，請共用網站集合，或將 Azure AD 安全性群組新增至網站集合的其中一個群組。

1. 瀏覽至 [SharePoint 網站集合]。 在網站集合的 [網站設定] 之下，選取 [人員和群組]。 

1. 選取 SharePoint 群組，然後選取 [新增] > [將使用者新增至此群組]。 當您輸入群組的名稱時，人員選擇器會顯示 Azure AD 安全性群組。

    ![將 Azure AD 群組新增至 SharePoint 群組](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>在 Azure 入口網站中將 SharePoint 內部部署的存取權授與來賓帳戶

因為 UPN 現在修改，您現在即可用一致的方式，將 SharePoint 網站的存取權授予來賓帳戶。 例如，使用者 `jdoe@outlook.com` 會以 `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` 表示。 若要與外部使用者共用您的網站，您需要在 Azure 入口網站的 [使用者屬性與宣告] 區段中新增一些修改。

1. 在 Azure 入口網站中，選取 [Azure Active Directory] > [企業應用程式]。 選取先前建立的企業應用程式名稱，然後選取 [單一登入]。

1. 在 [以 SAM 設定單一登入]  頁面上，編輯 [使用者屬性與宣告] 區段。

1. 在 [必要宣告] 區域中，選取 [唯一的使用者識別碼 (名稱識別碼)]。

1. 將 [來源屬性] 屬性值變更為 **user.localuserprincipalname**，然後選取 [儲存]。

    ![初始的使用者屬性與宣告](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. 使用功能區，返回 [SAML 型登入]。 現在 [使用者屬性與宣告] 區段如下所示： 

    ![最終的使用者屬性與宣告](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > 此安裝中不需要姓氏和名字。

1. 在 Azure 入口網站的最左邊窗格上，選取 [Azure Active Directory]，然後選取 [使用者]。

1. 選取 [新增來賓使用者]。

1. 選取 [邀請使用者] 選項。 填入使用者屬性，然後選取 [邀請]。

1. 您現在可以與 MyGuestAccount@outlook.com 共用網站，並允許此使用者存取。

    ![與來賓帳戶共用網站](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>為多個 Web 應用程式設定一個受信任的識別提供者

此組態適用於單一 Web 應用程式，但是如果您想要對多個 Web 應用程式使用相同受信任的識別提供者，則需要額外的組態。 例如，假設您已將 Web 應用程式擴充為使用 URL `https://sales.contoso.com`，而現在想要向 `https://marketing.contoso.com` 驗證使用者。 若要完成這項操作，請更新識別提供者以接受 WReply 參數，並且在 Azure AD 中更新應用程式註冊以新增回覆 URL。

1. 在 Azure 入口網站中，選取 [Azure Active Directory] > [企業應用程式]。 選取先前建立的企業應用程式名稱，然後選取 [單一登入]。

1. 在 [以 SAM 設定單一登入]  頁面上，編輯 [基本 SAML 組態]。

    ![基本 SAML 組態](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. 針對 [回覆 URL (判斷提示取用者服務 URL)]，新增其他 Web 應用程式的 URL，然後選取 [儲存]。

    ![編輯基本 SAML 組態](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. 在 SharePoint 伺服器上，開啟 SharePoint 201x 管理命令介面，然後執行下列命令。 使用您先前使用的受信任身分識別權杖簽發者名稱。
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. 在 [管理中心] 中，移至 Web 應用程式，並且啟用現有受信任的識別提供者。

在其他案例中，您可能想要為內部使用者授與 SharePoint 內部部署執行個體的存取權。 在此案例中，您必須部署 Microsoft Azure Active Directory Connect，允許同步處理內部部署使用者與 Azure AD。 此設定會在另一篇文章中討論。

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [什麼是 Azure Active Directory 的混合式身分識別？](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
