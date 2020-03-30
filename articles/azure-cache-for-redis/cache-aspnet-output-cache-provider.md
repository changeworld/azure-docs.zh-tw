---
title: 適用於 Azure Redis 快取的 ASP.NET 輸出快取提供者
description: 瞭解如何使用 Redis 的 Azure 緩存緩存ASP.NET頁輸出。 Redis 輸出快取提供者為輸出快取資料的程序外儲存體機制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: af003f1f0422c2351bcdf9b0c0010e38785c0344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530320"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>適用於 Azure Redis 快取的 ASP.NET 輸出快取提供者

Redis 輸出快取提供者為輸出快取資料的程序外儲存體機制。 此資料特別適用於完整 HTTP 回應 (頁面輸出快取)。 提供者插入 ASP.NET 4 中導入的新輸出快取提供者擴充點。

若要使用 Redis 輸出快取提供者，請先設定您的快取，然後使用「Redis 輸出快取提供者 NuGet 封裝」設定 ASP.NET 應用程式。 本主題提供為使用 Redis 輸出快取提供者而進行應用程式設定的相關指引。 如需有關建立及設定「Azure Redis 快取」執行個體的詳細資訊，請參閱[建立快取](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

## <a name="store-aspnet-page-output-in-the-cache"></a>將 ASP.NET 頁面輸出儲存在快取中

若要在 Visual Studio 中使用「Azure Cache for Redis 工作階段狀態」NuGet 套件來設定用戶端應用程式，請從 [工具]**** 功能表中，依序按一下 [NuGet 套件管理員]**** 和 [套件管理員主控台]****。

從 `Package Manager Console` 視窗執行下列命令。

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

「Redis 輸出快取提供者 NuGet 封裝」對「StackExchange.Redis.StrongName 封裝」有相依性。 如果 StackExchange.Redis.StrongName 封裝不在專案中，代表已經安裝。 如需「Redis 輸出快取提供者 NuGet 套件」的詳細資訊，請參閱 [RedisOutputCacheProvider (英文)](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 頁面。

>[!NOTE]
>除了強式名稱的 StackExchange.Redis.StrongName 封裝外，另外還有非強式名稱版本的 StackExchange.Redis。 如果專案使用的是非強式名稱的 StackExchange.Redis 版本，則必須卸載它;如果專案使用非強式名稱的 StackExchange.Redis 版本，則必須卸載它。否則，您將在專案中遇到命名衝突。 如需這些封裝的相關詳細資訊，請參閱 [設定 .NET 快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

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
| *主機* | 字串 | "本地主機" | Redis 伺服器 IP 位址或主機名稱 |
| *港口* | 正整數 | 6379 （非 SSL）<br/>6380 （SSL） | 雷裡斯伺服器埠 |
| *訪問金鑰* | 字串 | "" | 啟用 Redis 授權時，Redis 伺服器密碼。 預設情況下，該值為空字串，這意味著會話狀態提供程式在連接到 Redis 伺服器時不會使用任何密碼。 **如果 Redis 伺服器位於 Azure Redis 緩存等可公開訪問的網路中，請確保啟用 Redis 授權以提高安全性，並提供安全密碼。** |
| *Ssl* | boolean | **false** | 是否通過 SSL 連線到 Redis 伺服器。 預設情況下，此值**為 false，** 因為 Redis 不支援開箱即用的 SSL。 **如果使用 Azure Redis 緩存，該緩存在開箱即用時支援 SSL，請確保將此值設置為 true 以提高安全性。**<br/><br/>預設會為新快取停用非 SSL 連接埠。 為此設置指定**true**以使用 SSL 埠。 如需啟用非 SSL 連接埠的相關詳細資訊，請參閱[設定快取](cache-configure.md)主題中的[存取連接埠](cache-configure.md#access-ports)一節。 |
| *資料庫 IdNumber* | 正整數 | 0 | *此屬性只能通過 Web.config 或 AppSettings 指定。*<br/><br/>指定要使用的 Redis 資料庫。 |
| *連線時間在毫秒內* | 正整數 | 由堆疊交換提供. Redis | 用於在創建堆疊交換時設置*連接逾時*. Redis.Connection 多工器。 |
| *操作 時間不 在毫秒內* | 正整數 | 由堆疊交換提供. Redis | 用於在創建堆疊交換.Redis.Connection 多工器時設置*同步超時*。 |
| *連接字串*（有效堆疊交換.Redis 連接字串） | 字串 | *不適用* | 對 AppSettings 或 Web.config 的參數引用，或者有效的 StackExchange.Redis 連接字串。 此屬性可以為*主機*、*埠*、*訪問金鑰**、ssl*和其他 StackExchange.Redis 屬性提供值。 有關*連接字串*的仔細查看，請參閱[在屬性注釋](#attribute-notes)部分[設置連接String。](#setting-connectionstring) |
| *設置 類名稱*<br/>*設置 方法名稱* | 字串<br/>字串 | *不適用* | *這些屬性只能通過 Web.config 或 AppSettings 來指定。*<br/><br/>使用這些屬性提供連接字串。 *設置ClassName*應該是一個程式集限定類名稱，其中包含*由"方法名稱*"指定的方法。<br/><br/>設置指定的方法*方法方法方法方法*方法應為公共、靜態和 void（不採用任何參數），具有返回類型的**字串**。 此方法返回實際的連接字串。 |
| *日誌記錄類名稱*<br/>*日誌記錄方法名稱* | 字串<br/>字串 | *不適用* | *這些屬性只能通過 Web.config 或 AppSettings 來指定。*<br/><br/>使用這些屬性通過提供來自會話狀態/輸出緩存的日誌以及來自 StackExchange.Redis 的日誌來調試應用程式。 *日誌記錄ClassName*應該是一個程式集限定類名稱，其中包含*日誌記錄方法名稱*指定的方法。<br/><br/>*日誌記錄MethodName*指定的方法應該是公共的、靜態的和 void 的（不採用任何參數），返回類型為**System.IO.TextWriter**。 |
| *應用程式名稱* | 字串 | 當前流程的模組名稱或"/" | *僅限會話狀態提供程式*<br/>*此屬性只能通過 Web.config 或 AppSettings 指定。*<br/><br/>要在 Redis 緩存中使用的應用名稱首碼。 客戶可能出於不同目的使用相同的 Redis 緩存。 為了確保工作階段金鑰不衝突，可以使用應用程式名稱預固定工作階段金鑰。 |
| *引發錯誤* | boolean | true | *僅限會話狀態提供程式*<br/>*此屬性只能通過 Web.config 或 AppSettings 指定。*<br/><br/>是否在發生錯誤時引發異常。<br/><br/>有關 *"引發錯誤*"的詳細資訊，請參閱[屬性注釋](#attribute-notes)部分[中有關 *"引發OnError"*](#notes-on-throwonerror)的說明。 |>*微軟.Web.Redis.Redis會話狀態提供程式.最後例外*。 |
| *重試時間在毫秒內* | 正整數 | 5000 | *僅限會話狀態提供程式*<br/>*此屬性只能通過 Web.config 或 AppSettings 指定。*<br/><br/>操作失敗時重試多長時間。 如果此值小於*操作超時，* 則提供程式不會重試。<br/><br/>有關*重試時間出在"毫秒"中*，請參閱[屬性注釋](#attribute-notes)部分[中關於*重試時間出在"毫秒"* 中的注釋](#notes-on-retrytimeoutinmilliseconds)。 |
| *紅色序列器類型* | 字串 | *不適用* | 指定實現 Microsoft.Web.Redis 的類的程式集限定類型名稱。 I序列化器，其中包含自訂邏輯以序列化和非序列化值。 有關詳細資訊，請參閱[屬性注釋](#attribute-notes)部分中[關於 *"反序列化器類型*](#about-redisserializertype)"。。 |

## <a name="attribute-notes"></a>屬性注釋

### <a name="setting-connectionstring"></a>設置*連接字串*

如果 AppSettings 中存在此類字串，則*連接String*的值用作從 AppSettings 提取實際連接字串的鍵。 如果在 AppSettings 中找不到，*則連接String*的值將用作從 Web.config**連接字串**部分獲取實際連接字串的鍵（如果存在）。 如果連接字串在 AppSettings 或 Web.config**連接字串**部分中不存在，則在創建 StackExchange.Redis.ConnectMultixer 時，*連接字串*的文本值將用作連接字串。

以下示例說明了如何使用*連接String。*

#### <a name="example-1"></a>範例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

在`web.config`中，將上方的鍵用作參數值而不是實際值。

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

在`web.config`中，將上方的鍵用作參數值而不是實際值。

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

### <a name="notes-on-throwonerror"></a>關於*投擲的注釋*

目前，如果在會話操作期間發生錯誤，會話狀態提供程式將引發異常。 這將關閉應用程式。

此行為已被修改，以支援現有ASP.NET會話狀態提供程式使用者的期望，同時提供根據需要對異常執行操作的能力。 發生錯誤時，預設行為仍引發異常，與其他ASP.NET會話狀態提供程式一致;現有代碼應與以前相同。

如果將*throwOnError*設置為**false，** 則在發生錯誤時不要引發異常，而是以靜默方式失敗。 要查看是否有錯誤，如果是，請檢查異常是什麼，請查看靜態屬性*Microsoft.Web.Redis.Redis.RedisSessionState提供程式.Lastexception*。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>關於*重試時間不毫秒的說明*

這提供了一些重試邏輯，以簡化某些會話操作由於網路故障等故障而應重試的情況，同時還允許您控制重試超時或完全退出重試。

如果將*重試時間不計算*設置為數字（例如 2000，則當會話操作失敗時）將重試 2000 毫秒，然後再將其視為錯誤。 因此，要讓會話狀態提供程式應用此重試邏輯，只需配置超時。 第一次重試將在 20 毫秒後進行，在大多數情況下，當發生網路故障時，這就足夠了。 之後，它將重試每一秒，直到它超時。超時後，它將重試一次，以確保它不會縮短超時（最多）一秒。

如果您認為不需要重試（例如，當您在與應用程式相同的電腦上運行 Redis 伺服器時），或者如果您要自己處理重試邏輯，請將*重試超時 Inmilliseconds*設置為 0。

### <a name="about-redisserializertype"></a>關於*反序列化器類型*

預設情況下，在 Redis 上存儲值的序列化以**BinaryFormatter**類提供的二進位格式完成。 使用*redis 序列化器類型*指定實現**Microsoft.Web.Redis.I 序列化器**並具有自訂邏輯以序列化和非序列化值的類的程式集限定類型名稱。 例如，下面是使用 JSON.NET 的 Json 序列化器類：

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

假設類是在名稱**MyCompanyDll**的程式集中定義的，則可以將參數*redisSerializerType*設置為使用它：

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

## <a name="output-cache-directive"></a>輸出緩存指令

將 OutputCache 指示詞新增至每一個您要快取輸出的頁面。

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

在上一個範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。 有關輸出緩存指令的詳細資訊，請參閱[@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)。

一旦執行這些步驟，您的應用程式將設定為使用 Redis 輸出快取提供者。

## <a name="third-party-output-cache-providers"></a>協力廠商輸出緩存提供程式

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [阿帕奇點火](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>後續步驟

請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)。
