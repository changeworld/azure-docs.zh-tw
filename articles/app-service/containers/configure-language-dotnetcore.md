---
title: 設定 Linux ASP.NET Core 應用程式
description: 瞭解如何為您的應用程式設定預先建立的 ASP.NET Core 容器。 本文說明最常見的設定工作。
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: 30cd6ad1b5516eb3bc7e858ae364a88ace1b93b3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917624"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Linux ASP.NET Core 應用程式

ASP.NET Core 應用程式必須部署為已編譯的二進位檔。 Visual Studio 發佈工具會建立解決方案，然後直接部署已編譯的二進位檔，而 App Service 部署引擎會先部署程式碼存放庫，然後再編譯二進位檔。

本指南提供在 App Service 中使用內建 Linux 容器 ASP.NET Core 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[ASP.NET Core 快速入門](quickstart-dotnetcore.md)和[ASP.NET Core SQL Database 教學](tutorial-dotnetcore-sqldb-app.md)課程。

## <a name="show-net-core-version"></a>顯示 .NET Core 版本

若要顯示目前的 .NET Core 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 .NET Core 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>設定 .NET Core 版本

在[Cloud Shell](https://shell.azure.com)中執行下列命令，將 .net Core 版本設定為2.1：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>自訂群組建自動化

如果您使用 Git 或已開啟組建自動化的 zip 套件來部署應用程式，App Service 組建自動化會逐步執行下列順序：

1. 如果 `PRE_BUILD_SCRIPT_PATH`指定，請執行自訂腳本。
1. 執行 `dotnet restore` 以還原 NuGet 相依性。
1. 執行 `dotnet publish` 以建立用於生產的二進位檔。
1. 如果 `POST_BUILD_SCRIPT_PATH`指定，請執行自訂腳本。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是預設為空白的環境變數。 若要執行預先建立的命令，請定義 `PRE_BUILD_COMMAND`。 若要執行建立後命令，請定義 `POST_BUILD_COMMAND`。

下列範例會指定一系列命令的兩個變數，並以逗號分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

如需其他環境變數以自訂群組建自動化，請參閱[Oryx configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

如需有關 App Service 如何在 Linux 中執行和建立 ASP.NET Core 應用程式的詳細資訊，請參閱[Oryx 檔：如何偵測和建立 .Net Core 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準 ASP.NET Core 相依性插入模式，在任何類別中存取這些物件：

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

例如，如果您在 App Service 和*appsettings*中設定具有相同名稱的應用程式設定，則 App Service 值的優先順序會高於*appsettings. json*值。 本機*appsettings*可讓您在本機上對應用程式進行調試，但 App Service 值可讓您在產品中使用生產環境設定來執行應用程式。 連接字串的工作方式相同。 如此一來，您就可以在程式碼存放庫之外保留應用程式秘密，並存取適當的值，而不需要變更您的程式碼。

## <a name="get-detailed-exceptions-page"></a>取得詳細例外狀況頁面

當您的 ASP.NET 應用程式在 Visual Studio 偵錯工具中產生例外狀況時，瀏覽器會顯示詳細的例外狀況頁面，但在 App Service 該頁面會由一般**HTTP 500**錯誤取代，或在**處理您的要求時發生錯誤。** 訊息。 若要在 App Service 中顯示詳細的例外狀況頁面，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令，以將 `ASPNETCORE_ENVIRONMENT` 應用程式設定新增至您的應用程式。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要知道使用者要求是否已加密，請在*Startup.cs*中設定轉送的標頭中介軟體：

- 使用[ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions)設定中介軟體，以轉送 `Startup.ConfigureServices`中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭。
- 將私人 IP 位址範圍新增至已知的網路，讓中介軟體可以信任 App Service 負載平衡器。
- 呼叫其他中介軟體之前，請先在 `Startup.Configure` 中叫用[UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders)方法。

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

如需詳細資訊，請參閱[設定 ASP.NET Core 以使用 proxy 伺服器和負載平衡](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)器。

## <a name="deploy-multi-project-solutions"></a>部署多專案方案

當您使用根目錄中的 *.csproj*檔案將 ASP.NET 存放庫部署至部署引擎時，引擎會部署專案。 當您使用根目錄中的 *.sln*檔案部署 ASP.NET 存放庫時，引擎會挑選第一個找到的網站或 Web 應用程式專案做為 App Service 應用程式。 引擎可能無法挑選您想要的專案。

若要部署多專案方案，您可以用兩種不同的方式指定要在 App Service 中使用的專案：

### <a name="using-deployment-file"></a>使用 deployment 檔案

將*部署*檔案新增至存放庫根目錄，並新增下列程式碼：

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>使用應用程式設定

在<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>中，執行下列 CLI 命令，以將應用程式設定新增至您的 App Service 應用程式。 以適當的值取代 *\<應用程式名稱 >* 、 *\<資源群組名稱 >* 和 *\<專案名稱 >* 。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 SQL Database ASP.NET Core 應用程式](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
