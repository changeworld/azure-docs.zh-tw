---
title: 驗證用戶端應用程式
titleSuffix: Azure Digital Twins
description: 請參閱如何對 Azure 數位 Twins 服務驗證用戶端應用程式。
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390817"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>使用 Azure 數位 Twins 來驗證用戶端應用程式

[建立 Azure 數位 Twins 實例](how-to-set-up-instance.md)之後，您可以建立將用來與實例互動的用戶端應用程式。 當您設定了入門用戶端專案之後，本文將說明如何使用 Azure 數位 Twins 實例適當地驗證該用戶端應用程式。

這是透過下列兩個步驟達成：
1. 建立應用程式註冊
2. 在用戶端應用程式中撰寫驗證碼

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>建立應用程式註冊

若要從用戶端應用程式對 Azure 數位 Twins 進行驗證，您需要在[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)中設定**應用程式註冊**。

此應用程式註冊可讓您設定[Azure 數位 Twins api](how-to-use-apis-sdks.md)的存取權限。 您的用戶端應用程式會對應用程式註冊進行驗證，因此會授與對 Api 所設定的存取權限。

若要建立應用程式註冊，您必須提供 Azure 數位 Twins Api 的資源識別碼，以及 API 的基準許可權。 在您的工作目錄中，開啟新的檔案，並輸入下列 JSON 程式碼片段來設定這些詳細資料： 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

將此檔案儲存為*manifest.js開啟*。

> [!NOTE] 
> 有一些地方可以使用「易記」、人類可讀的字串，做 `https://digitaltwins.azure.net` 為 Azure 數位 Twins 資源應用程式識別碼，而不是 GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 。 比方說，這整篇檔集中的許多範例都是使用驗證搭配 MSAL 程式庫，而易記的字串則可以用於該程式。 不過，在建立應用程式註冊的這個步驟中，需要識別碼的 GUID 形式，如上所示。 

在 Cloud Shell 視窗中，按一下 [上傳/下載檔案] 圖示，然後選擇 [上傳]。

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="顯示上傳選項選取範圍的 Cloud Shell 視窗":::
流覽至您剛建立的*manifest.js* ，然後按 [開啟]。

接下來，執行下列命令以建立應用程式註冊（視需要取代預留位置）：

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

此命令的輸出看起來像這樣。

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="新的 AAD 應用程式註冊":::

建立應用程式註冊之後，請遵循[此連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)以流覽至 Azure 入口網站中的 AAD 應用程式註冊總覽頁面。

在此總覽中，從清單中選取您剛才建立的應用程式註冊。 這會在頁面中開啟其詳細資料，如下所示：

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure 入口網站：驗證識別碼":::

記**下頁面上**顯示的 [*應用程式（用戶端）識別碼*] 和 [*目錄（租使用者）] 識別碼*。 您稍後將會使用這些值來對 Azure 數位 Twins Api 驗證用戶端應用程式。

> [!NOTE]
> 視您的案例而定，您可能需要對應用程式註冊進行其他變更。 以下是您可能需要符合的一些常見需求：
> * 啟動公用用戶端存取
> * 為 web 和桌上型電腦存取設定特定的回復 Url
> * 允許隱含 OAuth2 驗證流程
> * 如果您的 Azure 訂用帳戶是使用 Microsoft 帳戶（例如 Live、Xbox 或 Hotmail）所建立，您必須在應用程式註冊上設定*signInAudience* ，以支援個人帳戶。
> 設定這些設定最簡單的方式是使用[Azure 入口網站](https://portal.azure.com/)。 如需此流程的詳細資訊，請參閱[使用 Microsoft 身分識別平臺註冊應用程式](https://docs.microsoft.com/graph/auth-register-app-v2)。

## <a name="write-client-app-authentication-code-net-c-sdk"></a>撰寫用戶端應用程式驗證碼： .NET （c #） SDK

本節說明您必須在用戶端應用程式中包含的程式碼，才能使用 .NET （c #） SDK 完成驗證程式。
Azure 數位 Twins c # SDK 是 Azure SDK for .NET 的一部分。 其位於此處：[適用于 .net 的 Azure IoT 數位對應項用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。

### <a name="prerequisites"></a>必要條件

如果您尚未設定 [入門用戶端應用程式] 專案，請建立要與本教學課程搭配使用的基本 .NET 專案。

若要使用 .NET SDK，您必須在專案中包含下列套件：
* `Azure.DigitalTwins.Core`（版本 `1.0.0-preview.2` ）
* `Azure.Identity`

視您選擇的工具而定，您可以使用 Visual Studio 套件管理員或命令列工具來執行此動作 `dotnet` 。 

### <a name="authentication-and-client-creation-net"></a>驗證和用戶端建立： .NET

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

如需更完整的範例來說明函式內容中的一些重要設定選項，請參閱[如何：設定用來處理資料的 Azure 函數](how-to-create-azure-function.md)。

此外，若要在函式中使用驗證，請記得：
* [啟用受控識別](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [環境變數](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 指派許可權給函式應用程式，讓它能夠存取數位 Twins Api。 如需詳細資訊，請參閱[如何：設定用來處理資料的 Azure 函數](how-to-create-azure-function.md)。

## <a name="authentication-in-an-autorest-generated-sdk"></a>在 AutoRest 產生的 SDK 中進行驗證

如果您不是使用 .NET，您可以選擇以您所選的語言建立 SDK 程式庫，如[如何：使用 AutoRest 建立 Azure 數位 Twins 的自訂 sdk](how-to-create-custom-sdks.md)中所述。

本節說明如何在這種情況下進行驗證。

### <a name="prerequisites"></a>必要條件

這個範例會使用以 AutoRest 產生的 Typescript SDK。 因此，它也需要：
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-chap-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>基本身份驗證程式代碼範例

若要使用 Azure 服務驗證 .NET 應用程式，您可以在用戶端應用程式中使用下列最少的程式碼。

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

MSAL 有更多您可以使用的選項，以執行快取和其他驗證流程等專案。 如需這項操作的詳細資訊，請參閱[Microsoft 驗證程式庫（MSAL）的總覽](../active-directory/develop/msal-overview.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 數位 Twins 中的安全性運作方式：
* [概念： Azure 數位 Twins 解決方案的安全性](concepts-security.md)

或者，現在已設定驗證，請繼續在您的實例中建立模型：
* [如何：管理自訂模型](how-to-manage-model.md)