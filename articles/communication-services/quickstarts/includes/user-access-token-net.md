---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779353"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 適用於您的作業系統的最新版本 [.NET Core 用戶端程式庫](https://dotnet.microsoft.com/download/dotnet-core)。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `UserAccessTokensQuickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：**Program.cs**。

```console
dotnet new console -o UserAccessTokensQuickstart
```

將您的目錄變更為新建立的應用程式資料夾，然後使用 `dotnet build` 命令來編譯您的應用程式。

```console
cd UserAccessTokensQuickstart
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

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用連接字串初始化 `CommunicationIdentityClient`。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../create-communication-resource.md#store-your-connection-string)。

將下列程式碼加入 `Main` 方法：

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>建立使用者

Azure 通訊服務會維護輕量身分識別目錄。 使用 `createUser` 方法，在目錄中建立具有唯一 `Id` 的新項目。 您應該維護應用程式使用者與通訊服務所產生身分識別之間的對應 (例如，將其儲存在應用程式伺服器的資料庫中)。

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>發行使用者存取權杖

使用 `issueToken` 方法來發行通訊服務使用者的存取權杖。 如果您未提供選擇性的 `user` 參數，則會建立新的使用者，並連同權杖一起傳回。

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

使用者存取權杖是短期的認證，需要重新發行才能防止使用者發生服務中斷。 `expiresOn` 回應屬性會指出權杖的存留期。

## <a name="revoke-user-access-tokens"></a>撤銷使用者存取權杖

在某些情況下，您可能需要明確撤銷使用者存取權杖，例如，當使用者變更用來向服務進行驗證的密碼時。 這是透過 Azure 通訊服務系統管理用戶端程式庫提供的功能。

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>刪除使用者

刪除身分識別會撤銷所有作用中的權杖，並防止您發行身分識別的後續權杖。 也會移除所有與使用者相關聯的保存內容。

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>執行程式碼

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```console
dotnet run
```
