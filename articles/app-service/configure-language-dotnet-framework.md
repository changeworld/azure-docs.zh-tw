---
title: 設定 ASP.NET 應用程式
description: 瞭解如何在 Azure App Service 中設定 ASP.NET 應用程式。 本文說明最常見的設定工作。
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 53654520ea20bd8ee797de61449a616eadd001a5
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080142"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 ASP.NET 應用程式

> [!NOTE]
> 如 ASP.NET Core，請參閱[設定適用于 Azure App Service 的 ASP.NET Core 應用程式](configure-language-dotnetcore.md)

ASP.NET apps 必須部署到 Azure App Service 做為已編譯的二進位檔。 Visual Studio 發佈工具會建立解決方案，然後直接部署已編譯的二進位檔，而 App Service 部署引擎會先部署程式碼存放庫，然後再編譯二進位檔。

本指南提供 ASP.NET 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[ASP.NET 快速入門](quickstart-dotnet-framework.md)和[ASP.NET with SQL Database 教學](app-service-web-tutorial-dotnet-sqldatabase.md)課程。

## <a name="show-supported-net-framework-runtime-versions"></a>顯示支援的 .NET Framework 執行階段版本

在 App Service 中，Windows 實例已安裝所有支援的 .NET Framework 版本。 若要顯示您可以使用的 .NET Framework 執行時間和 SDK 版本，請流覽至， `https://<app-name>.scm.azurewebsites.net/DebugConsole` 並在瀏覽器型主控台中執行適當的命令：

針對 CLR 4 執行階段版本 ( .NET Framework 4 和更新版本) ：

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

最新的 .NET Framework 版本可能無法立即使用。

針對 CLR 2 執行階段版本 ( .NET Framework 3.5 和以下) ：

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>顯示目前 .NET Framework 執行階段版本

在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

的值 `v4.0` 表示使用最新的 CLR 4 版本 ( .NET Framework 4.x) 。 的值 `v2.0` 表示使用的是 CLR 2 版本 ( .NET Framework 3.5) 。

## <a name="set-net-framework-runtime-version"></a>設定 .NET Framework 執行階段版本

根據預設，App Service 會使用最新支援的 .NET Framework 版本來執行 ASP.NET 應用程式。 若要改為使用 .NET Framework 3.5 執行應用程式，請在[Cloud Shell](https://shell.azure.com)中執行下列命令 (V2.0 表示 CLR 2) ：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以在應用程式程式碼之外[設定應用程式設定](configure-common.md#configure-app-settings)和連接字串。 然後您可以使用標準 ASP.NET 模式，在任何類別中存取它們：

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

如果您在 App Service 和*web.config*中設定具有相同名稱的應用程式設定，App Service 值的優先順序會高於*web.config*值。 本機*web.config*值可讓您在本機上對應用程式進行調試，但 App Service 值可讓您在產品中使用生產環境設定來執行應用程式。 連接字串的工作方式相同。 如此一來，您就可以在程式碼存放庫之外保留應用程式秘密，並存取適當的值，而不需要變更您的程式碼。

## <a name="deploy-multi-project-solutions"></a>部署多專案方案

當 Visual Studio 方案包含多個專案時，Visual Studio 發行程式已包含選取要部署的專案。 當您部署到 App Service 部署引擎（例如使用 Git 或使用 ZIP 部署），並開啟組建自動化時，App Service 部署引擎會挑選第一個找到的網站或 Web 應用程式專案作為 App Service 應用程式。 您可以藉由指定應用程式設定，指定 App Service 應該使用哪個專案 `PROJECT` 。 例如，在[Cloud Shell](https://shell.azure.com)中執行下列程式：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>取得詳細例外狀況頁面

當您的 ASP.NET 應用程式在 Visual Studio 偵錯工具中產生例外狀況時，瀏覽器會顯示詳細的例外狀況頁面，但在 App Service 該頁面會由一般錯誤訊息所取代。 若要在 App Service 中顯示詳細的例外狀況頁面，請開啟*Web.config*檔案，並在專案底下加入 `<customErrors mode="Off"/>` 元素 `<system.web>` 。 例如：

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

使用更新的*Web.config*重新部署您的應用程式。您現在應該會看到相同的詳細例外狀況頁面。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

您[可以使用 system.servicemodel](https://docs.microsoft.com/dotnet/api/system.diagnostics.trace)，在應用程式的程式碼中加入診斷訊息。 例如： 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)
