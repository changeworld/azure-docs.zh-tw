---
title: 使用 Azure AD 應用程式 Proxy 存取內部部署 Api
description: Azure Active Directory 的應用程式 Proxy 可讓原生應用程式安全地存取您在內部部署或雲端 Vm 上裝載的 Api 和商務邏輯。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: e72129b1f391996f6d5b085fe602adb35a3aecbe
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371213"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 來保護對內部部署 Api 的存取

您可能有在內部部署執行的商務邏輯 Api，或裝載于雲端中的虛擬機器。 您的原生 Android、iOS、Mac 或 Windows 應用程式必須與 API 端點互動，才能使用資料或提供使用者互動。 Azure AD 應用程式 Proxy 和 [Microsoft 驗證程式庫 (MSAL) ](/azure/active-directory/develop/active-directory-authentication-libraries) 讓您的原生應用程式安全地存取內部部署 api。 Azure Active Directory 應用程式 Proxy 是比在應用層級開啟防火牆埠及控制驗證和授權更快且更安全的解決方案。

本文將逐步引導您設定 Azure AD 的應用程式 Proxy 解決方案，以裝載可供原生應用程式存取的 web API 服務。

## <a name="overview"></a>概觀

下圖顯示發行內部部署 Api 的傳統方式。 這種方法需要開啟連入埠80和443。

