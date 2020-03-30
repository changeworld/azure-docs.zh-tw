---
title: 配置 Linux ASP.NET核心應用程式
description: 瞭解如何為應用配置預構建ASP.NET核心容器。 本文說明最常見的設定工作。
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255918"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>為 Azure 應用服務配置 Linux ASP.NET核心應用

ASP.NET核心應用必須部署為已編譯的二進位檔案。 Visual Studio 發佈工具生成解決方案，然後直接部署已編譯的二進位檔案，而 App Service 部署引擎首先部署代碼存儲庫，然後編譯二進位檔案。

本指南為在應用服務中使用內置 Linux 容器的ASP.NET核心開發人員提供了關鍵概念和說明。 如果您從未使用過 Azure 應用服務，請先使用 SQL 資料庫教程ASP.NET[核心快速入門](quickstart-dotnetcore.md)[和ASP.NET核心。](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>顯示 .NET 核心版本

要顯示當前 .NET Core 版本，可在[雲殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要顯示所有受支援的 .NET Core 版本，可在[雲外殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>設置 .NET 核心版本

在[雲殼](https://shell.azure.com)中運行以下命令，將 .NET Core 版本設置為 2.1：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>自訂構建自動化

如果使用打開的 Git 或 zip 包部署應用，應用服務將構建自動化步驟，執行以下順序：

1. 如果由`PRE_BUILD_SCRIPT_PATH`指定，則運行自訂腳本。
1. 運行`dotnet restore`以還原 NuGet 依賴項。
1. 運行`dotnet publish`以生成用於生產的二進位檔案。
1. 如果由`POST_BUILD_SCRIPT_PATH`指定，則運行自訂腳本。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是預設情況下為空的環境變數。 要運行預生成命令，請定義`PRE_BUILD_COMMAND`。 要運行生成後命令，請定義`POST_BUILD_COMMAND`。

下面的示例指定一系列命令的兩個變數，這些命令用逗號分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有關自訂生成自動化的其他環境變數，請參閱[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有關應用服務如何在 Linux 中運行和構建ASP.NET核心應用的詳細資訊，請參閱[Oryx 文檔：如何檢測和構建 .NET Core 應用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準ASP.NET核心依賴項注入模式在任何類中訪問它們：

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

如果在應用服務和應用*設置.json*中配置具有相同名稱的應用設置，則應用服務值優先于*appsettings.json*值。 本地*應用設置.json*值允許您在本地調試應用，但應用服務值允許您在具有生產設置的產品中運行應用。 連接字串的工作方式相同。 這樣，您可以將應用程式機密保存在代碼存儲庫之外，並在不更改代碼的情況下訪問適當的值。

## <a name="get-detailed-exceptions-page"></a>獲取詳細的異常頁面

當ASP.NET應用在 Visual Studio 調試器中生成異常時，瀏覽器將顯示一個詳細的異常頁，但在 App Service 中，該頁面將被泛型**HTTP 500**錯誤替換，或者**在處理請求時發生錯誤。** 訊息。 要在應用服務中顯示詳細的異常頁，通過在`ASPNETCORE_ENVIRONMENT`<a target="_blank" href="https://shell.azure.com" >雲外殼</a>中運行以下命令，將應用設置添加到應用。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果應用邏輯需要知道使用者請求是否加密，*請Startup.cs*配置轉發的標頭中介軟體：

- 請使用 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 來設定中介軟體以轉送 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭。
- 將私人 IP 位址範圍添加到已知網路，以便中介軟體可以信任應用服務負載等化器。
- 在調用其他中介軟體之前，在`Startup.Configure`中調用[使用前眉](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders)方法。

將所有三個元素放在一起，代碼如下所示：

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

## <a name="deploy-multi-project-solutions"></a>部署多專案解決方案

將ASP.NET存儲庫部署到根目錄中包含 *.csproj*檔的部署引擎時，引擎將部署該專案。 在根目錄中部署包含 *.sln*檔的ASP.NET存儲庫時，引擎將選取它找到的第一個 Web 網站或 Web 應用程式專案作為應用服務應用。 發動機可能不選擇您想要的專案。

要部署多專案解決方案，可以通過兩種不同的方式指定要在應用服務中使用的專案：

### <a name="using-deployment-file"></a>使用 .部署檔

將 *.部署*檔添加到存儲庫根，並添加以下代碼：

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>使用應用設置

在<a target="_blank" href="https://shell.azure.com">Azure 雲外殼中</a>，通過運行以下 CLI 命令向應用服務應用添加應用設置。 將*\<應用名稱>、**\<資源組名稱>* 和*\<專案名稱>* 替換為相應的值。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教程：使用 SQL 資料庫ASP.NET核心應用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
