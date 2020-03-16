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
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: bd9e916d1e50d9d5bc0717a1d283b3dcf5d383c9
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037015"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>教學課程：Azure Active Directory 與 SharePoint 內部部署整合

在本教學課程中，您將了解如何整合 SharePoint 內部部署與 Azure Active Directory (Azure AD)。
將 SharePoint 內部部署與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 SharePoint 內部部署的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 SharePoint 內部部署 (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SharePoint 內部部署整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 SharePoint 內部部署單一登入功能的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SharePoint 內部部署支援 **SP** 起始的 SSO

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>從資源庫新增 SharePoint 內部部署

若要設定將 SharePoint 內部部署整合到 Azure AD 中，您需要從資源庫將 SharePoint 內部部署新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 SharePoint 內部部署，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

    > [!NOTE]   
    > 如果元素應該無法使用，也可以透過左側導覽窗格頂端的固定 [所有服務]  連結來開啟。 在下列概觀中，[Azure Active Directory]  連結位於 [身分識別]  區段中，或者可以透過使用篩選文字方塊來搜尋。

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **SharePoint 內部部署**，從結果面板中選取 [SharePoint 內部部署]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 SharePoint 內部部署](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 SharePoint 內部部署來設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SharePoint 內部部署中相關使用者之間的連結關聯性。

若要設定及測試與 SharePoint 內部部署搭配運作的 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SharePoint 內部部署單一登入](#configure-sharepoint-on-premises-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[在 Azure 入口網站中建立 Azure AD 安全性群組](#create-an-azure-ad-security-group-in-the-azure-portal)** - 在 Azure AD 中啟用新的安全性群組以使用單一登入。
5. **[授與 SharePoint 內部部署安全性群組的存取權](#grant-access-to-sharepoint-on-premises-security-group)** - 將特定群組的存取權授與 Azure AD。
6. **[在 Azure 入口網站中指派 Azure AD 安全性群組](#assign-the-azure-ad-security-group-in-the-azure-portal)** - 將特定群組指派給 Azure AD 以進行驗證。
7. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 SharePoint 內部部署搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SharePoint 內部部署]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![[SharePoint 內部部署網域及 URL] 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`urn:sharepoint:federation`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [SharePoint 內部部署用戶端支援小組](https://support.office.com/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

    > [!Note]
    > 請記下您已將憑證檔案下載到其中的檔案路徑，因為您稍後需要在 PowerShell 指令碼中使用它來進行設定。

6. 在 [設定 SharePoint 內部部署]  區段上，依據您的需求複製適當的 URL。 針對**單一登入服務 URL**，請使用下列模式的值：`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ 是 Azure AD 訂用帳戶的租用戶識別碼。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    > [!NOTE]
    > SharePoint 內部部署應用程式使用 SAML 1.1 權杖，因此 Azure AD 會預期有來自 SharePoint 伺服器的 WS Fed 要求，然後會在驗證後發出 SAML 1.1 權杖。

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>設定 SharePoint 內部部署單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 SharePoint 內部部署公司網站。

2. **在 SharePoint Server 2016 中設定新的受信任身分識別提供者**

    登入 SharePoint Server 2016 伺服器，然後開啟 SharePoint 2016 管理命令介面。 從 Azure 入口網站填入 $realm (Azure 入口網站中，來自 SharePoint 內部部署網域及 URL 區段的識別碼值)、$wsfedurl (單一登入服務 URL) 及 $filepath (您已將憑證檔案下載到其中的檔案路徑) 的值，然後執行下列命令來設定信任的新身分識別提供者。

    > [!TIP]
    > 如果您不熟悉 PowerShell 的使用方式，或想要深入了解 PowerShell 的運作方式，請參閱 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)。

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    接著，依照下列步驟為您的應用程式啟用受信任的身分識別提供者：

    a. 在 [管理中心] 中，瀏覽至 [管理 Web 應用程式]  ，然後選取您想要以 Azure AD 保護的 Web 應用程式。

    b. 在功能區中，按一下 [驗證提供者]  ，然後選擇您想要使用的區域。

    c. 選取 [信任的身分識別提供者]  ，然後選取您剛才註冊名為 *AzureAD* 的身分識別提供者。

    d. 在 [登入頁面 URL] 設定中，選取 [自訂登入頁面]  並提供值 “/_trust/”。

    e. 按一下 [確定]  。

    ![設定您的驗證提供者](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > 某些外部使用者將無法使用這個單一登入整合，因為他們的 UPN 將含有類似 `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com` 的損害值。 我們很快地將會為客戶提供如何根據使用者類型來處理 UPN 的應用程式設定。 在那之後，您的所有來賓使用者應該都能以組織員工的身分順暢地使用 SSO。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure AD 安全性群組

1. 按一下 [Azure Active Directory] > [所有群組]  。

    ![建立 Azure AD 安全性群組](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. 按一下 [新增群組]  ：

    ![建立 Azure AD 安全性群組](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. 填寫 [群組類型]  、[群組名稱]  、[群組描述]  、[成員資格類型]  。 按一下箭頭來選取成員，然後搜尋或按一下您要新增至群組的成員。 按一下 [選取]  來新增選取的成員，然後按一下 [建立]  。

    ![建立 Azure AD 安全性群組](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > 若要將 Azure Active Directory 安全性群組指派給內部部署的 SharePoint，必須在內部部署的 SharePoint 伺服器陣列中安裝和設定 [AzureCP](https://yvand.github.io/AzureCP/)，或是為 SharePoint 開發及設定替代的自訂宣告提供者。  如果您不是使用 AzureCP，請參閱文件底部的詳細資訊一節，了解如何建立您自己的自訂宣告提供者。

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>授與 SharePoint 內部部署安全性群組的存取權

**在應用程式註冊上設定安全性群組與權限**

1. 在 Azure 入口網站中選取 [Azure Active Directory]  ，然後選取 [應用程式註冊]  。

    ![企業應用程式刀鋒視窗](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. 在搜尋方塊中，輸入並選取 [SharePoint 內部部署]  。

    ![結果清單中的 SharePoint 內部部署](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. 按一下 [資訊清單]  。

    ![資訊清單選項](./media/sharepoint-on-premises-tutorial/manifest.png)

4. 將 `groupMembershipClaims`: `NULL` 修改為 `groupMembershipClaims`: `SecurityGroup`。 然後按一下 [儲存]

    ![編輯資訊清單](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. 按一下 [設定]  ，然後按一下 [必要權限]  。

    ![所需的權限](./media/sharepoint-on-premises-tutorial/settings.png)

6. 按一下 [新增]  ，然後**選取 API**。

    ![API 存取](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. 新增 **Windows Azure Active Directory** 和 **Microsoft Graph API**，但一次只能選取一個。

    ![選取 API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. 選取 [Windows Azure Active Directory]，核取 [讀取目錄資料] 並按一下 [選取]。 返回並新增 Microsoft Graph，然後也為其選取 [讀取目錄資料]。  按一下 [選取]，然後按一下 [完成]。

    ![啟用存取](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. 現在，在 [必要設定] 底下按一下 [授與權限]  ，然後按一下 [是] 來授與權限。

    ![授與權限](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > 請檢查通知，判斷是否已成功授與權限。  如果未成功授與權限，則 AzureCP 將無法正常運作，並且無法對內部部署 SharePoint 設定 Azure Active Directory 安全性群組。

10. 請在 SharePoint 內部部署伺服器陣列上設定 AzureCP，或設定替代的自訂宣告提供者解決方案。  在此範例中，我們使用 AzureCP。

    > [!NOTE]
    > 請注意，AzureCP 並非 Microsoft 產品，無法使用 Microsoft 技術支援。 依循 [https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/)，在內部部署 SharePoint 伺服器陣列上下載、安裝及設定 AzureCP 

11. **在內部部署 SharePoint 中授與 Azure Active Directory 安全性群組的存取權**：群組必須能存取內部部署 SharePoint 中的應用程式。  您可以使用下列步驟來設定存取 Web 應用程式的權限。

12. 在 [管理中心] 內按一下 [應用程式管理] 和 [管理 Web 應用程式]，然後選取 Web 應用程式來啟用功能區，最後按一下 [使用者原則]。

    ![管理中心](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. 在 [Web 應用程式原則] 底下按一下 [新增使用者]，選取區域，然後按 [下一步]。  按一下 [通訊錄]。

    ![Web 應用程式原則](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. 然後，搜尋並新增 Azure Active Directory 安全性群組，並按一下 [確定]。

    ![新增安全性群組](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. 選取權限，然後按一下 [完成]。

    ![新增安全性群組](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. 檢查 [Web 應用程式原則]，您會看到 Azure Active Directory 群組已新增。  群組宣告會顯示該使用者名稱的 Azure Active Directory 安全性群組物件識別碼。

    ![新增安全性群組](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. 瀏覽至 SharePoint 網站集合，並在該處新增群組。 按一下 [網站設定]，然後按一下 [網站權限及授與權限]。  搜尋群組角色宣告、指派權限等級，然後按一下 [共用]。

    ![新增安全性群組](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>對多個 Web 應用程式設定一個受信任的識別提供者

此組態適用於單一 Web 應用程式，但是如果您想要對多個 Web 應用程式使用相同受信任的識別提供者，則需要額外的組態。 例如，假設我們已將 Web 應用程式擴充為使用 URL `https://portal.contoso.local`，而現在也想要向 `https://sales.contoso.local` 驗證使用者。 若要完成這項操作，我們必須更新識別提供者以接受 WReply 參數，並且在 Azure AD 中更新應用程式註冊以新增回覆 URL。

1. 在 Azure 入口網站中，開啟 Azure AD 目錄。 按一下 [應用程式註冊]  ，然後按一下 [檢視所有應用程式]  。 按一下您先前建立的應用程式 (SharePoint SAML 整合)。

2. 按一下 [設定]  。

3. 在 [設定] 刀鋒視窗中，按一下 [回覆 URL]  。

4. 為額外的 Web 應用程式新增 URL，將 `/_trust/default.aspx` 附加到 URL (例如 `https://sales.contoso.local/_trust/default.aspx`)，然後按一下 [儲存]  。

5. 在 SharePoint 伺服器上，開啟 [SharePoint 2016 管理命令介面]  ，然後使用您先前使用的受信任身分識別權杖簽發者名稱，來執行下列命令。

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. 在 [管理中心] 內，移至 Web 應用程式，並且啟用現有受信任的識別提供者。 請記得也要將登入頁面 URL 設定為自訂登入頁面 `/_trust/`。

7. 在 [管理中心] 內，按一下 Web 應用程式，然後選擇 [使用者原則]  。 如本文中先前的示範，新增具有適當權限的使用者。

### <a name="fixing-people-picker"></a>修正人員選擇器

使用者現在可以使用來自 Azure AD 的身分識別登入 SharePoint 2016，但此使用者體驗還有改善的空間。 例如，搜尋使用者會在人員選擇器中呈現多個搜尋結果。 在宣告對應中建立之 3 種宣告類型的每種類型都有一個搜尋結果。 若要使用人員選擇器來選擇使用者，您必須精確輸入使用者名稱，然後選擇 [name]  宣告結果。

![宣告搜尋結果](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

由於不會驗證您搜尋的值，因此可能會導致拼字錯誤或使用者不小心選擇錯誤的宣告類型 (例如 **SurName** 宣告) 來進行指派。 這可能會導致使用者無法成功存取資源。

為了協助應付這種情況，有一個稱為 [AzureCP](https://yvand.github.io/AzureCP/) 的開放原始碼解決方案，可為 SharePoint 2016 提供自訂宣告提供者。 該解決方案會使用 Microsoft Graph API 來解析使用者的輸入內容並執行驗證。 深入了解 [AzureCP](https://yvand.github.io/AzureCP/)。

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 入口網站中指派 Azure AD 安全性群組

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [SharePoint 內部部署]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入並選取 [SharePoint 內部部署]  。

    ![應用程式清單中的 SharePoint 內部部署連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 搜尋您要使用的安全性群組，然後按一下該群組，將其新增至 [選取成員] 區段。 按一下 [選取]  ，然後按一下 [指派]  。

    ![搜尋安全性群組](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > 檢查 Azure 入口網站功能表列中的通知，以取得群組已成功指派至企業應用程式的通知。

### <a name="create-sharepoint-on-premises-test-user"></a>建立 SharePoint 內部部署測試使用者

在本節中，您會在 SharePoint 內部部署中建立名為 Britta Simon 的使用者。 請與  [SharePoint 內部部署支援小組](https://support.office.com/) 合作，在 SharePoint 內部部署平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SharePoint 內部部署] 圖格時，應該會自動登入您設定 SSO 的 SharePoint 內部部署。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