![傳統 API 存取](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下圖顯示如何使用 Azure AD 應用程式 Proxy 安全地發佈 Api，而不需開啟任何傳入的埠：

![Azure AD 應用程式 Proxy API 存取](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 的應用程式 Proxy 形成解決方案骨幹、作為 API 存取的公用端點，以及提供驗證和授權。 您可以使用 [Microsoft 驗證程式庫 (MSAL) 程式庫 ](/azure/active-directory/develop/active-directory-authentication-libraries) ，從大量的平臺存取您的 api。

由於 Azure AD 的應用程式 Proxy 驗證和授權是以 Azure AD 為基礎，因此您可以使用 Azure AD 條件式存取，確保只有受信任的裝置可以存取透過應用程式 Proxy 發佈的 Api。 針對桌上型電腦使用 Azure AD 聯結或 Azure AD 混合式聯結，以及針對裝置管理 Intune。 您也可以利用 Azure Multi-Factor Authentication 的 Azure Active Directory Premium 功能，以及 [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)的機器學習支援安全性。

## <a name="prerequisites"></a>Prerequisites

若要遵循這個逐步解說，您需要：

- 具有可建立及註冊應用程式之帳戶的 Azure 目錄系統管理員存取權
- 範例 web API 和原生用戶端應用程式 [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>透過應用程式 Proxy 發佈 API

若要透過應用程式 Proxy 在內部網路外部發佈 API，您可以遵循與發佈 web 應用程式相同的模式。 如需詳細資訊，請參閱 [教學課程：新增內部部署應用程式，以透過應用程式 Proxy 在 Azure Active Directory 中進行遠端存取](application-proxy-add-on-premises-application.md)。

若要透過應用程式 Proxy 發佈 SecretAPI web API：

1. 在您的本機電腦或內部網路上建立併發布範例 SecretAPI 專案作為 ASP.NET web 應用程式。 請確定您可以在本機存取 web 應用程式。

1. 在 [Azure 入口網站] 中，選取 [Azure Active Directory]。 然後選取 [ **企業應用程式**]。

1. 在 [ **企業應用程式-所有應用程式** ] 頁面的頂端，選取 [ **新增應用程式**]。

1. 在 [ **加入應用程式** ] 頁面上，選取 [內部 **部署應用程式**]。 [ **新增您自己的內部部署應用程式** ] 頁面隨即出現。

1. 如果您未安裝應用程式 Proxy 連接器，系統將會提示您安裝它。 選取 [ **下載應用程式 Proxy 連接器** ] 以下載並安裝連接器。

1. 當您安裝應用程式 Proxy 連接器之後，請在 [ **新增您自己的內部部署應用程式** ] 頁面上：

   1. 在 [ **名稱**] 旁輸入 *SecretAPI*。

   1. 在 [ **內部 Url**] 旁，輸入您在內部網路中用來存取 API 的 Url。

   1. 請確定 **預先驗證** 設定為 **Azure Active Directory**。

   1. 選取頁面頂端的 [ **新增** ]，然後等待應用程式建立。

   ![新增 API 應用程式](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. 在 [ **企業應用程式-所有應用程式** ] 頁面上，選取 **SecretAPI** 應用程式。

1. 在 [ **SecretAPI-總覽** ] 頁面上，選取左側導覽中的 [ **屬性** ]。

1. 您不想讓 Api 可供使用者在 [ **MyApps** ] 面板中使用，因此，**使用者可以看到**[內容] 頁面底部的**Properties** [**否**]，然後選取 [**儲存**]。

   ![使用者看不到](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

您已透過 Azure AD 應用程式 Proxy 發佈您的 web API。 現在，新增可存取應用程式的使用者。

1. 在 [ **SecretAPI-總覽** ] 頁面上，選取左側導覽中的 [ **使用者和群組** ]。

1. 在 [ **使用者和群組** ] 頁面上，選取 [ **新增使用者**]。

1. 在 [ **新增指派** ] 頁面上，選取 [ **使用者和群組**]。

1. 在 [ **使用者和群組** ] 頁面上，搜尋並選取可以存取應用程式的使用者，至少包括您自己的使用者。 選取 [所有使用者] 之後，請選取 [ **選取**]。

   ![選取並指派使用者](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. 回到 [ **新增指派** ] 頁面上，選取 [ **指派**]。

> [!NOTE]
> 使用整合式 Windows 驗證的 Api 可能需要 [額外的步驟](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>註冊原生應用程式並授與 API 的存取權

原生應用程式是開發來在特定平臺或裝置上使用的程式。 在您的原生應用程式可以連線並存取 API 之前，您必須先在 Azure AD 中進行註冊。 下列步驟說明如何註冊原生應用程式，並讓它能夠存取您透過應用程式 Proxy 發佈的 web API。

註冊 AppProxyNativeAppSample 原生應用程式：

1. 在 [Azure Active Directory **總覽** ] 頁面上，選取 [ **應用程式註冊**]，然後在 [ **應用程式註冊** ] 窗格的頂端選取 [ **新增註冊**]。

1. 在 [ **註冊應用程式** ] 頁面上：

   1. 在 [ **名稱**] 底下，輸入 *AppProxyNativeAppSample*。

   1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。

   1. 在 [重新 **導向 URL**] 下，下拉並選取 [ **公用用戶端 (mobile & desktop]) **，然後輸入 *https://login.microsoftonline.com/common/oauth2/nativeclient* 。

   1. 選取 [ **註冊**]，然後等待應用程式成功註冊。

      ![新增應用程式註冊](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

您現在已在 Azure Active Directory 中註冊 AppProxyNativeAppSample 應用程式。 若要將原生應用程式存取權授與 SecretAPI web API：

1. 在 [Azure Active Directory**總覽**  >  **應用程式註冊**] 頁面上，選取**AppProxyNativeAppSample**應用程式。

1. 在 [ **AppProxyNativeAppSample** ] 頁面上，選取左側導覽中的 [ **API 許可權** ]。

1. 在 [ **API 許可權** ] 頁面上，選取 [ **新增許可權**]。

1. 在 [第一個 **要求 api 許可權** ] 頁面上，選取 [ **我的組織使用的 api** ] 索引標籤，然後搜尋並選取 [ **SecretAPI**]。

1. 在 [下一個 **要求 API 許可權** ] 頁面上，選取 [ **user_impersonation**] 旁的核取方塊，然後選取 [ **新增許可權**]。

    ![選取 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. 回到 [ **API 許可權** ] 頁面，您可以選取 **[授與 Contoso 的管理員同意** ]，以防止其他使用者個別同意應用程式。

## <a name="configure-the-native-app-code"></a>設定原生應用程式程式碼

最後一個步驟是設定原生應用程式。 下列來自 Nativeclient-windowsstore 範例應用程式中 *Form1.cs* 檔案的程式碼片段會導致 MSAL 程式庫取得要求 API 呼叫的權杖，並將其作為持有人附加至應用程式標頭。

   ```
   // Acquire Access Token from AAD for Proxy Application
 IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }
 
if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application
  
  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

若要將原生應用程式設定為連線至 Azure Active Directory 並呼叫 API 應用程式 Proxy，請使用來自 Azure AD 的值，更新 Nativeclient-windowsstore 範例應用程式 *App.config* 檔中的預留位置值：

- 在欄位中貼上 ** (租使用者) 識別碼的目錄** `<add key="ida:Tenant" value="" />` 。 您可以從任何應用程式的 [ **總覽** ] 頁面，找到並複製此值 (GUID) 。

- 將 AppProxyNativeAppSample **應用程式 (用戶端) 識別碼** 貼入 `<add key="ida:ClientId" value="" />` 欄位中。 您可以從 AppProxyNativeAppSample **總覽** 頁面找到並複製此值 (GUID) 。

- 將 AppProxyNativeAppSample 重新 **導向 URI** 貼到 `<add key="ida:RedirectUri" value="" />` 欄位中。 您可以從 AppProxyNativeAppSample **Authentication** 頁面)  (URI 中找到並複製此值。

- 將 SecretAPI **應用程式識別碼 URI** 貼到 `<add key="todo:TodoListResourceId" value="" />` 欄位中。 您可以從 SecretAPI **公開 API** 頁面，找到並複製此值 (URI) 。

- 將 SecretAPI **首頁 URL** 貼到欄位中 `<add key="todo:TodoListBaseAddress" value="" />` 。 您可以從 [SecretAPI **商標** ] 頁面中找到並複製此值 (URL) 。

設定參數之後，請建立並執行原生應用程式。 當您選取 [登 **入** ] 按鈕時，應用程式可讓您登入，然後顯示成功畫面，確認它已成功連線至 SecretAPI。

![螢幕擷取畫面顯示訊息秘密 A P I 成功，並顯示 [確定] 按鈕。](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>後續步驟

- [教學課程：新增內部部署應用程式以便透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)
- [快速入門：設定用戶端應用程式以存取 Web API](../develop/quickstart-configure-app-access-web-apis.md)
- [如何讓原生用戶端應用程式與 Proxy 應用程式互動](application-proxy-configure-native-client-application.md)
