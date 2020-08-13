---
title: 權杖快取序列化 (MSAL.NET) |Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用適用於 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 進行權杖快取的序列化和客戶序列化。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4edb0f356dd83ab1aa353e0791f619be497a9d91
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166020"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET 中的權杖快取序列化
在[取得權杖](msal-acquire-cache-tokens.md)後，該權杖會由 Microsoft 驗證程式庫 (MSAL) 快取。  應用程式程式碼應該先試著從快取中取得權杖，再用另一種方法取得權杖。  本文討論 MSAL.NET 中權杖快取的預設和自訂序列化。

本文適用於 MSAL.NET 3.x。 如果您對 MSAL.NET 2.x 有興趣，請參閱 [MSAL.NET 2.x 中的權杖快取序列化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x)。

## <a name="default-serialization-for-mobile-platforms"></a>行動平台的預設序列化

在 MSAL.NET 中，預設會提供記憶體內部權杖快取。 對於使用者有安全的儲存體可以使用的平台，預設會提供序列化。 這適用於通用 Windows 平台 (UWP)、Xamarin.iOS 和 Xamarin.Android。

> [!Note]
> 當您將 Xamarin.Android 專案從 MSAL.NET 1.x 遷移至 MSAL.NET 3.x 時，您可以將 `android:allowBackup="false"` 新增到您的專案，以免當 Visual Studio 部署觸發本機儲存體還原時，無法取回舊的快取權杖。 請參閱[問題 #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938)。

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Windows 傳統型應用程式和 Web 應用程式/Web API 的自訂序列化

請記住，自訂序列化無法在行動平台 (UWP、Xamarin.iOS 和 Xamarin.Android) 上使用。 MSAL 已經為這些平台定義安全且高效能的序列化機制。 不過，.NET 傳統型和 .NET Core 應用程式具有不同的架構，而且 MSAL 無法實作一般用途的序列化機制。 例如，網站可選擇將權杖儲存在 Redis 快取中，或傳統型應用程式會將權杖儲存在已加密的檔案中。 所以序列化並不是立即可用的形式提供。 若要擁有 .NET 傳統型或 .NET Core 架構的永續性權杖快取應用程式，您需要自訂序列化。

下列類別和介面用於權杖快取序列化：

- `ITokenCache`，其定義一些事件來訂閱權杖快取序列化要求，以及定義一些方法來序列化或還原序列化各種格式的快取 (ADAL v3.0、MSAL 2.x 和 MSAL 3.x = ADAL v5.0)。
- `TokenCacheCallback` 是傳遞至事件的回呼，以便您處理序列化。 系統將會透過 `TokenCacheNotificationArgs` 類型的引數進行呼叫。
- `TokenCacheNotificationArgs` 只提供應用程式的 `ClientId` 和參考給可使用權杖的使用者。

  ![類別圖表](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET 會為您建立權杖快取，並且在您呼叫應用程式的 `UserTokenCache` 和 `AppTokenCache` 屬性時提供 `IToken` 快取。 您不應該自行實作介面。 當您實作自訂權杖快取序列化時，您的責任是：
> - 對 `BeforeAccess` 和 `AfterAccess`「事件」(或其 Async 變體) 做出反應。 `BeforeAccess` 委派負責將快取還原序列化，而 `AfterAccess` 負責將快取序列化。
> - 其中有些事件會儲存或載入 Blob，其會透過事件引數傳遞到您想要的儲存體的。

視您要針對[公用用戶端應用程式](msal-client-applications.md) (傳統型) 或[機密用戶端應用程式](msal-client-applications.md)) (Web 應用程式 / Web API、精靈應用程式) 撰寫權杖快取序列化而定，策略會有所不同。

### <a name="token-cache-for-a-public-client"></a>公用用戶端的權杖快取

從 MSAL.NET v2.x 起，您有數個選項可將公用用戶端的權杖快取序列化。 您可以僅將快取序列化為 MSAL.NET 格式 (統一格式快取通用於 MSAL 和平台)。  您也可以支援 ADAL V3 的[舊版](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)權杖快取序列化。

下列範例會說明如何將權杖快取序列化自訂為在 ADAL.NET 3.x、ADAL.NET 5.x 和 MSAL.NET 之間共用單一登入狀態：[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)。

> [!Note]
> MSAL 2.x 不再支援 MSAL.NET 1.1.4-preview 權杖快取格式。 如果您有應用程式運用 MSAL.NET 1.x，您的使用者就必須重新登入。 或者，支援從 ADAL 4.x (和 3.x) 移轉。

#### <a name="simple-token-cache-serialization-msal-only"></a>簡單權杖快取序列化 (僅限 MSAL)

以下是適用於傳統型應用程式的自訂權杖快取序列化的單純實作範例。 在此，使用者權杖快取是位於與應用程式相同資料夾中的檔案。

建置應用程式之後，您可藉由呼叫 `TokenCacheHelper.EnableSerialization()` 方法並傳遞應用程式 `UserTokenCache` 來啟用序列化。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` 協助程式類別會定義為：

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
$"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

針對在 Windows、Mac 和) Linux 上執行的桌面應用程式 (的公用用戶端應用程式，以產品品質權杖快取檔案為基礎的序列化程式，可從[Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)開放原始碼程式庫取得。 您可以從下列 NuGet 套件，將其包含在您的應用程式中：[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>雙重權杖快取序列化 (MSAL 統一快取和 ADAL v3)

如果您想要使用統一快取格式 (通用於相同平台上的 ADAL.NET 4.x、MSAL.NET 2.x 及其他同一代或更舊的 MSAL) 實作權杖快取序列化，請查看下列程式碼：

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

協助程式類別這次定義為：

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web 應用程式 (機密用戶端應用程式) 的權杖快取

在 web 應用程式或 web Api 中，快取可以利用會話、Redis 快取或資料庫。 在 web 應用程式或 web Api 中，您應該為每個帳戶保留一個權杖快取。 

針對 web 應用程式，權杖快取應該以帳戶識別碼做為索引鍵。

針對 web Api，此帳戶應使用用來呼叫 API 之權杖的雜湊來做為索引鍵。

MSAL.NET 提供 .NET Framework 和 .NET Core subplatforms 中的自訂權杖快取序列化。 當存取快取時，會引發事件，應用程式可以選擇要序列化或還原序列化快取。 在處理使用者的機密用戶端應用程式 (登入使用者和呼叫 web Api 的 web 應用程式，以及呼叫下游 web Api) 的 web Api 時，可以有許多使用者，而且使用者會以平行方式處理。 基於安全性和效能的考慮，我們建議您將每個使用者的一個快取序列化。 序列化事件會根據已處理使用者的身分識別來計算快取索引鍵，並將該使用者的權杖快取序列化/還原序列化。

[Microsoft. identity. web](https://github.com/AzureAD/microsoft-identity-web) library 提供包含權杖快取序列化的預覽[NuGet 套件：](https://www.nuget.org/packages/Microsoft.Identity.Web)

| 擴充方法 | Microsoft. Identity. Web sub 命名空間 | 描述  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | 在記憶體權杖快取序列化中。 此實作為範例中的絕佳功能。 如果您不想在重新開機 web 應用程式時遺失權杖快取，也很適合在生產應用程式中提供。 `AddInMemoryTokenCaches`採用類型的選擇性參數 `MsalMemoryTokenCacheOptions` ，其可讓您指定持續時間，直到快取專案使用時才會到期。
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | 權杖快取會系結到使用者會話。 如果識別碼權杖包含許多宣告，因為 cookie 會變得太大，此選項並不理想。
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | 權杖快取是針對 ASP.NET Core 執行的介面卡 `IDistributedCache` ，因此可讓您選擇分散式記憶體快取、Redis 快取、分散式 NCache 或 SQL Server 快取。 如需有關此部署的詳細資訊 `IDistributedCache` ，請參閱 https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache 。

以下是在 ASP.NET Core 應用程式中[啟動](/aspnet/core/fundamentals/startup)類別的[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)方法中使用記憶體中快取的範例：

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

可能的分散式快取範例：

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

其使用方式在階段[2-2 權杖](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)快取的[ASP.NET Core web 應用程式教學](/aspnet/core/tutorials/first-mvc-app/)課程中有其特色。

## <a name="next-steps"></a>後續步驟

下列範例會說明權杖快取序列化。

| 範例 | 平台 | 描述|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | 桌上型 (WPF) | 呼叫 Microsoft Graph API 的 Windows 傳統型 .NET (WPF) 應用程式。 ![拓撲](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | 桌上型 (主控台) | 一組 Visual Studio 解決方案，說明如何使用 ADAL.NET) 將 Azure AD v1.0 應用程式遷移至 Microsoft 身分識別平臺應用程式 ( (使用 MSAL.NET) 。 特別是，請參閱權杖快取[遷移](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|