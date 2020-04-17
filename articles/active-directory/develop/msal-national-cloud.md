---
title: 在國家雲應用中使用 MSAL |蔚藍
titleSuffix: Microsoft identity platform
description: Microsoft 身份驗證庫 (MSAL) 使應用程式開發人員能夠獲取權杖,以便呼叫安全的 Web API。 這些 Web API 可以是 Microsoft 圖形、其他 Microsoft API、合作夥伴 Web API 或您自己的 Web API。 MSAL 支援多種應用程式架構和平台。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533984"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在國家雲環境中使用 MSAL

[國家雲](authentication-national-cloud.md)(也稱為主權雲)是 Azure 的物理隔離實例。 Azure 的這些區域有助於確保數據駐留、主權和合規性要求在地理範圍內得到遵守。

除了 Microsoft 全球雲端之外,Microsoft 身份驗證庫 (MSAL) 還允許國家雲端中的應用程式開發人員獲取權杖,以便對安全的 Web API 進行身份驗證和調用。 這些 Web API 可以是 Microsoft 圖形或其他 Microsoft API。

包括全域雲,Azure 活動目錄 (Azure AD) 部署在以下國家雲中:  

- Azure Government
- Azure China 21Vianet
- Azure Germany

本指南演示如何登錄到工作帳戶和學校帳戶、獲取訪問令牌以及在[Azure 政府雲](https://azure.microsoft.com/global-infrastructure/government/)環境中調用 Microsoft 圖形 API。

## <a name="prerequisites"></a>Prerequisites

在開始之前,請確保滿足這些先決條件。

### <a name="choose-the-appropriate-identities"></a>選擇適當的識別

[Azure 政府](https://docs.microsoft.com/azure/azure-government/)應用程式可以使用 Azure AD 政府識別和 Azure AD 公共標識對使用者進行身份驗證。 由於可以使用這些標識中的任何一個,因此需要決定應為方案選擇哪個許可權終結點:

- Azure AD 公共:如果組織已具有 Azure AD 公共租戶以支援 Office 365(公共或 GCC)或其他應用程式,則通常使用。
- Azure AD 政府:如果組織已具有 Azure AD 政府租戶以支援 Office 365(GCC High 或 DoD)或在 Azure AD 政府中創建新租戶,則常用。

在您做出決定后,一個特別的考慮因素是執行應用註冊的位置。 如果為 Azure 政府應用程式選擇 Azure AD 公共標識,則必須在 Azure AD 公共租戶中註冊該應用程式。

### <a name="get-an-azure-government-subscription"></a>取得 Azure 政府訂閱

要取得 Azure 政府訂閱,請參閱[在 Azure 政府 中管理和連線到訂閱](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)。

如果沒有 Azure 政府訂閱,請先創建[一個免費帳戶](https://azure.microsoft.com/global-infrastructure/government/request/)。"

有關使用具有特定程式設計語言的國家雲的詳細資訊,請選擇與您的語言匹配的選項卡:

## <a name="net"></a>[.NET](#tab/donet)

您可以使用MSAL.NET登錄使用者、獲取權杖並在國家雲中調用Microsoft圖形API。

以下教學探索如何建構 .NET 核心 2.2 MVC Web 應用。 該應用程式使用 OpenID Connect 在屬於國家雲的組織中使用工作帳戶和學校帳戶登錄使用者。

- 要登入使用者並取得權杖,請按照本教學操作:[使用 Microsoft 識別平台在主權雲中建構 ASP.NET 核心 Web 應用程式登入使用者](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 要呼叫 Microsoft 圖形 API,請按照本教學操作:[使用 Microsoft 識別平臺從 ASP.NET Core 2.x Web 應用呼叫 Microsoft 圖形 API,代表使用者使用他們的工作和學校帳戶在 Microsoft 國家雲中登入](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="javascript"></a>[JavaScript](#tab/javascript)

要啟用主權雲的 MSAL.js 應用程式,請執行:

### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.us/)。

   要查找其他國家雲的 Azure 門戶終結點,請參閱[應用註冊終結點](authentication-national-cloud.md#app-registration-endpoints)。

1. 如果您的帳戶允許您訪問多個租戶,請在右上角選擇您的帳戶,並將門戶會話設置為所需的 Azure AD 租戶。
1. 轉到 Microsoft 標識平臺上面向開發人員[的應用註冊](https://aka.ms/ra/ff)頁面。
1. [註冊應用程式]  頁面出現時，輸入您應用程式的名稱。
1. 在**支援帳號型態**型態下,選擇**任何組織目錄中的帳戶**。
1. 在 **「重定向 URI」** 部分中,選擇**Web**平台並根據 Web 伺服器將值設定為應用程式的 URL。 有關如何在 Visual Studio 和 Node 中設置和獲取重定向 URL 的說明,請參閱以下部分。
1. 選取 [註冊]  。
1. 在應用程式 [概觀]**** 頁面上，記下 [應用程式 (用戶端) 識別碼]**** 值。
1. 這個教學要求您開啟[隱式授予串流](v2-oauth2-implicit-grant-flow.md)。 在所註冊應用程式的左側窗格中，選取 [驗證]  。
1. 在 [進階設定]  的 [隱含授與]  底下，選取 [識別碼權杖]  和 [存取權杖]  核取方塊。 需要 ID 權杖和存取權杖,因為此應用需要登錄使用者並調用 API。
1. 選取 [儲存]  。

### <a name="step-2--set-up-your-web-server-or-project"></a>第二步:設定 Web 伺服器或專案

- 下載本地 Web 伺服器(如 Node)[的項目檔案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。

  或

- [下載視覺工作室專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然後跳到[配置 JavaScript SPA](#step-4-configure-your-javascript-spa)以在執行代碼範例之前設定代碼範例。

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>第三步:使用 Microsoft 身份驗證函式庫登入使用者

按照[JavaScript 教程](tutorial-v2-javascript-spa.md#create-your-project)中的步驟創建專案並與 MSAL 整合以登錄使用者。

### <a name="step-4-configure-your-javascript-spa"></a>第四步:設定 JavaScript SPA

在於專案設定期間建立的 `index.html` 檔案中，新增應用程式註冊資訊。 在 `index.html` 檔案主體頂端的 `<script></script>` 標記內，新增下列程式碼：

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

在該代碼中:

- `Enter_the_Application_Id_here`是您註冊的應用程式**的應用程式(用戶端)ID**值。
- `Enter_the_Tenant_Info_Here`設定為以下選項之一:
    - 如果應用程式支援**此組織目錄中的帳戶**,請將此值替換為租戶 ID 或租戶名稱(例如,contoso.microsoft.com)。
    - 如果應用程式支援**任何組織目錄中的帳戶**,請將此值取代`organizations`為 。

    要尋找所有國家雲的身份驗證終結點,請參閱 Azure [AD 認證終結點](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。

    > [!NOTE]
    > 國家雲不支援個人 Microsoft 帳戶。

- `graphEndpoint`是美國政府的微軟雲的微軟圖形終結點。

   要查找所有國家雲的 Microsoft 圖形終結點,請參閱[國家雲中的 Microsoft 圖形終結點](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

## <a name="python"></a>[Python](#tab/python)

要為主權雲啟用 MSAL Python 應用程式,請執行:

- 根據雲,請在特定門戶中註冊應用程式。 有關如何選擇門戶的詳細資訊,請參閱[應用註冊終結點](authentication-national-cloud.md#app-registration-endpoints)
- 根據接下來提到的雲,請使用來自回購的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample)對配置進行一些更改。
- 使用特定許可權,具體取決於您在其中註冊應用程式的雲。 有關不同雲端的頒發權限的詳細資訊,請參閱[Azure AD 身份驗證終結點](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

    下面是一個範例權限:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- 要呼叫 Microsoft 圖形需要特定的圖形終結點網址,具體取決於您使用的雲。 要尋找所有國家雲的 Microsoft 圖形的終結點,請參閱[Microsoft 圖像和圖像資源管理員服務根終結點](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

    下面是具有作用網域的圖形終結點的範例:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

要為主權雲啟用 JAVA 應用程式的 MSAL,請執行:

- 根據雲,請在特定門戶中註冊應用程式。 有關如何選擇門戶的詳細資訊,請參閱[應用註冊終結點](authentication-national-cloud.md#app-registration-endpoints)
- 根據接下來提到的雲,使用來自回購的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)對配置進行一些更改。
- 使用特定許可權,具體取決於您在其中註冊應用程式的雲。 有關不同雲端的頒發權限的詳細資訊,請參閱[Azure AD 身份驗證終結點](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

下面是一個範例權限:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- 要呼叫 Microsoft 圖形需要特定的圖形終結點網址,具體取決於您使用的雲。 要尋找所有國家雲的 Microsoft 圖形的終結點,請參閱[Microsoft 圖像和圖像資源管理員服務根終結點](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

下面是具有作用網域的圖形終結點的範例:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

適用於 iOS 和 macOS 的 MSAL 可用於在國家雲中`MSALPublicClientApplication`獲取權杖,但在創建時需要額外的配置。

例如,如果您希望應用程式是國家雲(此處為美國政府)中的多租戶應用程式,則可以編寫:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

適用於 iOS 和 macOS 的 MSAL 可用於在國家雲中`MSALPublicClientApplication`獲取權杖,但在創建時需要額外的配置。

例如,如果您希望應用程式是國家雲(此處為美國政府)中的多租戶應用程式,則可以編寫:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>後續步驟

深入了解：

- [國家雲中的身份驗證](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
