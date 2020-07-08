---
title: 設定 Windows ASP.NET Core 應用程式
description: 瞭解如何在 App Service 的原生 Windows 實例中設定 ASP.NET Core 應用程式。 本文說明最常見的設定工作。
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907887"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Windows ASP.NET Core 應用程式

> [!NOTE]
> 如需 .NET Framework 中的 ASP.NET，請參閱[設定適用于 Azure App Service 的 ASP.NET 應用程式](configure-language-dotnet-framework.md)

ASP.NET Core 應用程式必須部署成 Azure App Service 做為已編譯的二進位檔。 Visual Studio 發佈工具會建立解決方案，然後直接部署已編譯的二進位檔，而 App Service 部署引擎會先部署程式碼存放庫，然後再編譯二進位檔。 如需 Linux 應用程式的相關資訊，請參閱[設定適用于 Azure App Service 的 Linux ASP.NET Core 應用程式](containers/configure-language-dotnetcore.md)。

本指南提供 ASP.NET Core 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[ASP.NET 快速入門](app-service-web-get-started-dotnet.md)，並[使用 SQL Database 教學](app-service-web-tutorial-dotnetcore-sqldb.md)課程來 ASP.NET Core。

## <a name="show-supported-net-core-runtime-versions"></a>顯示支援的 .NET Core 執行階段版本

在 App Service 中，Windows 實例已安裝所有支援的 .NET Core 版本。 若要顯示您可以使用的 .NET Core 執行時間和 SDK 版本，請流覽至， `https://<app-name>.scm.azurewebsites.net/DebugConsole` 並在以瀏覽器為基礎的主控台中執行下列命令：

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>設定 .NET Core 版本

在 ASP.NET Core 專案的專案檔中設定目標 framework。 如需詳細資訊，請參閱選取要在 .NET Core 中[使用的 .Net core 版本](https://docs.microsoft.com/dotnet/core/versions/selection)檔。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](configure-common.md#configure-app-settings)。 然後，您可以使用標準 ASP.NET Core 相依性插入模式，在任何類別中存取這些物件：

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

例如，如果您在 App Service 中設定具有相同名稱的應用程式設定，並在中*appsettings.js*，則 App Service 值會優先于值*appsettings.js* 。 本機appsettings.js值可讓您在本機*上*對應用程式進行調試，但 App Service 值可讓您使用生產環境設定在產品中執行應用程式。 連接字串的工作方式相同。 如此一來，您就可以在程式碼存放庫之外保留應用程式秘密，並存取適當的值，而不需要變更您的程式碼。

> [!NOTE]
> 請注意， *appsettings.js*中的[階層](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data)式設定資料是使用 `:` .net Core 標準的分隔符號來存取。 若要覆寫 App Service 中的特定階層式設定，請在索引鍵中設定具有相同分隔格式的應用程式設定名稱。 您可以在[Cloud Shell](https://shell.azure.com)中執行下列範例：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>部署多專案方案

當 Visual Studio 方案包含多個專案時，Visual Studio 發行程式已包含選取要部署的專案。 當您部署到 App Service 部署引擎（例如使用 Git 或使用 ZIP 部署），並開啟組建自動化時，App Service 部署引擎會挑選第一個找到的網站或 Web 應用程式專案作為 App Service 應用程式。 您可以藉由指定應用程式設定，指定 App Service 應該使用哪個專案 `PROJECT` 。 例如，在[Cloud Shell](https://shell.azure.com)中執行下列程式：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

ASP.NET Core 提供[App Service 的內建記錄提供者](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service)。 在專案的*Program.cs*中，透過擴充方法將提供者新增至您的應用程式 `ConfigureLogging` ，如下列範例所示：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

接著，您可以使用[標準 .Net Core 模式](https://docs.microsoft.com/aspnet/core/fundamentals/logging)來設定和產生記錄。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

如需 App Service 中 ASP.NET Core 應用程式疑難排解的詳細資訊，請參閱[針對 Azure App Service 和 IIS 上的 ASP.NET Core 進行疑難排解](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>取得詳細例外狀況頁面

當您的 ASP.NET Core 應用程式在 Visual Studio 偵錯工具中產生例外狀況時，瀏覽器會顯示詳細的例外狀況頁面，但在中，App Service 該頁面會由一般**HTTP 500**錯誤取代，或在**處理您的要求時發生錯誤。** 訊息。 若要在 App Service 中顯示詳細的例外狀況頁面，請 `ASPNETCORE_ENVIRONMENT` 在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令，以將應用程式設定新增至您的應用程式。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要知道使用者要求是否已加密，請在*Startup.cs*中設定轉送的標頭中介軟體：

- 請使用 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 來設定中介軟體以轉送 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭。
- 將私人 IP 位址範圍新增至已知的網路，讓中介軟體可以信任 App Service 負載平衡器。
- 呼叫其他中介軟體之前，請先在中叫用[UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders)方法 `Startup.Configure` 。

將這三個元素全部放在一起，您的程式碼看起來如下列範例所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：ASP.NET Core 應用程式搭配 SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
