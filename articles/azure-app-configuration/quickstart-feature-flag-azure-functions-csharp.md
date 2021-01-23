---
title: 將功能旗標新增至 Azure Functions 的快速入門 | Microsoft Docs
description: 在本快速入門中，請搭配使用 Azure Functions 與 Azure 應用程式組態中的功能旗標，在本機測試函式。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 96efc0ea6300e482ddeeda8fa177847f02b7e126
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724241"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>快速入門：將功能旗標新增至 Azure Functions 應用程式

在本快速入門中，您會建立 Azure Functions 應用程式並在其中使用功能旗標。 您會使用 Azure 應用程式組態的管理功能來集中儲存所有功能旗標及控制其狀態。

.NET 功能功能管理程式庫可透過功能旗標支援來擴充架構。 這些程式庫會建置在 .NET 組態系統之上。 透過其 .NET 組態提供者與應用程式組態密切整合。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- 包含 **Azure 開發** 工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)。
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 選取 [功能管理員]   > [+新增]  ，以新增名為 `Beta` 的功能旗標。

    > [!div class="mx-imgBorder"]
    > ![啟用名為 Beta 的功能旗標](media/add-beta-feature-flag.png)

    請暫且將 `label` 和 `Description` 保留為未定義。

8. 選取 [套用]  以儲存新的功能旗標。

## <a name="create-a-functions-app"></a>建立 Functions 應用程式

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>連線至應用程式組態存放區

此專案將[在 .NET Azure Functions 中使用相依性插入](../azure-functions/functions-dotnet-dependency-injection.md)。 其會將 Azure 應用程式組態新增為您的功能旗標儲存所在的額外組態來源。

1. 以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]  。 在 [瀏覽] 索引標籤上，搜尋下列 NuGet 套件並新增至您的專案。
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) 4.1.0 版或更新版本
   - [Microsoft.FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) 2.2.0 版或更新版本
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) 1.1.0 版或更新版本 

2. 使用下列程式碼，新增 Startup.cs 檔案。 其會定義名為 `Startup` 的類別，以實作 `FunctionsStartup` 抽象類別。 元件屬性用來指定 Azure Functions 啟動期間所使用的類型名稱。

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. 藉由呼叫 `AddAzureAppConfiguration()`，更新 `ConfigureAppConfiguration` 方法，並將 Azure 應用程式組態提供者新增為額外的組態來源。 

   `UseFeatureFlags()` 方法會指示提供者載入功能旗標。 在重新檢查變更之前，所有功能旗標的預設快取到期時間均為 30 秒。 設定傳遞給 `UseFeatureFlags` 方法的 `FeatureFlagsOptions.CacheExpirationInterval` 屬性，即可更新到期間隔。 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > 如果不想將功能旗標以外的任何組態載入您的應用程式中，您可呼叫 `Select("_")`，只載入不存在的虛擬機碼 "_"。 根據預設，如果沒有呼叫 `Select` 方法，就會載入您應用程式組態存放區中的所有組態機碼值。

4. 更新 `Configure` 方法，使 Azure 應用程式組態服務和功能管理員可透過相依性插入來提供。

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. 開啟 Function1.cs，並新增下列命名空間。

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   新增用於透過相依性插入取得 `_featureManagerSnapshot` 和 `IConfigurationRefresherProvider` 執行個體的建構函式。 從 `IConfigurationRefresherProvider`，您可取得 `IConfigurationRefresher` 的執行個體。

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. 更新 `Run` 方法，以根據功能旗標的狀態變更顯示的訊息值。

   `TryRefreshAsync` 方法會在 Functions 呼叫的開頭進行呼叫，以重新整理功能旗標。 如果未達到快取到期時間範圍，這就不會有任何作用。 如果您想要在不封鎖目前函式呼叫的情況下重新整理功能旗標，請移除 `await` 運算子。 在此情況下，較新的函式呼叫將會取得更新的值。

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>在本機測試函式

1. 設定名為 **ConnectionString** 的環境變數，其值為您先前在應用程式組態存放區中的 [存取金鑰] 下擷取的連接字串。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 macOS 或 Linux，請執行下列命令：

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 按 F5 測試您的函式。 如果出現提示，請接受 Visual Studio 所發出要下載及安裝 **Azure Functions Core (CLI)** 工具的要求。 您可能也需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。

1. 從 Azure Functions 執行階段輸出複製函式的 URL。

    ![VS 中的函式偵錯快速入門](./media/quickstarts/function-visual-studio-debugging.png)

1. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 下圖顯示的回應指出已停用功能旗標 `Beta`。 

    ![已停用快速入門函式功能旗標](./media/quickstarts/functions-launch-ff-disabled.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]，然後選取您先前建立的應用程式組態存放區。

1. 選取 [功能管理員]，然後將 **Beta** 金鑰的狀態變更為 [開啟]。

1. 重新整理瀏覽器數次。 當快取的功能旗標在 30 秒後到期時，頁面應已變更，指出功能旗標 `Beta` 已開啟，如下圖所示。
 
    ![已啟用快速入門函式功能旗標](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> 本教學課程中使用的範例程式碼，可從 [Azure 應用程式組態 GitHub 存放庫](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)下載取得。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立功能旗標，並透過 [Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement) 程式庫將其與 Azure Functions 應用程式搭配使用。

- 深入了解[功能管理](./concept-feature-management.md)
- [編輯功能旗標](./manage-feature-flags.md)
- [使用條件式功能旗標](./howto-feature-filters-aspnet-core.md)
- [針對目標受眾啟用分段推出功能](./howto-targetingfilter-aspnet-core.md)
- [在 Azure Functions 應用程式中使用動態組態](./enable-dynamic-configuration-azure-functions-csharp.md)