---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 50819e8746860e72feda194915f75c4630677d0c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506230"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 適用於您的作業系統的最新版本 [.NET Core 用戶端程式庫](https://dotnet.microsoft.com/download/dotnet-core)。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `AccessTokensQuickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：**Program.cs**。

```console
dotnet new console -o AccessTokensQuickstart
```

將您的目錄變更為新建立的應用程式資料夾，然後使用 `dotnet build` 命令來編譯您的應用程式。

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `dotnet add package` 命令安裝適用於 .NET 套件的 Azure 通訊服務系統管理程式庫。

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在文字編輯器中開啟 **Program.cs**
1. 新增 `using` 指示詞以包含 `Azure.Communication.Administration` 命名空間
1. 更新 `Main` 方法宣告以支援非同步程式碼

使用下列程式碼開始作業：

```csharp
using System;
using Azure.Communication.Administration;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>驗證用戶端

使用連接字串初始化 `CommunicationIdentityClient`。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../create-communication-resource.md#store-your-connection-string)。

將下列程式碼加入 `Main` 方法：

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-an-identity"></a>建立身分識別

Azure 通訊服務會維護輕量身分識別目錄。 使用 `createUser` 方法，在目錄中建立具有唯一 `Id` 的新項目。儲存已接收的身分識別，並對應至您應用程式的使用者。 例如，將其儲存在您應用程式伺服器的資料庫中。 後續將需要以身分識別簽發存取權杖。

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>簽發身分識別存取權杖

使用 `issueToken` 方法來簽發現有通訊服務身分識別的存取權杖。 參數 `scopes` 會定義一組基本類型，進行此存取權杖的授權。 請參閱[支援的動作清單](../../concepts/authentication.md)。 您可以根據 Azure 通訊服務身分識別的字串表示法，建立參數 `communicationUser` 的新執行個體。

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

存取權杖是需要重新簽發的短期認證。 若未這麼做，可能會導致應用程式的使用者體驗中斷。 `expiresOn` 回應屬性會指出存取權杖的存留期。 

## <a name="refresh-access-tokens"></a>重新整理存取權杖

若要重新整理存取權杖，請使用 `CommunicationUser` 物件來重新簽發：

```csharp  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = new CommunicationUser(existingIdentity);
tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>撤銷存取權杖

在某些情況下，您可能要明確地撤銷存取權杖。 例如，當應用程式的使用者變更用來向您的服務進行驗證的密碼時。 方法 `RevokeTokensAsync` 會使簽發給身分識別的所有作用中存取權杖失效。

```csharp  
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>刪除身分識別

刪除身分識別會撤銷所有作用中的存取權杖，並防止您核發身分識別的存取權杖。 此外也會移除所有與身分識別相關聯的保存內容。

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>執行程式碼

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```console
dotnet run
```
