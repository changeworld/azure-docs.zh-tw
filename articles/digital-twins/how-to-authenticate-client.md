---
title: 撰寫應用程式驗證碼
titleSuffix: Azure Digital Twins
description: 瞭解如何在用戶端應用程式中撰寫驗證碼
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b929632318de41470412811885b9f1bd3054783a
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145968"
---
# <a name="write-client-app-authentication-code"></a>撰寫用戶端應用程式驗證碼

[設定 Azure 數位 Twins 實例和驗證](how-to-set-up-instance-portal.md)之後，您可以建立將用來與實例互動的用戶端應用程式。 設定好入門用戶端專案之後，您必須 **在該用戶端應用程式中撰寫程式碼，以** 對 Azure 數位 Twins 實例進行驗證。

Azure 數位 Twins 會使用以 [OAUTH 2.0 為基礎的 Azure AD 安全性權杖](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims)來執行驗證。 若要驗證您的 SDK，您必須使用 Azure 數位 Twins 的正確許可權取得持有人權杖，並將它與您的 API 呼叫一起傳遞。 

本文說明如何使用 `Azure.Identity` 用戶端程式庫取得認證。 雖然本文說明 c # 中的程式碼範例（例如您針對 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)撰寫的內容），但您可以使用版本的， `Azure.Identity` 不論您使用的 sdk 為何 (如需 azure 數位 Twins 可用 sdk 的詳細資訊，請參閱作法 [*：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)) 。

## <a name="prerequisites"></a>Prerequisites

首先，請完成 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md)中的設定步驟。 這可確保您擁有 Azure 數位 Twins 實例，而且您的使用者具有存取權限。 完成設定之後，您就可以開始撰寫用戶端應用程式程式碼。

若要繼續，您將需要一個用來撰寫程式碼的用戶端應用程式專案。 如果您尚未設定用戶端應用程式專案，請使用您選擇的語言建立基本專案，以搭配本教學課程使用。

## <a name="common-authentication-methods-with-azureidentity"></a>使用 Azure 身分識別的常見驗證方法

`Azure.Identity` 是一個用戶端程式庫，提供數個認證取得方法，可讓您用來取得持有人權杖，並使用您的 SDK 進行驗證。 雖然本文提供 c # 的範例，但您可以查看 `Azure.Identity` 數種語言，包括 .。。

* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

中有三種常見的認證取得方法 `Azure.Identity` ：

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) `TokenCredential` 會針對將部署至 Azure 的應用程式提供預設驗證流程，而且是 **本機開發的建議選擇** 。 也可以啟用此功能，以嘗試本文中建議的其他兩種方法;它會包裝 `ManagedIdentityCredential` 和存取設定 `InteractiveBrowserCredential` 變數。
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) 適用于您需要 [受控識別 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md)的情況，而且是使用 Azure Functions 及部署至 Azure 服務的絕佳候選。
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) 適用于互動式應用程式，可用於建立已驗證的 SDK 用戶端

下列範例示範如何搭配 .NET (c # ) SDK 來使用上述各項。

## <a name="authentication-examples-net-c-sdk"></a>驗證範例： .NET (c # ) SDK

本節說明 c # 中使用提供的 .NET SDK 來撰寫驗證碼的範例。

首先，請 `Azure.DigitalTwins.Core` 在您的專案中包含 SDK 套件和 `Azure.Identity` 套件。 根據您選擇的工具，您可以使用 Visual Studio 套件管理員或命令列工具來包含套件 `dotnet` 。 

您也需要將下列 using 語句新增至您的專案程式碼：

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

然後，新增程式碼，以使用中的其中一個方法來取得認證 `Azure.Identity` 。

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 方法

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) `TokenCredential` 會針對將部署至 Azure 的應用程式提供預設驗證流程，而且是 **本機開發的建議選擇** 。

若要使用預設的 Azure 認證，您將需要 Azure 數位 Twins 實例的 URL ([指示來尋找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) 。

以下是將加入至專案的程式碼範例 `DefaultAzureCredential` ：

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

#### <a name="set-up-local-azure-credentials"></a>設定本機 Azure 認證

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 方法

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet)方法適用于您需要[受控識別 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md)的情況，例如，使用 Azure Functions 時。

這表示您可以 `ManagedIdentityCredential` 在和或相同的專案中 `DefaultAzureCredential` 使用 `InteractiveBrowserCredential` ，以驗證專案的不同部分。

若要使用預設的 Azure 認證，您將需要 Azure 數位 Twins 實例的 URL ([指示來尋找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) 。

在 Azure 函式中，您可以使用受控識別認證，如下所示：

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 方法

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)方法適用于互動式應用程式，並會啟動網頁瀏覽器以進行驗證。 您可以使用這種方式，而不是 `DefaultAzureCredential` 在需要互動式驗證的情況下使用。

若要使用互動式瀏覽器認證，您將需要具有 Azure 數位 Twins Api 許可權的 **應用程式註冊** 。 如需有關如何設定此應用程式註冊的步驟，請參閱 [*如何：建立應用程式註冊*](how-to-create-app-registration.md)。 一旦設定應用程式註冊，您將需要 .。。
* 應用程式註冊的 *應用程式 (用戶端) 識別碼* ( [要尋找的指示](how-to-create-app-registration.md#collect-client-id-and-tenant-id)) 
* 應用程式註冊的 *目錄 (租使用者) 識別碼* ( [要尋找的指示](how-to-create-app-registration.md#collect-client-id-and-tenant-id)) 
* Azure 數位 Twins 實例的 URL (尋找) 的[指示](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

以下是使用建立已驗證之 SDK 用戶端的程式碼範例 `InteractiveBrowserCredential` 。

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>有關驗證 Azure Functions 的其他注意事項

請參閱作法 [*：設定 Azure 函式來處理資料*](how-to-create-azure-function.md) ，以取得更完整的範例，以說明函式內容中的一些重要設定選項。

此外，若要在函式中使用驗證，請記得：
* [啟用受控識別](../app-service/overview-managed-identity.md?tabs=dotnet)
* 適當地使用[環境變數](/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 指派許可權給函式應用程式，讓它能夠存取數位 Twins Api。 如需 Azure Functions 程式的詳細資訊，請參閱 how [*to：設定用來處理資料的 Azure 函數*](how-to-create-azure-function.md)。

## <a name="other-credential-methods"></a>其他認證方法

如果上述的醒目提示驗證案例並未涵蓋您應用程式的需求，您可以探索 [**Microsoft 身分識別平臺**](../active-directory/develop/v2-overview.md#getting-started)所提供的其他驗證類型。 此平臺的檔涵蓋其他驗證案例，依應用程式類型進行組織。

## <a name="next-steps"></a>下一步

深入瞭解 Azure 數位 Twins 中的安全性運作方式：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)

或者，現在已設定驗證，請繼續在您的實例中建立模型：
* [操作說明：管理自訂模型](how-to-manage-model.md)