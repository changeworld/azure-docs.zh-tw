---
title: 發佈原生用戶端應用程式 - Azure AD | Microsoft Docs
description: 涵蓋如何讓原生用戶端應用程式與 Azure AD 應用程式 Proxy 連接器通訊，為內部部署的應用程式提供安全的遠端存取。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f8494852bcff49602645c940470b529302f119f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165068"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何讓原生用戶端應用程式與 Proxy 應用程式互動

您可以使用 Azure Active Directory (Azure AD) 應用程式 Proxy 來發佈 Web 應用程式，但其也可用來發佈使用 Microsoft 驗證程式庫 (MSAL) 所設定的原生用戶端應用程式。 原生用戶端應用程式與 Web 應用程式不同，因為這種應用程式會安裝在裝置上，而 Web 應用程式則是透過瀏覽器存取。

為了支援原生用戶端應用程式，應用程式 Proxy 會接受在標頭中傳送的 Azure AD 發行權杖。 應用程式 Proxy 服務會對使用者進行驗證。 此解決方案不使用應用程式權杖進行驗證。

![使用者、Azure AD 和已發佈應用程式之間的關係](./media/application-proxy-configure-native-client-application/richclientflow.png)

若要發佈原生應用程式，請使用 Microsoft 驗證程式庫，該程式庫會處理驗證並支援許多用戶端環境。 應用程式 Proxy 適合[代表登入使用者呼叫 Web API 的傳統型應用程式](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user)案例。

本文引導您完成使用應用程式 Proxy 和 Azure AD 驗證程式庫發佈原生應用程式的四個步驟。

## <a name="step-1-publish-your-proxy-application"></a>步驟 1:發佈您的 Proxy 應用程式

如同任何其他應用程式一般，發佈您的 Proxy 應用程式，並指派使用者以存取您的應用程式。 如需詳細資訊，請參閱[使用應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>步驟 2:註冊您的原生應用程式

您現在必須在 Azure AD 中註冊您的應用程式，如下所示：

1. 登入 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 [Azure Active Directory 管理中心] 的 [儀表板] 隨即顯示。
1. 在側邊欄中，選取 [Azure Active Directory]。 **Azure Active Directory** 概觀頁面隨即顯示。
1. 在 Azure AD 概觀側邊欄中，選取 [應用程式註冊]。 所有應用程式註冊的清單隨即顯示。
1. 選取 [新增註冊]。 [註冊應用程式] 頁面隨即顯示。

   ![在 Azure 入口網站中建立新的應用程式註冊](./media/application-proxy-configure-native-client-application/create.png)

1. 在 [名稱] 標題中，為您的應用程式指定使用者面向的顯示名稱。
1. 在 [支援的帳戶類型] 標題下，使用下列指導方針選取存取層級：

   - 若只要將組織內部的帳戶設為目標，請選取 [僅此組織目錄中的帳戶]。
   - 若只要將商業或教育客戶設為目標，請選取 [任何組織目錄中的帳戶]。
   - 若要將最廣泛的一組 Microsoft 身分識別設為目標，請選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 在 [重新導向 URI] 底下，選取 [公用用戶端 (行動和桌面)]，然後為您的應用程式輸入重新導向 URI `https://login.microsoftonline.com/common/oauth2/nativeclient`。
1. 選取並閱讀 [Microsoft 平台原則]，然後選取 [註冊]。 隨即會建立並顯示新應用程式註冊的概觀頁面。

如需建立新應用程式註冊的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../develop/quickstart-register-app.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>步驟 3：授與 Proxy 應用程式的存取權

既然您已註冊原生應用程式，就可以讓其存取您目錄中的其他應用程式，在此案例中，是存取 Proxy 應用程式。 若要讓原生應用程式公開給 Proxy 應用程式：

1. 在新應用程式註冊頁面的側邊欄中，選取 [API 權限]。 新應用程式註冊的 [API 權限] 頁面隨即顯示。
1. 選取 [新增權限]。 [要求 API 權限] 頁面隨即顯示。
1. 在 [選取 API] 設定底下，選取 [我的組織使用的 API]。 清單隨即顯示，其中包含您目錄中公開 API 的應用程式。
1. 在搜尋方塊中輸入，或捲動以尋找您在[步驟 1：發佈您的 Proxy 應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-native-client-application#step-1-publish-your-proxy-application)中發佈的 Proxy 應用程式，然後選取 Proxy 應用程式。
1. 在 [您的應用程式需要什麼類型的權限？] 標題中，選取權限類型。 如果您的原生應用程式需要以登入使用者的身分存取 Proxy 應用程式 API，請選擇 [委派的權限]。
1. 在 [選取權限] 標題中，選取所需的權限，然後選取 [新增權限]。 您的原生應用程式的 [API 權限] 頁面現在會顯示您新增的 Proxy 應用程式和權限 API。

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>步驟 4：將 Microsoft 驗證程式庫新增至您的程式碼 (.NET C# 範例)

在 Microsoft 驗證程式庫 (MSAL) 的驗證內容中編輯原生應用程式程式碼，以包含下列文字： 

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

您可以在 Azure AD 入口網站中找到範例程式碼中的必要資訊，如下所示：

| 必要資訊 | 如何在 Azure AD 入口網站中尋找 |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **屬性**  >  **目錄識別碼** |
| \<App ID of the Native app> | **應用程式註冊**  >  *您的原生應用程式*  >  **概觀**  >  **應用程式識別碼** |
| \<Scope> | **應用程式註冊**  >  *您的原生應用程式*  >  **API 權限** > 按一下 [權限 API] (user_impersonation) > 具有標題 **user_impersonation** 的面板隨即顯示在右側。 > 範圍是編輯方塊中的 URL。
| \<Proxy App Url> | API 的外部 URL 和路徑

使用這些參數編輯 MSAL 程式碼之後，您的使用者就可以驗證原生用戶端應用程式，即使他們位於公司網路外部也可以。

## <a name="next-steps"></a>後續步驟

如需原生應用程式流程的詳細資訊，請參閱 [Azure Active Directory 中的原生應用程式](../azuread-dev/native-app.md)。

深入了解設定 [Azure Active Directory 中應用程式的單一登入](sso-options.md#choosing-a-single-sign-on-method)。