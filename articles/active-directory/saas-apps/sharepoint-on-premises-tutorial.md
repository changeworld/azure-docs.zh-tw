---
title: 教學課程：Azure Active Directory 與 SharePoint 內部部署整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SharePoint 內部部署之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867100"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SharePoint 內部部署整合

在本教學課程中，您將了解如何整合 SharePoint 內部部署與 Azure Active Directory (Azure AD)。 在整合 SharePoint 內部部署與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 SharePoint 內部部署的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SharePoint 內部部署。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure Active Directory 與 SharePoint 內部部署整合，您需要下列項目：

* Azure Active Directory 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* SharePoint 2013 伺服器陣列或更新版本。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure Active Directory 單一登入。 Azure Active Directory 的使用者將可以存取您的「Sharepoint 內部部署」。

## <a name="create-the-enterprise-applications-in-azure-portal"></a>在 Azure 入口網站中建立企業應用程式

若要設定將 SharePoint 內部部署整合到 Azure AD 中，您需要從資源庫將 SharePoint 內部部署新增到受控 SaaS 應用程式清單。

若要從資源庫新增 SharePoint 內部部署，請執行下列步驟：

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

 > [!NOTE]
 > 如果元素應該無法使用，也可以透過左側導覽窗格頂端的固定 [所有服務]  連結來開啟。 在下列概觀中，[Azure Active Directory]  連結位於 [身分識別]  區段中，或者可以透過使用篩選文字方塊來搜尋。

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

