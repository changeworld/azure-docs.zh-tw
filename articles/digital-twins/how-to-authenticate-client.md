---
title: 撰寫應用程式驗證碼
titleSuffix: Azure Digital Twins
description: 瞭解如何在用戶端應用程式中撰寫驗證碼
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 0438632a36fe14d35210cb5acb8d3a50d0f038b7
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767826"
---
# <a name="write-client-app-authentication-code"></a>撰寫用戶端應用程式驗證碼

[設定 Azure 數位 Twins 實例和驗證](how-to-set-up-instance-portal.md)之後，您可以建立將用來與實例互動的用戶端應用程式。 設定好入門用戶端專案後，本文將說明 **如何在該用戶端應用程式中撰寫程式碼，以** 對 Azure 數位 Twins 實例進行驗證。

本文中有兩種方法可以撰寫範例程式碼。 您可以使用最適合您的語言，視您選擇的語言而定：
* 範例程式碼的第一個區段會使用 Azure 數位 Twins .NET (c # ) SDK。 SDK 是 Azure SDK for .NET 的一部分，位於這裡： [*適用于 .net 的 Azure IoT 數位對應項用戶端程式庫*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。 此外，也有支援 [JAVA](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0-beta.1/jar ) 和 [JavaScript](https://www.npmjs.com/package/@azure/digital-twins/v/1.0.0-preview.1)的 sdk，可透過類似的方式使用。
* 範例程式碼的第二個部分是針對未使用提供之 SDK 的使用者，改為使用 AutoRest 產生的其他語言 Sdk。 如需此策略的詳細資訊，請參閱作法 [*：使用 AutoRest 建立 Azure 數位 Twins 的自訂 sdk*](how-to-create-custom-sdks.md)。

您也可以在 how [*to：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)中，深入瞭解適用于 Azure 數位 Twins 的 Api 和 sdk。

## <a name="prerequisites"></a>必要條件

首先，請完成 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md)中的設定步驟。 這可確保您有 Azure 數位 Twins 實例、您的使用者具有存取權限，而且您已設定用戶端應用程式的許可權。 完成此設定之後，您就可以開始撰寫用戶端應用程式程式碼。

若要繼續，您將需要一個用來撰寫程式碼的用戶端應用程式專案。 如果您尚未設定用戶端應用程式專案，請使用您選擇的語言建立基本專案，以搭配本教學課程使用。

## <a name="authentication-and-client-creation-net-c-sdk"></a>驗證和用戶端建立： .NET (c # ) SDK

本節說明 c # 中使用所提供 .NET SDK 的範例。

首先，請在您的專案中包含下列套件，以使用 .NET SDK 和驗證工具進行此操作說明：
* `Azure.DigitalTwins.Core`
* `Azure.Identity`

根據您選擇的工具，您可以使用 Visual Studio 套件管理員或命令列工具來包含套件 `dotnet` 。 

您也需要下列 using 語句：

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
若要使用 .NET SDK 進行驗證，請使用 [Azure 身分識別](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) 程式庫中所定義的其中一個認證取得方法。 以下是在相同應用程式) 中經常使用 (的兩個：

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 適用于互動式應用程式，可用於建立已驗證的 SDK 用戶端
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) 適用于您需要受控識別 (MSI) 的情況，而且是使用 Azure Functions 的絕佳候選。

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 方法
[InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)方法適用于互動式應用程式，並會啟動網頁瀏覽器以進行驗證。

若要使用互動式瀏覽器認證來建立已驗證的 SDK 用戶端，請新增下列程式碼：

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> 雖然您可以將用戶端識別碼、租使用者識別碼和實例 URL 直接放入程式碼中（如上所示），但最好讓您的程式碼改為從設定檔或環境變數取得這些值。

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 方法
 [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true)方法適用于您需要[受控識別 (MSI) ](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的情況，例如，使用 Azure Functions 時。
在 Azure 函式中，您可以使用受控識別認證，如下所示：

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

請參閱作法 [*：設定 Azure 函式來處理資料*](how-to-create-azure-function.md) ，以取得更完整的範例，以說明函式內容中的一些重要設定選項。

此外，若要在函式中使用驗證，請記得：
* [啟用受控識別](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* 適當地使用[環境變數](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 指派許可權給函式應用程式，讓它能夠存取數位 Twins Api。 如需 Azure Functions 程式的詳細資訊，請參閱 how [*to：設定用來處理資料的 Azure 函數*](how-to-create-azure-function.md)。

## <a name="authentication-with-an-autorest-generated-sdk"></a>使用 AutoRest 產生的 SDK 進行驗證

如果您未使用其中一個提供的 Sdk ( .NET、JAVA、JavaScript) ，您可以選擇以您選擇的語言建立 SDK 程式庫，如 [*如何：使用 AutoRest 建立 Azure 數位 Twins 的自訂 sdk*](how-to-create-custom-sdks.md)（英文）中所述。

本節說明如何在這種情況下進行驗證。

### <a name="prerequisites"></a>必要條件

首先，您應該使用 AutoRest 中的步驟來完成建立自訂 SDK 的步驟，方法是使用 how [*to：建立適用于 Azure 數位 Twins 的自訂 sdk 和 AutoRest*](how-to-create-custom-sdks.md)。

此範例會使用以 AutoRest 產生的 Typescript SDK。 因此，它也需要：
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>基本身份驗證程式代碼範例

若要使用 Azure 服務驗證應用程式，您可以在用戶端應用程式中使用下列最少量的程式碼。

您將需要您的 *應用程式 (用戶端) 識別碼* 和 *目錄 (租使用者) 識別碼* ，以及 Azure 數位 Twins 實例的 URL。

> [!TIP]
> Azure 數位 Twins 實例的 URL 是藉由將 *HTTPs://* 新增至 Azure 數位 Twins 實例 *主機名稱*的開頭來建立。 若要查看 *主機名稱*，以及實例的所有屬性，您可以執行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。 您可以使用 `az account show --query tenantId` 命令來查看 * (租使用者) 識別碼的目錄*。 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

同樣地，請注意，上述程式碼會將用戶端識別碼、租使用者識別碼和實例 URL 直接放入程式碼中，以方便簡化，讓您的程式碼可以改為從設定檔或環境變數取得這些值。

MSAL 有更多選項可供您使用，以執行快取和其他驗證流程等專案。 如需詳細資訊，請參閱 [*Microsoft 驗證程式庫 (MSAL) 的總覽 *](../active-directory/develop/msal-overview.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 數位 Twins 中的安全性運作方式：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)

或者，現在已設定驗證，請繼續在您的實例中建立模型：
* [操作說明：管理自訂模型](how-to-manage-model.md)