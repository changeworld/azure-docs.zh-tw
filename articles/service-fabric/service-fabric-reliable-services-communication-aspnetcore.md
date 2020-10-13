---
title: 服務與 ASP.NET Core 的通訊
description: 瞭解如何在無狀態和具狀態的 Azure Service Fabric Reliable Services 應用程式中使用 ASP.NET Core。
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 69423e7545178fd74ad44f5cab7b37b6f24b3577
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022185"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric Reliable Services 中的 ASP.NET Core

ASP.NET Core 是開放原始碼和跨平臺架構。 此架構的設計目的是要建立雲端式、網際網路連線的應用程式，例如 web 應用程式、IoT 應用程式和行動後端。

本文是使用**ServiceFabric AspNetCore**在 Service Fabric Reliable Services 中裝載 ASP.NET Core 服務的深入指南。 NuGet 套件的集合。

如需 Service Fabric 中 ASP.NET Core 的簡介教學課程，以及如何設定開發環境的指示，請參閱 [教學課程：使用 ASP.NET Core WEB API 前端服務和具狀態後端服務來建立和部署應用程式](service-fabric-tutorial-create-dotnet-app.md)。

本文的其餘部分假設您已經熟悉 ASP.NET Core。 如果沒有，請仔細閱讀 [ASP.NET Core 的基本](/aspnet/core/fundamentals/index)概念。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 環境中的 ASP.NET Core

ASP.NET Core 和 Service Fabric 應用程式都可以在 .NET Core 或完整 .NET Framework 上執行。 您可以透過兩種不同的方式，在 Service Fabric 中使用 ASP.NET Core：
 - **裝載作為客體可執行檔**。 這種方法主要是用來在 Service Fabric 上執行現有的 ASP.NET Core 應用程式，而不需變更程式碼。
 - **在可靠的服務內執行**。 如此一來，就能更有效地整合 Service Fabric 執行時間，並允許具狀態的 ASP.NET Core 服務。

本文的其餘部分將說明如何透過 Service Fabric SDK 隨附的 ASP.NET Core 整合元件，在可靠的服務內使用 ASP.NET Core。

## <a name="service-fabric-service-hosting"></a>Service Fabric 服務裝載

在 Service Fabric 中，服務的一個或多個實例及/或複本會在 *服務主機進程*中執行：執行您的服務程式代碼的可執行檔。 您以服務作者的身份，擁有服務主機進程，Service Fabric 為您啟用並監視。

傳統 ASP.NET (直到 MVC 5) 已透過 System.Web.dll 與 IIS 緊密結合。 ASP.NET Core 能區別網頁伺服器與 web 應用程式。 這種分隔可讓 web 應用程式在不同的網頁伺服器之間可移植。 它也可讓 web 伺服器 *自我*裝載。 這表示您可以在自己的程式中啟動網頁伺服器，而不是由專用 web 伺服器軟體（例如 IIS）所擁有的進程啟動。

若要結合 Service Fabric 服務和 ASP.NET （可做為來賓可執行檔或可靠的服務），您必須能夠在服務主機進程內啟動 ASP.NET。 ASP.NET Core 自我裝載可讓您執行這項操作。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>在可靠的服務中裝載 ASP.NET Core
一般而言，自我裝載的 ASP.NET Core 應用程式會在應用程式的進入點建立 WebHost，例如 `Program.cs` 方法中的 `static void Main()`。 在此情況下，WebHost 的生命週期會系結至進程的生命週期。

![在處理序中裝載 ASP.NET Core][0]

但應用程式進入點並不是在可靠服務中建立 WebHost 的正確位置。 這是因為應用程式進入點僅用來向 Service Fabric 執行時間註冊服務類型，因此它可以建立該服務類型的實例。 WebHost 應該建立在可靠的服務本身。 在服務主機進程內，服務實例及/或複本可以經歷多個生命週期。 

Reliable Service 執行個體是由您衍生自 `StatelessService` 或 `StatefulService` 的服務類別代表。 服務的通訊堆疊包含在您服務類別中的 `ICommunicationListener` 實作。 `Microsoft.ServiceFabric.AspNetCore.*`NuGet 套件包含的實作為 `ICommunicationListener` ，可啟動及管理在可靠服務中 Kestrel 或 HTTP.sys 的 ASP.NET Core WebHost。

