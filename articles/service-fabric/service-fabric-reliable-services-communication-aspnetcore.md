---
title: 與ASP.NET核心的服務通信
description: 瞭解如何在無狀態和有狀態的 Azure 服務結構可靠服務應用程式中使用 ASP.NET Core。
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639627"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Azure 服務結構可靠服務的核心

ASP.NET核心是一個開源和跨平臺的框架。 此框架旨在構建基於雲的互聯網連接應用程式，如 Web 應用、IoT 應用和移動後端。

本文是使用**Microsoft.ServiceFabric.AspNetCore**託管ASP.NET服務交換矩陣可靠服務中的一個深入指南。 一組 NuGet 包。

有關 ASP.NET核心在 Service Fabric 中的介紹性教程和有關設置開發環境的說明，請參閱[教程：創建和部署具有ASP.NET核心 Web API 前端服務和有狀態後端服務的應用程式](service-fabric-tutorial-create-dotnet-app.md)。

本文的其餘部分假定您已經熟悉ASP.NET核心。 如果沒有，請閱讀[ASP.NET核心基礎知識](https://docs.microsoft.com/aspnet/core/fundamentals/index)。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 環境中的 ASP.NET Core

ASP.NET核心應用和服務交換矩陣應用都可以在 .NET Core 或完整的 .NET 框架上運行。 您可以在"服務結構"中以兩種不同的方式使用 ASP.NET 核心：
 - **裝載作為客體可執行檔**。 這種方法主要用於在 Service Fabric 上運行現有ASP.NET核心應用程式，無需更改代碼。
 - **在可靠的服務內運行**。 通過這種方式可以更好地與 Service Fabric 運行時集成，並允許有狀態ASP.NET核心服務。

本文的其餘部分通過服務結構 SDK 附帶的ASP.NET核心集成元件，介紹如何在可靠的服務中使用ASP.NET核心。

## <a name="service-fabric-service-hosting"></a>Service Fabric 服務裝載

在 Service Fabric 中，服務的一個或多個實例和/或副本在*服務主機進程中*運行：運行服務代碼的可執行檔。 作為服務作者，您擁有服務主機進程，服務結構會為您啟動和監視它。

傳統 ASP.NET (直到 MVC 5) 已透過 System.Web.dll 與 IIS 緊密結合。 ASP.NET Core 能區別網頁伺服器與 web 應用程式。 這種分離允許 Web 應用程式在不同的 Web 服務器之間可移植。 它還允許 Web 服務器*是自託管的*。 這意味著您可以在自己的進程中啟動 Web 服務器，而不是由專用 Web 服務器軟體（如 IIS）擁有的進程。

要將 Service Fabric 服務和ASP.NET（作為來賓可執行檔或可靠服務）組合在一起，您必須能夠在服務主機進程中啟動ASP.NET。 ASP.NET Core 自我裝載可讓您執行這項操作。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>以可靠的服務託管ASP.NET核心
一般而言，自我裝載的 ASP.NET Core 應用程式會在應用程式的進入點建立 WebHost，例如 `Program.cs` 方法中的 `static void Main()`。 在這種情況下，WebHost 的生命週期綁定到進程的生命週期。

![在處理序中裝載 ASP.NET Core][0]

但是，應用程式進入點不是在可靠的服務中創建 WebHost 的正確位置。 這是因為應用程式進入點僅用於將服務類型註冊到 Service Fabric 運行時，以便它可以創建該服務類型的實例。 WebHost 應在可靠的服務本身中創建。 在服務主機進程中，服務實例和/或副本可以經歷多個生命週期。 

Reliable Service 執行個體是由您衍生自 `StatelessService` 或 `StatefulService` 的服務類別代表。 服務的通訊堆疊包含在您服務類別中的 `ICommunicationListener` 實作。 `Microsoft.ServiceFabric.AspNetCore.*` NuGet 包包含該啟動的`ICommunicationListener`實現，並在可靠的服務中管理 Kestrel 或 HTTP.sys 的ASP.NET核心 WebHost。

![用於在可靠的服務中託管ASP.NET核心圖][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
NuGet 包中 Kestrel 和 HTTP.sys 的`ICommunicationListener`實現具有類似的使用模式。 `Microsoft.ServiceFabric.AspNetCore.*` 但是，它們執行特定于每個 Web 服務器的略有不同的操作。 

這兩種通訊接聽程式都會提供使用下列引數的建構函式︰
 - **`ServiceContext serviceContext`**：這是包含有關正在運行的`ServiceContext`服務的資訊的物件。
 - **`string endpointName`**：這是 ServiceManifest.xml`Endpoint`中配置的名稱。 它主要是兩個通信攔截器的不同。 HTTP.sys*requires*需要`Endpoint`配置，而凱斯特雷爾不需要配置。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**：這是您實現的 lambda，您可以在其中創建並返回 。 `IWebHost` 它允許您在 ASP.NET`IWebHost`核心應用程式中配置通常的方式。 lambda 提供為您生成的 URL，具體取決於您使用的 Service Fabric 集成選項以及您提供的`Endpoint`配置。 然後，您可以修改或使用該 URL 啟動 Web 服務器。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 整合中介軟體
`Microsoft.ServiceFabric.AspNetCore` NuGet 包包括添加`UseServiceFabricIntegration``IWebHostBuilder`服務結構感知中介軟體的擴充方法。 此中介軟體配置 Kestrel 或 HTTP.sys`ICommunicationListener`以將唯一的服務 URL 註冊到服務交換矩陣命名服務。 然後，它驗證用戶端請求，以確保用戶端連接到正確的服務。 

此步驟對於防止用戶端錯誤地連接到錯誤的服務是必要的。 這是因為，在共用主機環境（如 Service Fabric）中，多個 Web 應用程式可以在同一物理或虛擬機器上運行，但不使用唯一的主機名稱。 此案例會在下一節中詳細說明。

### <a name="a-case-of-mistaken-identity"></a>誤用身分識別的案例
不論通訊協定，服務複本會接聽唯一 IP:port 組合。 服務副本開始偵聽 IP：埠終結點後，它將終結點位址報告給服務結構命名服務。 在那裡，用戶端或其他服務可以發現它。 如果服務使用動態分配的應用程式埠，則服務副本可能巧合地使用相同的 IP：埠終結點，而以前在同一物理或虛擬機器上。 這可能會造成用戶端不小心連線至錯誤的服務。 如果發生以下事件序列，則可能導致此情況：

 1. 服務 A 透過 HTTP 接聽 10.0.0.1:30000。 
 2. 用戶端解析服務 A 並獲取位址 10.0.0.1：30000。
 3. 服務 A 移到另一個節點。
 4. 服務 B 置於 10.0.0.1 且碰巧使用相同的連接埠 30000。
 5. 用戶端使用快取的位址 10.0.0.1:30000 嘗試連線到服務 A。
 6. 用戶端現在已成功連接到服務 B，沒有意識到它連接到了錯誤的服務。

這會不定時造成 難以診斷的 Bug。

### <a name="using-unique-service-urls"></a>使用唯一的服務 URL
為了防止這些 Bug，服務可以使用唯一識別碼將終結點發布到命名服務，然後在用戶端請求期間驗證該唯一識別碼。 這在非惡意租用戶受信任環境中的服務之間為合作式動作。 它不在惡意租戶環境中提供安全的服務身份驗證。

在受信任的環境中，`UseServiceFabricIntegration`該方法添加的中介軟體會自動將唯一識別碼追加到發佈到命名服務的位址。 它驗證每個請求上的識別碼。 如果識別碼不匹配，中介軟體將立即返回 HTTP 410 消失回應。

使用動態指派連接埠的服務應該要使用此中介軟體。

使用固定唯一端口的服務在協作環境中沒有此問題。 固定的唯一連接埠通常是用於對外開放的服務，需要用戶端應用程式連線的知名通訊埠。 例如，大多數面向 Internet 的 Web 應用程式將使用埠 80 或 443 進行 Web 瀏覽器連接。 在這種情況下，不應啟用唯一識別碼。

下圖顯示已啟用中介軟體的要求流程︰

![Service Fabric ASP.NET Core 整合][2]

Kestrel 和 HTTP.sys`ICommunicationListener`實現都以完全相同的方式使用此機制。 儘管 HTTP.sys 可以使用基礎**HTTP.sys**埠共用功能在內部區分基於唯一 URL 路徑的請求*not*，但 HTTP.sys`ICommunicationListener`實現不會使用該功能。 這是因為在前面描述的情景中，它會導致 HTTP 503 和 HTTP 404 錯誤狀態碼。 這反過來又使用戶端難以確定錯誤的意圖，因為 HTTP 503 和 HTTP 404 通常用於指示其他錯誤。 

因此，Kestrel 和 HTTP.sys`ICommunicationListener`實現都對`UseServiceFabricIntegration`擴充方法提供的中介軟體進行了標準化。 因此，用戶端只需對 HTTP 410 回應執行服務終結點重新解析操作。

## <a name="httpsys-in-reliable-services"></a>HTTP.sys 在可靠服務
通過導入**Microsoft.ServiceFabric.AspNetCore.HTTPSys** NuGet 包，可以在可靠服務中使用 HTTP.sys。 此包包含`HttpSysCommunicationListener`， 的`ICommunicationListener`實現。 `HttpSysCommunicationListener`允許您使用 HTTP.sys 作為 Web 服務器，在可靠的服務中創建ASP.NET核心 WebHost。

HTTP.sys 是建立在[Windows HTTP 伺服器 API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)上的。 此 API 使用**HTTP.sys**內核驅動程式處理 HTTP 要求並將其路由到運行 Web 應用程式的進程。 這允許同一物理或虛擬機器上的多個進程在同一端口上託管 Web 應用程式，從而消除唯一 URL 路徑或主機名稱的歧義。 這些功能對於在 Service Fabric 中於相同叢集裝載多個網站很有用。

>[!NOTE]
>HTTP.sys 實現僅適用于 Windows 平臺。

下圖說明瞭 HTTP.sys 如何使用 Windows 上的**HTTP.sys**內核驅動程式進行埠共用：

![HTTP.sys 圖][3]

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

### <a name="httpsys-in-a-stateful-service"></a>有狀態服務中的 HTTP.sys

`HttpSysCommunicationListener`由於基礎**HTTP.sys**埠共用功能的複雜性，目前未設計為用於有狀態服務。 有關詳細資訊，請參閱以下有關使用 HTTP.sys 的動態埠分配的部分。 對於有狀態服務，Kestrel 是建議的 Web 服務器。

### <a name="endpoint-configuration"></a>端點組態

對於`Endpoint`使用 Windows HTTP 伺服器 API（包括 HTTP.sys）的 Web 服務器，需要配置。 使用 Windows HTTP 伺服器 API 的 Web 服務器必須首先將其 URL 與 HTTP.sys 一起保留（這通常通過[netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)工具完成）。 

此操作需要服務預設沒有的提升許可權。 ServiceManifest.xml 中`Protocol``Endpoint`配置屬性的"HTTP"或"HTTPs"選項專門用於指示 Service Fabric 運行時代表您向 HTTP.sys 註冊 URL。 它通過使用[*強萬用字元*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)URL 首碼來這樣做。

例如，要為服務`http://+:80`預留，請使用 ServiceManifest.xml 中的以下配置：

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

#### <a name="use-httpsys-with-a-static-port"></a>將 HTTP.sys 與靜態埠一起使用
要將靜態埠與 HTTP.sys 一起使用，請提供`Endpoint`配置中的埠號：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>將 HTTP.sys 與動態埠一起使用
要將動態分配的埠與 HTTP.sys 一起使用，`Port`請省略配置`Endpoint`中的屬性：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

由`Endpoint`配置分配的動態埠*僅為每個主機進程*提供一個埠。 當前 Service Fabric 託管模型允許在同一進程中託管多個服務實例和/或副本。 這意味著當通過配置分配時，每個埠將共用同一`Endpoint`個埠。 多個**HTTP.sys**實例可以使用基礎**HTTP.sys**埠共用功能共用埠。 但是，`HttpSysCommunicationListener`由於它引入了用戶端請求的複雜性，因此不支援它。 對於動態埠使用，Kestrel 是建議的 Web 服務器。

## <a name="kestrel-in-reliable-services"></a>Reliable Services 中的 Kestrel
您可以通過導入**Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet 包在可靠服務中使用 Kestrel。 此包包含`KestrelCommunicationListener`， 的`ICommunicationListener`實現。 `KestrelCommunicationListener`允許您使用 Kestrel 作為 Web 服務器，在可靠的服務中創建ASP.NET核心 WebHost。

Kestrel 是 ASP.NET Core 的跨平台網頁伺服器。 與 HTTP.sys 不同，Kestrel 不使用集中式終結點管理器。 與 HTTP.sys 不同，Kestrel 不支援多個進程之間的埠共用。 Kestrel 的每個執行個體必須使用唯一的連接埠。 有關 Kestrel 的詳細資訊，請參閱[實施詳細資訊](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)。

![凱斯特雷爾圖][4]

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

在此範例中，`IReliableStateManager` 的單一執行個體會提供給 WebHost 相依性插入容器。 這不是絕對必要的，但它允許您在 MVC 控制器操作`IReliableStateManager`方法中使用和可靠集合。

`Endpoint` 組態名稱*不*提供給具狀態服務中的 `KestrelCommunicationListener`。 我們會在下列各節中詳細說明這個部分。

### <a name="configure-kestrel-to-use-https"></a>將 Kestrel 設定為使用 HTTPS
在服務中啟用帶有 Kestrel 的 HTTPS 時，您需要設置多個偵聽選項。 更新`ServiceInstanceListener`以使用*終結點 Https*終結點並偵聽特定埠（如埠 443）。 將 Web 主機配置為使用 Kestrel Web 服務器時，必須配置 Kestrel 以偵聽所有網路介面上的 IPv6 位址： 

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

有關教程中的完整示例，請參閱配置[Kestrel 以使用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)。


### <a name="endpoint-configuration"></a>端點組態
使用`Endpoint`Kestrel 不需要配置。 

Kestrel 是一個簡單的獨立 Web 服務器。 與 HTTP.sys（或 HttpListener）不同，它不需要`Endpoint`ServiceManifest.xml 中的配置，因為它在啟動之前不需要 URL 註冊。 

#### <a name="use-kestrel-with-a-static-port"></a>搭配使用 Kestrel 與靜態連接埠
您可以在 ServiceManifest.xml 的`Endpoint`配置中配置靜態埠，以便與 Kestrel 一起使用。 雖然這並非絕對必要，但它提供了兩個潛在的好處：
 - 如果埠不屬於應用程式埠範圍，則通過服務結構通過 OS 防火牆打開該埠。
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

如果 ServiceManifest.xml 不使用`Endpoint`配置，則省略建構函式中`KestrelCommunicationListener`的名稱。 在這種情況下，它將使用動態埠。 有關此詳細資訊，請參閱下一節。

#### <a name="use-kestrel-with-a-dynamic-port"></a>搭配使用 Kestrel 與動態連接埠
Kestrel 無法使用 ServiceManifest.xml 中的配置`Endpoint`中的自動埠分配。 這是因為來自配置的`Endpoint`自動埠分配為每個*主機進程*分配了一個唯一的埠，並且單個主機進程可以包含多個 Kestrel 實例。 這不能與 Kestrel 一起工作，因為它不支援埠共用。 因此，必須在唯一端口上打開每個 Kestrel 實例。

要將動態埠分配與 Kestrel 一起`Endpoint`使用，請使用 ServiceManifest.xml 中的配置，並且不要將終結點名稱傳遞給`KestrelCommunicationListener`建構函式，如下所示：

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

在此組態中，`KestrelCommunicationListener` 會自動從應用程式連接埠範圍選取未使用的連接埠。

對於 HTTPS，它應該使用 HTTPS 協定配置終結點，而不在 ServiceManifest.xml 中指定的埠，並將終結點名稱傳遞給 KestrelCommunicationListener 建構函式。


## <a name="service-fabric-configuration-provider"></a>服務結構配置提供程式
ASP.NET Core 中的應用程式佈建基於配置提供程式建立的鍵值對。 閱讀[ASP.NET 核心中的配置](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/)，瞭解有關一般ASP.NET核心配置支援的更多內容。

本節介紹服務結構配置提供程式如何通過導入`Microsoft.ServiceFabric.AspNetCore.Configuration`NuGet 包與 ASP.NET核心組態集成。

### <a name="addservicefabricconfiguration-startup-extensions"></a>添加ServiceFabric配置啟動擴展
導入`Microsoft.ServiceFabric.AspNetCore.Configuration`NuGet 包後，您需要使用 ASP.NET 核心配置 API 註冊服務交換矩陣配置源。 為此，可以通過選中命名空間中的`Microsoft.ServiceFabric.AspNetCore.Configuration` **AddServiceFabric 配置**副檔名。 `IConfigurationBuilder`

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

現在，ASP.NET核心服務可以訪問服務交換矩陣配置設置，就像任何其他應用程式設定一樣。 例如，可以使用選項模式將設置載入到強型別物件中。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>預設金鑰映射
預設情況下，服務結構配置提供套裝程式括包名稱、節名稱和屬性名稱。 這些組合一起構成ASP.NET核心配置金鑰，如下所示：
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

例如，如果您有一個命名`MyConfigPackage`了以下內容的配置包，則配置值將通過`IConfiguration`*"我的配置包：MyConfigSection：我的參數*"在ASP.NET核心版上可用。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>服務交換矩陣配置選項
服務結構配置提供程式還支援`ServiceFabricConfigurationOptions`更改金鑰映射的預設行為。

#### <a name="encrypted-settings"></a>加密設定
服務交換矩陣支援加密設定，服務結構配置提供程式也是如此。 預設情況下，加密的設置不會解密為 ASP.NET酷`IConfiguration`睿。 加密的值將存儲在那裡。 但是，如果要解密要存儲在ASP.NET核心 I配置中的值，可以在`AddServiceFabricConfiguration`擴展中將*DecryptValue*標誌設置為 false，如下所示：

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>多個配置包
服務交換矩陣支援多個配置包。 預設情況下，包名稱包含在配置金鑰中。 但是，`IncludePackageName`您可以將標誌設置為 false，如下所示：
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>自訂金鑰映射、值提取和資料填充
Service Fabric 配置提供程式還支援更高級的方案，以便使用`ExtractKeyFunc`自訂金鑰映射，並自訂使用`ExtractValueFunc`的值提取值。 您甚至可以通過使用`ConfigAction`將資料填充過程從 Service Fabric 配置更改為ASP.NET核心配置。

以下示例說明了如何使用自訂`ConfigAction`資料填充：
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

### <a name="configuration-updates"></a>配置更新
服務結構配置提供程式還支援配置更新。 您可以使用ASP.NET核心`IOptionsMonitor`接收更改通知，然後使用 重新`IOptionsSnapshot`載入配置資料。 有關詳細資訊，請參閱[ASP.NET核心選項](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

預設情況下，這些選項受支援。 無需進一步編碼即可啟用配置更新。

## <a name="scenarios-and-configurations"></a>案例和組態
本節提供 Web 服務器、埠配置、Service Fabric 集成選項和我們建議解決以下方案的雜項設置的組合：
 - 外部公開 ASP.NET Core 無狀態服務
 - 僅內部ASP.NET核心無狀態服務
 - 僅內部ASP.NET核心狀態服務

**外部公開的服務**是公開從群集外部調用的終結點的服務，通常通過負載等化器。

**僅內部**服務是僅從群集中調用其終結點的服務。

> [!NOTE]
> 有狀態的服務終結點通常不應暴露在互聯網上。 不知道服務結構服務解析的負載等化器後面的群集（如 Azure 負載等化器）將無法公開有狀態服務。 這是因為負載等化器無法定位流量並將其路由到適當的有狀態服務副本。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部公開 ASP.NET Core 無狀態服務
Kestrel 是建議用於公開外部面向 Internet 的 HTTP 終結點的前端服務的 Web 服務器。 在 Windows 上，HTTP.sys 可以提供埠共用功能，允許您使用相同的埠在同一組節點上託管多個 Web 服務。 在這種情況下，Web 服務按主機名稱或路徑進行區分，而無需依賴前端代理或閘道來提供 HTTP 路由。
 
當暴露到 Internet 時，無狀態服務應使用可通過負載等化器訪問的已知且穩定的終結點。 您將向應用程式的使用者提供此 URL。 建議使用下列設定：

|  |  | **注意** |
| --- | --- | --- |
| 網頁伺服器 | Kestrel | Kestrel 是首選的 Web 服務器，因為它在 Windows 和 Linux 中受支援。 |
| 連接埠組態 | static | 已知的靜態連接埠應在 ServiceManifest.xml 的 `Endpoints` 組態中設定，例如 HTTP 為 80 或 443 為 HTTPS。 |
| ServiceFabricIntegrationOptions | None | 在配置`ServiceFabricIntegrationOptions.None`Service Fabric 集成中介軟體時使用 該選項，以便服務不會嘗試驗證唯一識別碼的傳入請求。 應用程式的外部使用者不會知道中介軟體使用的唯一標識資訊。 |
| 執行個體計數 | -1 | 在典型的用例中，實例計數設置應設置為 *-1*。 這樣做是為了在從負載等化器接收流量的所有節點上都有實例可用。 |

如果多個外部公開的服務共用同一組節點，則可以使用 HTTP.sys 具有唯一但穩定的 URL 路徑。 您可以通過修改配置 IWebHost 時提供的 URL 來實現此目的。 請注意，這僅適用于 HTTP.sys。

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

|  |  | **注意** |
| --- | --- | --- |
| 網頁伺服器 | Kestrel | 儘管您可以將 HTTP.sys 用於內部無狀態服務，但 Kestrel 是允許多個服務實例共用主機的最佳伺服器。  |
| 連接埠組態 | 動態指派 | 有狀態服務的多個副本可能共用主機進程或主機作業系統，因此將需要唯一的埠。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 使用動態連接埠指派，此設定可防止稍早所述的誤用識別問題。 |
| InstanceCount | 任意 | 執行個體計數設定可以設定為操作本服務所需的任何值。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>僅供內部使用的具狀態 ASP.NET Core 服務
只會從叢集內呼叫的具狀態服務應該使用動態指派連接埠，以確保多個服務之間的合作。 建議使用下列設定：

|  |  | **注意** |
| --- | --- | --- |
| 網頁伺服器 | Kestrel | `HttpSysCommunicationListener`並非設計為由有狀態服務使用，其中副本共用主機進程。 |
| 連接埠組態 | 動態指派 | 有狀態服務的多個副本可能共用主機進程或主機作業系統，因此將需要唯一的埠。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 使用動態連接埠指派，此設定可防止稍早所述的誤用識別問題。 |

## <a name="next-steps"></a>後續步驟
[使用 Visual Studio 偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
