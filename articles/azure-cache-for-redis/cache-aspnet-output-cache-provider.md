---
title: 適用於 Azure Redis 快取的 ASP.NET 輸出快取提供者
description: 瞭解如何使用 Azure Cache for Redis 快取 ASP.NET 網頁輸出。 Redis 輸出快取提供者為輸出快取資料的程序外儲存體機制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: cb986f1e0172c4a44381b2f9cf043025cb5abe8a
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705022"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>適用於 Azure Redis 快取的 ASP.NET 輸出快取提供者

Redis 輸出快取提供者為輸出快取資料的程序外儲存體機制。 此資料特別適用於完整 HTTP 回應 (頁面輸出快取)。 提供者插入 ASP.NET 4 中導入的新輸出快取提供者擴充點。 對於 ASP.NET Core 應用程式，請 [在 ASP.NET Core 中讀取回應](https://docs.microsoft.com/aspnet/core/performance/caching/response)快取。 

若要使用 Redis 輸出快取提供者，請先設定您的快取，然後使用「Redis 輸出快取提供者 NuGet 封裝」設定 ASP.NET 應用程式。 本主題提供為使用 Redis 輸出快取提供者而進行應用程式設定的相關指引。 如需有關建立及設定「Azure Redis 快取」執行個體的詳細資訊，請參閱[建立快取](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

## <a name="store-aspnet-page-output-in-the-cache"></a>將 ASP.NET 頁面輸出儲存在快取中

若要在 Visual Studio 中使用「Azure Cache for Redis 工作階段狀態」NuGet 套件來設定用戶端應用程式，請從 [工具]**** 功能表中，依序按一下 [NuGet 套件管理員]**** 和 [套件管理員主控台]****。

從 `Package Manager Console` 視窗執行下列命令。

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

「Redis 輸出快取提供者 NuGet 封裝」對「StackExchange.Redis.StrongName 封裝」有相依性。 如果 StackExchange.Redis.StrongName 封裝不在專案中，代表已經安裝。 如需「Redis 輸出快取提供者 NuGet 套件」的詳細資訊，請參閱 [RedisOutputCacheProvider (英文)](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 頁面。

>[!NOTE]
>除了強式名稱的 StackExchange.Redis.StrongName 封裝外，另外還有非強式名稱版本的 StackExchange.Redis。 如果您的專案使用非強式名稱的 >stackexchange.redis. Redis 版本，您必須將它卸載;否則，您會在專案中遇到命名衝突。 如需這些封裝的相關詳細資訊，請參閱 [設定 .NET 快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

NuGet 封裝會下載和加入必要的組件參考，並將下列區段加入您的 web.config 檔案。 此區段包含讓您的 ASP.NET 應用程式使用 Redis 輸出快取提供者所需的設定。

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

以來自 Microsoft Azure 入口網站之快取刀鋒視窗的值來設定屬性，並視需要設定其他值。 如需關於存取快取屬性的指示，請參閱[設定 Azure Cache for Redis 設定](cache-configure.md#configure-azure-cache-for-redis-settings)。

| 屬性 | 類型 | 預設 | 描述 |
| --------- | ---- | ------- | ----------- |
| *主機* | 字串 | 主機 | Redis 伺服器 IP 位址或主機名稱 |
| *port* | 正整數 | 6379 (非 TLS/SSL) <br/>6380 (TLS/SSL)  | Redis 伺服器埠 |
| *accessKey* | 字串 | "" | 啟用 Redis 授權時的 Redis 伺服器密碼。 預設值為空字串，表示會話狀態提供者在連線到 Redis 伺服器時，不會使用任何密碼。 **如果您的 Redis 伺服器位於可公開存取的網路（例如 Azure Redis Cache）中，請務必啟用 Redis 授權以改善安全性，並提供安全的密碼。** |
| *Ssl* | boolean | **false** | 是否透過 TLS 連接到 Redis 伺服器。 此值預設為 **false** ，因為 Redis 不支援現成的 TLS。 **如果您使用的 Azure Redis 快取支援現成的 SSL，請務必將此設定為 true，以提升安全性。**<br/><br/>新快取的非 TLS 埠預設為停用。 針對此設定指定 **true** ，以使用非 TLS 埠。 如需啟用非 TLS 埠的詳細資訊，請參閱[設定](cache-configure.md)快取主題中的「[存取埠](cache-configure.md#access-ports)」一節。 |
| *databaseIdNumber* | 正整數 | 0 | *這個屬性只能透過 web.config 或 AppSettings 來指定。*<br/><br/>指定要使用的 Redis 資料庫。 |
| *connectionTimeoutInMilliseconds* | 正整數 | 由 >stackexchange.redis Redis 提供。 | 用來在建立 >stackexchange.redis. Redis. ConnectionMultiplexer 時設定 *ConnectTimeout* 。 |
| *operationTimeoutInMilliseconds* | 正整數 | 由 >stackexchange.redis Redis 提供。 | 用來在建立 >stackexchange.redis. Redis. ConnectionMultiplexer 時設定 *SyncTimeout* 。 |
| *connectionString* (有效的 >stackexchange.redis. Redis 連接字串)  | 字串 | *n/a* | AppSettings 或 web.config 的參數參考，或其他有效的 >stackexchange.redis. Redis 連接字串。 這個屬性可以提供 *host*、 *port*、 *AccessKey*、 *ssl*和其他 >stackexchange.redis Redis 屬性的值。 若要深入瞭解*connectionString*，請參閱[屬性附注](#attribute-notes)一節中的[設定 connectionString](#setting-connectionstring) 。 |
| *settingsClassName*<br/>*settingsMethodName* | 字串<br/>字串 | *n/a* | *這些屬性只能透過 web.config 或 AppSettings 來指定。*<br/><br/>使用這些屬性來提供連接字串。 *settingsClassName* 應該是元件限定的類別名稱，其中包含 *settingsMethodName*所指定的方法。<br/><br/>*SettingsMethodName*所指定的方法應該是 public、static 和 void (不採用任何) 的參數，其傳回型別為**string**。 這個方法會傳回實際的連接字串。 |
| *loggingClassName*<br/>*loggingMethodName* | 字串<br/>字串 | *n/a* | *這些屬性只能透過 web.config 或 AppSettings 來指定。*<br/><br/>藉由提供會話狀態/輸出快取的記錄以及 >stackexchange.redis. Redis 的記錄，即可使用這些屬性來對應用程式進行偵錯工具。 *loggingClassName* 應該是元件限定的類別名稱，其中包含 *loggingMethodName*所指定的方法。<br/><br/>LoggingMethodName 所指定的方法應該是 public、static 和 void (不會採用任何) 的參數，且傳回型別**為** *loggingMethodName* 。 |
| *applicationName* | 字串 | 目前進程的模組名稱，或 "/" | *僅限 SessionStateProvider*<br/>*這個屬性只能透過 web.config 或 AppSettings 來指定。*<br/><br/>要在 Redis 快取中使用的應用程式名稱前置詞。 客戶可使用相同的 Redis 快取來進行不同的用途。 為確保工作階段金鑰不會衝突，可以在前面加上應用程式名稱。 |
| *throwOnError* | boolean | true | *僅限 SessionStateProvider*<br/>*這個屬性只能透過 web.config 或 AppSettings 來指定。*<br/><br/>發生錯誤時，是否擲回例外狀況。<br/><br/>如需*throwOnError*的詳細資訊，請參閱[屬性附注](#attribute-notes)一節中[ *throwOnError*的附注](#notes-on-throwonerror)。 |>*Redis. >redissessionstateprovider. LastException*。 |
| *retryTimeoutInMilliseconds* | 正整數 | 5000 | *僅限 SessionStateProvider*<br/>*這個屬性只能透過 web.config 或 AppSettings 來指定。*<br/><br/>作業失敗時要重試的時間長度。 如果這個值小於 *operationTimeoutInMilliseconds*，提供者將不會重試。<br/><br/>如需*retryTimeoutInMilliseconds*的詳細資訊，請參閱[屬性附注](#attribute-notes)一節中[ *retryTimeoutInMilliseconds*的附注](#notes-on-retrytimeoutinmilliseconds)。 |
| *redisSerializerType* | 字串 | *n/a* | 指定 Redis 之類別的元件限定型別名稱。 ISerializer，其中包含要序列化和還原序列化值的自訂邏輯。 如需詳細資訊，請參閱[屬性附注](#attribute-notes)一節中的[關於*redisSerializerType* ](#about-redisserializertype) 。 |

## <a name="attribute-notes"></a>屬性附注

### <a name="setting-connectionstring"></a>設定 *connectionString*

如果 AppSettings 中存在這類字串，則 *connectionString* 的值會當做索引鍵，以便從 AppSettings 提取實際的連接字串。 如果在 AppSettings 中找不到， *connectionString* 的值將作為索引鍵，以從 web.config **connectionString** 區段提取實際的連接字串（如果該區段存在的話）。 如果連接字串不存在於 AppSettings 或 web.config **ConnectionString** 區段中，則在建立 >stackexchange.redis. Redis. ConnectionMultiplexer 時， *ConnectionString* 的常值會用來做為連接字串。

下列範例說明如何使用 *connectionString* 。

#### <a name="example-1"></a>範例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

在中 `web.config` ，使用上述索引鍵做為參數值，而不是實際值。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>範例 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

在中 `web.config` ，使用上述索引鍵做為參數值，而不是實際值。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>範例 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>*ThrowOnError*上的注意事項

目前，如果會話作業期間發生錯誤，會話狀態提供者將會擲回例外狀況。 這會關閉應用程式。

此行為已經過修改，可支援現有 ASP.NET 會話狀態提供者使用者的期望，同時也提供在需要時處理例外狀況的能力。 當發生錯誤時，預設行為仍然會擲回例外狀況，與其他 ASP.NET 會話狀態提供者一致;現有程式碼的運作方式應該與之前相同。

如果您將 *throwOnError* 設定為 **false**，則不會在錯誤發生時擲回例外狀況，而是會無訊息地失敗。 若要查看是否有錯誤，如果有，請探索例外狀況，並檢查 Redis 的靜態屬性。 *>redissessionstateprovider. LastException*。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>*RetryTimeoutInMilliseconds*上的注意事項

這會提供一些重試邏輯，以簡化某些會話作業應該在失敗時重試的情況（因為網路問題），同時也可讓您控制重試超時或完全退出重試。

如果您將 *retryTimeoutInMilliseconds* 設定為數字（例如2000），則當會話作業失敗時，將會重試2000毫秒，然後將它視為錯誤。 因此，若要讓會話狀態提供者套用此重試邏輯，只要設定 timeout 即可。 第一次重試會在20毫秒後發生，這在大多數情況下都已足夠，因為發生網路問題。 之後它會每秒重試一次。在經過一段時間之後，它會再重試一次，以確保它不會在一秒的最)  (的情況下減少。

如果您不認為需要重試 (例如，當您在與應用程式相同的電腦上執行 Redis 伺服器時) 或者，如果您想要自行處理重試邏輯，請將 *retryTimeoutInMilliseconds* 設定為0。

### <a name="about-redisserializertype"></a>關於 *redisSerializerType*

根據預設，將值儲存在 Redis 上的序列化會以 **BinaryFormatter** 類別所提供的二進位格式來完成。 使用 *redisSerializerType* 來指定類別的元件限定型別名稱，這個類別會 **Redis ISerializer** ，而且具有自訂邏輯來序列化和還原序列化值。 例如，以下是使用 JSON.NET 的 Json 序列化程式類別：

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

假設這個類別是在名稱為 **MyCompanyDll**的元件中定義，您可以將參數 *redisSerializerType* 設定為使用它：

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Output cache 指示詞

將 OutputCache 指示詞新增至每一個您要快取輸出的頁面。

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

在上一個範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。 如需 OutputCache 指示詞的詳細資訊，請參閱 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837) 。

一旦執行這些步驟，您的應用程式將設定為使用 Redis 輸出快取提供者。

## <a name="third-party-output-cache-providers"></a>協力廠商輸出快取提供者

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>後續步驟

請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)。
