---
title: 撰寫應用程式驗證碼
titleSuffix: Azure Digital Twins
description: 瞭解如何在用戶端應用程式中撰寫驗證碼
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 1eebacefa2e91970263c301de4ff160ea7c9f2eb
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420340"
---
# <a name="write-client-app-authentication-code"></a>撰寫用戶端應用程式驗證碼

[設定 Azure 數位 Twins 實例和驗證](how-to-set-up-instance-scripted.md)之後，您可以建立將用來與實例互動的用戶端應用程式。 設定好入門用戶端專案之後，本文將說明**如何在該用戶端應用程式中撰寫程式碼，以**對 Azure 數位 Twins 實例進行驗證。

這篇文章中的範例程式碼有兩種方法。 視您選擇的語言而定，您可以使用最適合您的類型：
* 範例程式碼的第一個區段會使用 Azure 數位 Twins .NET （c #） SDK。 SDK 是 Azure SDK for .NET 的一部分，位於此處：[*適用于 .net 的 Azure IoT 數位對應項用戶端程式庫*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。
* 範例程式碼的第二個區段適用于不使用 .NET SDK 的使用者，而是改為使用其他語言的 AutoRest 產生 Sdk。 如需此策略的詳細資訊，請參閱[*如何：使用 AutoRest 建立適用于 Azure 數位 Twins 的自訂 sdk*](how-to-create-custom-sdks.md)。

您也可以在[*如何：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)中，閱讀更多有關 Api 和 Sdk For Azure 數位 Twins 的資訊。

## <a name="prerequisites"></a>必要條件

首先，完成[*如何：設定實例和驗證*](how-to-set-up-instance-scripted.md)中的設定步驟。 這可確保您有 Azure 數位 Twins 實例、您的使用者擁有存取權限，而且您已設定用戶端應用程式的許可權。 完成此設定之後，您就可以開始撰寫用戶端應用程式程式碼。

若要繼續，您將需要一個用戶端應用程式專案，您可以在其中撰寫程式碼。 如果您尚未設定用戶端應用程式專案，請以您選擇的語言建立基本專案，以搭配本教學課程使用。

## <a name="authentication-and-client-creation-net-c-sdk"></a>驗證和用戶端建立： .NET （c #） SDK

首先，在您的專案中包含下列套件，以便使用 .NET SDK 和驗證工具來進行此操作說明：
* `Azure.DigitalTwins.Core`（版本 `1.0.0-preview.2` ）
* `Azure.Identity`

視您選擇的工具而定，您可以使用 Visual Studio 封裝管理員或 `dotnet` 命令列工具來包含封裝。 

若要使用 .NET SDK 進行驗證，請使用[Azure 身分識別](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)程式庫中所定義的其中一個認證取得方法。

以下是一般使用的兩個： 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)。 這個方法適用于互動式應用程式，並會啟動網頁瀏覽器進行驗證。
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet)。 當您需要[受控識別（MSI）](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)時（例如，使用 Azure Functions 時，這個方法很適合用）。 

您也需要下列 using 語句：

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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
> 雖然您可以將 [用戶端識別碼]、[租使用者識別碼] 和 [實例 URL] 直接放在程式碼中（如上所示），但最好是讓程式碼改為從設定檔或環境變數取得這些值。

在 Azure 函式中，您可以接著使用受控識別認證，如下所示：

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

如需更完整的範例來說明函式內容中的一些重要設定選項，請參閱[*如何：設定用來處理資料的 Azure 函數*](how-to-create-azure-function.md)。

此外，若要在函式中使用驗證，請記得：
* [啟用受控識別](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* 適當地使用[環境變數](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 指派許可權給函式應用程式，讓它能夠存取數位 Twins Api。 如需 Azure Functions 進程的詳細資訊，請參閱[*如何：設定 Azure 函式以處理資料*](how-to-create-azure-function.md)。

## <a name="authentication-with-an-autorest-generated-sdk"></a>使用 AutoRest 產生的 SDK 進行驗證

如果您不是使用 .NET，您可以選擇以您所選的語言建立 SDK 程式庫，如[*如何：使用 AutoRest 建立 Azure 數位 Twins 的自訂 sdk*](how-to-create-custom-sdks.md)中所述。

本節說明如何在這種情況下進行驗證。

### <a name="prerequisites"></a>必要條件

首先，您應該使用[*操作說明：使用 AutoRest 建立適用于 Azure 數位 Twins 的自訂*](how-to-create-custom-sdks.md)sdk 中的步驟，完成建立自訂 SDK 與 AutoRest 的步驟。

這個範例會使用以 AutoRest 產生的 Typescript SDK。 因此，它也需要：
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-chap-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>基本身份驗證程式代碼範例

若要使用 Azure 服務驗證應用程式，您可以在用戶端應用程式中使用下列最少的程式碼。

您將需要稍早的*應用程式（用戶端）識別碼*和*目錄（租使用者）識別碼*，以及 Azure 數位 Twins 實例的 URL。

> [!TIP]
> Azure 數位 Twins 實例的 URL 是藉由將*HTTPs://* 新增至 Azure 數位 Twins 實例*主機名稱*的開頭來建立。 若要查看*主機名稱*，以及實例的所有屬性，您可以執行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。 您可以使用 `az account show --query tenantId` 命令來查看您的*目錄（租使用者）識別碼*。 

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

請注意，上面的程式碼會將用戶端識別碼、租使用者識別碼和實例 URL 直接放入程式碼中，以方便您使用，因此建議您讓程式碼改為從設定檔或環境變數取得這些值。

MSAL 有更多您可以使用的選項，以執行快取和其他驗證流程等專案。 如需這項操作的詳細資訊，請參閱[*Microsoft 驗證程式庫（MSAL）的總覽*](../active-directory/develop/msal-overview.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 數位 Twins 中的安全性運作方式：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)

或者，現在已設定驗證，請繼續在您的實例中建立模型：
* [操作說明：管理自訂模型](how-to-manage-model.md)