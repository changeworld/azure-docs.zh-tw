---
title: 記錄 MSAL.NET 中的錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何在 MSAL.NET 中記錄錯誤和例外狀況
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47576265c9b1a20f801231abe2fb37a929d5a27c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763368"
---
# <a name="logging-in-msalnet"></a>MSAL.NET 中的記錄

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>在 MSAL.NET 中設定記錄

在 MSAL 3.x 中，記錄必須在每個應用程式的建立期間，使用 `.WithLogging` 建立器修飾詞針對該應用程式進行個別設定。 此方法可接受選擇性參數：

- `Level` 可讓您決定想要的記錄層級。 將它設定為 Errors 將只會取得錯誤
- `PiiLoggingEnabled` 如果設定為 true，可讓您記錄個人和組織資料。 此參數預設為 false，這會使您的應用程式不會記錄個人資料。
- `LogCallback` 設定為執行記錄的委派。 如果 `PiiLoggingEnabled` 是 true，這個方法會接收訊息兩次：一次使用 `containsPii` 參數等於 false，而不含個人資料的訊息，而第二次 `containsPii` 參數等於 true 且訊息可能包含個人資料。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。
- `DefaultLoggingEnabled` 啟用平臺的預設記錄。 根據預設，它是 false。 如果您將它設定為 true，它會使用桌面/UWP 應用程式中的事件追蹤、iOS 上的 NSLog，以及 Android 上的 logcat。

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > 如需 MSAL.NET 記錄和其他的範例，請參閱 [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 。

## <a name="next-steps"></a>後續步驟

如需更多程式碼範例，請參閱 [Microsoft 身分識別平臺程式碼範例) ](sample-v2-code.md)。