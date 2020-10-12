---
title: 設定 ASP.NET apps
description: 瞭解如何在 Azure App Service 中設定 ASP.NET 應用程式。 本文說明最常見的設定工作。
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 30fddaec9ca5d0439beadedf7c5ca6b6c7d51d83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961698"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>針對 Azure App Service 設定 ASP.NET 應用程式

> [!NOTE]
> 如 ASP.NET Core，請參閱 [設定適用于 Azure App Service 的 ASP.NET Core 應用程式](configure-language-dotnetcore.md)

ASP.NET 應用程式必須部署到 Azure App Service 為編譯的二進位檔。 Visual Studio 發佈工具會建立解決方案，然後直接部署已編譯的二進位檔，而 App Service 部署引擎會先部署程式碼存放庫，然後再編譯二進位檔。

本指南提供適用于 ASP.NET 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 [ASP.NET 快速入門](quickstart-dotnet-framework.md) 和 [使用 SQL Database 教學](app-service-web-tutorial-dotnet-sqldatabase.md) 課程的 ASP.NET。

## <a name="show-supported-net-framework-runtime-versions"></a>顯示支援的 .NET Framework 執行階段版本

在 App Service 中，Windows 實例已安裝所有支援的 .NET Framework 版本。 若要顯示可供您使用的 .NET Framework 執行時間和 SDK 版本，請 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 在瀏覽器型主控台中流覽至並執行適當的命令：

針對 CLR 4 執行階段版本 ( .NET Framework 4 和更新版本) ：

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

可能無法立即使用最新的 .NET Framework 版本。

針對 CLR 2 執行階段版本 ( .NET Framework 3.5 和以下) ：

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>顯示目前的 .NET Framework 執行階段版本

在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

的值 `v4.0` 表示使用最新的 CLR 4 版本 ( .NET Framework 4.x) 。 的值 `v2.0` 表示使用 CLR 2 版本 ( .NET Framework 3.5) 。

## <a name="set-net-framework-runtime-version"></a>設定 .NET Framework 執行階段版本

根據預設，App Service 會使用最新支援的 .NET Framework 版本來執行您的 ASP.NET 應用程式。 若要改為使用 .NET Framework 3.5 來執行您的應用程式，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令 (V2.0 表示 CLR 2) ：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以在應用程式程式碼之外 [設定應用程式設定](configure-common.md#configure-app-settings) 和連接字串。 然後您就可以在任何類別中使用標準 ASP.NET 模式來存取它們：

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

如果您在 App Service 和 *web.config*中以相同的名稱來設定應用程式設定，App Service 值的優先順序會高於 *web.config* 值。 本機 *web.config* 值可讓您在本機對應用程式進行偵錯工具，但 App Service 值可讓您在產品中使用生產設置來執行應用程式。 連接字串的運作方式相同。 如此一來，您就可以將應用程式秘密保存在程式碼存放庫之外，並在不變更程式碼的情況下存取適當的值。

## <a name="deploy-multi-project-solutions"></a>部署多專案方案

當 Visual Studio 方案包含多個專案時，Visual Studio 發行程式已經包含選取要部署的專案。 當您部署至 App Service 部署引擎（例如，使用 Git 或使用「ZIP 部署」）時，如果組建自動化已開啟，則 App Service 部署引擎會挑選第一個找到的網站或 Web 應用程式專案做為 App Service 應用程式。 您可以指定應用程式設定，以指定 App Service 應使用的專案 `PROJECT` 。 例如，在 [Cloud Shell](https://shell.azure.com)中執行下列程式：

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>取得詳細的例外狀況頁面

當您的 ASP.NET 應用程式在 Visual Studio 偵錯工具中產生例外狀況時，瀏覽器會顯示詳細的例外狀況頁面，但在 App Service 該頁面會被一般錯誤訊息取代。 若要在 App Service 中顯示詳細的例外狀況頁面，請開啟 *Web.config* 檔案，然後在專案底下加入 `<customErrors mode="Off"/>` 元素 `<system.web>` 。 例如：

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

使用更新的 *Web.config*重新部署您的應用程式。您現在應該會看到相同的詳細例外狀況頁面。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

您可以在應用程式程式碼 [中使用 system.servicemodel](/dotnet/api/system.diagnostics.trace)新增診斷訊息。 例如： 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)