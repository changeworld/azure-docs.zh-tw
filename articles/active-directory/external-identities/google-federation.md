---
title: 將 Google 新增為 B2B 的識別提供者 - Azure AD
description: 與 Google 同盟，讓來賓使用者能夠使用自己的 Gmail 帳戶來登入您的 Azure AD 應用程式。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926230"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>將 Google 新增為 B2B 來賓使用者的識別提供者

藉由設定與 Google 的同盟，您可以讓受邀的使用者使用自己的 Gmail 帳戶來登入您的共用應用程式和資源，而不需要建立 Microsoft 帳戶。 

> [!NOTE]
> Google 同盟是專為 Gmail 使用者所設計。 若要與 G Suite 網域同盟，請使用 [直接同盟](direct-federation.md)。

## <a name="what-is-the-experience-for-the-google-user"></a>Google 使用者有何體驗？
當您將邀請傳送給 Google Gmail 使用者時，來賓使用者應使用包含租使用者內容的連結來存取您共用的應用程式或資源。 其體驗會因為是否已登入 Google 而有所不同：
  - 未登入 Google 的來賓使用者將會收到提示。
  - 系統會提示已登入 Google 的來賓使用者選擇他們想要使用的帳戶。 他們必須選擇您用來邀請他們的帳戶。

看到「標頭太久」錯誤的來賓使用者可以清除其 cookie，或開啟私用或 incognito 視窗，然後再次嘗試登入。

![顯示 Google 登入頁面的螢幕擷取畫面。](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>限制

Teams 完全支援所有裝置上的 Google 來賓使用者。 Google 使用者可以從一般端點 (例如 `https://teams.microsoft.com`) 登入 Teams。

其他應用程式的一般端點可能不支援 Google 使用者。 Google 來賓使用者必須使用包含租使用者資訊的連結來登入。 以下是範例：
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   如果 Google 來賓使用者嘗試使用或之類的連結 `https://myapps.microsoft.com` `https://portal.azure.com` ，則會收到錯誤訊息。

只要連結包含您的租使用者資訊，您也可以將應用程式或資源的直接連結授與 Google 來賓使用者。 例如： `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`。 

## <a name="step-1-configure-a-google-developer-project"></a>步驟 1:設定 Google 開發人員專案
首先，在 Google 開發人員主控台中建立新的專案，以取得用戶端識別碼和用戶端密碼，稍後您可以新增至 Azure Active Directory (Azure AD) 。 
1. 移至 Google API (網址為： https://console.developers.google.com )，並使用 Google 帳戶來登入。 建議您使用共用小組的 Google 帳戶。
2. 如果系統提示您，請接受服務條款。
3. 建立新專案：在儀表板上，選取 [ **建立專案** ]，為專案命名 (例如 **Azure AD B2B** ) ，然後選取 [ **建立** ]： 
   
   ![顯示新專案頁面的螢幕擷取畫面。](media/google-federation/google-new-project.png)

4. 在 [ **api & 服務** ] 頁面上，選取新專案下的 [ **View** ]。

5. 在 [Api] 卡片上，選取 [ **移至 api] 總覽** 。 選取 **OAuth 同意畫面** 。

6. 選取 [外部]，然後選取 [建立]。 

7. 在 **OAuth 同意畫面** 上，輸入 **應用程式名稱** ：

   ![顯示 Google OAuth 同意畫面的螢幕擷取畫面。](media/google-federation/google-oauth-consent-screen.png)

8. 滾動至 [ **授權的網域** ] 區段，然後輸入 **microsoftonline.com** ：

   ![顯示授權網域區段的螢幕擷取畫面。](media/google-federation/google-oauth-authorized-domains.PNG)

9. 選取 [儲存]。

10. 選取 [Credentials]  \(認證\)。 在 [ **建立認證** ] 功能表上，選取 [ **OAUTH 用戶端識別碼** ：

    ![顯示 Google Api 建立認證功能表的螢幕擷取畫面。](media/google-federation/google-api-credentials.png)

11. 在 [應用程式類型]  下方，選取 [Web 應用程式]  。 為應用程式提供適當的名稱，例如 **AZURE AD B2B** 。 在 [ **授權重新導向 uri** ] 下，輸入下列 uri：
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br> (，其中 `<tenant ID>` 是您的租使用者識別碼) 
   
    > [!NOTE]
    > 若要尋找您的租使用者識別碼，請移至 [Azure 入口網站](https://portal.azure.com)。 在 [ **Azure Active Directory** ] 下，選取 [ **屬性** ]，然後複製 **租使用者識別碼** 。

    ![顯示授權重新導向 Uri 區段的螢幕擷取畫面。](media/google-federation/google-create-oauth-client-id.png)

12. 選取 [建立]。 複製用戶端識別碼和用戶端密碼。 當您在 Azure 入口網站中新增識別提供者時，將會使用它們。

    ![顯示 OAuth 用戶端識別碼和用戶端密碼的螢幕擷取畫面。](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>步驟 2:在 Azure AD 中設定 Google 同盟 
您現在將設定 Google 用戶端識別碼和用戶端密碼。 您可以使用 Azure 入口網站或 PowerShell 來執行此動作。 請務必邀請自己來測試您的 Google 同盟設定。 使用 Gmail 位址，並嘗試使用您受邀的 Google 帳戶兌換邀請。 

**在 Azure 入口網站中設定 Google 同盟** 
1. 移至 [Azure 入口網站](https://portal.azure.com)。 在左窗格上，選取 [Azure Active Directory]。 
2. 選取 [外部身分識別]。
3. 選取 [ **所有身分識別提供者** ]，然後選取 [ **Google** ] 按鈕。
4. 輸入您稍早取得的用戶端識別碼和用戶端密碼。 選取 [ **儲存** ]： 

   ![顯示 [新增 Google 身分識別提供者] 頁面的螢幕擷取畫面。](media/google-federation/google-identity-provider.png)

**使用 PowerShell 設定 Google 同盟**
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行這個命令：`Connect-AzureAD`
3. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。  
4. 執行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > 使用您在「步驟1：設定 Google 開發人員專案」中建立之應用程式的用戶端識別碼和用戶端密碼。 如需詳細資訊，請參閱 [AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview)。 
 
## <a name="how-do-i-remove-google-federation"></a>如何移除 Google 同盟？
您可以刪除 Google 同盟設定。 如果您這樣做，已兌換其邀請的 Google 來賓使用者將無法登入。 但是，您可以從目錄中刪除資源並重新邀請，讓他們再次存取您的資源。 
 
**在 Azure AD 入口網站中刪除 Google 同盟**
1. 移至 [Azure 入口網站](https://portal.azure.com)。 在左窗格上，選取 [Azure Active Directory]。 
2. 選取 [外部身分識別]。
3. 選取 [所有識別提供者]。
4. 在 **Google** 行上，選取省略號按鈕 ( **...** ) 然後選取 [ **刪除** ]。 
   
   ![顯示社交識別提供者的 [刪除] 按鈕的螢幕擷取畫面。](media/google-federation/google-social-identity-providers.png)

1. 選取 **[是]** 確認刪除。 

**使用 PowerShell 刪除 Google 同盟** 
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行 `Connect-AzureAD`。  
4. 在出現登入提示時，使用受控的全域管理員帳戶來登入。  
5. 輸入下列命令：

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 如需詳細資訊，請參閱 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)。
