---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: c044e9dc7b8ee622b157a93726e7700e62ba2dfa
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779716"
---
藉由使用通訊服務 C# SMS 用戶端程式庫來傳送 SMS 訊息，以開始使用 Azure 通訊服務。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 適用於您的作業系統的最新版本 [.NET Core 用戶端程式庫](https://dotnet.microsoft.com/download/dotnet-core)。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../../create-communication-resource.md)。
- 已啟用 SMS 的電話號碼。 [取得電話號碼](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先決條件檢查

- 在終端機或命令視窗中執行 `dotnet` 命令，確認已安裝 .NET 用戶端程式庫。
- 若要檢視與您通訊服務資源相關聯的電話號碼，請登入 [Azure 入口網站](https://portal.azure.com/)、尋找您的通訊服務資源，然後從左側瀏覽窗格開啟 [電話號碼] 索引標籤。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `SmsQuickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：**Program.cs**。

```console
dotnet new console -o SmsQuickstart
```

將您的目錄變更為新建立的應用程式資料夾，然後使用 `dotnet build` 命令來編譯您的應用程式。

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `dotnet add package` 命令安裝適用於 .NET 套件的 Azure 通訊服務 SMS 用戶端程式庫。

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.2
```

將 `using` 指示詞新增至 **Program.cs** 的頂端，以包含 `Azure.Communication` 命名空間。

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務 SMS 用戶端程式庫的一些主要 C# 功能。

| Name                                       | 描述                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | 這是所有 SMS 功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來傳送 SMS 訊息。                           |
| SendSmsOptions | 此類別提供設定傳遞報告的選項。 如果 enable_delivery_report 設定為 True，則事件會在傳遞成功時發出。 |

## <a name="authenticate-the-client"></a>驗證用戶端

 在文字編輯器中開啟 **Program.cs**，並將 `Main` 方法的本文取代為程式碼，以使用您的連接字串來初始化 `SmsClient`。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../../create-communication-resource.md#store-your-connection-string)。


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>傳送 SMS 訊息

呼叫 Send 方法，以傳送 SMS 訊息。 將此程式碼加入到 **Program.cs** 中 `Main` 方法的結尾處：

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

您應該將 `<leased-phone-number>` 取代為與您通訊服務資源相關聯且已啟用 SMS 的電話號碼，並使用您想要對其傳送訊息的電話號碼取代 `<to-phone-number>`。

`EnableDeliveryReport` 參數是選擇性參數，可讓您用來設定傳遞報告。 這適用於想要在傳遞 SMS 訊息時發出事件的案例。 請參閱[處理 SMS 事件](../handle-sms-events.md)快速入門，以設定 SMS 訊息的傳遞報告。

## <a name="run-the-code"></a>執行程式碼

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```console
dotnet run
```