4. 在搜尋方塊中，輸入 **SharePoint 內部部署**，從結果面板中選取 [SharePoint 內部部署]  。

    <kbd>![結果清單中的 SharePoint 內部部署](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. 指定 SharePoint 內部部署的 [名稱]，然後按一下 [新增]  按鈕以新增應用程式。

1. 在新的企業應用程式中按一下 [屬性]，然後檢查 [需要使用者指派]  的值

   <kbd>![結果清單中的 SharePoint 內部部署](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

在我們的案例中，此值設定為「否」  。

## <a name="configure-and-test-azure-ad"></a>設定並測試 Azure AD

在本節中，您會設定與 SharePoint 內部部署搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SharePoint 內部部署中相關使用者之間的連結關聯性。

若要設定及測試與 SharePoint 內部部署搭配運作的 Azure Active Directory 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[設定 SharePoint 內部部署單一登入](#configure-sharepoint-on-premises)** - 在應用程式端設定單一登入設定。
1. **[在 Azure 入口網站中建立 Azure AD 測試使用者](#create-an-azure-ad-test-user-in-the-azure-portal)** - 在 Azure AD 中建立新使用者以進行單一登入。
1. **[在 Azure 入口網站中建立 Azure AD 安全性群組](#create-an-azure-ad-security-group-in-the-azure-portal)** - 在 Azure AD 中建立新的安全性群組以使用單一登入。
1. **[在 SharePoint 內部部署中將權限授與 Azure Active Directory 帳戶](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** - 將權限提供給 Azure AD 使用者。
1. **[在 SharePoint 內部部署中將權限授與 Azure AD 群組](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** - 將權限提供給 Azure AD 群組。
1. **[在 Azure 入口網站中，對來賓帳戶授與 SharePoint 內部部署的存取權](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** - 將 SharePoint 內部部署的權限提供給 Azure AD 中的來賓帳戶。
1. **[為多個 Web 應用程式設定信任的識別提供者](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** - 如何對多個 Web 應用程式使用相同的受信任身分識別提供者

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 SharePoint 內部部署搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟 Azure AD 目錄，依序按一下**企業應用程式**、**先前建立的企業應用程式名稱**，然後按一下 [單一登入]  。

2. 在 [選取單一登入方法]  對話方塊中，按一下 [SAML]  模式以啟用單一登入。
 
3. 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  圖示以開啟 [基本 SAML 設定]  對話方塊。

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![[SharePoint 內部部署網域及 URL] 單一登入資訊](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`urn:<sharepointFarmName>:<federationName>`

    1. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<YourSharePointSiteURL>/_trust/`

    1. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<YourSharePointSiteURL>/`
    1. 按一下 [儲存]。

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. 在 [設定 SharePoint 內部部署]  區段上，依據您的需求複製適當的 URL。
    
    1. **登入 URL**
    
        複製登入 URL，以 **/wsfed** 取代結尾的 **/saml2**，這看起來會像 **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** (此 URL 不正確)

    1. **Azure AD 識別碼**
    1. **登出 URL**
    > [!NOTE]
    > 在 SharePoint 中無法直接使用此 URL：您必須以 **/wsfed** 取代 **/saml2**。 SharePoint 內部部署應用程式使用 SAML 1.1 權杖，因此 Azure AD 會預期有來自 SharePoint 伺服器的 WS Fed 要求，然後會在驗證後發出 SAML 1.1 權杖。

### <a name="configure-sharepoint-on-premises"></a>設定 SharePoint 內部部署

1. **在 SharePoint Server 2016 中建立新的受信任身分識別提供者**

    登入 SharePoint 伺服器，然後開啟 SharePoint 管理命令介面。 填寫值：
    1. **$realm** (Azure 入口網站中 SharePoint 內部部署網域和 URL 區段的識別碼值)。
    1. **$wsfedurl** (單一登入服務 URL)。
   1. **$filepath** (將憑證檔案從 Azure 入口網站下載到其中的檔案路徑)。

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
1. **為您的應用程式啟用受信任的識別提供者**

    a. 在 [管理中心] 中，瀏覽至 [管理 Web 應用程式]  ，然後選取您想要以 Azure AD 保護的 Web 應用程式。

    b. 在功能區中，按一下 [驗證提供者]  ，然後選擇您想要使用的區域。

    c. 選取 [信任的身分識別提供者]  ，然後選取您剛才註冊名為 *AzureAD* 的身分識別提供者。

    d. 按一下 [確定]  。

    ![設定您的驗證提供者](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure AD 測試使用者

本節的目標是在 Azure 入口網站中建立測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]  ，並在 [管理]  窗格中選取 [使用者]  。

2. 然後依序選取畫面頂端的 [所有使用者]  和 [新增使用者]  。

3. 選取 [建立使用者]  選項，然後在 [使用者屬性] 中，執行下列步驟。  
   您可能可以使用您的租用戶尾碼或任何已驗證的網域，在 Azure AD 中建立使用者。 

    a. 在 [名稱]  欄位中輸入使用者名稱，我們使用 **TestUser**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `TestUser@yourcompanydomain.extension`  
    例如， TestUser@contoso.com

    ![[使用者] 對話方塊](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

    e. 您現在可以與 TestUser@contoso.com 共用網站，並允許此使用者存取網站。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure AD 安全性群組

1. 按一下 [Azure Active Directory] > [群組]  。

2. 按一下 [新增群組]  ：

3. 填寫 [群組類型]  、[群組名稱]  、[群組描述]  、[成員資格類型]  。 按一下箭頭來選取成員，然後搜尋或按一下您要新增至群組的成員。 按一下 [選取]  來新增選取的成員，然後按一下 [建立]  。

![建立 Azure AD 安全性群組](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>在 SharePoint 內部部署中授與權限給 Azure Active Directory 帳戶

若要在內部部署 SharePoint 中將存取權授與 Azure Active Directory 使用者，您必須共用網站集合，或將 Azure Active Directory 使用者新增至其中一個網站集合的群組。雖然使用者現在可以使用 Azure AD 的身分識別登入 SharePoint 201x，但仍有機會改善使用者體驗。 例如，搜尋使用者會在人員選擇器中呈現多個搜尋結果。 在宣告對應中建立的每個宣告類型都有一個搜尋結果。 若要使用人員選擇器來選擇使用者，您必須精確輸入使用者名稱，然後選擇 [name]  宣告結果。

![宣告搜尋結果](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

由於不會驗證您搜尋的值，因此可能會導致拼字錯誤或使用者不小心選擇錯誤的宣告類型。 這可能會導致使用者無法成功存取資源。

**為了修正人員選擇器**的這種情況，有一個稱為 [AzureCP](https://yvand.github.io/AzureCP/) 的開放原始碼解決方案，可為 SharePoint 2013、2016 和 2019 提供自訂宣告提供者。 該解決方案會使用 Microsoft Graph API 來解析使用者的輸入內容並執行驗證。 深入了解 [AzureCP](https://yvand.github.io/AzureCP/)。

  > [!NOTE]
  > 若沒有 AzureCP，您可以藉由新增 Azure AD 群組的識別碼來新增群組，但這不是使用者容易記得且可靠的名稱。 看起來會像這樣：  
  >   
  >![將 Azure AD 群組新增至 Sharepoint 群組](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>在 SharePoint 內部部署中將權限授與 Azure AD 群組

若要將 Azure Active Directory 安全性群組指派給 SharePoint 內部部署，則必須使用適用於 SharePoint Server 的自訂宣告提供者。 在我們的範例中，我們使用 AzureCP。

 > [!NOTE]
 > 請注意，AzureCP 並非 Microsoft 產品，無法使用 Microsoft 技術支援。 依循 https://yvand.github.io/AzureCP/ ，在內部部署 SharePoint 伺服器陣列上下載、安裝及設定 AzureCP。 

1. 請在 SharePoint 內部部署伺服器陣列上設定 AzureCP，或設定替代的自訂宣告提供者解決方案。 AzureCP 設定的步驟可在 https://yvand.github.io/AzureCP/Register-App-In-AAD.html 取得

1. 在 Azure 入口網站中，開啟 Azure AD 目錄。 依序按一下**企業應用程式**、**先前建立的企業應用程式名稱**，然後按一下 [單一登入]  。

1. 在 [以 SAM 設定單一登入]  頁面中，編輯 [使用者屬性與宣告]  區段。

1. 按一下 [新增群組宣告]  。

1. 選取要在宣告中傳回哪些與使用者相關聯的群組，在我們的案例中，請選取 [所有群組]  ，然後在 [來源屬性] 區段中選取 [群組識別碼]  ，並按一下 [儲存]  。

若要在內部部署 SharePoint 中將存取權授與 Azure Active Directory 安全性群組，您必須共用網站集合，或將 Azure Active Directory 安全性群組新增至其中一個網站集合的群組。

1. 瀏覽至 SharePoint 網站集合，在網站集合的 [網站設定] 底下，按一下 [人員和群組]。 選取 SharePoint 群組，然後按一下 [新增]、[將使用者新增至此群組] 並開始輸入群組的名稱。此時人員選擇器會顯示 Azure Active Directory 安全性群組。

    ![將 Azure AD 群組新增至 Sharepoint 群組](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>在 Azure 入口網站中將 SharePoint 內部部署的存取權授與來賓帳戶

您現在可以用一致的方式，將 SharePoint 網站的存取權授與來賓帳戶。 UPN 也剛好進行了修改。 使用 jdoe@outlook.com 的使用者將會以 `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` 的形式呈現。 為了在與外部使用者共用您的網站時，可以獲得順暢的體驗，您必須在 Azure 入口網站中的 [使用者屬性與宣告]  區段內新增一些修改。

1. 在 Azure 入口網站中，開啟 Azure AD 目錄。 依序按一下**企業應用程式**、**先前建立的企業應用程式名稱**，然後按一下 [單一登入]  。

1. 在 [以 SAM 設定單一登入]  頁面中，編輯 [使用者屬性與宣告]  區段。

1. 在 [必要宣告]  區域中，按一下 [唯一的使用者識別碼 (名稱識別碼)]  。

1. 將 [來源屬性]  的屬性值變更為 **user.localuserprincipalname**，然後**儲存**。

    ![初始的使用者屬性與宣告](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. 使用功能區返回**以 SAML 為基礎的登入**，現在 [使用者屬性與宣告]  區段應如下所示： 

    ![最終的使用者屬性與宣告](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > 此安裝中不需要姓氏和名字。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]  ，然後選取 [使用者]  。

1. 按一下 [新增來賓使用者] 

1. 選取 [邀請使用者]  選項，並填寫使用者的屬性，然後按一下 [邀請]  。

1. 您現在可以與 MyGuestAccount@outlook.com 共用網站，並允許此使用者存取。

    ![與來賓帳戶共用網站](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>對多個 Web 應用程式設定一個受信任的識別提供者

此組態適用於單一 Web 應用程式，但是如果您想要對多個 Web 應用程式使用相同受信任的識別提供者，則需要額外的組態。 例如，假設我們已將 Web 應用程式擴充為使用 URL `https://sales.contoso.com`，而現在也想要向 `https://marketing.contoso.com` 驗證使用者。 若要完成這項操作，我們必須更新識別提供者以接受 WReply 參數，並且在 Azure AD 中更新應用程式註冊以新增回覆 URL。

1. 在 Azure 入口網站中，開啟 Azure AD 目錄。 依序按一下**企業應用程式**、**先前建立的企業應用程式名稱**，然後按一下 [單一登入]  。

2. 在 [以 SAM 設定單一登入]  頁面中，編輯 [基本 SAML 設定]  。

    ![編輯基本 SAML 設定](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. 在 [回覆 URL (判斷提示取用者服務 URL)]  中新增其他 Web 應用程式的 URL，然後按一下 [儲存]  。

    ![編輯基本 SAML 設定](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. 在 SharePoint 伺服器上，開啟 [SharePoint 201x 管理命令介面]  ，然後使用您先前使用的受信任身分識別權杖簽發者名稱，來執行下列命令。
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. 在 [管理中心] 內，移至 Web 應用程式，並且啟用現有受信任的識別提供者。

您也可以為內部使用者授與內部部署 Sharepoint 的存取權。在此情況中，您必須部署 Microsoft Azure Active Directory Connect，以允許同步您的內部部署使用者與 Azure Active Directory，這項設定會在另一篇文章中說明。 

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [什麼是 Azure Active Directory 的混合式身分識別？](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
