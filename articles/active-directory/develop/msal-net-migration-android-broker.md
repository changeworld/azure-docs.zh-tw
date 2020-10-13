---
title: 使用代理程式將 Xamarin Android 應用程式遷移至 MSAL.NET
titleSuffix: Microsoft identity platform
description: 瞭解如何將使用 Microsoft Authenticator 或 Intune 公司入口網站從 ADAL.NET 遷移至 MSAL.NET 的 Xamarin Android 應用程式。
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89183586"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>將使用 broker 的 Android 應用程式從 ADAL.NET 遷移至 MSAL.NET

如果您目前有 Xamarin Android 應用程式正在使用適用于 .NET 的 Azure Active Directory Authentication Library (ADAL.NET) 以及 [驗證訊息代理](brokered-auth.md)程式，現在可以遷移至 [適用于 .Net 的 Microsoft 驗證程式庫 ](msal-overview.md) (MSAL.NET) 。

## <a name="prerequisites"></a>必要條件

* Xamarin Android 應用程式已經與 broker ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [Intune 公司入口網站](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) 和 ADAL.NET，您必須遷移至 MSAL.NET。

## <a name="step-1-enable-the-broker"></a>步驟1：啟用訊息代理程式

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
在 ADAL.NET 中，會根據每個驗證的內容來啟用 broker 支援。

若要呼叫訊息代理程式，您必須在此函式中將設定 `useBroker` 為 *true* `PlatformParameters` ：

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

在適用于 Android 的平臺專屬頁面轉譯器程式碼中，您可以將旗標設定 `useBroker` 為 true：

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

然後，將參數包含在取得權杖呼叫中：

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，會以每個 PublicClientApplication 為基礎啟用 broker 支援。

使用 `WithBroker()` (預設設定為 true) 的參數，以呼叫 broker：

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

然後，在 AcquireToken 呼叫中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>步驟2：設定活動

在 ADAL.NET 中，您傳入的活動 (通常是 >mainactivity) 作為 PlatformParameters 的一部分，如 [步驟1：啟用訊息代理](#step-1-enable-the-broker)程式所示。

MSAL.NET 也會使用活動，但在沒有 broker 的情況下，不需要在一般的 Android 中使用。 若要使用訊息代理程式，請將活動設定為從 broker 傳送和接收回應。

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
活動會傳遞至 Android 特定平臺中的 PlatformParameters。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

在 MSAL.NET 中，請執行下列兩項動作來設定 Android 的活動：

1. 在中 `MainActivity.cs` ，將設定 `App.RootViewController`  為， `MainActivity` 以確保有活動與 broker 的呼叫。

    如果設定不正確，您可能會收到此錯誤： `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. 在 AcquireTokenInteractive 呼叫中，使用 `.WithParentActivityOrWindow(App.RootViewController)` 並傳入您將使用之活動的參考。 此範例會使用 >mainactivity。

**例如：**

在 *App.cs*中：

```CSharp
   public static object RootViewController { get; set; }
```

在 *MainActivity.cs*中：

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

在 AcquireToken 呼叫中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>後續步驟

如需有關搭配 Xamarin 使用 MSAL.NET 時 Android 特定考慮的詳細資訊，請參閱 [Xamarin Android with MSAL.NET 的設定需求和疑難排解秘訣](msal-net-xamarin-android-considerations.md)。