![在可靠服務中裝載 ASP.NET Core 的圖表][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener`NuGet 套件中的 Kestrel 和 HTTP.sys 的實 `Microsoft.ServiceFabric.AspNetCore.*` 有類似的使用模式。 但它們執行的動作與每個 web 伺服器的特定動作稍有不同。 

這兩種通訊接聽程式都會提供使用下列引數的建構函式︰
 - **`ServiceContext serviceContext`**：此 `ServiceContext` 物件包含執行中服務的相關資訊。
 - **`string endpointName`**：這是 ServiceManifest.xml 中的設定名稱 `Endpoint` 。 這主要是兩個通訊接聽程式不同的位置。 HTTP.sys *需要* 設定 `Endpoint` ，Kestrel 則否。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**：這是您所執行的 lambda，您可以在其中建立和傳回 `IWebHost` 。 它可讓您設定 `IWebHost` 在 ASP.NET Core 應用程式中正常運作的方式。 Lambda 會根據您所使用的 Service Fabric 整合選項和您提供的設定，提供為您產生的 URL `Endpoint` 。 然後您可以修改或使用該 URL 來啟動網頁伺服器。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 整合中介軟體
`Microsoft.ServiceFabric.AspNetCore`NuGet 套件包含上的 `UseServiceFabricIntegration` 擴充方法 `IWebHostBuilder` ，可新增 Service Fabric 感知中介軟體。 此中介軟體會設定 Kestrel 或 HTTP.sys `ICommunicationListener` ，以 Service Fabric 命名服務註冊唯一的服務 URL。 接著，它會驗證用戶端要求，以確保用戶端連接到正確的服務。 

您必須執行此步驟，才能防止用戶端錯誤地連接到錯誤的服務。 這是因為在 Service Fabric 的共用主機環境中，可以在相同的實體或虛擬機器上執行多個 web 應用程式，但不會使用唯一的主機名稱。 此案例會在下一節中詳細說明。

### <a name="a-case-of-mistaken-identity"></a>誤用身分識別的案例
不論通訊協定，服務複本會接聽唯一 IP:port 組合。 一旦服務複本開始接聽 IP： port 端點，它就會將該端點位址報告給 Service Fabric 命名服務。 用戶端或其他服務可以找到它。 如果服務使用動態指派的應用程式埠，服務複本可能會剛好在相同的實體或虛擬機器上，使用另一個服務的相同 IP：埠端點。 這可能會造成用戶端不小心連線至錯誤的服務。 如果發生下列事件順序，就會產生此案例：

 1. 服務 A 透過 HTTP 接聽 10.0.0.1:30000。 
 2. 用戶端會解析服務 A 並取得位址10.0.0.1：30000。
 3. 服務 A 移到另一個節點。
 4. 服務 B 置於 10.0.0.1 且碰巧使用相同的連接埠 30000。
 5. 用戶端使用快取的位址 10.0.0.1:30000 嘗試連線到服務 A。
 6. 用戶端現在已成功連接至服務 B，而不會將它連線到錯誤的服務。

這會不定時造成 難以診斷的 Bug。

### <a name="using-unique-service-urls"></a>使用唯一的服務 URL
為了避免這些錯誤，服務可以使用唯一識別碼將端點張貼至命名服務，然後在用戶端要求期間驗證唯一識別碼。 這在非惡意租用戶受信任環境中的服務之間為合作式動作。 它不會在惡意租使用者環境中提供安全的服務驗證。

在信任的環境中，方法所新增的中介軟體 `UseServiceFabricIntegration` 會自動將唯一識別碼附加至張貼至命名服務的位址。 它會驗證每個要求的識別碼。 如果識別碼不相符，中介軟體會立即傳回 HTTP 410 的回應。

使用動態指派連接埠的服務應該要使用此中介軟體。

使用固定唯一通訊埠的服務在合作的環境中沒有這個問題。 固定的唯一連接埠通常是用於對外開放的服務，需要用戶端應用程式連線的知名通訊埠。 例如，大部分網際網路對向 web 應用程式會使用埠80或443進行網頁瀏覽器連線。 在此情況下，不應啟用唯一識別碼。

下圖顯示已啟用中介軟體的要求流程︰

![Service Fabric ASP.NET Core 整合][2]

Kestrel 和 HTTP.sys `ICommunicationListener` 的實作為以完全相同的方式使用此機制。 雖然 HTTP.sys 可以使用基礎 **HTTP.sys** 埠共用功能，根據唯一的 URL 路徑內部區分要求，但 HTTP.sys 的執行 *不* 會使用該功能 `ICommunicationListener` 。 這是因為它會導致稍早所述案例中的 HTTP 503 和 HTTP 404 錯誤狀態碼。 如此一來，用戶端就很難判斷錯誤的意圖，因為 HTTP 503 和 HTTP 404 通常用來表示其他錯誤。 

因此，Kestrel 和 HTTP.sys `ICommunicationListener` 的實作為標準化擴充方法所提供的中介軟體 `UseServiceFabricIntegration` 。 因此，用戶端只需要在 HTTP 410 回應上執行服務端點重新解析動作。

## <a name="httpsys-in-reliable-services"></a>Reliable Services 中的 HTTP.sys
您可以使用 Reliable Services 中的 HTTP.sys，方法是匯入 **HttpSys** NuGet 套件。 此封裝包含的 `HttpSysCommunicationListener` 實作為 `ICommunicationListener` 。 `HttpSysCommunicationListener` 可讓您使用 HTTP.sys 作為網頁伺服器，在可靠的服務內建立 ASP.NET Core WebHost。

HTTP.sys 是以 [WINDOWS HTTP 伺服器 API](/windows/win32/http/http-api-start-page)為基礎。 此 API 會使用 **HTTP.sys** 核心驅動程式來處理 HTTP 要求，並將它們路由傳送到執行 web 應用程式的進程。 如此一來，相同的實體或虛擬機器上的多個進程，就可以在相同的埠上裝載 web 應用程式，而不是由唯一的 URL 路徑或主機名稱所區分。 這些功能對於在 Service Fabric 中於相同叢集裝載多個網站很有用。

>[!NOTE]
>HTTP.sys 的執行只適用于 Windows 平臺。

下圖說明 HTTP.sys 如何在 Windows 上使用 **HTTP.sys** 的核心驅動程式進行埠共用：

![HTTP.sys 圖表][3]

### <a name="httpsys-in-a-stateless-service"></a>無狀態服務中的 HTTP.sys
若要在無狀態服務中使用 `HttpSys`，請覆寫 `CreateServiceInstanceListeners` 方法並傳回 `HttpSysCommunicationListener` 執行個體︰

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>具狀態服務中的 HTTP.sys

`HttpSysCommunicationListener` 目前無法在具狀態服務中使用，因為基礎 **HTTP.sys** 埠共用功能有複雜性。 如需詳細資訊，請參閱下一節有關使用 HTTP.sys 的動態埠配置。 針對具狀態服務，Kestrel 是建議的 web 伺服器。

### <a name="endpoint-configuration"></a>端點組態

`Endpoint`使用 WINDOWS HTTP SERVER API 的 web 伺服器需要設定，包括 HTTP.sys。 使用 Windows HTTP Server API 的 Web 服務器必須先使用 HTTP.sys 保留其 URL (這通常是使用 [netsh](/windows/win32/http/netsh-commands-for-http) 工具) 來完成。 

此動作需要較高的許可權，您的服務預設不會有這些許可權。 ServiceManifest.xml 中設定之屬性的 "HTTP" 或 "HTTPs" 選項 `Protocol` `Endpoint` 是專門用來指示 Service Fabric 執行時間，以代表您向 HTTP.sys 註冊 URL。 它會使用 [*強式萬用字元*](/windows/win32/http/urlprefix-strings) URL 前置詞來執行這項工作。

例如，若要保留 `http://+:80` 服務，請使用 ServiceManifest.xml 中的下列設定：

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

端點名稱必須傳遞給 `HttpSysCommunicationListener` 建構函式︰

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>使用 HTTP.sys 搭配靜態埠
若要搭配 HTTP.sys 使用靜態埠，請在設定中提供埠號碼 `Endpoint` ：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>使用 HTTP.sys 搭配動態埠
若要使用動態指派的埠搭配 HTTP.sys，請省略設定 `Port` 中的屬性 `Endpoint` ：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

由設定所配置的動態埠 `Endpoint` 只提供 *每個主機進程*一個埠。 目前的 Service Fabric 裝載模型允許在相同的進程中裝載多個服務實例及/或複本。 這表示在透過設定配置時，每一個埠都會共用相同的埠 `Endpoint` 。 多個 **HTTP.sys** 實例可以使用基礎 **HTTP.sys** 埠共用功能來共用埠。 但 `HttpSysCommunicationListener` 由於用戶端要求所帶來的複雜性，因此不支援。 針對動態埠使用方式，Kestrel 是建議的 web 伺服器。

## <a name="kestrel-in-reliable-services"></a>Reliable Services 中的 Kestrel
您可以在 Reliable Services 中使用 Kestrel，方法是匯入 **ServiceFabric. AspNetCore. Kestrel** NuGet 套件。 此封裝包含的 `KestrelCommunicationListener` 實作為 `ICommunicationListener` 。 `KestrelCommunicationListener` 可讓您使用 Kestrel 作為網頁伺服器，在可靠的服務內建立 ASP.NET Core WebHost。

Kestrel 是 ASP.NET Core 的跨平台網頁伺服器。 不同于 HTTP.sys，Kestrel 不會使用集中式端點管理員。 與 HTTP.sys 不同的是，Kestrel 不支援多個進程之間的埠共用。 Kestrel 的每個執行個體必須使用唯一的連接埠。 如需 Kestrel 的詳細資訊，請參閱 [ [執行詳細資料](/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)]。

![Kestrel 圖][4]

### <a name="kestrel-in-a-stateless-service"></a>無狀態服務中的 Kestrel
若要在無狀態服務中使用 `Kestrel`，請覆寫 `CreateServiceInstanceListeners` 方法並傳回 `KestrelCommunicationListener` 執行個體︰

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>具狀態服務中的 Kestrel
若要在具狀態服務中使用 `Kestrel`，請覆寫 `CreateServiceReplicaListeners` 方法並傳回 `KestrelCommunicationListener` 執行個體︰

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

在此範例中，`IReliableStateManager` 的單一執行個體會提供給 WebHost 相依性插入容器。 這並不是絕對必要的，但可讓您 `IReliableStateManager` 在 MVC 控制器動作方法中使用和可靠的集合。

`Endpoint` 組態名稱*不*提供給具狀態服務中的 `KestrelCommunicationListener`。 我們會在下列各節中詳細說明這個部分。

### <a name="configure-kestrel-to-use-https"></a>將 Kestrel 設定為使用 HTTPS
在您的服務中啟用 HTTPS 與 Kestrel 時，您必須設定數個接聽選項。 更新 `ServiceInstanceListener` 以使用 *EndpointHttps* 端點，並在特定埠上接聽 (例如埠 443) 。 將 web 主機設定為使用 Kestrel web 伺服器時，您必須設定 Kestrel 以接聽所有網路介面上的 IPv6 位址： 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

如需教學課程中的完整範例，請參閱 [設定 Kestrel 使用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)。


### <a name="endpoint-configuration"></a>端點組態
`Endpoint`使用 Kestrel 並不需要設定。 

Kestrel 是簡單的獨立 web 伺服器。 與 HTTP.sys (或 HttpListener) 不同，它不需要 `Endpoint` ServiceManifest.xml 中的設定，因為它不需要在啟動之前註冊 URL。 

#### <a name="use-kestrel-with-a-static-port"></a>搭配使用 Kestrel 與靜態連接埠
您可以在 ServiceManifest.xml 設定中設定靜態埠， `Endpoint` 以便與 Kestrel 搭配使用。 雖然這不是絕對必要的，但提供兩種潛在優點：
 - 如果埠不在應用程式埠範圍內，則會透過 Service Fabric，透過 OS 防火牆來開啟。
 - 透過 `KestrelCommunicationListener` 提供給您的 URL 會使用此連接埠。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

如果設定了 `Endpoint`，其名稱必須傳遞至 `KestrelCommunicationListener` 建構函式︰ 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

如果 ServiceManifest.xml 未使用設定 `Endpoint` ，請省略函式中的名稱 `KestrelCommunicationListener` 。 在此情況下，它會使用動態埠。 如需有關這個的詳細資訊，請參閱下一節。

#### <a name="use-kestrel-with-a-dynamic-port"></a>搭配使用 Kestrel 與動態連接埠
Kestrel 無法使用 ServiceManifest.xml 中設定的自動埠指派 `Endpoint` 。 這是因為從設定的自動埠指派 `Endpoint` 會為每個 *主機進程*指派唯一的埠，而單一主機進程可以包含多個 Kestrel 實例。 這不適用於 Kestrel，因為它不支援埠共用。 因此，每個 Kestrel 實例都必須在唯一的埠上開啟。

若要使用動態通訊埠指派與 Kestrel，請完全略過 `Endpoint` ServiceManifest.xml 中的設定，而且不要將端點名稱傳遞至函式，如下所示 `KestrelCommunicationListener` ：

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

在此組態中，`KestrelCommunicationListener` 會自動從應用程式連接埠範圍選取未使用的連接埠。

若為 HTTPS，則應該將端點設定為使用 HTTPS 通訊協定，而不需要在 ServiceManifest.xml 中指定埠，然後將端點名稱傳遞給 KestrelCommunicationListener 的函式。


## <a name="service-fabric-configuration-provider"></a>Service Fabric 設定提供者
ASP.NET Core 中的應用程式設定是以設定提供者所建立的索引鍵/值組為基礎。 閱讀 [ASP.NET Core 中](/aspnet/core/fundamentals/configuration/) 的設定，以深入瞭解一般 ASP.NET Core 設定支援。

本節說明如何藉由匯入 NuGet 套件，Service Fabric 設定提供者與 ASP.NET Core 設定整合 `Microsoft.ServiceFabric.AspNetCore.Configuration` 。

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 啟始延伸模組
匯入 `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 套件之後，您必須使用 ASP.NET Core 設定 API 來註冊 Service Fabric 設定來源。 做法是檢查命名空間中的 **AddServiceFabricConfiguration** 延伸模組 `Microsoft.ServiceFabric.AspNetCore.Configuration` `IConfigurationBuilder` 。

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

現在 ASP.NET Core 服務可以存取 Service Fabric 的設定，就像任何其他應用程式設定一樣。 例如，您可以使用選項模式將設定載入強型別物件。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>預設金鑰組應
根據預設，Service Fabric 設定提供者會包含套件名稱、區段名稱和屬性名稱。 這些會一起形成 ASP.NET Core 的設定機碼，如下所示：
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

例如，如果您的設定套件 `MyConfigPackage` 以下列內容命名，則 `IConfiguration` 透過 *MyConfigPackage： MyConfigSection： >myparameter*可在 ASP.NET Core 上取得設定值。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric 設定選項
Service Fabric 設定提供者也支援 `ServiceFabricConfigurationOptions` 變更金鑰組應的預設行為。

#### <a name="encrypted-settings"></a>加密設定
Service Fabric 支援加密設定，就像 Service Fabric 設定提供者一樣。 根據預設，加密的設定不會解密成 ASP.NET Core `IConfiguration` 。 加密值會改為儲存在該處。 但是，如果您想要將值解密以儲存在 ASP.NET Core IConfiguration 中，您可以在擴充功能中將 *DecryptValue* 旗標設定為 false，如下所示 `AddServiceFabricConfiguration` ：

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>多個設定套件
Service Fabric 支援多個設定套件。 依預設，套件名稱會包含在設定機碼中。 但是，您可以將 `IncludePackageName` 旗標設定為 false，如下所示：
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>自訂索引鍵對應、值提取和資料擴展
Service Fabric 設定提供者也支援使用自訂索引鍵對應 `ExtractKeyFunc` 和自訂-將值解壓縮的更先進案例 `ExtractValueFunc` 。 您甚至可以使用將 Service Fabric 設定擴展資料的整個程式，變更為 ASP.NET Core 設定 `ConfigAction` 。

下列範例說明如何使用 `ConfigAction` 自訂資料填入：
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>設定更新
Service Fabric 設定提供者也支援設定更新。 您可以使用 ASP.NET Core `IOptionsMonitor` 來接收變更通知，然後使用 `IOptionsSnapshot` 來重載設定資料。 如需詳細資訊，請參閱 [ASP.NET Core 選項](/aspnet/core/fundamentals/configuration/options)。

預設支援這些選項。 不需要進一步的編碼來啟用設定更新。

## <a name="scenarios-and-configurations"></a>案例和組態
本節提供 web 伺服器、埠設定、Service Fabric 整合選項，以及我們建議您針對下列案例進行疑難排解的其他設定的組合：
 - 外部公開 ASP.NET Core 無狀態服務
 - 僅內部 ASP.NET Core 無狀態服務
 - 僅內部 ASP.NET Core 具狀態服務

**外部公開的服務**是一種公開從叢集外部呼叫之端點的服務，通常是透過負載平衡器。

僅供 **內部** 使用的服務是指只從叢集內呼叫端點的服務。

> [!NOTE]
> 可設定狀態的服務端點通常不應該公開到網際網路。 未察覺到 Service Fabric 服務解析（例如 Azure Load Balancer）的負載平衡器後方的叢集，將無法公開具狀態服務。 這是因為負載平衡器無法找出流量並將其路由傳送至適當的具狀態服務複本。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部公開 ASP.NET Core 無狀態服務
Kestrel 是針對前端服務所建議的 web 伺服器，可公開外部、網際網路面向的 HTTP 端點。 在 Windows 上，HTTP.sys 可以提供埠共用功能，讓您使用相同的埠，在同一組節點上裝載多個 web 服務。 在此案例中，web 服務會以主機名稱或路徑來區分，而不需依賴前端 proxy 或閘道來提供 HTTP 路由。
 
當公開至網際網路時，無狀態服務應該使用可透過負載平衡器連線的知名且穩定的端點。 您會將此 URL 提供給您的應用程式使用者。 建議使用下列設定：

| 類型 | 建議 | 注意 |
| ---- | -------------- | ----- |
| 網頁伺服器 | Kestrel | Kestrel 是慣用的 web 伺服器，因為它支援跨 Windows 和 Linux。 |
| 連接埠組態 | static | 已知的靜態連接埠應在 ServiceManifest.xml 的 `Endpoints` 組態中設定，例如 HTTP 為 80 或 443 為 HTTPS。 |
| ServiceFabricIntegrationOptions | 無 | 設定 `ServiceFabricIntegrationOptions.None` Service Fabric 整合中介軟體時，請使用選項，如此一來，服務就不會嘗試驗證唯一識別碼的傳入要求。 您應用程式的外部使用者不會知道中介軟體所使用的唯一識別資訊。 |
| 執行個體計數 | -1 | 在一般使用案例中，實例計數設定應該設定為 *-1*。 這樣做的目的是要讓實例可在接收來自負載平衡器之流量的所有節點上使用。 |

如果有多個外部公開的服務共用相同的節點集，您可以使用 HTTP.sys 搭配唯一但穩定的 URL 路徑。 您可以藉由修改設定 IWebHost 時所提供的 URL 來完成這項工作。 請注意，這只適用于 HTTP.sys。

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>僅供內部使用的無狀態 ASP.NET Core 服務
只會從叢集內呼叫的無狀態服務應該使用唯一的 URL 並動態指派連接埠，以確保多個服務之間的合作。 建議使用下列設定：

| 類型 | 建議 | 注意 |
| ---- | -------------- | ----- |
| 網頁伺服器 | Kestrel | 雖然您可以使用內部無狀態服務的 HTTP.sys，但 Kestrel 是允許多個服務實例共用主機的最佳伺服器。  |
| 連接埠組態 | 動態指派 | 具狀態服務的多個複本可能會共用主機進程或主機作業系統，因此將需要唯一的埠。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 使用動態連接埠指派，此設定可防止稍早所述的誤用識別問題。 |
| InstanceCount | 任意 | 執行個體計數設定可以設定為操作本服務所需的任何值。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>僅供內部使用的具狀態 ASP.NET Core 服務
只會從叢集內呼叫的具狀態服務應該使用動態指派連接埠，以確保多個服務之間的合作。 建議使用下列設定：

| 類型 | 建議 | 注意 |
| ---- | -------------- | ----- |
| 網頁伺服器 | Kestrel | 的 `HttpSysCommunicationListener` 設計目的不是讓複本共用主機進程的具狀態服務使用。 |
| 連接埠組態 | 動態指派 | 具狀態服務的多個複本可能會共用主機進程或主機作業系統，因此將需要唯一的埠。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 使用動態連接埠指派，此設定可防止稍早所述的誤用識別問題。 |

## <a name="next-steps"></a>後續步驟
[使用 Visual Studio 偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
